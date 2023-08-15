# SdkUtils

SDK class encapsulating utility functions.

## Hierarchy

*   `SdkShared`

    ↳ **`SdkUtils`**

## Methods

### approveIfNeeded

▸ **approveIfNeeded**(`domainId`, `assetId`, `amount`, `infiniteApprove?`): `Promise`<`undefined` | `TransactionRequest`>

Returns the transaction request for an allowance approval.

**Parameters**

| Name              | Type      | Default value | Description                                       |
| ----------------- | --------- | ------------- | ------------------------------------------------- |
| `domainId`        | `string`  | `undefined`   | The domain ID.                                    |
| `assetId`         | `string`  | `undefined`   | The address of the token.                         |
| `amount`          | `string`  | `undefined`   | The amount of the token.                          |
| `infiniteApprove` | `boolean` | `true`        | (optional) Whether to approve an infinite amount. |

**Returns**

`Promise`<`undefined` | `TransactionRequest`>

providers.TransactionRequest object.

**Inherited from**

SdkShared.approveIfNeeded

***

### calculateCanonicalKey

▸ **calculateCanonicalKey**(`domainId`, `canonicalId`): `string`

Returns the hash of the canonical ID + canonical domain.

**`Remarks`**

This key is used as the unique identifier for a canonical token, across all domains.

**Parameters**

| Name          | Type     | Description                           |
| ------------- | -------- | ------------------------------------- |
| `domainId`    | `string` | The canonical domain ID of the token. |
| `canonicalId` | `string` | The canonical ID of the token.        |

**Returns**

`string`

**Inherited from**

SdkShared.calculateCanonicalKey

***

### changeSignerAddress

▸ **changeSignerAddress**(`signerAddress`): `Promise`<`void`>

Switches the signer address in the SDK config.

**Parameters**

| Name            | Type     | Description             |
| --------------- | -------- | ----------------------- |
| `signerAddress` | `string` | The new signer address. |

**Returns**

`Promise`<`void`>

**Inherited from**

SdkShared.changeSignerAddress

***

### getAssetsData

▸ **getAssetsData**(): `Promise`<`AssetData`\[]>

Fetches the list of registered assets.

**Returns**

`Promise`<`AssetData`\[]>

Array of objects containing assets registered to the network, in the form of:

```ts
{
  "local": "0x2983bf5c334743aa6657ad70a55041d720d225db",
  "adopted": "0x82af49447d8a07e3bd95bd0d56f35241523fbab1",
  "canonical_id": "0x000000000000000000000000c02aaa39b223fe8d0a0e5c4f27ead9083c756cc2",
  "canonical_domain": "6648936",
  "domain": "1634886255",
  "key": "0x12acadfa38ab02479ae587196a9043ee4d8bf52fcb96b7f8d2ba240f03bcd08a",
  "id": "0x2983bf5c334743aa6657ad70a55041d720d225db"
},
```

**Inherited from**

SdkShared.getAssetsData

***

### getAssetsDataByDomainAndKey

▸ **getAssetsDataByDomainAndKey**(`domainId`, `key`): `Promise`<`undefined` | `AssetData`>

Retrieve the asset data for a specific domain and key.

**Parameters**

| Name       | Type     | Description                                |
| ---------- | -------- | ------------------------------------------ |
| `domainId` | `string` | The domain ID.                             |
| `key`      | `string` | The canonical hash of the canonical token. |

**Returns**

`Promise`<`undefined` | `AssetData`>

The object containing asset data.

**Inherited from**

SdkShared.getAssetsDataByDomainAndKey

***

### getCanonicalTokenId

▸ **getCanonicalTokenId**(`domainId`, `tokenAddress`): `Promise`<\[`string`, `string`]>

Returns the canonical ID and canonical domain of a token.

**Parameters**

| Name           | Type     | Description                           |
| -------------- | -------- | ------------------------------------- |
| `domainId`     | `string` | The canonical domain ID of the token. |
| `tokenAddress` | `string` | The address of the token.             |

**Returns**

`Promise`<\[`string`, `string`]>

**Inherited from**

SdkShared.getCanonicalTokenId

***

### getRoutersData

▸ **getRoutersData**(): `Promise`<`any`>

Fetches a list of router liquidity data.

**Returns**

`Promise`<`any`>

Array of objects containing the router address and liquidity information, in the form of:

```ts
{
  "address": "0xf26c772c0ff3a6036bddabdaba22cf65eca9f97c",
  "asset_canonical_id": "0x000000000000000000000000a0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
  "asset_domain": "1869640809",
  "router_address": "0xf26c772c0ff3a6036bddabdaba22cf65eca9f97c",
  "balance": 8816006545,
  "local": "0x67e51f46e8e14d4e4cab9df48c59ad8f512486dd",
  "adopted": "0x7f5c764cbc14f9669b88837ca1490cca17c31607",
  "canonical_id": "0x000000000000000000000000a0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
  "canonical_domain": "6648936",
  "domain": "1869640809",
  "key": "0x6d9af4a33ed4034765652ab0f44205952bc6d92198d3ef78fe3fb2b078d0941c",
  "id": "0x67e51f46e8e14d4e4cab9df48c59ad8f512486dd",
  "fees_earned": 7249237
}
```

***

### getTransfers

▸ **getTransfers**(`params`): `Promise`<`any`>

Fetches the transfers that match filter criteria.

**Parameters**

| Name                      | Type              |
| ------------------------- | ----------------- |
| `params`                  | `Object`          |
| `params.range?`           | `Object`          |
| `params.range.limit?`     | `number`          |
| `params.range.offset?`    | `number`          |
| `params.routerAddress?`   | `string`          |
| `params.status?`          | `XTransferStatus` |
| `params.transactionHash?` | `string`          |
| `params.transferId?`      | `string`          |
| `params.userAddress?`     | `string`          |

**Returns**

`Promise`<`any`>

The object containing transfer data in the form of:

```ts
{
  "transfer_id": "0x4a379d3367bb589ddc00dd7c2d7d6557bed75c9595e5cd6a4369d85e587ec386",
  "nonce": 34,
  "to": "0x6d2a06543d23cc6523ae5046add8bb60817e0a94",
  "call_data": "0x",
  "origin_domain": "6778479",
  "destination_domain": "6648936",
  "receive_local": false,
  "origin_chain": "100",
  "origin_transacting_asset": "0x6a023ccd1ff6f2045c3309768ead9e68f978f6e1",
  "origin_transacting_amount": "100000000000000",
  "origin_bridged_asset": "0x538e2ddbfdf476d24ccb1477a518a82c9ea81326",
  "origin_bridged_amount": "99407526243394",
  "xcall_caller": "0x6d2a06543d23cc6523ae5046add8bb60817e0a94",
  "xcall_transaction_hash": "0xd1b4f723c1f7453bc38e8dd64f56830ed1e907b95c8b5eba55a9f1a26d867ea8",
  "xcall_timestamp": 1672964955,
  "xcall_gas_price": "4654771330",
  "xcall_gas_limit": "511921",
  "xcall_block_number": 25819530,
  "destination_chain": "1",
  "status": "CompletedFast",
  "routers": [
    "0xf26c772c0ff3a6036bddabdaba22cf65eca9f97c"
  ],
  "destination_transacting_asset": "0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2",
  "destination_transacting_amount": "99357822480272",
  "destination_local_asset": "0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2",
  "destination_local_amount": "99407526243394",
  "execute_caller": "0x75c6a865c30da54e365cb5def728890b3dd8bdc4",
  "execute_transaction_hash": "0x7e02bd79087ec48d9588f655474ba7f38921b46ab8ff812f2b2a8b97bad9fa72",
  "execute_timestamp": 1672965155,
  "execute_gas_price": "32181349289",
  "execute_gas_limit": "4000000",
  "execute_block_number": 16344186,
  "execute_origin_sender": "0x6d2a06543d23cc6523ae5046add8bb60817e0a94",
  "reconcile_caller": "0xf7c4d7dcec2c09a15f2db5831d6d25eaef0a296c",
  "reconcile_transaction_hash": "0xe3c8042bcd2e943df1d3a38c75bfee132827f3a8a3a1efedf1e4e96cecd72e6b",
  "reconcile_timestamp": 1672986047,
  "reconcile_gas_price": "19184906166",
  "reconcile_gas_limit": "4000000",
  "reconcile_block_number": 16345915,
  "update_time": "2023-01-12T04:56:14.72407",
  "delegate": "0x6d2a06543d23cc6523ae5046add8bb60817e0a94",
  "message_hash": "0x327618edf7bab0e7c6b97ecee50ad6572e9c069a85db3083c942e0c0ddc469b7",
  "canonical_domain": "6648936",
  "slippage": 300,
  "origin_sender": "0x6d2a06543d23cc6523ae5046add8bb60817e0a94",
  "bridged_amt": "99407526243394",
  "normalized_in": "100000000000000",
  "canonical_id": "0x000000000000000000000000c02aaa39b223fe8d0a0e5c4f27ead9083c756cc2",
  "router_fee": null,
  "xcall_tx_origin": "0x6d2a06543d23cc6523ae5046add8bb60817e0a94",
  "execute_tx_origin": "0x29d33fcd30240d55b9280362599d5066c1a2cf10",
  "reconcile_tx_origin": "0x29d33fcd30240d55b9280362599d5066c1a2cf10",
  "relayer_fee": "8424181656635272573"
}
```

***

### getSupported

▸ **getSupported**(): `Promise`<`ConnextSupport`\[]>

Fetches the list of supported networks and assets.

**Returns**

`Promise`<`ConnextSupport`\[]>

Array of objects containing networks and assets supported by the protocol, in the form of:

```ts
{	
  "name": "arbitrum",	
  "chainId": 42161,	
  "domainId": "1634886255",	
  "assets": [	
    "0x82af49447d8a07e3bd95bd0d56f35241523fbab1",	
    "0xff970a61a04b1ca14834a43f5de4533ebddb5cc8"	
  ]	
},	
```

**Inherited from**

SdkShared.getSupported

***

### isNextAsset

▸ **isNextAsset**(`tokenAddress`): `Promise`<`undefined` | `boolean`>

Returns whether the specified token is a Connext-issued (local) token.

**Parameters**

| Name           | Type     | Description               |
| -------------- | -------- | ------------------------- |
| `tokenAddress` | `string` | The address of the token. |

**Returns**

`Promise`<`undefined` | `boolean`>

Boolean or undefined if the specified token is not registered.

**Inherited from**

SdkShared.isNextAsset

***

### parseConnextTransactionReceipt

▸ **parseConnextTransactionReceipt**(`transactionReceipt`): `any`

Parses a providers.TransactionReceipt for the logs.

**Parameters**

| Name                 | Type                 | Description                          |
| -------------------- | -------------------- | ------------------------------------ |
| `transactionReceipt` | `TransactionReceipt` | providers.TransactionReceipt object. |

**Returns**

`any`

Array of providers.Log objects.

**Inherited from**

SdkShared.parseConnextTransactionReceipt

***

### create

▸ `Static` **create**(`_config`): `Promise`<`SdkUtils`>

Create a singleton instance of the SdkUtils class.

**Parameters**

| Name                    | Type                                                                                   | Default value | Description                                             |
| ----------------------- | -------------------------------------------------------------------------------------- | ------------- | ------------------------------------------------------- |
| `_config`               | `Object`                                                                               | undefined     | SdkConfig object.                                       |
| `_config.chains`        | `Record`<`string`, { providers: string\[] }>                                           | undefined     | Chain config, at minimum with providers for each chain. |
| `_config.signerAddress` | `string`                                                                               | undefined     | Signer address for transactions.                        |
| `_config.logLevel`      | `"fatal"` \| `"error"` \| `"warn"` \| `"info"` \| `"debug"` \| `"trace"` \| `"silent"` | "info"        | (optional) Logging severity level.                      |
| `_config.network`       | `"testnet"` \| `"mainnet"`                                                             | "mainnet"     | (optional) Blockchain environment to interact with.     |

**Returns**

`Promise`<`SdkUtils`>

providers.TransactionRequest object.

**`Example`**

```ts
import { SdkUtils } from "@connext/sdk";

const config = {
  signerAddress: "<wallet_address>",
  network: "mainnet",
  chains: {
    6648936: { // the domain ID for Ethereum Mainnet
      providers: ["https://rpc.ankr.com/eth"],
    },
    1869640809: { // the domain ID for Optimism
      providers: ["https://mainnet.optimism.io"]
    },
    1886350457: { // the domain ID for Polygon
      providers: ["https://polygon-rpc.com"]
    },
  },
}

const sdkBase = await SdkUtils.create(config);
```

{% hint style="info" %}
See the [Deployments](broken-reference) page for all domain IDs and asset addresses.
{% endhint %}

***

### domainToChainName

▸ `Static` **domainToChainName**(`domainId`): `string`

Returns the chain name for a specified domain.

**Parameters**

| Name       | Type     | Description    |
| ---------- | -------- | -------------- |
| `domainId` | `string` | The domain ID. |

**Returns**

`string`

The chain name.

**Inherited from**

SdkShared.domainToChainName

***

### getBlockNumberFromUnixTimestamp

▸ `Static` **getBlockNumberFromUnixTimestamp**(`domainId`, `unixTimestamp`): `Promise`<`number`>

**Parameters**

| Name            | Type     | Description         |
| --------------- | -------- | ------------------- |
| `domainId`      | `string` | The domain ID.      |
| `unixTimestamp` | `number` | The unix timestamp. |

**Returns**

`Promise`<`number`>

**Inherited from**

SdkShared.getBlockNumberFromUnixTimestamp
