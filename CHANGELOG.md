# Changelog

All notable changes to this deployment definition are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added

- Optional second ACME certificate resolver for a private CA (`acme_internal_ca`), disabled by default.
- Environment variables for extra CA roots of the ACME client (`LEGO_CA_CERTIFICATES`, `LEGO_CA_SYSTEM_CERT_POOL`).

## [0.0.1]

### Added

- Initial App-as-Code service definition for Traefik `v3.7` with HTTP, HTTPS and dashboard ports.
- Docker provider integration, Let's Encrypt DNS-01 resolver (IONOS) and Homepage/AutoDNS integrations.
- README with configuration reference and project icon.
