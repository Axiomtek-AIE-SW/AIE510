# PoE Control Tool

`poe_ctl.sh` reads PD69104B port status, measures voltage and current, reports fault events, and controls individual PoE ports on supported Jetson products.

## Commands

| Command | Purpose |
|---|---|
| `info` | Show model, profile, configured port count, and physical port mapping without accessing I2C |
| `status [port]` | Show enabled state, Power Good, PD detection, classification, mode, and per-chip supply events |
| `measure [port]` | Show voltage in V, current in mA, and calculated power in W |
| `on port` | Enable detection/classification and request power for one port |
| `off port` | Request power-off and read back the enabled state |
| `restart port` | Turn off, wait three additional seconds, then turn on |

Omit the port only for status, measure, and faults to inspect every configured port. Port arguments are physical, one-based numbers. A selected port still causes status/faults to display its controller's shared supply events.

```sh
poe_ctl.sh status
poe_ctl.sh status 1
poe_ctl.sh measure 1
sudo poe_ctl.sh off 1
sudo poe_ctl.sh on 1
sudo poe_ctl.sh restart 1
```

Power commands immediately affect the connected device. The script never automatically restores the previous state.

## Reading status

```text
nvidia@nvidia-desktop:~$ poe_ctl.sh status
Port 1 [bus7/0x28 P1]: Power=0  PowerGood=0  Detection=Open circuit (no PD)  Class=off  Mode=Auto
Port 2 [bus7/0x28 P2]: Power=1  PowerGood=1  Detection=Good (PD detected)  Class=Class 2  Mode=Auto
System event register (0x0A) [bus7/0x28] = 0x32
```

- Power is the controller's power-enabled bit.
- PowerGood indicates successful startup.
- Detection is the most recent PD detection result.
- Class displays off when PowerGood is zero, matching the ARM EAPI demo. Powered ports display Class 0-4 or error.
- Mode is Shutdown, Manual, Semi-auto, or Auto.

The classification register can retain an earlier result after disconnection. The off display prevents stale classifications from appearing as current powered-device classifications.

## Power control behavior

The script verifies the controller ID and accepts control only in Auto or Semi-auto mode. It writes only the per-port PWRPB (0x19) and DETPB (0x18) command bits. It leaves operating mode, budgets, and current thresholds to the existing system configuration.

On waits up to eight polling intervals for the enabled bit. A valid PD signature triggers one explicit power-on request. Off waits one second before readback. Restart adds a three-second cooldown after off.

A timeout can leave detection enabled. Auto mode may power a valid PD later. Success confirms the enabled bit; inspect PowerGood and the connected device to confirm successful operation.

## Measurements and events

Measurements read current LSB/MSB followed by voltage LSB/MSB in one four-byte transaction. Scaling uses 122.07 uA per current count and 5.835 mV per voltage count. Power is calculated from measured voltage and current.

The measure command reads raw measurement registers even when a port is off.

Faults reads 0x06, 0x08, and 0x0A, avoiding their clear-on-read counterparts. Events may describe earlier conditions. Successful write commands and errors are sent to the system logger when available:

```text
nvidia@nvidia-desktop:~$ poe_ctl.sh measure
Port 1: Voltage=N/A  Current=N/A  Power=0.0 W (PowerGood=0)
Port 2: Voltage=53.81 V  Current=67.5 mA  Power=3.6 W
nvidia@nvidia-desktop:~$
```

```text
nvidia@nvidia-desktop:~$ journalctl -t poe_ctl
Nov 20 11:27:52 nvidia-desktop poe_ctl[24535]: OFF port 2 (bus7/0x28 P2) OK
Nov 20 11:28:13 nvidia-desktop poe_ctl[24924]: ON port 2 (bus7/0x28 P2) OK (1s)
Nov 20 11:28:52 nvidia-desktop poe_ctl[25615]: RESTART port 2
Nov 20 11:28:53 nvidia-desktop poe_ctl[25655]: OFF port 2 (bus7/0x28 P2) OK
Nov 20 11:28:57 nvidia-desktop poe_ctl[25736]: ON port 2 (bus7/0x28 P2) OK (1s)
```
