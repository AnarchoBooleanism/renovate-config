# renovate-config
Renovate presets for both personal and Sapphic Homelab repositories

The `default` preset does a number of things for a repository that uses it:
- Pins digests wherever possible (from `config:best-practices`)
- Pins development dependencies (from `config:best-practices`)
- Ensure lock files are maintained (from `config:best-practices`)
- Forces semantic commits (from `:semanticCommits`)
- Enable major version updates for Docker images (from `docker:enableMajor`)
- Sets a weekly update schedule to run every Monday at 6:00 AM, `America/Los_Angeles` time (from `modules/scheduling`)
- Uncaps the amount of pull requests made at a time (from `modules/scheduling`)
- Adds labels for pull requests based on the package ecosystem (from `modules/pr-labels`)
- Creates a custom manager type that can be applied to any version number declaration in any YAML file (from `modules/managers`)
- Groups updates for presets from this repository together for pull requests (from `modules/groups`)

**NOTE**: Unlike Dependabot, Renovate does not automatically create labels for package ecosystems, with colors and descriptions! When introducing new package ecosystems to a repository, make sure to create a label for the ecosystem with the color described in this repository's Renovate file! As well, make sure to use this template for the label's description: `Pull requests that update <ECOSYSTEM> code`

To use the preset(s) in this repository, simply add `github>AnarchoBooleanism/renovate-config` as an entry in the `extends` part of your Renovate config, like this:
```jsonc
{
  // Overall Renovate config
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>AnarchoBooleanism/renovate-config"
  ]
}
```

As best practice for reproducibility, however, it is recommended to specify the preset(s) with a Git version tag, like this:
```jsonc
{
  // Overall Renovate config
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>AnarchoBooleanism/renovate-config#v1.0.0"
  ]
}
```

Generally, tags will be in SemVer format, with a leading `v`; each tag (for production) should have a corresponding GitHub Release, as well.

As another note, adding `config:best-practices` or `config:recommended` is not necessary when using the default configuration, as this Renovate configuration already adds those presests.

## Self-updating

If using version tags, assuming that the name of the repository is in the context of `github`, then Renovate should be able to automatically update itself (no extra work needed). However, if the name of the repository is in the context of `local`, then Renovate will not be able to match it to a package.

For such cases, this Renovate configuration, by default, comes with the ability to have Renovate automatically update the version tag, just like any other dependency. However, with it being based on regex, and therefore unable to understand context, it is opt-in for each specific reference to the presets in this repository. Opting in is done by adding a specific type of comment (with just the text, `self-update`, and nothing else beyond whitespace) after the preset is specified. Depending on the circumstances, either single-line or multi-line comments can be used.

Here is an example of how this is used (with single-line comments):
```jsonc
{
  // Overall Renovate config
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "local>AnarchoBooleanism/renovate-config#v1.0.0", // self-update
    "local>AnarchoBooleanism/renovate-config//example#v1.0.0" // self-update
  ]
}
```

In the above example, the comment with `self-update` is listed at the end of the line where a preset is specified, marking the preset before it as a dependency to update. Note that the comment can be placed after the comma, and, as well, that the amount of non-newline whitespace that you can add is flexible, going from any amount of whitespace to no whitespace. However, with the single-line comment approach, the `// self-update` comment has to be on the same line as the selected preset.

Using multi-line comments (with just the text `self-update`) can be much more flexible in certain scenarios, like in this example:
```jsonc
{
  // Overall Renovate config
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "local>AnarchoBooleanism/renovate-config#v1.0.0" /* self-update */, // Example comment
    "example-preset"
  ]
}
```

Here is another example where the multi-line comment approach is used:
```jsonc
{
  // Overall Renovate config
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>AnarchoBooleanism/renovate-config#v1.0.0"/*self-update*/,"example-preset"]
}
```

In the above examples, the comment with `self-update` is listed just after a preset is specified, but *before* another preset can be specified (meaning before the comma) and before the end of the array is marked with the `]` symbol.

Like the single-line comment approach, the amount of whitespace is flexible, going from none to any amount, but unlike the single-line approach, the whitespace can also include newlines, like in the below example:
```jsonc
{
  // Overall Renovate config
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "local>AnarchoBooleanism/renovate-config#v1.0.0"
    /*
    self-update
    */,
    "example-preset"
  ]
}
```

There can be any amount of newlines between the preset name, `/*`, `self-update`, `*/` and the next tokens. The only hard rule is that, outside of whitespace, the comment can only include `self-update`, and that it must be placed before a comma or the end of the array (`]`).

Furthermore, if the presets in this repository are referenced more than once (and use the self-update functionality), updates to their version tags will be grouped together as a single pull request. 

## Modules

There also exist various modules of configuration in the case you don't want to pull `default.json` wholesale, all in the `modules` subdirectory:
- `groups`: Defines groups of packages to group together for pull requests, including that of the presets of this repository
- `managers`: Defines custom managers, e.g. the custom YAML comment manager
- `pr-labels`: Creates rules for creating labels for pull requests, based on package ecosystem
- `scheduling`: Rules for schedule and time-based limits
- `self-update`: Functionality for self-updating this Renovate configuration as a dependency in other repositories

To use the individual modules provided by this repository, after the end of the repository name, simply add two forward slashes (`//`) and the path of the module relative to the repository root (without the file extension), with another leading slash, resulting in this syntax: `github>AnarchoBooleanism/renovate-config//MODULE-FILE-PATH` (note that the `.json` part of the file name should be skipped here)

Here is an example of this in action (for `/modules/pr-labels.json`):
```jsonc
{
  // Overall Renovate config
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>AnarchoBooleanism/renovate-config//modules/pr-labels"
  ]
}
```

Like the default configuration, it is best practice for reproducibility to use modules with version tags, by adding the `#` symbol and the version tag name at the end, like this:
```jsonc
{
  // Overall Renovate config
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>AnarchoBooleanism/renovate-config//modules/pr-labels#v1.0.0"
  ]
}
```

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