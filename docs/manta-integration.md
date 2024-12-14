# Integrate Bifrost SLPx on Manta

## Getting started

Make sure you add RPC for Manta. Information can be obtained from [chainlist.org](https://chainlist.org/?search=manta)

Or manual input with the information below
| Name | Value |
|---|---|
| Network Name | Manta Pacific L2 |
| Description | The public mainnet for Manta Pacific. |
| Network URL | https://pacific-info.manta.network/ |
| Explorer URL | https://pacific-explorer.manta.network/ |
| RPC URL | https://manta-pacific.drpc.org |
| Chain ID | 169 |
| Currency Symbol | ETH |

### `MANTA` token

| Name | Value |
|---|---|
| Address | [0x95CeF13441Be50d20cA4558CC0a27B601aC544E5](https://pacific-explorer.manta.network/address/0x95CeF13441Be50d20cA4558CC0a27B601aC544E5?tab=internal_txns) |
| Name | Manta |
| Symbol | `MANTA` |
| Decimals | 18 |

### `vMANTA` token

| Name | Value |
|---|---|
| Address | [0x7746ef546d562b443AE4B4145541a3b1a3D75717](https://pacific-explorer.manta.network/address/0x7746ef546d562b443AE4B4145541a3b1a3D75717) |
| Name | vManta |
| Symbol | `vManta` |
| Decimals | 18 |
| ABI | [VoucherMantaOFT.json](https://github.com/bifrost-io/slpx-contracts/blob/main/deployments/manta/VoucherMantaOFT.json) |


### `MantaPacificSlpx` contract

| Name | Value |
|---|---|
| Address | [0x95A4D4b345c551A9182289F9dD7A018b7Fd0f940](https://pacific-explorer.manta.network/address/0x95A4D4b345c551A9182289F9dD7A018b7Fd0f940?tab=internal_txns)   |
| Source code | [MantaPacificSlpx.sol](https://github.com/bifrost-io/slpx-contracts/blob/main/contracts/MantaPacificSlpx.sol) |
| ABI | [MantaPacificSlpx.json](https://github.com/bifrost-io/slpx-contracts/blob/main/deployments/manta/MantaPacificSlpx.json) |


## Integration

Manta Pacific's SLPx currently does not support atomic contract calls. That means you can't integrate within your contract logic. The reason are as follows:
- the minting process relies on `msg.sender` to be the receiver so this can have unintended effect on your contract logic
- there is a wait time of about 8 to 10 minutes to receive the `vMANTA` token.

However, you can still interact with the contract directly from the frontend or use another contract but the call is structured at the end of the logic.

There are 3 main functions you use to integrate with `MantaPacificSlpx`:

`minAmount` (derived from the public variable `minAmount`) 
```solidity
function minAmount() returns (uint256)
```

`estimateSendAndCallFee`  
```solidity
function estimateSendAndCallFee(
    address assetAddress,
    uint256 amount,
    uint32 channel_id,
    uint64 dstGasForCall,
    bytes calldata adapterParams
) public view returns (uint256)
```

`create_order`  
```solidity
function create_order(
    address assetAddress,
    uint256 amount,
    uint32 channel_id,
    uint64 dstGasForCall,
    bytes calldata adapterParams
) external payable
```

You want to fetch the `minAmount()` function to check on the minimum amount of `MANTA` that you need to use to mint. Currently this value is `2000000000000000000` equal to `2 MANTA`.

Next, you want to call the function `estimateSendAndCallFee` to get the mint fee in ETH. The input values are defined as follows:

| Variable | Input value | Definition | 
|---|---|---|
| `address assetAddress` | `0x95CeF13441Be50d20cA4558CC0a27B601aC544E5` | Address of `MANTA` token |
| `uint256 amount` | `uint256` | Amount of `MANTA` token be used to mint to `vMANTA` |
| `uint32 channel_id` | `0` | ID of the channel |
| `uint64 dstGasForCall` | `4000000` | Destination gas for call |
| `bytes calldata adapterParams` | `["uint16", "uint256"], [1, 4200000]` | Parameters to be encoded for the Adapter. Refer to the **adapterParams encoding** section below on how to call |

**adapterParams encoding**

With `Ethers.js v5.7`, you can use `solidityPack` function

```ts
ethers.utils.solidityPack(["uint16", "uint256"], [1, 4200000])
```

With `Viem`, you can use `encodePacked` function

```ts
encodePacked(["uint16", "uint256"], [1, 4200000])
```

### Example Viem integration

`minAmount`

```ts
async function getMinAmount() {
  const minAmount = await publicClient.readContract({
    address: "0x95CeF13441Be50d20cA4558CC0a27B601aC544E5",
    abi: mantaSLPxAbi,
    functionName: "minAmount",
  });
  console.log("minAmount", minAmount);

  // Output: minAmount 2000000000000000000n
}
```

`estimateSendAndCallFee`

```ts
const sendAndCallFee = await publicClient.readContract({
  address: "0x95A4D4b345c551A9182289F9dD7A018b7Fd0f940",
  abi: mantaSLPxAbi,
  functionName: "estimateSendAndCallFee",
  args: [
    "0x95CeF13441Be50d20cA4558CC0a27B601aC544E5", // MANTA token
    parseUnits(3, 18), // amount
    0, // channel_id
    4000000, // dstGasForCall
    encodePacked(["uint16", "uint256"], [1, BigInt(4200000)]), // adapterParams
  ],
});
console.log("sendAndCallFee", sendAndCallFee);

// Output: sendAndCallFee 83556372916216n
```
Last call returns a value of `83556372916216` equal to `0.000083556372916216 ETH`.

Then, to mint `vMANTA` with `MANTA`, call `approve` on `MANTA` token contract to the `MantaPacificSlpx` contract, then call the `create_order` function with the same inputs as `estimateSendAndCallFee`.

```ts
const { request: approvalRequest } = await publicClient.simulateContract({
  account: currentAddress, // connected address
  address: "0x95CeF13441Be50d20cA4558CC0a27B601aC544E5",
  abi: erc20Abi,
  functionName: "approve",
  args: [
    "0x95A4D4b345c551A9182289F9dD7A018b7Fd0f940",
    parseUnits(3, 18),
  ],
});
let approvalHash = await walletClient.writeContract(approvalRequest);
console.log("approvalHash", approvalHash);

const { request: mintRequest } = await publicClient.simulateContract({
  account: currentAddress, // connected address
  address: "0x95A4D4b345c551A9182289F9dD7A018b7Fd0f940",
  abi: mantaSLPxAbi,
  functionName: "create_order",
  args: [
    "0x95CeF13441Be50d20cA4558CC0a27B601aC544E5", // MANTA token address
    parseUnits(3, 18), // amount
    0, // channel_id
    4000000, // dstGasForCall
    encodePacked(["uint16", "uint256"], [1, BigInt(4200000)]), // adapterParams
  ],
  value: sendAndCallFee as bigint,
});

hash = await walletClient.writeContract(request);
transaction = await publicClient.waitForTransactionReceipt({
  hash: hash,
});
console.log("hash", hash);
```

Then wait for 8 to 10 minutes to receive the `vMANTA` token in the caller address.