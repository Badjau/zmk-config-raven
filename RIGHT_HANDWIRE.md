# Right 6x4 ortho handwire

The right handwire uses the original Raven right-side bindings as its base.
The completed left-handwire configuration is unchanged.

Viewed from the keycap side:

```text
Y      U      I      O      P      Backspace
H      J      K      L      ;      '
N      M      ,      .      /      Shift
Del    Nav    Alt    Ctrl   XCut   Enter
```

Columns, left to right: P1.04, P0.09, P0.10, P1.11, P1.13, P1.15.
Rows, top to bottom: P0.24, P1.00, P0.11, P1.06.
The transform maps all 24 switches in row-major physical order.

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
It is configured as a standalone central so its 24-key map can be tested
over USB or Bluetooth. It is not yet a paired split configuration: pairing
with the left requires a shared full-board keymap/transform and restoring
the right peripheral role.

The OLED is disabled, and the build omits `nice_oled`, because its existing
SDA/SCL pins P1.00/P0.11 are now matrix rows. An OLED requires separate,
confirmed free pins before it can be enabled again.

Check all 24 keys individually, both held layers, modifiers, and J wake.
Firmware compilation and physical switch validation require the ZMK build
and hardware; structural checks alone do not establish hardware operation.
