# Golden tests — locking the contracts during the refactor

The "golden master" strategy is the most powerful safety net for a refactor: it guarantees that the observable behavior of the public interfaces does not change, by comparing the new code's output, byte for byte, against the captured output of the reference code.

## How to set them up

1. Identify the contracts to lock from the Phase 2 inventory: endpoints/API, response formats, hooks with payloads, serialized outputs.
2. Capture the reference version's responses (the one currently in production) for a representative set of inputs, including error cases. Store them as versioned fixtures in the repo, recording their provenance (which reference version, when).
3. Write tests that run the new code with the same inputs and compare the output byte for byte against the fixtures.
4. Put these tests in CI as blocking: if one fails, it does not merge.

## Sequencing — when the capture happens decides whether it protects anything

The capture is the development AI's **sprint 0**: the old code runs in the real test environment and the fixtures are taken from it BEFORE a single line of new code exists. The same sprint captures the performance baseline defined in `TESTING.md`. Fixtures are NEVER generated — or regenerated — from the new code: a golden test whose fixture was captured after the rewrite compares the rewrite against itself and protects nothing. If a fixture ever needs to change, that is a contract change, not test maintenance: it requires the user's explicit approval and the corresponding update to `DATA-MODEL.md`/`API.md`, never a silent re-capture.

## Common traps that break contracts silently

- **Serialization format**: if the old version returns errors as language-serialized data (not JSON) and the client deserializes them, "modernizing" it to JSON breaks every client. Replicate the exact format, even if it is ugly.
- **Salts and hash/signature formulas**: if the client stores and resends a value derived from a specific salt, changing the formula invalidates everything already issued. It is untouchable.
- **Order and field names** in responses that clients parse positionally or by key.
- **Error codes and texts** the client compares literally.

## When it does NOT apply

Golden master locks an existing contract, so it only applies in compatibility modes A and B. In mode C (no backward compatibility) there is no old contract to preserve — it is being dropped on purpose — so skip the golden master of the old responses and test the new behavior directly.

## Operating rule

Public contracts come before code elegance. If modernizing something risks the contract, keep the behavior even if the code is less clean. Cleanliness can wait; a broken client in production cannot.
