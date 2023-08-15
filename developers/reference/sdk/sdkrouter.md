# SdkRouter

SDK class encapsulating router functions.

## Hierarchy

*   `SdkShared`

    ↳ **`SdkRouter`**

## Methods

### addLiquidityForRouter

▸ **addLiquidityForRouter**(`params`): `Promise`<`TransactionRequest`>

Returns the transaction request for adding liquidity to a router.

**Parameters**

| Name                  | Type     | Description                              |
| --------------------- | -------- | ---------------------------------------- |
| `params`              | `Object` | addLiquidityForRouter parameters object. |
| `params.amount`       | `string` | The amount of the token to add.          |
| `params.domainId`     | `string` | The domain ID.                           |
| `params.router`       | `string` | The address of the router.               |
| `params.tokenAddress` | `string` | The address of the token.                |

**Returns**

`Promise`<`TransactionRequest`>

providers.TransactionRequest object.

***

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

### removeRouterLiquidity

▸ **removeRouterLiquidity**(`params`): `Promise`<`TransactionRequest`>

Returns the transaction request for removing liquidity from a router.

**`Remarks`**

This function is permissioned to the router owner only.

**Parameters**

| Name                  | Type     | Description                                            |
| --------------------- | -------- | ------------------------------------------------------ |
| `params`              | `Object` | removeRouterLiquidity parameters object.               |
| `params.amount`       | `string` | The amount of the token to add.                        |
| `params.domainId`     | `string` | The domain ID.                                         |
| `params.recipient`    | `string` | The address where the removed funds will be delivered. |
| `params.tokenAddress` | `string` | The address of the token.                              |

**Returns**

`Promise`<`TransactionRequest`>

providers.TransactionRequest object.

***

### removeRouterLiquidityFor

▸ **removeRouterLiquidityFor**(`params`): `Promise`<`TransactionRequest`>

**Parameters**

| Name                  | Type     |
| --------------------- | -------- |
| `params`              | `Object` |
| `params.amount`       | `string` |
| `params.domainId`     | `string` |
| `params.recipient`    | `string` |
| `params.router`       | `string` |
| `params.tokenAddress` | `string` |

**Returns**

`Promise`<`TransactionRequest`>

***

### create

▸ `Static` **create**(`_config`): `Promise`<`SdkRouter`>

Create a singleton instance of the SdkRouter class.

**Parameters**

| Name                    | Type                                                                                   | Default value | Description                                             |
| ----------------------- | -------------------------------------------------------------------------------------- | ------------- | ------------------------------------------------------- |
| `_config`               | `Object`                                                                               | undefined     | SdkConfig object.                                       |
| `_config.chains`        | `Record`<`string`, { providers: string\[] }>                                           | undefined     | Chain config, at minimum with providers for each chain. |
| `_config.signerAddress` | `string`                                                                               | undefined     | Signer address for transactions.                        |
| `_config.logLevel`      | `"fatal"` \| `"error"` \| `"warn"` \| `"info"` \| `"debug"` \| `"trace"` \| `"silent"` | "info"        | (optional) Logging severity level.                      |
| `_config.network`       | `"testnet"` \| `"mainnet"`                                                             | "mainnet"     | (optional) Blockchain environment to interact with.     |

**Returns**

`Promise`<`SdkRouter`>

providers.TransactionRequest object.

**`Example`**

```ts
import { SdkRouter } from "@connext/sdk";

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

const sdkRouter = await SdkRouter.create(config);
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
