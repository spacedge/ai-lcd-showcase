# Attribution and licenses

This public showcase is documentation and a reference adapter contract. It is
not a redistribution of the private runtime or of any third-party account
manager. The reference adaptation depends on the following independently
maintained projects:

- https://github.com/MysticRichard/magpie-lcd-bridge — LCD image protocol and
  BLE reference; MIT license. The original controller/firmware attribution to
  tsl0922 is retained in the runtime project's notice.
- https://github.com/Wei-Shaw/sub2api — independent account/usage manager;
  LGPL-3.0 upstream. No upstream code is copied into this showcase.
- https://github.com/TakWolf/fusion-pixel-font — Fusion Pixel Font, SIL Open
  Font License 1.1. The runtime project carries the complete font license.
- https://github.com/openai/codex — local coding-client attribution reference.

The photographs and rendered state frames in `docs/images/` are supplied or
generated for this project's display use. They remain subject to the owner's
authorization for third-party reuse. The character artwork is not offered as a
general-purpose open asset.

The source project uses a custom per-model color quantization path for the
character sprites. The visual state pages were checked with Pillow; successful
image transfer to one device does not establish compatibility with another
LCD model or firmware.
