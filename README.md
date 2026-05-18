# myOpenLog_4.3

This is a partial fork of https://github.com/sparkfun/OpenLog.

Base firmware: OpenLog version 4.3 (full OpenLog.ino source from SparkFun).

In this project, OpenLog.ino was converted to OpenLog.cpp so it can be built and maintained in VS Code with PlatformIO.

## Project Goal

The objective of this fork is to insert time stamps into received NMEA streams.

For timestamp-enabled operation:

- Any received message beginning with `$` is prefixed with `NNNN,`.
- The resulting on-log prefix format is `NNNN,$`.
- `NNNN` is a four-digit decimal millisecond value.

Example:

- Input: `$GPGGA,...`
- Logged: `0427,$GPGGA,...`

## Timestamp Behavior Details

- Timestamp insertion is configurable through `config.txt`.
- The config field name is `timestampBeforeDollar`.
- Values:
  - `0` = disabled
  - `1` = enabled
- The feature uses a four-digit millisecond window (0000 to 9999), then rolls over.
- Time is estimated close to UART receive time (with small jitter expected on embedded systems).

## Power Behavior When Timestamping

When timestamp insertion is enabled, timing accuracy is prioritized over low-power idle behavior.

- The firmware avoids entering the deeper idle/sleep timing path used by stock logging mode.
- This keeps timekeeping aligned with real data arrival.

## Config Format

The first line of `config.txt` contains numeric settings in this order:

`baud,escape,esc#,mode,verb,echo,ignoreRX,maxFilesize,maxFilenum,timestampBeforeDollar`

Example:

`9600,26,3,0,1,1,0,100,60,1`

## Build And Upload (PlatformIO)

From the project root:

- Build: `platformio run`
- Upload: `platformio run --target upload`

This project uses PlatformIO environment `openlog` configured in `platformio.ini`.

## Notes

- This fork focuses on practical NMEA timestamp insertion while preserving original OpenLog behavior as much as possible.
- Core OpenLog architecture, command shell behavior, and SD logging flow remain based on SparkFun OpenLog 4.3.
- The libraries from OpenLog are untouched.
- The base is https://github.com/sparkfun/OpenLog commit (140fe56) as of Oct 14, 2021

## Fork Change Log

### 2026-05

- Migrated project build from Arduino IDE workflow to PlatformIO + VS Code.
- Converted `OpenLog.ino` to `src/OpenLog.cpp` for PlatformIO compatibility.
- Added configurable timestamp insertion before NMEA-style `$` messages.
- Added new config field `timestampBeforeDollar` in `config.txt` parsing and recording.
- Added EEPROM setting storage for timestamp feature enable/disable state.
- Implemented timestamp output format as `NNNN,$` where `NNNN` is a four-digit millisecond value.
- Updated timestamp timing logic to estimate event time close to UART arrival using buffered read timing.
- Prioritized timing accuracy over low-power idle behavior when timestamping is enabled.
