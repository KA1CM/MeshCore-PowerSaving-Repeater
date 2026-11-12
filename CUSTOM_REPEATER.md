# Custom PowerSaving 18 repeater

Base: https://github.com/IoTThinks/MeshCore/tree/PowerSaving-v18

Starting commit: `2e328f891d055a231cf049afd742435b5a339f57`.

This is a separate source checkout from the Fairfield bot in `vendor/MeshCore`.

## One-byte path-hash forwarding

Admin CLI (USB serial or authenticated remote repeater CLI):

```text
get repeat.1byte
set repeat.1byte off
set repeat.1byte on
```

The setting takes effect immediately and is stored in `prefs.json` as
`repeat.disable_1byte`. Missing settings default to forwarding disabled, including
when migrating legacy preferences. Invalid arguments return usage without saving.

`off` blocks relaying packets with one-byte path hashes, for flood and direct
routing. This includes one-byte floods whose path is still empty. TRACE uses the
route hash width in its payload flags, because its path contains SNR samples.
Two- and three-byte traffic retains upstream forwarding rules. `set repeat off`
still disables all forwarding, regardless of this setting.

This is a forwarding filter: it does not discard locally addressed traffic or
change the hash width of packets originated by this repeater. Configure the
existing radio hash mode separately if desired. Remote management through an
intermediate repeater with this filter disabled for one-byte forwarding requires
a supported wider-hash route.

## Board targets

- Heltec V4.3 OLED: `heltec_v4_repeater`
- Heltec T114 with display: `Heltec_t114_repeater`
- Heltec T114 without display: `Heltec_t114_without_display_repeater`
- RAK4631: `RAK_4631_repeater`

All use the same `examples/simple_repeater` forwarding and command implementation.
No board-specific power or radio settings were changed.

## Validation

On 2026-11-12, all four board targets above compiled and linked successfully with
PlatformIO. Source inspection and whitespace checks also passed. No device testing
or flashing was performed. Collected firmware is in `out/custom-repeater-2026-11-12/`.
Manual validation should check the default state, both settings, invalid arguments,
restart persistence, one-/two-/three-byte flood and direct forwarding, TRACE,
local admin access, and interaction with the global repeat switch.
