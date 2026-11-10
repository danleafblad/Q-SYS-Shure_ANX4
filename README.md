# Shure ANX4 – Q-SYS Plugin

This plugin controls and monitors a Shure ANX4 scalable wireless receiver using Shure's command strings (TCP port 2202).

## Install
1. Copy `ShureANX4.qplug` to `Documents\QSC\Q-Sys Designer\Plugins\`, then restart Designer. It appears under **Plugins → Shure → ANX4 Scalable Wireless Receiver**.
2. On the ANX4, turn on **Device Configuration → Network Services → Command Strings**.
3. Set the plugin properties, then enter the receiver's IP on the **Device** page (or wire it to the `IP Address` pin).

## Properties
| Property | Values | Notes |
|---|---|---|
| Transmission Mode | Axient Digital / ULX-D | Sets which controls are created. AD adds channel quality, Quadversity (RSSI C/D), FD-C (Frequency 2, F2 RSSI, Interference 2), Unregistered TX and ADX transmitter detail. ULX-D adds the ULXD6/8 mute-button status. |
| Licensed Channels (Axient Digital) | 0, 4, 8, 12, 16 | Shown only in Axient Digital mode. Licences come in banks of 4. 0 = device control only. |
| Licensed Channels (ULX-D) | 0, 4, 8, 12, 16, 20, 24 | Shown only in ULX-D mode. |
| Meter Rate (ms) | 100 – 5000 | Sent to each channel as `METER_RATE`. The default is 200 ms. |
| Debug Print | None / Tx / Rx / Tx/Rx / All | Prints traffic to the debug window. |

At runtime the plugin checks the receiver's reported `TRANSMISSION_MODE` and `NUMBER_CHANNELS_LICENSED`. If either doesn't match the properties, it lights **Mode Mismatch** and sets Status to *Compromised* with an explanation. The **Available** LED on each channel follows `AVAILABLE_CHANNELS`, so a channel shows as unavailable when FD-C or Quadversity uses it up.

## Pages
- **Overview** – one strip per channel: name, mute, RF level, audio peak, battery bars and runtime, and TX present / alarm / OL LEDs. There is also a device status and a Mute All button.
- **Device** – IP, status, model, device ID, firmware, mode, licence, Dante name, network settings, identify and Mute All.
- **Channel N** – full detail in five sections: Audio, RF, Meters, Transmitter and Battery.

## Controls (per channel; all have optional pins)
- **Settable:** Mute, Gain (-18…+42 dB), Channel Name (the device takes max 8 characters), Frequency (type MHz `602.125` or kHz `602125`), Group,Channel (`6,100`), Identify.
- **RF:** RF Level (the strongest active antenna), RSSI A–D with antenna LEDs (blue = active, red = inactive), Quality (AD), Interference, Encryption mode and error, Unregistered TX (AD).
- **Audio:** Peak and RMS (dBFS), OL LED.
- **Battery:** Bars, Charge %, Runtime (h:mm and minutes), Type, Health, Cycle count, Temperature, Battery Low (≤ 1 bar, or a battery comm warning).
- **Transmitter:** Present, Model, Device ID, RF power, power source, TX mute mode. AD only: firmware, lock, offset, input pad, polarity, phantom, HPF, talk switch.
- **Alarm:** any of interference, encryption error, unregistered TX or battery low.

## Behaviour
- When it connects, the plugin polls the device properties and `GET x ALL` for each channel, then enables metering. After that it relies on the device's unsolicited REPs, so it doesn't poll continuously.
- It sends a heartbeat every 15 s. If nothing is received for 30 s, it reconnects. The socket also reconnects automatically every 5 s.
- Meters drop to minimum when the connection is lost.

## Not yet implemented
- Transmitter slot commands (`SLOT_*`, ADX remote control of RF mute / power / offset).

## Testing status
The parser and design functions were tested offline in Lua 5.3, using the message examples from Shure's ANX4 command-string document, in both modes and at 1, 8 and 24 channels. They have not yet been tested against real hardware.
