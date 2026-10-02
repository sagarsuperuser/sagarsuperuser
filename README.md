Most of my work is backend systems where a wrong answer costs money. I publish the evidence: every number below links to a dated write-up in its repo, failed runs included.

**Golang** · PostgreSQL · AWS (RDS, SQS, CloudWatch) · Kubernetes · Terraform · Redis

## [Velox](https://github.com/getvelox/velox): open-source billing engine for AI products

Usage metering, pricing, invoicing, Stripe payments, dunning, credits and tax. Self-hosted, built in Golang, PostgreSQL (row-level security for tenant isolation) and React. I'm the sole engineer. It's AI-assisted under a written review and testing process, and the commit trailers disclose it.

- [Correctness under failure](https://github.com/getvelox/velox/blob/main/docs/benchmarks/failure-correctness.md): the billing leader was `SIGKILL`ed at five points mid-cycle, then four leaders raced the same cycle. **0 duplicate invoices, 0 lost, 0 cents of drift.** The negative control removes one partial unique index and reruns: 103 invoices for 40 periods, $2,575.00 billed against $1,000.00 owed, and every leader reported success.
- [Sustained throughput on AWS](https://github.com/getvelox/velox/blob/main/docs/benchmarks/sustained-throughput.md): **12,000 events/s at p99 22.6 ms**, and 15,000 at p99 43.8 ms, on a `db.m7g.4xlarge`. That held in 4 of 5 ten-minute repeats on stock settings, and 5 of 5 after a one-parameter WAL fix the runs themselves diagnosed. Every event reconciled. The failed repeats and superseded figures stay on the page.
- **110+ architecture decision records**, and a 29-check money-invariant sweep that runs in CI on every pull request.

## [notif-service](https://github.com/sagarsuperuser/notif-service): distributed SMS platform on AWS

Golang services on SQS and RDS Postgres behind RDS Proxy, with queue-driven autoscaling (KEDA) on k3s, Terraform and Kustomize. Runs are on real AWS. Sends go to an in-cluster provider simulator, not a live carrier.

- [100,000-message campaign](https://github.com/sagarsuperuser/notif-service/blob/main/docs/campaign-100k/README.md): **100,000 delivered in 293 s**, reconciled minute by minute against CloudWatch, an independent record the service doesn't produce. No message waited in the queue longer than 11 s.
- [Retry-handling A/B](https://github.com/sagarsuperuser/notif-service/blob/main/docs/campaign-100k/retry-handling-ab-2026-08-15.md): **90,146 → 98,874 delivered** at fixed load (100,000 accepts in 408 s on both arms), after fixing a retry classifier that checked the error before the HTTP status. The remaining 1,125 failures match the provider's 1,125 permanent rejections exactly.
- [Accept-path benchmark](https://github.com/sagarsuperuser/notif-service/blob/main/docs/benchmark-2026-08-14.md): **2,000 accepts/s for three minutes** against real RDS and SQS. 359,598 requests, 0 failures, p99 141.5 ms.

## [user-service](https://github.com/sagarsuperuser/user-service): Google OIDC sign-in in Golang

Authorization Code with PKCE, `state` validated on the callback, the in-flight exchange held in a `__Host-` five-minute cookie, and **JWT validation with the signing algorithm pinned** against algorithm-confusion attacks. Session tokens are stored hashed and credentials in bcrypt. Scoped as a relying party, not an authorization server.

## Upstream contributions

- [envoyproxy/ratelimit#1013](https://github.com/envoyproxy/ratelimit/pull/1013), **merged** Dec 2025: the local-cache statistics test never ran, wrote to a null stats sink and asserted the wrong counts and types. It now validates hit, miss, lookup and expiry counts.
- [SigNoz#12668](https://github.com/SigNoz/signoz/pull/12668), **open**: while reviewing [a multi-tenant alert-routing fix](https://github.com/SigNoz/signoz/pull/12658#pullrequestreview-5005760883), I found a second instance it didn't reach. At boot, the first organization with no alert rules ended rule loading for every organization after it, and returned success. One-word fix, with a test that fails on the old code.

Also: [leaky-bucket-gcra](https://github.com/sagarsuperuser/leaky-bucket-gcra), a Redis-backed GCRA rate limiter in a single Lua script, published as a Go module. And a [text encoder for uber-go/zap](https://github.com/sagarsuperuser/zap/tree/text-encoder) on a fork branch, benchmarked against slog and klog, not upstreamed.

---

[sagar10018233@gmail.com](mailto:sagar10018233@gmail.com) · [LinkedIn](https://linkedin.com/in/sagar-waidande-1337bb9a)
