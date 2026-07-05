# Vimeo Bug Bounty Recon - 2026-07-05

## Summary
- **Target**: vimeo.com
- **Subdomains Enumerated**: 361 (via subfinder)
- **Date**: 2026-07-05
- **Primary Domain**: vimeo.com

## Live Hosts
| Host | Status | Notes |
|------|--------|-------|
| www.vimeo.com | 301 -> vimeo.com |
| api.vimeo.com | 401 | Requires Bearer token |
| help.vimeo.com | 302 -> help.vimeo.com/hc | Zendesk |
| developer.vimeo.com | 200 OK | Developer portal |

## CORS Testing
- **CRITICAL**: `api.vimeo.com` reflects the Origin header and sets:
  - `access-control-allow-origin: https://evil.com`
  - `access-control-allow-credentials: true`
  - `access-control-allow-methods: GET, OPTIONS`
  - `access-control-allow-headers: Accept, Authorization, Content-Type...`
- This allows an attacker on any origin to make authenticated API requests on behalf of users

## Exposed Files
| Path | Status | Size | Notes |
|------|--------|------|-------|
| /robots.txt | 200 | 14 KB | Valid robots.txt |
| /.env | 403 | | Blocked |
| /.git/config | 403 | | Blocked |
| /dump.sql | 404 | | |
| /admin | 404 | | |

## Directory Fuzzing (ffuf)
- `.env` and `.git/config` return HTTP 403 (blocked)
- All other paths return HTTP 301 redirects
- `/robots.txt` accessible (14KB)

## Security Findings
- **Severity**: CRITICAL - CORS on api.vimeo.com allows arbitrary origin + credentials
- HSTS with preload enabled
- API properly requires authentication (401 without token)
