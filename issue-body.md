**Camera:** DJI Osmo Action 4 (firmware: see the attached camera report)
**osmo-client:** 1.6.2 (release tarball), run with `--no-browser --port 8091`, and `--report`
**Computer:** Raspberry Pi 4 Model B (2 GB), Raspberry Pi OS (Debian trixie, aarch64), Python 3.13, built-in Wi-Fi and Bluetooth (BlueZ 5.82, NetworkManager). Internet over Ethernet while the Wi-Fi is on the camera's network.
**Phone app:** DJI Mimo closed; the camera was not connected to anything else.

## What works
- The camera is found over Bluetooth ("Osmo Action 4 · good signal"), linking works with no prompt on the camera (it had been paired to this computer before).
- The computer joins the camera's own Wi-Fi network (5 GHz, strong signal around -51 to -62 dBm, 192.168.2.130).
- The page shows the camera as connected with live status: battery, mode (Video), quality "4K 16:9 estimated", bitrate, ISO, shutter, card time left ("1 h 29 min of video left").
- The datalink handshake and registration succeed every time.

## What doesn't
- The picture never appears. Every session logs, within a second of "Registered and live view requested (Action series)":
  - `Live view: a picture arrived incomplete (lost packets)`
  - `Live view: no picture or a broken one, asked the camera for a fresh keyframe`
  - then after 6 s: `No video, restarting the session (camera packets: link 607, video 1700–1900, link_data 77; replies: 00/99 from 28: 01020211, 00/81 from 01: e0, 09/a8 from 41: 00, 00/88 from 28: 18000000, 02/09 from 01: d9, 00/4f from 48: 00)`
  - and the loop repeats indefinitely (dozens of sessions over several minutes).
- So the camera **is** sending video packets (about 300 per second, roughly 2 Mbit/s; the 10-second session dumps are 2.4–3.0 MB each), but no complete picture is assembled.
- A capture-mode change from the page (Video -> Photo) did not take effect while this loop was running (the Mode field stayed "Video"); I did not test recording or photos.

## Ruled out on the computer side
- No UDP receive errors at all during the sessions (`nstat UdpInErrors` / `UdpRcvbufErrors` stayed 0; `net.core.rmem_max` 4 MB).
- Wi-Fi link to the camera: -51 to -62 dBm, 230–390 Mbit/s rates, 2 tx failures over the whole session.
- Run as root (as a normal user, saving the camera's Wi-Fi profile failed with "Insufficient privileges" from NetworkManager, which the README mentions).

## Attached (in https://github.com/stvdrag225/osmo-action4-report)
- [`action4-live-session.osmodump`](https://github.com/stvdrag225/osmo-action4-report/blob/main/action4-live-session.osmodump) — one 10-second live session recorded with `OSMO_VIDEO_DUMP` (3.0 MB)
- [`osmo-client-report-osmo-action-4-20261021-1821.json`](https://github.com/stvdrag225/osmo-action4-report/blob/main/osmo-client-report-osmo-action-4-20261021-1821.json) — the `--report` camera report (10 minutes; "no live view within 10 minutes (stage: on, no error)")
- [`action4-console.log`](https://github.com/stvdrag225/osmo-action4-report/blob/main/action4-console.log) — console output of the dump run
- [`action4-console-first-link.log`](https://github.com/stvdrag225/osmo-action4-report/blob/main/action4-console-first-link.log) — console output of the first link
- (Bluetooth addresses in the logs are replaced with XX:XX:XX:XX:XX:XX)

## One small Linux note
After the client process is killed (not stopped cleanly), BlueZ keeps the Bluetooth link to the camera open, and the camera then stops advertising, so the next start logs "Camera ... not found over Bluetooth" until `bluetoothctl disconnect <camera>` is run.

Happy to run more tests on this camera.
