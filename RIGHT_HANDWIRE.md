# Right 6x4 ortho handwire

The right handwire uses the original Raven right-side bindings as its base.
The left remains central and the right is peripheral. Both use a shared
48-key transform/keymap. Left wiring and existing key bindings are preserved.

Viewed from the keycap side:

```text
Y      U      I      O      P      Backspace
H      J      K      L      ;      '
N      M      ,      .      /      Shift
Del    Nav    Alt    Ctrl   XCut   Enter
```

Columns, left to right: P1.04, P0.09, P0.10, P1.11, P1.13, P1.15.
Rows, top to bottom: P0.24, P1.00, P0.11, P1.06.
The shared transform interleaves six left keys and six right keys per row.
The right scanner uses a column offset of 6; the left retains reversed
electrical column order. Layers: Base=0, Num=1, FN=2, Nav=3, XCut=4.

Diode polarity is assumed to match the completed left handwire: columns
are active-high outputs, rows are inputs with pull-downs, and diode stripes
face the switches/rows (`col2row`). Confirm this assumption on the hardware.
Soft-off wake uses J (column P0.09, row P1.00).

Hold Nav for the original right navigation layout: I=Up, J=Left,
K=Down, L=Right, P=Home, Backspace=End, semicolon=Page Up,
apostrophe=Page Down. Release Nav to return to Base.

Hold XCut for the original right shortcuts: Y=soft off, H=bootloader,
I=Insert, O=Print Screen, P=Mute, Backspace=Volume Up,
J=Previous Track, K=Play/Pause, L=Next Track, apostrophe=Volume Down,
and Del=clear the current Bluetooth profile. Release XCut to return.
Shift, Alt, and Ctrl hold as modifiers and tap as quick sticky modifiers.

## Build and test

`build.yaml` adds the `raven-right-handwire` artifact for `nice_nano_v2`
with shield `raven_right`. Flash this artifact to the right controller.
Build and flash the updated left artifact as well: the central needs the
shared map to interpret right-side positions. The left connects to the host
via USB/Bluetooth; the right sends positions to the left over split BLE.
The right is explicitly configured with `CONFIG_ZMK_SPLIT_ROLE_CENTRAL=n`.

The OLED is disabled, and the build omits `nice_oled`, because its existing
SDA/SCL pins P1.00/P0.11 are now matrix rows. An OLED requires separate,
confirmed free pins before it can be enabled again.

Check all 24 keys individually, both held layers, modifiers, and J wake.
Firmware compilation and physical switch validation require the ZMK build
and hardware; structural checks alone do not establish hardware operation.
