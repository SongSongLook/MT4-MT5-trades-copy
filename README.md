# MT4/MT5 Signal Copier with TCP Support (Issued Fixing)

This repository contains Expert Advisors (EAs) for MetaTrader 4 (MT4) and MetaTrader 5 (MT5) that allow copying trading signals between platforms using TCP. The EA supports both sender and receiver modes, with a `Multiplier` parameter to adjust the copied lot sizes.

---

## Features
- **TCP Communication**: Real-time signal transfer between MT4 and MT5.
- **Dual Modes**: Each EA can function as a sender or receiver.
- **Lot Multiplier**: Adjust the copied lot sizes on the receiver side.
- **Cross-Platform**: Supports copying signals between MT4 to MT5 or vice versa.

---

## Setup Instructions

###  Requirements
- An active trading account on both platforms.
- Local network connectivity or proper IP/port configuration for cross-machine communication.

---

###  Parameters
| Parameter      | Description                                                                 |
|----------------|-----------------------------------------------------------------------------|
| `IsSender`     | Set to `true` to enable sender mode.                                        |
| `IsReceiver`   | Set to `true` to enable receiver mode.                                      |
| `Multiplier`   | Adjusts the copied lot size (e.g., `2.0` means double the original lot size).|
| `IPAddress`    | The IP address of the receiver (use `127.0.0.1` for local communication).   |
| `Port`         | The TCP port for communication (default: `8888`).                          |

---

###  Installation
#### For MT4
1. Open MetaTrader 4.
2. Navigate to `File > Open Data Folder`.
3. Copy the MT4 EA (`MT4_TCP_SignalCopier.mq4`) to `MQL4/Experts`.
4. Restart MetaTrader 4.
5. Attach the EA to any chart and configure the parameters.

#### For MT5
1. Open MetaTrader 5.
2. Navigate to `File > Open Data Folder`.
3. Copy the MT5 EA (`MT5_TCP_SignalCopier.mq5`) to `MQL5/Experts`.
4. Restart MetaTrader 5.
5. Attach the EA to any chart and configure the parameters.

---

###  Usage
#### Sender Mode
1. Set `IsSender = true` in the EA parameters.
2. Specify the receiver's IP address (`IPAddress`) and port (`Port`).
3. Run the EA on the platform where trades originate.

#### Receiver Mode
1. Set `IsReceiver = true` in the EA parameters.
2. Ensure the IP address and port match the sender's configuration.
3. Adjust `Multiplier` to modify the lot size of copied trades.
4. Run the EA on the platform where trades will be copied.

---

###  Example Scenarios
#### Copy Trades from MT4 to MT5
- Install the EA on both platforms.
- Set `IsSender = true` on MT4 and `IsReceiver = true` on MT5.
- Use `127.0.0.1` as the `IPAddress` if both platforms are on the same machine.

#### Copy Trades with Lot Multiplier
- Configure the receiver EA with `Multiplier = 2.0` to double the copied lot size.
- For smaller trades, set `Multiplier = 0.5`.

---

###  Troubleshooting
- **No Connection**: Ensure the IP address and port match on both sender and receiver.
- **Firewall Issues**: Allow the specified port (e.g., `8888`) in your system's firewall.
- **Error Messages**: Check the EA logs in the `Experts` and `Journal` tabs for detailed error descriptions.

---

###  Limitations
- Only supports basic buy orders in the current version.
- Trades are copied in real-time but may experience slight delays due to network latency.
- Both platforms must remain online for successful signal copying.

