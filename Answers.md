# Incident Summary

## What Happened
A legitimate ubuntu user logged in via publickey, executed a sudo command, and later disconnected. A single external IP performed an automated SSH brute-force scan targeting many common usernames and root.

**Attacker IP:** 185.231.54.12 — repeated Invalid user and Failed password attempts over ~14:08–14:22.

**Impact:** No evidence in the provided log of a successful compromise from that IP; legitimate sessions (ubuntu, sysadmin) show normal activity.

**Immediate Actions:** Block 185.231.54.12 at the firewall; enable fail2ban or rate-limiting; disable root password login and enforce key-only SSH.

**Escalation:** Escalate to L2 to review authorized_keys, check for persistence, and scan for post-attack anomalies.

**Severity:** Medium (automated brute force, no confirmed breach in provided logs).

---

## Key Evidence

```
Jun 10 14:03:12 ip-172-31-12-45 sshd[1142]: Accepted publickey for ubuntu from 203.0.113.45 port 51234 ssh2: RSA SHA256:xyz123...  
Jun 10 14:08:11 ip-172-31-12-45 sshd[1204]: Invalid user admin from 185.231.54.12 port 38472.
```

---

## Lab Answers

| # | Question | Answer |
|---|----------|--------|
| 1 | When did ubuntu successfully log in and from which IP? | 14:03:12 from 203.0.113.45 (Accepted publickey for ubuntu … from 203.0.113.45) |
| 2 | Which command did ubuntu run with sudo? | `/usr/bin/apt-get update` at 14:06:22 |
| 3 | Which IP attempted multiple failed logins using different usernames? | 185.231.54.12 (numerous Invalid user and Failed password entries) |
| 4 | List at least five different usernames tried from this IP | admin, administrator, user1, test, oracle, pi, postgres, webmaster, ftpuser, guest, apache, mysql |
| 5 | How many failed login attempts for root from 185.231.54.12? | 15 failed root password attempts |
| 6 | What does this pattern suggest about the attacker's strategy? | Automated brute-force / credential-spray scanning targeting common usernames and root — likely a bot/script trying default or weak credentials |
| 7 | When did the ubuntu user's session end and how? | 14:28:10 — Received disconnect / Disconnected from user ubuntu |
| 8 | Which other user successfully logged in later and from which IP? | sysadmin at 14:31:14 from 198.51.100.77 |
| 9 | Which IPs other than 185.231.54.12 attempted logins? | 45.33.22.11 (git, deploy); 112.85.42.13 (nagios) — isolated probes |
| 10 | Coordinated attack or random probes? | 185.231.54.12 shows coordinated behavior; other IPs show opportunistic probes |
| 11 | Which accounts appear legitimate vs targeted? | **Legitimate:** ubuntu, sysadmin **Targeted:** root, admin, postgres, oracle, ftpuser, apache, mysql |
| 12 | Defensive measures (L1-focused)? | **Immediate:** block 185.231.54.12, enable fail2ban, set `PermitRootLogin no` **Short term:** key-only SSH, review authorized_keys **Medium term:** rate limiting, MFA, centralized logging |

---

## Timeline

- **14:00:01** — CRON session opened/closed (system noise)
- **14:03:12** — Accepted publickey for ubuntu from 203.0.113.45
- **14:06:22** — ubuntu runs `sudo /usr/bin/apt-get update`
- **14:08:11 → 14:22:13** — Burst of Invalid user / Failed password from 185.231.54.12
- **14:28:10** — ubuntu disconnects
- **14:31:14** — Accepted publickey for sysadmin from 198.51.100.77
- **14:32:05** — sysadmin runs `sudo /usr/bin/tail -f /var/log/auth.log`

---

## Quick Commands

```bash
# Show relevant lines
grep -E "Accepted|Failed|Invalid|sudo|session opened|session closed|Disconnected" logs/auth.log

# Count attempts from an IP
grep "185.231.54.12" logs/auth.log | wc -l

# Count failed root attempts
grep "Failed password for root from 185.231.54.12" logs/auth.log | wc -l

# List unique usernames tried
grep "185.231.54.12" logs/auth.log | awk -F' ' '{for(i=1;i<=NF;i++) if($i=="for") print $(i+1)}' | sort -u
```

