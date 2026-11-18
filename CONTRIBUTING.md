# Contributing

Server Edition is a set of Ansible roles that turn a Debian install into a
hardened base machine. A change is accepted when it keeps that promise and
passes the gate.

## Before you write

- Read `roles/README.md`: one role per concern, one catalog entry per role.
- Keep the invariants: base machine only, systemd end to end, fully-qualified
  modules, no site data, idempotence. `AGENTS.md` states them.

## External contributions

External contributions are **not accepted at this time**. Server Edition is maintained
by the owner; unsolicited pull requests, patches and issues may be closed
without review. This policy does not limit the rights the licences grant you:
the source is free software, and you may fork it and modify it under the licence
terms.

## Support and warranty

No support, service level or warranty is provided, express or implied. The
product is distributed under **AGPL-3.0-or-later** and the documentation under
**CC-BY-SA-4.0**, without warranty, as stated in [`LICENSE`](LICENSE),
`LICENSES/` and `NOTICE`. The product configures a machine; the services a site adds are the site's. You are responsible for your deployment and
for complying with the licences when you redistribute or modify the code.

## What a change must carry

1. **A reason that survives a reboot.** A value is declared because the
   machine must have it, not because a blog post did. A measurement names the
   date and the command.
2. **A read-back.** A role that writes a value asserts the running system took
   it. Writing a file is not proof.
3. **No site data.** No address, host name, domain, SSID, secret or customer
   name. The example inventory carries documentation values only.
4. **A second run that changes nothing.** Idempotence is a gate, not a goal.

## The gate

Run from the repository root; a change is not done until it passes:

```sh
ansible-lint playbooks roles
yamllint -c .yamllint roles playbooks inventories
reuse lint
scripts check tools/scripts/binaries/gate.scs
scripts run tools/scripts/binaries/gate.scs
```

The gate also builds the collection and inspects the archive, so a file that
should not ship fails before the tag.

A change that touches the link, the firewall or the accounts is **not** proven
by `--check`. Say what was measured, on which machine, and keep a console path.

## Commits

- One branch per task, `work/<slug>`.
- English, imperative subject; the body says **why**, not what the diff shows.
- One concern per commit.
- Do not commit an artifact (`dist/`, `artifacts/`), a vault or a site
  inventory.

## Licence of a contribution

The product is `AGPL-3.0-or-later`; the documentation is `CC-BY-SA-4.0`. By
sending a change you agree that it is distributed under those terms.

## Reporting a vulnerability

Do not open a public issue. Follow `SECURITY.md`.
