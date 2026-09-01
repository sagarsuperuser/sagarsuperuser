**[Velox](https://github.com/getvelox/velox)** — open-source usage-based billing engine (Go, PostgreSQL, React).  
**[notif-service](https://github.com/sagarsuperuser/notif-service)** — distributed SMS platform on AWS (Go, SQS, Kubernetes).  
**[user-service](https://github.com/sagarsuperuser/user-service)** — Google OIDC sign-in in Go (OAuth 2.0, PKCE, JWT).  
Everything below is public, dated, and reproducible from the repo it lives in — start with the two benchmark reports.

## [Velox](https://github.com/getvelox/velox) — open-source usage-based billing engine

Metering, pricing, invoicing, Stripe payments, dunning, credits, tax. Go, PostgreSQL with row-level security, React. 110+ architecture decision records; a 29-check money-invariant sweep runs in CI on every pull request. Sole engineer, on an AI-assisted workflow under a written money-path protocol, disclosed in the repo's commit trailers.

- **[Correctness under failure](https://github.com/getvelox/velox/blob/main/docs/benchmarks/failure-correctness.md)** — the billing leader `SIGKILL`ed at five kill points chosen by watching the database rather than by sleeping, then four leaders racing the same cycle at once. 0 duplicate invoices, 0 lost invoices, 0 cents of drift. The negative control drops the partial unique index and reruns: 103 invoices for 40 periods, $2,575.00 billed for $1,000.00 of periods, and every leader reported success.
- **[Sustained throughput on AWS](https://github.com/getvelox/velox/blob/main/docs/benchmarks/sustained-throughput.md)** — 12,000 events/sec at p99 22.6 ms and 15,000 at p99 43.8 ms on a `db.m7g.4xlarge`, each 4 of 5 ten-minute repeats on stock settings — rising to 5 of 5 once a one-parameter WAL fix the runs themselves diagnosed was applied. Every event reconciled, and `pgbench` as the control denominator. The failed repeats, the two product bottlenecks the runs found in Velox itself, and the superseded earlier figures are all on the page.

## [notif-service](https://github.com/sagarsuperuser/notif-service) — distributed SMS platform on AWS

Go services over SQS and RDS Postgres behind RDS Proxy; KEDA queue-driven autoscaling on k3s; Terraform and Kustomize. The runs below are on real AWS; sends go to an in-cluster provider simulator, not a live carrier.

- **[100,000-message campaign](https://github.com/sagarsuperuser/notif-service/blob/main/docs/campaign-100k/README.md)** — 100,000 delivered in 293 s, reconciled minute by minute against AWS CloudWatch: a recording this service does not produce, so it cannot be wrong in the same direction as the code. Queue depth peaked at 2.5% of the campaign and the oldest message never waited longer than eleven seconds. The page also says what the run does not show — nothing failed on it, so its empty dead-letter queue is evidence that failure handling never ran, not that it worked, which is exactly what the A/B below went and measured.
- **[Retry-handling A/B](https://github.com/sagarsuperuser/notif-service/blob/main/docs/campaign-100k/retry-handling-ab-2026-08-15.md)** — controlled experiment on live AWS with accepts (100,000) and accept duration (408 s) held fixed: 90,146 → 98,874 delivered once a retry classifier that tested the error before the HTTP status was fixed. The remaining 1,125 failures match the provider's 1,125 permanent rejections exactly.
- **[Accept-path benchmark](https://github.com/sagarsuperuser/notif-service/blob/main/docs/benchmark-2026-08-14.md)** — 2,000 accepts/sec for three minutes against real RDS and SQS: 359,598 requests, 0 failures, p99 141.5 ms.

## [user-service](https://github.com/sagarsuperuser/user-service) — identity flows in Go

Google OIDC sign-in built end to end: **Authorization Code with PKCE**, the `state` parameter validated on the callback, the in-flight exchange held in a `__Host-` prefixed five-minute cookie, and **JWT validation with the signing algorithm pinned** — the line that closes algorithm-confusion attacks. Session tokens are stored hashed, credentials are bcrypt. Deliberately narrow: a relying party, not an authorization server, and the README says so.

## Upstream contributions

- **[envoyproxy/ratelimit#1013](https://github.com/envoyproxy/ratelimit/pull/1013)** — merged 2025-12-03. The local-cache statistics test never ran, wrote to a null stats sink, expected the wrong counts, and asserted on the wrong types; fixed so hit, miss, lookup and expiry counts are actually validated.
- **[SigNoz#12668](https://github.com/SigNoz/signoz/pull/12668)** — open. Reviewing [a multi-tenant alert-routing fix](https://github.com/SigNoz/signoz/pull/12658#pullrequestreview-5005760883) I traced the site-set for the pattern it corrected and found a second instance the fix didn't reach: at boot, the first organization with no alert rules ended rule-loading for every organization after it, and returned success while doing it. One-word fix, with a test that fails on the old code rather than merely passing on the new.

Also public: **[leaky-bucket-gcra](https://github.com/sagarsuperuser/leaky-bucket-gcra)**, a Redis-backed rate limiter published as a Go module, and a **[text encoder for uber-go/zap](https://github.com/sagarsuperuser/zap/tree/text-encoder)** — a fork branch with its own benchmark module, not an upstream contribution.
