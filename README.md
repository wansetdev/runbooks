# Hosting Troubleshooting Runbooks

Step-by-step runbooks for common web hosting failures. I'm building a Linux lab server, breaking it on purpose, and documenting how to diagnose and fix each problem.

**Status:** just started. Runbooks will be added as I complete them.

## Runbook format

Every runbook follows the same five steps:

1. **Symptom**: what the customer or monitoring sees
2. **Diagnosis**: the commands I ran, in order, and what each one told me
3. **Fix**: the exact change that resolved it
4. **Verify**: how I confirmed it was really fixed
5. **Prevention**: what stops it from happening again

New runbooks start from [TEMPLATE.md](TEMPLATE.md).

## Planned runbooks

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
| 15 | Cannot SSH into the server | Linux / Networking | Planned |→

