# Server Edition

[![Release](https://img.shields.io/badge/release-0.3.0-blue)](https://github.com/benmoussa-younes/server/releases/tag/v0.3.0)
[![Licence](https://img.shields.io/badge/licence-AGPL--3.0--or--later-blue)](LICENSE)
[![CI](https://github.com/benmoussa-younes/server/actions/workflows/ci.yml/badge.svg)](https://github.com/benmoussa-younes/server/actions/workflows/ci.yml)

Ansible roles that turn a freshly installed **Debian** machine into a
hardened, identified, systemd-native **base server**: accounts, identity, the
link under `systemd-networkd`, the resolver under `systemd-resolved`, time, the
firewall, hardening, the cryptographic policy and a local encrypted backup.

It is the base of a server. It configures a host, and no service is part of it:
DNS authority, VPN, load balancing, storage, databases and PKI belong to the
site that declares them. It configures a **server, not a desktop**: a host that
still runs NetworkManager is refused rather than converted. It is the base of
the Server/Workstation/Container family, shipped as the collection
`benmoussa.server`.

## What it does

- **One account, one identity**: the human account with its groups and its
  password lifetime, the product's name in os-release and on the login banner,
  the console keyboard and the login shell.
- **One owner for the link**: `systemd-networkd` carries the wired link, the
  stub resolver takes its DNS, and one daemon owns the clock. A wireless
  interface is refused: a server is wired.
- **A security baseline**: nftables input default-deny with a failsafe that
  survives a broken rule file, sshd hardened, a password and lockout policy,
  sysctl/module/auditd/resource-limit hardening, and a system-wide OpenSSL and
  SSH algorithm policy.
- **A restic backup in place**: a local encrypted repository, its timer and its
  weekly check, plus a SHA-256 baseline of the files the product writes outside
  a package.
  need; a published port stays a site decision.
- **A read-only audit playbook** that measures the declared state and writes one
  JSON report, separate from the converge.
- **Provable by construction**: every role reads its own change back, and a
  second converge must print `changed=0`.

## Requirements

- a target running **Debian**; the supported release is named in the identity
  distribution table, and an unknown release is refused;
- `ansible-core` **2.19 or later** on the controller, with SSH access to the
  target;
- a privilege-escalation password: no account of the product holds `NOPASSWD`;
- the target's host key in the controller's `known_hosts`.

Only `ansible.builtin` is used. `requirements.yml` declares no external
collection.

## Install the collection

From the release tarball:

```sh
ansible-galaxy collection install benmoussa-server-0.3.0.tar.gz -p ./collections
```

The release also carries `server-0.3.0-src.tar.gz` (the source tree with the
example inventory and the repository tooling) and `SHA256SUMS`. Verify the
checksums before installing:

```sh
sha256sum -c SHA256SUMS
```

The collection carries the roles, the playbooks, `meta/` and the licence texts.
It does not carry an inventory: the site owns its inventory and its vault. The
optional emerald visuals (`assets/`) stay in the source archive, not in the
collection. The source archive carries `inventories/example/`, which documents
the site-facing variables with TEST-NET-1 values only.

## Use it

Create the site inventory from the source archive's `inventories/example/`, or
write a minimal one. The example's `group_vars/all/vars.yml` maps
`backup_restic_password` to the vault; create `vault.yml` from
`vault.yml.example` beside it, then run with `--ask-vault-pass`:

```yaml
all:
  vars:
    ansible_user: debian
    identity_product: server
    accounts_human:
      name: "{{ ansible_user }}"
      groups: [sudo, adm, systemd-journal]
      password_expiry: never
    backup_restic_password: "{{ vault_backup_restic_password }}"
    network_mode: static
    network_address: 192.0.2.10
    network_prefix: "24"
    network_gateway: 192.0.2.1
    network_dns: [192.0.2.53]
    network_apply: verify
    security_ssh_sources: [192.0.2.0/24]
    time_timezone: Etc/UTC
  hosts:
    example-server-01:
      ansible_host: 192.0.2.10
```

A site playbook imports the collection:

```yaml
- import_playbook: benmoussa.server.site
```

Or address the collection playbook directly:

```sh
ansible-playbook -i inventories/acme/hosts.yml benmoussa.server.site \
  --check --diff --ask-become-pass --ask-vault-pass
ansible-playbook -i inventories/acme/hosts.yml benmoussa.server.site \
  --ask-become-pass --ask-vault-pass
ansible-playbook -i inventories/acme/hosts.yml benmoussa.server.site \
  --ask-become-pass --ask-vault-pass          # must print changed=0
```

The second converge must report `changed=0`; a run that still changes something
is a defect. Keep a console path for the first run: the link is moved only in
`manage` mode, and the example uses `verify`.

The read-only audit is separate:

```sh
ansible-playbook -i inventories/acme/hosts.yml benmoussa.server.audit \
  --ask-become-pass
```

## The roles

| Role | Concern |
|---|---|
| `common` | The Debian package baseline, the shared directories and periodic security upgrades. No MTA. |
| `accounts` | The one human account, its groups and its password lifetime. No root-equivalent automation account. |
| `identity` | The product name on os-release, issue, issue.net and the login banner; a real machine identity. |
| `journald` | The journal's size and retention. |
| `keyboard` | The console keyboard, its GRUB copy and the console font. |
| `shell` | The login shell: umask, history, locale, editor, pager and the login dashboard. |
| `hardening` | sysctls, module lists, auditd rules, resource limits, the kernel command line and the boot title. |
| `performance` | The declared VM, I/O and TCP tuning, THP and the CPU governor. |
| `network` | The wired link, owned by `systemd-networkd`. A wireless interface is refused. |
| `resolver` | The stub resolver, fed by the link or by a declared resolver. |
| `time` | One owner of the clock and the declared time zone. |
| `security` | nftables input default-deny with a failsafe, sshd and the password policy. |
| `crypto` | The SSH algorithm lists and the system-wide OpenSSL policy. |
| `backup` | A local encrypted restic repository, its timer and its weekly check. |
| `integrity` | A SHA-256 baseline of the files the product writes outside a package. |
| `audit` | Read-only: measures the declared state and writes one JSON report. Not part of `playbooks/site.yml`. |

`roles/README.md` is the catalog with the key variables. A role carries no
README of its own: its contract is its variables and its
`meta/argument_specs.yml`.

## Branding

The displayed name is `Server Edition`; a site overrides `identity_name` (and
the URL variables the `identity` role reads). The source archive carries an
optional emerald visual set under the AGPL (`assets/branding/`): a wallpaper, a
mark and a GRUB image. The collection archive excludes it. No role installs
them: a site that wants them copies them, or declares its own files from its
own path and keeps its own licence for them.

## Verify a run

- `--check --diff` is a forecast, not proof.
- A real converge is proved by a second real converge at `changed=0`.
- A change that touches the link, the firewall or the accounts is not proved by
  `--check`: keep a console path.
- The `audit` playbook measures the declared state after the converge.

## Project status

`0.3.0` is the current release (the first was `0.1.0`). Debian amd64 is the only supported base; a newer
Debian release is added when the product declares it. The product is maintained
by one author; there is no support contract, service level or warranty, express
or implied (see [`CONTRIBUTING.md`](CONTRIBUTING.md) and
[`SECURITY.md`](SECURITY.md)).

## Support

- A defect or a question: open an issue on
  [GitHub](https://github.com/benmoussa-younes/server/issues).
- A security report: **not** a public issue — follow [`SECURITY.md`](SECURITY.md).
- The gate and the release process are described in [`AGENTS.md`](AGENTS.md).

## Licence

The product code is `AGPL-3.0-or-later`; the documentation is
`CC-BY-SA-4.0`. `LICENSE` is the AGPL text, `LICENSES/` holds every licence
text, `NOTICE` states the layers and `REUSE.toml` maps every path.
`reuse lint` proves the map.

## Contributing and security

External contributions are not accepted at this time; unsolicited pull requests
or patches may be closed without review. No support, service level or warranty
is provided beyond the licences. See [`CONTRIBUTING.md`](CONTRIBUTING.md) for
the maintainer workflow and the private vulnerability report in
[`SECURITY.md`](SECURITY.md).
