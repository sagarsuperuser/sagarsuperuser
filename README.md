Backend engineer — billing, payments, and delivery systems.  
**[Velox](https://github.com/getvelox/velox)** — open-source usage-based billing engine (Go, PostgreSQL, React).  
**[notif-service](https://github.com/sagarsuperuser/notif-service)** — distributed SMS platform on AWS (Go, SQS, Kubernetes).  
Everything below is public, dated, and reproducible from the repo it lives in.

## [Velox](https://github.com/getvelox/velox) — open-source usage-based billing engine

Metering, pricing, invoicing, Stripe payments, dunning, credits, tax. Go, PostgreSQL with row-level security, React. 112 architecture decision records; a 28-check money-invariant sweep runs in CI on every pull request. Sole engineer, on an AI-assisted workflow under a written money-path protocol, disclosed in the repo's commit trailers.

- **[Correctness under failure](https://github.com/getvelox/velox/blob/main/docs/benchmarks/failure-correctness.md)** — the billing leader `SIGKILL`ed at five kill points chosen by watching the database rather than by sleeping, then four leaders racing the same cycle at once. 0 duplicate invoices, 0 lost invoices, 0 cents of drift. The negative control drops the partial unique index and reruns: 103 invoices for 40 periods, $2,575.00 billed for $1,000.00 of periods, and every leader reported success.
- **[Sustained throughput on AWS](https://github.com/getvelox/velox/blob/main/docs/benchmarks/sustained-throughput.md)** — 12,000 events/sec at p99 22.6 ms and 15,000 at p99 43.8 ms on a `db.m7g.4xlarge`, each 4 of 5 ten-minute repeats with every event reconciled, and `pgbench` as the control denominator. The failed repeats, the two product bottlenecks the runs found in Velox itself, and the superseded earlier figures are all on the page.

## [notif-service](https://github.com/sagarsuperuser/notif-service) — distributed SMS platform on AWS

Go services over SQS and RDS Postgres behind RDS Proxy; KEDA queue-driven autoscaling on k3s; Terraform and Kustomize. The runs below are on real AWS; sends go to an in-cluster provider simulator, not a live carrier.

- **[100,000-message campaign](https://github.com/sagarsuperuser/notif-service/blob/main/docs/campaign-100k/README.md)** — 100,000 delivered in 293 s, reconciled minute by minute against AWS CloudWatch: a recording this service does not produce, so it cannot be wrong in the same direction as the code. Queue depth peaked at 2.5% of the campaign and the oldest message never waited longer than eleven seconds. The page says plainly what it does not show — nothing failed on this run, so its empty dead-letter queue is evidence that failure handling never ran, not that it worked, which is what the A/B below went and measured. A later audit of every metric against its increment sites found the instruments behind three of that page's figures faulty; they are annotated where they appear rather than removed.
- **[Retry-handling A/B](https://github.com/sagarsuperuser/notif-service/blob/main/docs/campaign-100k/retry-handling-ab-2026-08-15.md)** — controlled experiment on live AWS with accepts (100,000) and accept duration (408 s) held fixed: 90,146 → 98,874 delivered once a retry classifier that tested the error before the HTTP status was fixed. The remaining 1,125 failures match the provider's 1,125 permanent rejections exactly.
- **[Accept-path benchmark](https://github.com/sagarsuperuser/notif-service/blob/main/docs/benchmark-2026-08-14.md)** — 2,000 accepts/sec for three minutes against real RDS and SQS: 359,598 requests, 0 failures, p99 141.5 ms.

## Upstream contributions

- **[envoyproxy/ratelimit#1013](https://github.com/envoyproxy/ratelimit/pull/1013)** — merged 2025-12-03. The local-cache statistics test never ran, wrote to a null stats sink, expected the wrong counts, and asserted on the wrong types; fixed so hit, miss, lookup and expiry counts are actually validated.

Also public: **[leaky-bucket-gcra](https://github.com/sagarsuperuser/leaky-bucket-gcra)**, a Redis-backed rate limiter published as a Go module, and a **[text encoder for uber-go/zap](https://github.com/sagarsuperuser/zap/tree/text-encoder)** — a fork branch with its own benchmark module, not an upstream contribution.
