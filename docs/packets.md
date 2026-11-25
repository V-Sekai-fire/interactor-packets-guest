# The Packet codec and the order of a body

`packets.sgd` writes and reads the 100-byte entity Packet of `contract-entity-packet`, and
`data/parts.yamlld` gives the 21 bodies of a person their order on the wire; it is the wire order, and no other table is.

## The part table

`data/parts.yamlld` is a canonical YAML-LD document (one line of RFC 8785 JSON, a `@list` of `{body, frame, part}`). The first frame is a whole coarse
body: head, both hands, pelvis, chest, the base of the neck, both thighs, both shins and both
heels. The second frame is the refinement: the low back, both collar bones, both upper arms, both
forearms and both sets of toes, and then the three dummy Packets. The order is chosen so that a
viewer who is handed the first frame alone still has a body to draw, which a later rung tests.
The gate refuses a table that is not exactly the bodies the drive table of the muscle course
names.

## The codec

`packets.sgd` writes and reads the 100-byte Packet of `contract-entity-packet` and nothing else.

| Call | What it does |
| --- | --- |
| `raw(ints, payload)` | the 16 integer fields and up to 40 payload bytes, as 100 bytes |
| `fields(bytes)` | the 16 integer fields of a Packet |
| `rot_codes(x, y, z, w)` | a rotation in the wire's frame as the contract's three codes: swing X, swing Z, twist about Y |
| `rot_quat(cx, cz, ct)` | the rotation of three codes |
| `body(person, part, tick, origin, place, motion)` | one body of the muscle skeleton, in MuJoCo's frame and in metres from an origin, as a Packet |
| `placed(bytes, origin)` | the same back: id, tick, place and motion in MuJoCo's frame, in metres from the origin |

The contract's frame for MuJoCo is used as it is written there: a vector (x, y, z) of the engine
is (-y, z, -x) on the wire. Velocity is rounded to the nearest code. Acceleration is rounded up
in size, because the contract uses it as a ceiling.

## A hair, in a solar system

"Please keep error under a hair for a solar system" (operator, 2026-11-13). The Packet can do
this and a careless codec cannot. A position on the wire is a whole number of micrometres in 64
bits, which reaches 9.2 billion kilometres either way, past the orbit of Neptune, in steps of one
micrometre. A 64-bit float has 53 bits of precision, so at 4 billion kilometres its steps are
half a millimetre, and a position that passes through one as metres from the sun is wrong by more
than a hair before anything is drawn.

So no absolute position is ever a float. `body` and `placed` take an origin, three whole
micrometres on the wire's axes. A body is simulated and drawn in metres from the origin, which
are small numbers, and the origin is added and taken away in whole numbers. Sender and viewer may
each choose their own.

The gate takes a hair as 17 micrometres, the thinnest a human hair is. With an origin 4, 8 and
6.5 billion kilometres out on the three axes, 2,000 random bodies come back within 0.82
micrometres, and each wire position is the origin plus the rounded offset exactly. The `float`
control adds the origin as a float and is off by 1,230 micrometres. The body gate runs at that
same origin, and its line is the same to every printed digit as at an origin of zero.

This is a statement about arithmetic. What a viewer draws between two sends is a curve through
them, and its error is a different thing and far larger; `docs/body.md` of
`interactor-presence-gates` has it.

## The gates

    godot --headless --path . -s tests/packets_gate.gd -- <packet_golden_full.csv> [--control=flip|axes|twist|float]
    godot --headless --path . -s tests/proof_gate.gd -- <packet_golden.csv> [--control=flip]

`packets_gate.gd` writes and reads all 64 golden rows of the contract, checks the contract's four
rotation examples within one code, round-trips 2,000 random bodies, and runs the hair test at a
far origin. `proof_gate.gd` is the first proof that a SafeGDScript guest reads the id of every
golden Packet, with `proof/gid.sgd`.
