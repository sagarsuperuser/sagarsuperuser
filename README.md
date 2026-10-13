I'm Sagar Waidande, a backend engineer in Pune, India. Eight years on money systems: trading platforms, payment APIs across several providers, and billing. I've written Go professionally since 2021. Since 2025 I've been building open-source backend systems full time and load-testing them on AWS. Open to senior backend roles in Bengaluru or remote.

Go · PostgreSQL · AWS · Kubernetes · Terraform · Redis

## [Velox](https://github.com/getvelox/velox): open-source billing engine for AI products

Usage metering, pricing, invoicing, Stripe payments, failed-payment retries, credits and tax. Go, PostgreSQL and React. I'm the sole engineer. It is AI-assisted under a written review and testing process.

- [Correctness under failure](https://github.com/getvelox/velox/blob/main/docs/benchmarks/failure-correctness.md): the billing leader was killed at five points mid-run, then four leaders raced the same run. No duplicate or lost invoices. With one database constraint removed as a control, the same test billed $2,575 against $1,000 owed.
- [Sustained throughput on AWS](https://github.com/getvelox/velox/blob/main/docs/benchmarks/sustained-throughput.md): 12,000 events/s at p99 22.6 ms on RDS Postgres, with every event reconciled.

## [notif-service](https://github.com/sagarsuperuser/notif-service): SMS platform on AWS

Go services on SQS and RDS Postgres, autoscaled on Kubernetes. [100,000 messages delivered in 293 s](https://github.com/sagarsuperuser/notif-service/blob/main/docs/campaign-100k/README.md), reconciled against CloudWatch. Sends go to a provider simulator, not a live carrier.

## Upstream

- [envoyproxy/ratelimit#1013](https://github.com/envoyproxy/ratelimit/pull/1013), merged: a statistics test that never ran and asserted the wrong counts now runs and checks the right ones.
- [SigNoz#12668](https://github.com/SigNoz/signoz/pull/12668), open: at boot, the first organization with no alert rules stopped rule loading for every organization after it.

Smaller projects: [leaky-bucket-gcra](https://github.com/sagarsuperuser/leaky-bucket-gcra), a Redis-backed rate limiter for Go, and [user-service](https://github.com/sagarsuperuser/user-service), Google sign-in with PKCE.

---

[sagar10018233@gmail.com](mailto:sagar10018233@gmail.com) · [LinkedIn](https://linkedin.com/in/sagar-waidande-1337bb9a)
