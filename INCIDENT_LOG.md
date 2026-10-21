# Incident Log

**Group:** Bubbles

**Members:**
- Eaindi Linn Moe Oo — 6705140069 — Eaindi
- Phone Pyae — 6705140070 — Payphone-boop
- Min Thu Kha — 6705140031 — 6705140031-creator
- Pyae Pyae Phyo — 6705140068 — 6705140068-PPP
- Soe Wai Yan Htet — 6705140049 — wy69-stack

**Company / domain:** NovaPeak / novapeak.lab

---

## Fault 1 — Incorrect DNS zone-file path

- **Symptom (what we observed):**

  The initial checker reported that the DNS zone configuration failed validation.

  ```text
  [   1/2 ] C4 named-checkconf -z FAILED; named-checkzone clean
  ```

- **Hypothesis (what we suspected and why):**

  The forward DNS zone referenced an incorrect zone-file path. BIND could not load the `novapeak.lab` zone because the configured file was `/etc/bind/db.MISSING`.

- **Proof (the command that confirmed the cause):**

  The original error was recorded in `/root/session.log`.

  ```text
  root@ubuntu:~$ named-checkconf -z
  zone novapeak.lab/IN: loading from master file /etc/bind/db.MISSING failed: file ...
  ```

  The original error line was truncated in the captured terminal output.

- **Fix (what we changed):**

  Changed the forward zone file path in `/etc/bind/named.conf.local` from `/etc/bind/db.MISSING` to `/etc/bind/db.novapeak.lab`.

- **Confirmation (output showing it now works):**

  Command:

  ```text
  root@ubuntu:~$ named-checkconf -z
  zone novapeak.lab/IN: loaded serial 2024010102
  zone 19.168.192.in-addr.arpa/IN: loaded serial 2024010101
  zone localhost/IN: loaded serial 2
  zone 127.in-addr.arpa/IN: loaded serial 1
  zone 0.in-addr.arpa/IN: loaded serial 1
  zone 255.in-addr.arpa/IN: loaded serial 1
  ```

- **Found/proved by:** [Name(s) of the group member(s) who found or verified this fault]

---

## Fault 2 — Incorrect API DNS record

- **Symptom (what we observed):**

  The initial checker could not resolve the expected DNS records, including the API record.

  ```text
  [ 0.0/3 ] C2 ns1 expected 192.168.19.10 got nothing; www expected 192.168.19.20 got nothing; mail expected 192.168.19.30 got nothing; api expected 192.168.19.40 got nothing
  ```

- **Hypothesis (what we suspected and why):**

  The `api` A record might point to an incorrect IP address, causing it
