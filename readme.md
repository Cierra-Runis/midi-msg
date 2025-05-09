# midi-msg

[![Crates.io](https://img.shields.io/crates/v/midi-msg)](https://crates.io/crates/midi-msg)
[![Docs.rs](https://docs.rs/midi-msg/badge.svg)](https://docs.rs/midi-msg)

midi-msg aims to be a complete representation of the MIDI 1.0 Detailed Specification and its many extensions and addenda, to allow for the serialization and deserialization of MIDI byte streams to and from a typed representation. MIDI 2.0 may be supported at a later date.

midi-msg types follow the taxonomy detailed in the MIDI spec, and have the goal of being entirely safely serializable. That is to say, any `MidiMsg` can be serialized into a valid MIDI byte sequence. Likewise, as it strives for completeness, any valid MIDI byte sequence can be deserialized into a `MidiMsg`. A corollary to this is that "MIDI-like" messages that do not conform to the spec are mostly not representable. Additionally, midi-msg strives to capture the semantic meaning of MIDI with types that are not simply "bags of bytes". Any values that are not numeric atoms are represented with their meaning in mind. Nonetheless, much of what MIDI achieves is predicated on passing around numeric values and as such they are handled according to the following approach.

Since the MIDI spec makes extensive use of non-byte-aligned integers, a Rust representation could either be achieved by introducing "exotic" integer types or by clamping Rust's primitive types to the desired range. For the sake of ergonomics, the latter approach was taken, though this does make this typed representation slightly "lossy". Any overflows between these representations are treated as max values (or min, for negative signed values). Other libraries, which have taken other approaches with respect to these design decisions, can be found below.


## Installing
Add the following line to your Cargo.toml file:

```
midi-msg = "0.8"
```

If you want to use midi-msg in a `no_std` environment, add this line instead:

```
midi-msg = { version = "0.8", default-features = false, features=["sysex"/"file"] }
```

## Disabling system exclusive or MIDI File functionality

The default `sysex` and `file` Cargo features can be disabled to exclude code related to system exclusive or Standard Midi File (SMF) functionality, which can be useful to reduce the binary size in resource constrained environments. If `sysex` is not used and an attempt is made to parse a system exclusive message, an error will be returned.


## To be implemented
- Deserialization of most of `UniversalRealTimeMsg` and `UniversalNonRealTimeMsg`


## Support
MIDI messages described by the following [Midi Manufacturer Association (MMA) documents](https://www.midi.org/specifications/midi1-specifications) are supported (with their corresponding Midi Manufacturer Association [MMA] publication number, Recommended Practice [RP] number, or Changes/Additions [CA] number as noted):

- MIDI 1.0 Detailed Specification 4.2.1 (The base specification. All types should be assumed to be defined by this document unless otherwise specified)
- Standard MIDI Files 1.0 (RP-001)
- MIDI Time Code (MTC) (MMA-001 / RP-004 / RP-008)
- General MIDI System Level 1 (GM1) (MMA-007 / RP-003)
- General MIDI 2 1.2 (GM2) (RP-024/RP-036/RP-037/RP-045)
- File Reference System Exclusive Message (CA-018)
- Sample Dump Size, Rate and Name Extensions (CA-019)
- MIDI Tuning Updated Specification (CA-020/CA-021/RP-020)
- Controller Destination Setting (CA-022)
- Key-Based Instrument Controllers (CA-023)
- Global Parameter Control (CA-024)
- Master Fine/Coarse Tuning (CA-025)
- Modulation Depth Range RPN (CA-026)
- Extension 00-01 to File Reference Sysex Message (CA-028)
- CC #88 High Resolution Velocity Prefix (CA-031)
- Response to Data Inc/Dec Controllers (RP-018)
- Sound Controller Defaults (RP-021)
- Redefinition of RPN 01/02 (RP-022)
- Renaming of CC91 and CC93 (RP-023)
- Three Dimensional Sound Controllers (RP-049)
- MIDI Polyphonic Expression 1.0 (RP-053)


The following addenda are not yet fully supported by midi-msg, though hooks are provided to access these messages:

- MIDI Machine Control 1.0 (MMC) (MMA-016 / RP-013) (partial support)
- MIDI Show Control 1.1.1 (MSC) (RP-002/RP-014)
- SMF Device Name and Program Name Meta Events (RP-019)
- SMF Meta-Event for XMF Patch Type Prefix (RP-032)


The following addenda were consulted but considered not relevant to this library:

- SMF Lyric Events Definition (RP-017)
- Response to Reset All Controllers (RP-018)
- SMF Language and Display Extensions (RP-026)
- Default Pan Formula (RP-036)


## Other Rust MIDI representation libraries
- [apres](https://crates.io/crates/apres)
- [helgoboss-midi](https://crates.io/crates/helgoboss-midi)
- [midi-control](https://crates.io/crates/midi-control)
- [midi-types](https://crates.io/crates/midi-types)
- [midly](https://crates.io/crates/midly)


## Exploring

If you would like to see how midi-msg interprets the results of received
messages, you can check out this repository and run the following to use a
modified version of `midir`'s `test_read_input` example.
```
cargo run --example test_read_input
```

This will produce output like:
```
1816489080: ChannelVoice { channel: Ch1, msg: NoteOn { note: 62, velocity: 59 } }
1816643991: ChannelVoice { channel: Ch1, msg: NoteOff { note: 62, velocity: 53 } }
```

Note that `midir` is installed as a dev-dependency and this does not impose any
requirement on the use of `midir` when specifying this crate as a dependency.


## Contributing
[Pull requests](https://github.com/AlexCharlton/midi-msg/pulls) for the features listed above as not-yet supported, for bug fixes (any omissions from the spec are considered bugs), or for documentation additions are most welcome, as are [bug reports](https://github.com/AlexCharlton/midi-msg/issues).

## Changelog
- **0.8**
  - Added `TryFrom<u8>` impls for `repr(u8)` enums (`Channel`, `GMSoundSet`, `GMPercussionMap`)
  - Added `MidiFile` `remove_track` method
  - `file` feature now works with `no_std`
  - Lots of file parsing bug fixes
- **0.7**
  - Added "simple" CC deserialization and made it the default. See [RecieverContext](https://docs.rs/midi-msg/latest/midi_msg/struct.ReceiverContext.html) and [ControlChange](https://docs.rs/midi-msg/latest/midi_msg/enum.ControlChange.html). This renames `ControlChange::Undefined` and `ControlChange::UndefinedHighRes` to `ControlChange::CC` and `ControlChange::CCHighRes`.
  - Added `is_*` predicates to `MidiMsg` (i.e. `is_note`).
  - Added `Track::len` and `Track::events` methods.
- **0.6**
  - Added MIDI File (SMF) support (feature: `file`)