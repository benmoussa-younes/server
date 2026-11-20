# AGENTS.md — Server

Ansible roles that turn a freshly installed Debian machine into a hardened,
identified, systemd-native base server, shipped as the collection
`benmoussa.server`. The product is the base machine; a service belongs to the
site that declares it.

## Build and test

```sh
ansible-lint playbooks roles
yamllint -c .yamllint roles playbooks inventories
reuse lint
scripts check tools/scripts/binaries/gate.scs
scripts run tools/scripts/binaries/gate.scs      # one VERDICT line, exit 0/1
```

The gate is the repository's single entry point: the tree, the version, the
licences, the collection archive and the secret shapes, then the linters and the
playbooks' syntax. It does not change tracked files.

## Conventions

- Only `ansible.builtin` is used; `requirements.yml` declares no collection.
- systemd end to end: one owner for the link (`systemd-networkd`), one for the
  resolver, one for the clock. A host that runs NetworkManager is refused.
- A role keeps its `meta/argument_specs.yml` contract and its idempotence, and
  reads its own change back.
- No site data in a role: an address, a host name, a secret or a customer name
  belongs in the site inventory and its vault.

## Repository facts

- The collection is `benmoussa.server`, version `0.1.0`.
- The product code is `AGPL-3.0-or-later`; the documentation is
  `CC-BY-SA-4.0`.
- A release is a signed tag `v<version>` and three assets: the collection
  tarball, the source tarball and `SHA256SUMS`.
- `roles/README.md` is the role catalog; `playbooks/base.yml` is the desired
  state and `playbooks/audit.yml` the read-only measure.

## Commits and releases

- One branch per task, `work/<slug>`; one concern per commit; English,
  imperative subject; the body says why.
- Commits and tags are GPG-signed. A change lands on `main` only when
  `tools/scripts/binaries/gate.scs` prints `VERDICT: PASS`; a release is the maintainer's decision.

## Security

- No secret in a tracked file: a password, a restic key or a host credential
  lives in the site vault, and a tracked vault is ciphertext.
- The firewall, sshd and the account policy are declared, not left to the
  distribution; a change that touches them names what was measured.
- A vulnerability report is private: follow `SECURITY.md`.

## Maintenance

- Keep `VERSION`, `galaxy.yml`, `roles/identity/defaults/main.yml` and
  `CHANGELOG.md` on the same version.
- The bootstrap password is retired once, in `post_tasks`, never before a task
  that needs `become`: an expired password makes sudo ask for a new one and the
  connection is lost.
