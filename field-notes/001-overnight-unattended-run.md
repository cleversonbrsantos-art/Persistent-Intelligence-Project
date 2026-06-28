# Field Note 001 — Overnight Unattended Run: Background Activity Scaling and Fallback Resilience

## Context

The server was left running unattended for ~7.5 hours (02:24–09:52), with zero user interaction, as an incidental real-world test rather than a planned experiment.

Logs from this session were compared against two same-week sessions with active user interaction (1h52m and 44min respectively) to check two open questions about system behavior under sustained, unattended uptime.

---

# 1. Background cognitive activity: proportional or runaway?

Metrics normalized per hour across the three sessions:

| Metric (per hour)     | Session A (1h52m) | Session B (44min) | Overnight (7h28m) |
| --------------------- | ----------------: | ----------------: | ----------------: |
| Hypotheses generated  |              3.20 |              2.72 |              3.21 |
| Validator verdicts    |              9.59 |              4.08 |              7.23 |
| Quarantine promotions |              5.33 |              6.80 |              4.82 |
| Belief updates        |             15.46 |             21.77 |             16.06 |
| Expiry checks         |              5.33 |              4.08 |              8.30 |

### Conclusion

Absolute counts during the overnight session were much higher (e.g. 120 belief updates vs. 16–29), but that's fully explained by the session being 4–10× longer than the others.

Per-hour rates are consistent across all three.

No runaway behavior.

No unexpected acceleration tied to lack of user interaction.

The background reflection loop behaves the same whether or not a user is actively present.

---

# 2. Model fallback chain: validated under real, sustained failure

The architecture includes an automatic fallback: if a model call returns a provider error (rate limit, model unavailability), the system retries with the next model in a prioritized list rather than failing the whole operation.

During the overnight session, the background hypothesis-generation subsystem hit a free-tier model repeatedly over several hours, producing:

* 15 provider-error events (12× HTTP 429 rate-limit, 3× HTTP 404 model unavailable)
* 8 confirmed successful fallbacks — a different model picked up the request within seconds to a couple of minutes, and the cycle completed normally afterward
* 4 cycles where fallback occurred but the cycle still failed — not due to exhausting available models, but due to a downstream JSON-parsing error on the fallback model's response (malformed/truncated output)
* 2 cycles with zero output and no associated error — the model responded normally but simply found nothing worth generating that cycle; unrelated to fallback behavior

### Conclusion

The fallback mechanism is functioning as designed and has now been validated against real, sustained provider failures rather than synthetic test conditions.

The residual issue — parser fragility against malformed fallback-model output — is a separate, now-scoped problem for a future fix, distinct from the fallback logic itself.

---

# Why this is being documented

This wasn't a planned chaos test.

It was an unattended overnight run, analyzed after the fact out of curiosity about activity and cost.

Treating incidental long-uptime logs as a resilience signal — rather than only trusting unit tests with mocked failures — surfaced a real, narrowly scoped bug (parser fragility) that synthetic testing had not caught, while also confirming the core fallback design works under the exact conditions it was built for.
