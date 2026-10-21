# Incident Log

**Group:** Bubbles

**Members:**
- Eaindi Linn Moe Oo — 6705140069 — Eaindi
- Phone Pyae — 6705140070 — Payphone-boop
- Min Thu Kha — 6705140031 — 6705140031-creator
- Pyae Pyae Phyo — 6705140068 — 6705140068-PPP
- Soe Wai Yan Htet — 6705140049 — wy69-stack

**Company / domain:** NovaPeak / novapeak.lab
Fill in one entry per fault found. Include real command output as evidence.

---

## Fault 1 — Incorrect DNS zone-file path

- **Symptom (what we observed):**
  ```
  [root@ubuntu:~$ named-checkconf -z
zone novapeak.lab/IN: loaded serial 2024010102
zone 19.168.192.in-addr.arpa/IN: loaded serial 2024010101
zone localhost/IN: loaded serial 2
zone 127.in-addr.arpa/IN: loaded serial 1
zone 0.in-addr.arpa/IN: loaded serial 1
zone 255.in-addr.arpa/IN: loaded serial 1]
  ```
- **Hypothesis (what we suspected and why):**
  The DNS forward zone may reference a zone file that does not exist at the configured path.
- **Proof (the command that confirmed the cause):**
  ```
  [Paste actual command and output]
  ```
- **Fix (what we changed):**
  Changed the forward zone file path in `/etc/bind/named.conf.local` to `/etc/bind/db.novapeak.lab`.
- **Confirmation (output showing it now works):**
  ```
  [Paste actual validation output]
  ```
- **Found/proved by:** [Name]

---

## Fault 2 — Incorrect API DNS record

- **Symptom (what we observed):**
  ```
  [Paste actual command and output showing the problem]
  ```
- **Hypothesis (what we suspected and why):**
  The `api` A record may point to the wrong IP address.
- **Proof (the command that confirmed the cause):**
  ```
  [Paste actual command and output]
  ```
- **Fix (what we changed):**
  Changed the `api` A record to `192.168.19.40` and incremented the zone serial.
- **Confirmation (output showing it now works):**
  ```
  [Paste actual DNS lookup output]
  ```
- **Found/proved by:** [Name]

---

## Fault 3 — Firewall blocking DNS

- **Symptom (what we observed):**
  ```
  [Paste actual command and output showing the problem]
  ```
- **Hypothesis (what we suspected and why):**
  An INPUT firewall rule may be dropping DNS queries over UDP port 53.
- **Proof (the command that confirmed the cause):**
  ```
  [Paste actual iptables output]
  ```
- **Fix (what we changed):**
  Removed the rule dropping UDP traffic to destination port 53.
- **Confirmation (output showing it now works):**
  ```
  [Paste actual firewall and DNS test output]
  ```
- **Found/proved by:** [Name]

---

## Fault 4 — DHCP advertising the wrong DNS server

- **Symptom (what we observed):**
  ```
  [Paste actual command and output showing the problem]
  ```
- **Hypothesis (what we suspected and why):**
  DHCP may be advertising the public DNS server `8.8.8.8` instead of the company DNS server.
- **Proof (the command that confirmed the cause):**
  ```
  [Paste actual DHCP configuration output]
  ```
- **Fix (what we changed):**
  Set the DHCP DNS server option to `192.168.19.10`.
- **Confirmation (output showing it now works):**
  ```
  [Paste actual checker output confirming C9 and C11 pass]
  ```
- **Found/proved by:** [Name]
