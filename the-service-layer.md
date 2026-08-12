---
type: Boundary
belongs_to: "[[the-capability-grid]]"
related_to:
  - "[[the-network]]"
  - "[[disturb]]"
---
# The service layer

**Software talking to software by name rather than by wire.**

As embedded systems adopt service-oriented architectures, controllers stop broadcasting fixed periodic frames and start behaving like networked software.
They offer *services*, other components *discover* and *call* them, and things occur on demand.

This is a newer boundary than the rest, and it behaves much more like distributed-systems testing than like traditional bench work.

## Interaction patterns

| Shape | Pattern | Testing it means |
| --- | --- | --- |
| **Methods** | Request/response. Someone calls, someone answers, possibly with an error. | Calling the DUT's methods, and standing in as a service the DUT calls. |
| **Events** | Publish/subscribe. The DUT emits a notification. Subscribers receive it. | Subscribing and checking what arrives, when, and in what order. |
| **Fields** | Shared state with getters, setters, and change notifications. | Reading, writing, and confirming the notification actually fires. |

## Capabilities

| Capability | What it means here |
| --- | --- |
| [[observe]] | A field, or the last event received |
| [[stimulate]] | Call methods, publish events, or write fields |
| [[capture]] | The message trace (usually the primary evidence for these tests) |
| [[replay]] | Awkward here, because blind playback desynchronizes a conversational protocol |
| [[disturb]] | Ways with no analog elsewhere. Return errors, time out, respond out of order, vanish mid-session, fail discovery |

You cannot [[interrogate]] a service.
The device's self-report has its own boundary, [[the-diagnostic-channel]].

## Constraints

### Dynamic rather than periodic

Nothing occurs on a fixed cycle.
A service can be absent until discovered, and discovery has its own timing and failure modes.

### Stateful

Sessions, subscriptions, and connections all have lifecycles.
Test order starts to matter in ways it does not at other boundaries.

### Asynchronous

Responses and events arrive on their own schedule.
Your test needs to *wait for* things rather than *poll for* them, and needs sane timeouts on each wait.

### Both roles

For most boundaries the bench is a stand-in for the physical world.
Here it stands in as a peer, sometimes the client, sometimes the server, frequently both in the same test.

## Common mistakes

The most common mistake is to test only the happy path.
In service-oriented systems, the interesting behavior is almost entirely in the failure modes.
What does the DUT do when a service it depends on never appears, answers slowly, answers incorrectly, or disappears halfway through?
Those are cheap to inject here and expensive to discover in the field.

The second most common is to assume ordering.
Two events published in order do not necessarily *arrive* in order across different services.
If your test depends on ordering, verify the system actually guarantees it.
