# Hosting Troubleshooting Runbooks

Step-by-step runbooks for common web hosting failures. I'm building a Linux lab server, breaking it on purpose, and documenting how to diagnose and fix each problem.

**Status:** 1 runbook published, 15 planned. I add a new one each time I finish a lab.

## How to use a runbook

1. Find the runbook that matches what the customer sees (its **Symptom**).
2. Run the **Quick check** to confirm it is this problem.
3. Follow **Diagnosis** step by step, comparing your output with *Healthy* and *Broken*.
4. Apply the **Fix**, then prove it with **Verify**.

Paths use `example.com` as the site name, for example `/var/www/example.com/public`. Replace it with the real site.

## Runbook format

Every runbook has the same sections:

| Section | What it answers |
| --- | --- |
| Symptom | What does the customer see? |
| Quick check | Which command confirms it in under a minute? |
| Diagnosis | Which commands find the cause, and what do healthy and broken results look like? |
| Fix | What fixes it, why, and which tempting fixes to avoid? |
| Verify | How do I prove it is fixed, on the server and as the customer? |
| Prevention | What stops it from happening again? |
| Escalate when | What would tell me it is a different problem for someone else? |
| Diagnosis log | When I met it, and how long it took me to find the cause |

New runbooks start from [TEMPLATE.md](TEMPLATE.md).

## Runbooks

| # | Runbook | Area | Status |
| --- | --- | --- | --- |
| 01 | 502 Bad Gateway: PHP-FPM down | Web server | Planned |
| 02 | SSL certificate expired | TLS | Planned |
| 03 | Domain not resolving | DNS | Planned |
| 04 | Disk full | Linux | Planned |
| 05 | 403 Forbidden: wrong file permissions | Linux / Web server | Planned |
| 06 | MySQL "Too many connections" | Database | Planned |
| 07 | Redis out of memory | Cache | Planned |
| 08 | Email going to spam: SPF/DKIM/DMARC failing | Email | Planned |
| 09 | 504 Gateway Timeout: slow PHP or database | Web server / Database | Planned |
| 10 | Server out of memory (OOM killer) | Linux | Planned |
| 11 | WordPress "Error establishing a database connection" | Application | Planned |
| 12 | Redirect loop between HTTP and HTTPS | Web server / TLS | Planned |
| 13 | High CPU load | Linux | Planned |
| 14 | Mixed content warnings after enabling SSL | TLS / Application | Planned |
| 15 | Cannot SSH into the server | Linux / Networking | Planned |
| 16 | [WordPress: "Could not create directory"](linux/wordpress-could-not-create-directory.md) | WordPress / Linux permissions | Done |
