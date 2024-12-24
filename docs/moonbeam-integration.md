# Integrate Bifrost SLPx on Manta

## Getting started

Make sure you add RPC for Moonbeam. Information can be obtained from [chainlist.org](https://chainlist.org/?search=moonbeam)

Or manual input with the information below
| Name | Value |
|---|---|
| Network Name | Moonbeam Network |
| Description | The public mainnet for Moonbeam Network. |
| Network URL | https://moonbeam.network |
| Explorer URL | https://moonscan.io/ |
| RPC URL | https://rpc.api.moonbeam.network |
| Chain ID | 1284 |
| Currency Symbol | GLMR |

### `xcDOT` token

| Name | Value |
|---|---|
| Address | [0xFfFFfFff1FcaCBd218EDc0EbA20Fc2308C778080](https://moonscan.io/address/0xffffffff1fcacbd218edc0eba20fc2308c778080) |
| Name | xcDOT |
| Symbol | `xcDOT` |
| Decimals | 10 |

### `xcvDOT` token

| Name | Value |
|---|---|
| Address | [0xFFFfffFf15e1b7E3dF971DD813Bc394deB899aBf](https://moonscan.io/address/0xFFFfffFf15e1b7E3dF971DD813Bc394deB899aBf) |
| Name | xcvDOT |
| Symbol | `xcvDOT` |
| Decimals | 10 |

### `xcASTR` token

| Name | Value |
|---|---|
| Address | [0xFfFFFfffA893AD19e540E172C10d78D4d479B5Cf](https://moonscan.io/address/0xffffffffa893ad19e540e172c10d78d4d479b5cf) |
| Name | xcASTR |
| Symbol | `xcASTR` |
| Decimals | 18 |

### `xcvASTR` token

| Name | Value |
|---|---|
| Address | [0xFffFffff55C732C47639231a4C4373245763d26E](https://moonscan.io/address/0xFffFffff55C732C47639231a4C4373245763d26E) |
| Name | xcvASTR |
| Symbol | `xcvASTR` |
| Decimals | 18 |

### `xcvGLMR` token

| Name | Value |
|---|---|
| Address | [0xFfFfFFff99dABE1a8De0EA22bAa6FD48fdE96F6c](https://moonscan.io/address/0xFfFfFFff99dABE1a8De0EA22bAa6FD48fdE96F6c) |
| Name | xcvGLMR |
| Symbol | `xcvGLMR` |
| Decimals | 18 |

### `MoonbeamSlpx.sol` contract

| Name | Value |
|---|---|
| Address | [0xF1d4797E51a4640a76769A50b57abE7479ADd3d8](https://moonscan.io/address/0xF1d4797E51a4640a76769A50b57abE7479ADd3d8)   |
| Source code | [MoonbeamSlpx.sol](https://github.com/bifrost-io/slpx-contracts/blob/main/contracts/MoonbeamSlpx.sol) |
| ABI | [MoonbeamSlpx.json](https://github.com/bifrost-io/slpx-contracts/blob/main/deployments/moonbeam/MoonbeamSlpx.json) |

`create_order`  
```solidity
/**
  * @dev Create order to mint vAsset or redeem vAsset on bifrost chain
  * @param assetAddress The address of the asset to mint or redeem
  * @param amount The amount of the asset to mint or redeem
  * @param dest_chain_id When order is executed on Bifrost, Asset/vAsset will be transferred to this chain
  * @param receiver The receiver address on the destination chain, 20 bytes for EVM, 32 bytes for Substrate
  * @param remark The remark of the order, less than 32 bytes. For example, "OmniLS"
  * @param channel_id The channel id of the order, you can set it. Bifrost chain will use it to share reward.
  **/
  function create_order(
      address assetAddress,
      uint128 amount,
      uint64 dest_chain_id,
      bytes memory receiver,
      string memory remark,
      uint32 channel_id
  ) external payable;
```

| Variable | Input value | Definition | 
|---|---|---|
| `address assetAddress` | a valid Moonbeam token contract address | Address of different tokens on Moonbeam |
| `uint128 amount` | `uint128` | Amount of tokens to mint or redeem |
| `uint64 dest_chain_id` | `1284` | Chain ID of Moonbeam |
| `bytes memory receiver` | a valid Moonbeam address | Asset receiver address on the destination chain, 20 bytes for EVM |
| `string memory remark` | `string` of less than 32 bytes | A string used to identify the order |
| `uint32 channel_id` | `uint32` | Channel ID of the order. Used for the Bifrost Protocol Revenue Sharing Program (RSP). You can set it if you have one. Check [here](https://docs.bifrost.io/for-partners/reward-share-program-rsp) to learn more |


**Token address list**

| Token | Address |
|---|---|
| `xcDOT` | `0xFfFFfFff1FcaCBd218EDc0EbA20Fc2308C778080` |
| `xcASTR` | `0xFfFFFfffA893AD19e540E172C10d78D4d479B5Cf` |
| `GLMR` | `0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE` as per [ERC-7528](https://eips.ethereum.org/EIPS/eip-7528). Only use this for frontend identification |
