# nixos

## Install

The recommended multi-user Nix installation creates system users, and a system service for the Nix daemon.

1. Multi-user install: `sh <(curl -L https://nixos.org/nix/install)`
2. Add `direnv` as a convenience: `nix-env -i direnv`

## Workflow

1. Edit `flake.nix`
2. Change to directory containing `flake.nix` ???