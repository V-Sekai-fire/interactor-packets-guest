# interactor-packets-guest

The entity Packet codec as a SafeGDScript sandbox guest, and the order of a body's 21 parts on the wire.

## What is here

- `packets.sgd`: the 16 integer fields of a Packet, the swing-twist rotation code, and a body of the muscle skeleton to and from a Packet in the contract's frame for MuJoCo.
- `data/parts.yamlld`: the 21 bodies in wire order, canonical YAML-LD (RFC 8785 JSON, as the workspace writes it). The first frame is a whole coarse body and the second its refinement.
- `tests/packets_gate.gd` and `tests/proof_gate.gd`: the gates, against the golden rows of `contract-entity-packet`.
- `docs/packets.md`: the calls, the part table, and how a position stays right to under a hair at solar-system distances.

A position is whole micrometres on the wire. The codec takes a whole-number origin and no absolute position ever passes through a float.

## The other parts

Each part of the presence slice is a repository of its own in `V-Sekai-fire`, and they are checked
out side by side.

| Repository | What it holds |
| --- | --- |
| `interactor-packets-guest` | the Packet codec and the order of a body's 21 parts |
| `interactor-zone-router-guest` | the model of a zone that only routes, and the trace a relay replays |
| `interactor-observer-guest` | one client's check of what the zone relays: hashes, tags and verdicts |
| `interactor-curve-guest` | curve playback between sends |
| `interactor-replica-guest` | a shared object as four replicas |
| `interactor-anny-follow-guest` | the avatar posed from the muscle skeleton |
| `transport-zone-relay` | the relay in C, on sockets, with its load generator |
| `interactor-presence-gates` | the gates that join the parts on a real body, the videos and the ledger |

## Rules of the source

Guests are SafeGDScript and gates are GDScript run on a double-precision editor with the sandbox
built in. A gate prints a `RESULT:` line and has controls that must fail. Comments are at most 3%
of a file's lines, which `tools/comment_density.exs` of `interactor-presence-gates` checks, and
the explanation is in the documentation.

## Licence

MIT; see `LICENSE`.
