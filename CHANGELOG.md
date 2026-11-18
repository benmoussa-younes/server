# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.0] - 2026-11-18

### Added

- The Ansible collection `benmoussa.server` and its 0.1.0 release.
- The base machine roles: accounts, identity, journald, keyboard, shell,
  hardening, performance, network, resolver, time, security, crypto, backup and
  integrity.
- The read-only `audit` role and its JSON report, separate from `site.yml`.
- An optional rootless Podman engine (`podman_enabled`): packages, registry
  search path, user socket, linger and subordinate IDs. It never creates a
  container.
- The example inventory with documentation values only (TEST-NET-1).
- The repository gate, the CI workflow and the release workflow.

### Changed

- The default displayed name is `Server Edition`; a site overrides
  `identity_name` and the related identity variables.
- The `network` role configures **wired** links only: a wireless interface is
  refused, and the Wi-Fi variables, units and templates are gone. A server is
  wired; the Workstation collection keeps Wi-Fi.
- With `podman_enabled`, the `security` role accepts the rootless engine's
  `tap*`/`podman*` forward links; a published container port remains a site
  service declared in `security_extra_services`.
- The optional visual set in `assets/branding/` uses the emerald palette; no
  role installs it.

### Security

- The firewall is default-deny and the SSH and OpenSSL policies are declared.
- The local backup is encrypted and its password is read from the site vault.
- The integrity role records a SHA-256 baseline of the files the product writes
  outside a package.

[Unreleased]: https://github.com/benmoussa-younes/server/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/benmoussa-younes/server/releases/tag/v0.1.0
