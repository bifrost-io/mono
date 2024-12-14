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
| Address | [0x95A4D4b345c551A9182289F9dD7A018b7Fd0f940](https://pacific-explorer.manta.network/address/0x95A4D4b345c551A9182289F9dD7A018b7Fd0f940?tab=internal_txns) |
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
