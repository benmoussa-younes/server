# Security policy

## Supported versions

| Version | Supported |
|---|---|
| 0.1.x | yes |
| older | no |

A release is supported until a later release explicitly withdraws it. Debian
is the supported base; a future Debian release is added when the product
declares it.

## Reporting a vulnerability

Do not open a public issue. Report it privately to:

**younes.benmoussa@pm.me**

Include:

- the product version (`cat VERSION`);
- the role and task, or the file and line;
- the affected Debian release;
- a reproduction, if it is safe to share;
- the impact you believe it has.

We aim to acknowledge a report within five business days. A fix and a disclosure
date are agreed with the reporter. If you need an encrypted channel, ask for the
current security contact key in the first message.

## Scope

In scope:

- the roles in `roles/`, the playbooks, the collection archive and the
  repository tooling;
- the declared firewall, SSH, account and cryptographic policy;
- the vault and secret-handling paths;
- a defect that makes a correct machine fail or a converge unsafe.

Out of scope:

- the services a site adds;
- the site's network, hardware, DNS, NTP or Debian mirror;
- a host that is not Debian;
- a site inventory or vault that was edited outside the documented workflow.

## What the product does not do

- It does not send telemetry.
- It does not run an MTA or relay mail.
- It does not reboot a machine silently.
- It does not store a secret outside the site vault and the root-only files the
  roles create.

## Hardening baseline

The product declares:

- `nftables` input default-deny, with SSH restricted to the declared sources;
- SSH algorithm lists without SHA-1 or CBC, and a declared OpenSSL policy;
- `pam_pwquality`, `pam_faillock` and a password lifetime;
- sysctl, module, auditd and resource-limit hardening;
- a local encrypted restic backup with a weekly integrity check.

The exact values are in `roles/*/defaults/main.yml` and the role comments.
