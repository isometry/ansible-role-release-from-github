# Ansible Role: Release from GitHub

Fetch and install release archives from GitHub with automatic latest detection and special handling for HashiCorp products.

## Requirements

The Ansible user must have write access to `release_tmp_path` (default: `/tmp`) and `release_bin_path` (default: `/usr/local/bin`). The role expects to be called with `become` if privilege escalation is required, and will chown all files to the effective user/group with which ansible runs, maintaining rwx permissions from the source archive but stripping write permissions from other users.

## Role Variables

All role variables are prefixed with `release_` with defaults listed in `defaults/main.yml`.

The only mandatory variable is `release_repo` which is expected to take the form `<user|organisation>/<repo>`:

```yaml
release_repo: hashicorp/terraform
```

If `release_version` is not specified the role will attempt to determine the "latest" release using the GitHub Releases API, falling back to Git tags:

```yaml
release_version: 0.12.23
```

By default, remote TLS certificates are not validated on macOS:

```yaml
release_validate_certs: yes
```

The role will leave a copy of the release archive in `release_tmp_path` (default: `/tmp`) and only re-download if the version or checksum (remote value checked on each run) changes:

```yaml
release_tmp_path: ~/Downloads
```

The role extracts the release archive to `release_bin_path` (default: `/usr/local/bin`), ignoring files listed in `release_exclude_files`:

```yaml
release_bin_path: ~/bin
release_exclude_files:
  - README
```

## Dependencies

None

## Example Playbooks

Deploy v1.24.0 of [GoBGP](https://github.com/osrg/gobgp) to `/usr/local/bin`:

```yaml
- hosts: all
  become: yes
  roles:
    - name: breathe.release-from-github
      release_repo: osrg/gobgp
      release_version: 1.24.0
```

Deploy the latest version of [Terraform](https://github.com/hashicorp/terraform) to ~/bin:

```yaml
- hosts: localhost
  roles:
    - name: breathe.release-from-github
      release_repo: hashicorp/terraform
      release_tmp_path: ~/Downloads
      release_bin_path: ~/bin
```

## License

MIT
