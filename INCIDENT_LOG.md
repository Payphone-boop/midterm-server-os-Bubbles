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

  The `api` A record might point to an incorrect IP address, causing it to fail the DNS record check.

- **Proof (the command that confirmed the cause):**

  The original zone-file content recorded in `/root/session.log` showed:

  ```text
  api IN A 192.168.19.1
  ```

  The expected API address was `192.168.19.40`.

- **Fix (what we changed):**

  Changed the `api` A record in `/etc/bind/db.novapeak.lab` to `192.168.19.40` and incremented the zone serial to `2024010102`.

- **Confirmation (output showing it now works):**

  The final checker reported:

  ```text
  [3/3] C2 all four A records correct
  TOTAL: 25.0 / 25
  ```

- **Found/proved by:** [Name(s) of the group member(s) who found or verified this fault]

---

## Fault 3 — Firewall blocking DNS

- **Symptom (what we observed):**

  The initial checker reported that the client could not reach the DNS server at `192.168.19.10`.

  ```text
  [   0/2 ] C1 client cannot reach named at 192.168.19.10 (timeout/no status). Check listen-on, allow-query, firewall, and that it serves on lan0.
  ```

- **Hypothesis (what we suspected and why):**

  A firewall rule might be dropping incoming DNS queries over UDP port 53, preventing clients from reaching the DNS server.

- **Proof (the command that confirmed the cause):**

  The original firewall configuration contained this rule:

  ```text
  -A INPUT -p udp -m udp --dport 53 -j DROP
  ```

- **Fix (what we changed):**

  Removed the firewall rule that dropped incoming UDP traffic to destination port 53.

- **Confirmation (output showing it now works):**

  The final checker reported:

  ```text
  [   2/2 ] C1 named is authoritative for novapeak.lab and reachable from the client.
  TOTAL: 25.0 / 25
  ```

- **Found/proved by:** [Name(s) of the group member(s) who found or verified this fault]

---

## Fault 4 — DHCP advertising the wrong DNS server

- **Symptom (what we observed):**

  The initial checker reported that DHCP was advertising the public DNS server `8.8.8.8` instead of the company DNS server `192.168.19.10`.

  ```text
  [   0/3 ] C9 DHCP advertises 8.8.8.8, expected the company DNS 192.168.19.10
  [   0/3 ] C11 using the leased resolver 8.8.8.8, www did not resolve to 192.168.19.20 (got nothing).
  ```

- **Hypothesis (what we suspected and why):**

  The DHCP configuration was advertising an incorrect DNS server address. Clients receiving `8.8.8.8` would not use the company's DNS server to resolve internal domain names.

- **Proof (the command that confirmed the cause):**

  The initial checker output recorded:

  ```text
  [   0/3 ] C9 DHCP advertises 8.8.8.8, expected the company DNS 192.168.19.10
  ```

- **Fix (what we changed):**

  Changed the DNS server option in `/etc/dhcp/dhcpd.conf` from `8.8.8.8` to `192.168.19.10`.

- **Confirmation (output showing it now works):**

  The final checker reported:

  ```text
  [3/3] C9 DHCP advertises the company DNS (192.168.19.10)
  [3/3] C11 client resolves www via the leased resolver
  TOTAL: 25.0 / 25
  ```

- **Found/proved by:** [Name(s) of the group member(s) who found or verified this fault]
