# 🔐 Auth Log Analysis Challenge

## Scenario
You are a SOC analyst investigating authentication logs from a Linux server. Your task is to analyze the provided log file (`logs/auth.log`) and answer the following questions. Each question requires careful log inspection and reasoning.

---

## Questions

### Normal Activity Recognition
1. At what time did the `ubuntu` user successfully log in, and from which IP address?
2. Which command did the `ubuntu` user run with `sudo` after logging in?

### Suspicious Activity Detection
3. Identify the IP address that attempted multiple failed logins using different usernames.
4. List at least **five different usernames** that were tried from this IP.

### Brute Force Analysis
5. How many failed login attempts for the `root` account can you find from IP `185.231.54.12`?
6. What does this pattern suggest about the attacker’s strategy?

### Session Tracking
7. When did the `ubuntu` user’s session end, and how was it terminated?
8. Which other user successfully logged in later, and from which IP?

### Anomaly Investigation
9. Which IP addresses other than `185.231.54.12` attempted logins, and what usernames did they try?
10. Do these attempts look like part of a coordinated attack or random probes?

### Critical Thinking
11. Based on the logs, which accounts appear to be legitimate, and which are likely targeted by attackers?
12. What defensive measures would you recommend to protect this server?

---

## Instructions
- Download the `logs/auth.log` file from this repository.
- Investigate the log using tools like `grep`, `awk`, or manual inspection.
- Submit your answers by:
  - Opening an **Issue** in this repo, or
  - Forking the repo and adding your answers in a file called `answers.md`.

---

## Learning Outcomes
By completing this challenge, learners will:
- Differentiate between successful and failed authentication events.
- Recognize brute-force attack patterns in SSH logs.
- Track user sessions and privilege escalation (`sudo` usage).
- Correlate events across timestamps and IP addresses.
- Develop recommendations for hardening SSH access.

---

## Next Steps
- Fork this repository and attempt the challenge.
- Document your investigation process, not just the answers.
- Share your findings with peers or submit via Issues/PRs.
