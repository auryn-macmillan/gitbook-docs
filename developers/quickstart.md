# Quickstart

## Quickstart (Greeter)

This quickstart will teach you how to use `xcall`, the cross-chain communication primitive, to send funds and data across chains.

***

### Introduction

In this guide, we will build a cross-chain Greeter. The `DestinationGreeter` contract on the destination chain has an `updateGreeting` function that changes a stored `greeting` variable. The `SourceGreeter` contract on the origin chain uses `xcall` to send encoded calldata for `updateGreeting`.

To demonstrate a combination of an asset transfer and an arbitrary call in a single `xcall`, the `updateGreeting` function will require a payment to update the greeting. For this example, the contract will be okay with any amount greater than 0.

`updateGreeting` is implemented as an unauthenticated call (there are no checks to determine _who_ is calling the function). Therefore, this type of `xcall` will be go through the "Fast Path".

{% hint style="success" %}
If you prefer to fork a repo instead of following this step-by-step guide, our [xapp-starter](https://github.com/connext/xapp-starter) kit contains a full example of this quickstart (plus more) and is compatible with both Hardhat and Foundry.
{% endhint %}

### Prerequisites

*   Node v18 installed

    Follow the instructions to install [Node.js](https://nodejs.dev/en/learn/how-to-install-nodejs/) and use **Node.js v18**. We also recommend installing `nvm`, a node version manager, which will make switching versions easier.
*   An Ethereum development environment like Foundry, Hardhat, Truffle, etc.

    This guide will be using Hardhat. Follow the instructions to install [Hardhat](https://hardhat.org/hardhat-runner/docs/getting-started#overview).
* If you don't already have gas funds on Goerli, try these faucets to get some:
  * https://goerli-faucet.mudit.blog/ (Requires Twitter account)
  * https://goerlifaucet.com/ (Requires signing up with Alchemy)

### Create a new project

Create a new project by running the following command:

```bash
$ npx hardhat
888    888                      888 888               888
888    888                      888 888               888
888    888                      888 888               888
8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
888    888 .d888888 888    888  888 888  888 .d888888 888
888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888

👷 Welcome to Hardhat v2.12.1 👷‍

? What do you want to do? …
❯ Create a JavaScript project
  Create a TypeScript project
  Create an empty hardhat.config.js
  Quit
```

Choose a Javascript project. Choose `y` on all of the prompts.

Install the latest version of Connext contracts package in your project:

```bash
npm install @connext/interfaces
```

Next, install the OpenZeppelin contract package:

```bash
npm install @openzeppelin/contracts
```

You'll need to manually install the library `@openzeppelin/contracts-upgradeable`

```bash
npm install @openzeppelin/contracts-upgradeable 
```

Install `dotenv` to protect your private key needed to deploy your contract:

```bash
npm install dotenv
```

In the root of your project, create a new `.env` file. Here you will store your private key used to deploy your contract.

Update `.env` with the following line:

```console
PRIVATE_KEY = YOUR-PRIVATE-KEY-HERE
```

***

### Source Contract

The source contract initiates the cross-chain operation with `xcall` and passes the encoded greeting into the call. All `xcall` params are detailed here.

In the `/contracts` directory, create a new contract called `SourceGreeter.sol`:

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.15;

import {IConnext} from "@connext/interfaces/core/IConnext.sol";
import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

/**
 * @title SourceGreeter
 * @notice Example source contract that updates a greeting on DestinationGreeter.
 */
contract SourceGreeter {
  // The Connext contract on this domain
  IConnext public immutable connext;

  // The token to be paid on this domain
  IERC20 public immutable token;
  
  // Slippage (in BPS) for the transfer set to 100% for this example
  uint256 public immutable slippage = 10000;

  constructor(address _connext, address _token) {
    connext = IConnext(_connext);
    token = IERC20(_token);
  }

  /** @notice Updates a greeting variable on the DestinationGreeter contract.
    * @param target Address of the DestinationGreeter contract.
    * @param destinationDomain The destination domain ID.
    * @param newGreeting New greeting to update to.
    * @param relayerFee The fee offered to relayers.
    */
  function xUpdateGreeting (
    address target, 
    uint32 destinationDomain,
    string memory newGreeting,
    uint256 amount,
    uint256 relayerFee
  ) external payable {
    require(
      token.allowance(msg.sender, address(this)) >= amount,
      "User must approve amount"
    );

    // User sends funds to this contract
    token.transferFrom(msg.sender, address(this), amount);

    // This contract approves transfer to Connext
    token.approve(address(connext), amount);

    // Encode calldata for the target contract call
    bytes memory callData = abi.encode(newGreeting);

    connext.xcall{value: relayerFee}(
      destinationDomain, // _destination: Domain ID of the destination chain
      target,            // _to: address of the target contract
      address(token),    // _asset: address of the token contract
      msg.sender,        // _delegate: address that can revert or forceLocal on destination
      amount,            // _amount: amount of tokens to transfer
      slippage,          // _slippage: max slippage the user will accept in BPS (e.g. 300 = 3%)
      callData           // _callData: the encoded calldata to send
    );
  }
}
```

`xUpdateGreeting` is what the user will call on origin to initiate the `xcall`.



{% hint style="info" %}
One important detail to note is that `xUpdateGreeting` is a `payable` method. This is necessary because a `relayerFee` in native gas is passed into the `xcall`. More on how this fee is determined later.
{% endhint %}

#### Compile Contract

Make sure the solidity compiler version in your `hardhat.config.js` is at least `0.8.17`.

```js
module.exports = {
  solidity: "0.8.17",
};
```

Compile the contract with the following command:

```bash
npx hardhat compile
```

> Note: Hardhat may require you to manually install dependencies for @nomicfoundation/hardhat-toolbox. If you get an error about missing dependencies for that plugin, run the following command:

```bash
npm install --save-dev "@nomicfoundation/hardhat-network-helpers@^1.0.0" "@nomicfoundation/hardhat-chai-matchers@^1.0.0" "@nomiclabs/hardhat-ethers@^2.0.0" "@nomiclabs/hardhat-etherscan@^3.0.0" "@types/chai@^4.2.0" "@types/mocha@^9.1.0" "@typechain/ethers-v5@^10.1.0" "@typechain/hardhat@^6.1.2" "solidity-coverage@^0.8.1" "ts-node@>=8.0.0" "typescript@>=4.5.0"
```

#### Deploy Contract

Update the `hardhat.config.js` file:

```js
require("@nomicfoundation/hardhat-toolbox");
require('@openzeppelin/hardhat-upgrades');
require('dotenv').config();
 
module.exports = {
  solidity: "0.8.17",
  networks:{
    goerli:{
      url: "https://rpc.ankr.com/eth_goerli",
      accounts: [`0x${process.env.PRIVATE_KEY}`]
    }
  }
};
```

Create a `/scripts/deploySource.js` file with the following:

```js
const main = async () => {
  const sourceGreeterContract = await hre.ethers.deployContract(
    "SourceGreeter",
    [
      "0xFCa08024A6D4bCc87275b1E4A1E22B71fAD7f649", // Connext on Goerli
      "0x7ea6eA49B0b0Ae9c5db7907d139D9Cd3439862a1" // TEST on Goerli
    ]
  );
  await sourceGreeterContract.waitForDeployment();
  console.log("Contract deployed to:", await sourceGreeterContract.getAddress());
};

const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.log(error);
    process.exit(1);
  }
};

runMain();
```

The addresses for Connext and supported tokens in different domains can be referenced [here](https://docs.connext.network/resources/deployments). We'll be using Goerli as our origin domain and the TEST token for this contract.

Now run the deploy script:

```bash
npx hardhat run scripts/deploySource.js --network goerli
```

> Output: `Contract deployed to: 0x9Af84578B89FcA019580af02326388987A074ca1`

#### Verify Contract

Add an `etherScan` section to `hardhat.config.js` with your `goerli` api key (note: Etherscan API keys for the mainnet explorers will work for testnets):

```js
module.exports = {
  solidity: "0.8.17",
  networks:{
    goerli:{
      url: "https://rpc.ankr.com/eth_goerli",
     // PRIVATE_KEY loaded from .env file
      accounts: [`0x${process.env.PRIVATE_KEY}`]
    }
  },
  //highlight-start
  etherscan: {
    apiKey: {
      goerli: "YOUR-API-KEY-HERE",
    }
  }
  //highlight-end
};
```

Using the contract address you just deployed, run the `hardhat verify` command, including the contract address and its constructor arguments:

```bash
npx hardhat verify --network goerli 0x9Af84578B89FcA019580af02326388987A074ca1 0xFCa08024A6D4bCc87275b1E4A1E22B71fAD7f649 0x7ea6eA49B0b0Ae9c5db7907d139D9Cd3439862a1
```

If you run into any errors like `ProviderError: Too Many Requests`, then replace the public RPC url in `hardhat.config.js` with another one from https://chainlist.org/ or use your own private RPC from a provider like Infura or Alchemy.

### Target Contract

In the `/contracts` directory, create another contract called `DestinationGreeter.sol`:

All target contracts must implement Connext's `IXReceiver` interface. This interface ensures that Connext can call the contract and pass necessary data.

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.15;

import {IXReceiver} from "@connext/interfaces/core/IXReceiver.sol";
import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

/**
 * @title DestinationGreeter
 * @notice Example destination contract that stores a greeting.
 */
contract DestinationGreeter is IXReceiver {
  string public greeting;

  // The token to be paid on this domain
  IERC20 public immutable token;

  constructor(address _token) {
    token = IERC20(_token);
  }

  /** @notice The receiver function as required by the IXReceiver interface.
    * @dev The Connext bridge contract will call this function.
    */
  function xReceive(
    bytes32 _transferId,
    uint256 _amount,
    address _asset,
    address _originSender,
    uint32 _origin,
    bytes memory _callData
  ) external returns (bytes memory) {
    // Check for the right token
    require(
      _asset == address(token),
      "Wrong asset received"
    );
    // Enforce a cost to update the greeting
    require(
      _amount > 0,
      "Must pay at least 1 wei"
    );

    // Unpack the _callData
    string memory newGreeting = abi.decode(_callData, (string));

    _updateGreeting(newGreeting);
  }

  /** @notice Internal function to update the greeting.
    * @param newGreeting The new greeting.
    */
  function _updateGreeting(string memory newGreeting) internal {
    greeting = newGreeting;
  }
}
```

#### Compile Contract

Compile:

```bash
npx hardhat compile
```

#### Deploy Contract

Add another entry to `hardhat.config.js`, this time for Optimism-Goerli.

```js
module.exports = {
  solidity: "0.8.17",
  networks:{
    "goerli":{
      url: "https://rpc.ankr.com/eth_goerli",
      accounts: [`0x${process.env.PRIVATE_KEY}`]
    },
    //highlight-start
    "optimism-goerli":{
      url: "https://goerli.optimism.io",
      accounts: [`0x${process.env.PRIVATE_KEY}`],
      // gasPrice: 800000 // you may need to set this manually if you get "transaction underpriced"
    }
    //highlight-end
  }
};
```

Create a `scripts/deployTarget.js` file with the following:

```js
const main = async () => {
  const destinationGreeterContract = await hre.ethers.deployContract(
    "DestinationGreeter",
    [
      "0x68Db1c8d85C09d546097C65ec7DCBFF4D6497CbF" // TEST on Optimism-Goerli
    ]
  );
  await destinationGreeterContract.waitForDeployment();
  console.log("Contract deployed to:", await destinationGreeterContract.getAddress());
};

const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.log(error);
    process.exit(1);
  }
};

runMain();
```

Then run the deploy script:

```bash
npx hardhat run scripts/deployTarget.js --network optimism-goerli
```

> Output: `Contract deployed to: 0xC4e508cEe84499958a84C3562e92bD9e71d7D38a`

#### Verify Contract

Add an `apiKey` to `hardhat.config.js` for `optimism-goerli`:

```js
module.exports = {
  solidity: "0.8.17",
  networks:{
    goerli:{
      url: "https://rpc.ankr.com/eth_goerli",
     // PRIVATE_KEY loaded from .env file
      accounts: [`0x${process.env.PRIVATE_KEY}`]
    }
  },
  etherscan: {
    apiKey: {
      goerli: "YOUR-API-KEY-HERE",
      //highlight-start
      optimisticGoerli: "YOUR-API-KEY-HERE",
      //highlight-end
    }
  }
};
```

Using the contract address you just deployed, verify it:

```bash
npx hardhat verify --network optimism-goerli 0xC4e508cEe84499958a84C3562e92bD9e71d7D38a 0x68Db1c8d85C09d546097C65ec7DCBFF4D6497CbF
```

If you run into any errors like `ProviderError: Too Many Requests`, then replace the public RPC url in `hardhat.config.js` with another one from https://chainlist.org/ or use your own private RPC with a provider like Infura or Alchemy.

### Executing the Transaction

You should try the following steps on your own deployed contracts. For the lazy ones, you can just use these contracts we've deployed already:

* [`SourceGreeter.sol`](https://goerli.etherscan.io/address/0x9Af84578B89FcA019580af02326388987A074ca1)
* [`DestinationGreeter.sol`](https://goerli-optimism.etherscan.io/address/0xC4e508cEe84499958a84C3562e92bD9e71d7D38a)

#### Mint TEST Tokens

First, you will need some TEST tokens. Recall that the destination contract requires a payment > 0 TEST in order to update its greeting.

Since you'll be updating the greeting from the origin chain, you will need to acquire some TEST tokens on the origin chain.

You can use Etherscan to call functions on (verified) contracts. Go to the [TEST Token on Etherscan](https://goerli.etherscan.io/address/0x7ea6eA49B0b0Ae9c5db7907d139D9Cd3439862a1#writeContract) and click on the "Write Contract" button.

A new tab will show up with all write functions of the contract. Connect your wallet, switch to the Goerli network, and enter the parameters for the `mint` function:

* `account`: \<YOUR\_WALLET\_ADDRESS>
* `amount`: 10000000000000000000
  * 10 TEST. You can actually mint however much you want.

#### Approve TEST Tokens

Tokens will move from `User's wallet` => `SourceGreeter` => `Connext` => `DestinationGreeter`.

The user must first approve a spending allowance of the TEST ERC20 to the `SourceGreeter` contract. The `require` clause starting on line 39 checks for this allowance.

Again, on the Etherscan page for the TEST token, enter the parameters for the `approve` function:

* `spender`: 0x9Af84578B89FcA019580af02326388987A074ca1
  * This is the address of `SourceGreeter`.
* `amount`: 10000000000000000000

Then "Write" to the `approve` function.

#### Execute `xUpdateGreeting`

Similarly to the approval function for TEST, navigate to the `SourceGreeter` contract on Etherscan. Fill out the `xUpdateGreeting` function parameters and "Write" to the contract.

Let's walk through the different parameters.

* `xUpdateGreeting` (payableAmount): 0.03
  * This is the native gas that you're sending into the `xcall`. This value must match what you pass in as `relayerFee`, but note that it's in ETH units here and wei units in `relayerFee`.
* `target`: 0xC4e508cEe84499958a84C3562e92bD9e71d7D38a
  * The address of `DestinationGreeter`.
* `destinationDomain`: 1735356532
  * The Domain ID of the destination chain. You can find a mapping of Domain IDs here. For this example, `DestinationGreeter` is deployed to Optimism-Goerli.
* `newGreeting`: hello chain!
  * Whatever string you want to update the greeting to.
* `amount`: 1000000000000000000
  * The amount of TEST tokens to pay. We send 1 TEST here.
* `relayerFee`: 30000000000000000
  * 0.03 goerli ETH, in wei units. Just a conservative estimate for relayers on testnet.
  *   **IMPORTANT!** This is a fee paid to relayers, which are off-chain agents that help execute the final leg of the cross-chain transfer on destination. Relayers get paid in the origin chain's native asset. This is why `SourceGreeter` passes the fee like so:

      ```solidity
      connext.xcall{value: relayerFee}(...)
      ```

{% hint style="info" %}
As a xApp developer, you have some tools available to estimate what this `relayerFee` should be. For now, there are offchain methods for doing so - check out the guide on [Estimating Fees](guides/estimating-fees.md).
{% endhint %}

#### Track the xcall

After executing `updateGreeting`, you can use [Connextscan (testnet)](https://testnet.connextscan.io) to check the status of the `xcall`. Just search up the transaction hash from the execution transaction.&#x20;

Note that if your `relayerFee` was too low, the explorer will prompt you to increase it.

#### Check `DestinationGreeter`

`DestinationGreeter` should be updated in just a few minutes (because this call is unauthenticated!). Cross-chain calls are not always this fast - see our guide on Authentication.

Head over to the `DestinationGreeter` contract on Etherscan. This time, we'll go to the `Read Contract` tab and look at the value of `greeting`. It has updated!

Send a couple more updates from `SourceGreeter` but make it a different string. At some point, your TEST allowance to `HelloSource` will run out and you'll need to do the approval dance again.

Congrats! You've gone cross-chain!

***

### Next Steps

* Try [tracking the status](guides/xcall-status.md) of an `xcall` after you send it.
* Learn about [authentication](guides/authentication.md) and important security considerations.
* See how [nested xcalls](guides/nested-xcalls.md) can open up infinite cross-chain possibilities.
* Fork the [xApp Starter Kit](https://github.com/connext/xapp-starter/) (includes code for this example) and build your own xApp.
