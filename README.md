# renovate-config
Renovate presets for both personal and Sapphic Homelab repositories

The `default` preset does a number of things for a repository that uses it:
- Pins digests wherever possible (from `config:best-practices`)
- Pins development dependencies (from `config:best-practices`)
- Ensure lock files are maintained (from `config:best-practices`)
- Forces semantic commits (from `:semanticCommits`)
- Enable major version updates for Docker images (from `docker:enableMajor`)
- Sets a weekly update schedule to run every Monday at 6:00 AM (`America/Los_Angeles` time)
- Uncaps the amount of pull requests made at a time
- Adds labels for pull requests based on the package ecosystem
- Creates a custom manager type that can be applied to any version number declaration in any YAML file

**NOTE**: Unlike Dependabot, Renovate does not automatically create labels for package ecosystems, with colors and descriptions! When introducing new package ecosystems to a repository, make sure to create a label for the ecosystem with the color described in this repository's Renovate file!

As well, make sure to use this template for the label's description: `Pull requests that update <ECOSYSTEM> code`

## Custom package managers
In addition to the pre-defined package manager types in Renovate, you can also add your own; this can be useful for package ecosystems that Renovate does not support or for package version declarations outside of a formal package manager.

### YAML definitions
This repository provides a built-in custom manager for YAML files. To use it, use this format:
```yaml
# renovate: datasource=DATASOURCE depName=DEPNAME versioning=OPTIONAL_VERSIONING registryUrl=OPTIONAL_REGISTRY_URL
VERSION_KEY_NAME: &OPTIONAL_ANCHOR_NAME "VERSION_VALUE" 
# or
- VERSION_KEY_NAME: &OPTIONAL_ANCHOR_NAME "VERSION_VALUE" 
```

Note that the sections labeled as "optional" can be skipped if they are not needed; Renovate can infer its own relevant values. As well, like shown above, this can be used for where dictionaries are entries in arrays, which are prefixed by hyphens. The amount of whitespace between tokens, newlines between the comment and the version declaration, and the amount of indentation is flexible.

Here is an example of this in action:
```yaml
# renovate: datasource=github-releases depName=AnarchoBooleanism/nixos-cloud-init-installer versioning=semver
NIXOS_CLOUDINIT_ISO_VERSION: &cloudinit-iso-version "v2.12.0"
```

From the perspective of the YAML parser, it just sees the `NIXOS_CLOUDINIT_ISO_VERSION` key defined as `v2.12.0`, with an anchor (`cloudinit-iso-version` attached to it). However, Renovate is able to see the datasource (`github-releases`), the dependency/package name (`AnarchoBooleanism/nixos-cloud-init-installer`), a versioning type (`semver`), and a version number to change (`v2.12.0`), and can easily change the version number without affecting the formatting.