## The Manifest Format

The `Cargo.toml` file for each package is called its *manifest*. Every manifest
file consists of the following sections:

* [`cargo-features`](unstable.md) — Unstable, nightly-only features.
* [`[package]`](#the-package-section) — Defines a package.
  * [`name`](#the-name-field) — The name of the package.
  * [`version`](#the-version-field) — The version of the package.
  * [`authors`](#the-authors-field) — The authors of the package.
  * [`edition`](#the-edition-field) — The Rust edition.
  * [`description`](#the-description-field) — A description of the package.
  * [`documentation`](#the-documentation-field) — URL of the package documentation.
  * [`readme`](#the-readme-field) — Path to the package's README file.
  * [`homepage`](#the-homepage-field) — URL of the package homepage.
  * [`repository`](#the-repository-field) — URL of the package source repository.
  * [`license`](#the-license-and-license-file-fields) — The package license.
  * [`license-file`](#the-license-and-license-file-fields) — Path to the text of the license.
  * [`keywords`](#the-keywords-field) — Keywords for the package.
  * [`categories`](#the-categories-field) — Categories of the package.
  * [`workspace`](#the-workspace-field) — Path to the workspace for the package.
  * [`build`](#the-build-field) — Path to the package build script.
  * [`links`](#the-links-field) — Name of the native library the package links with.
  * [`exclude`](#the-exclude-and-include-fields) — Files to exclude when publishing.
  * [`include`](#the-exclude-and-include-fields) — Files to include when publishing.
  * [`publish`](#the-publish-field) — Can be used to prevent publishing the package.
  * [`metadata`](#the-metadata-table) — Extra settings for external tools.
  * [`default-run`](#the-default-run-field) — The default binary to run by [`cargo run`].
  * [`autobins`](cargo-targets.md#target-auto-discovery) — Disables binary auto discovery.
  * [`autoexamples`](cargo-targets.md#target-auto-discovery) — Disables example auto discovery.
  * [`autotests`](cargo-targets.md#target-auto-discovery) — Disables test auto discovery.
  * [`autobenches`](cargo-targets.md#target-auto-discovery) — Disables bench auto discovery.
* Target tables: (see [configuration](cargo-targets.md#configuring-a-target) for settings)
  * [`[lib]`](cargo-targets.md#library) — Library target settings.
  * [`[[bin]]`](cargo-targets.md#binaries) — Binary target settings.
  * [`[[example]]`](cargo-targets.md#examples) — Example target settings.
  * [`[[test]]`](cargo-targets.md#tests) — Test target settings.
  * [`[[bench]]`](cargo-targets.md#benchmarks) — Benchmark target settings.
* Dependency tables:
  * [`[dependencies]`](specifying-dependencies.md) — Package library dependencies.
  * [`[dev-dependencies]`](specifying-dependencies.md#development-dependencies) — Dependencies for examples, tests, and benchmarks.
  * [`[build-dependencies]`](specifying-dependencies.md#build-dependencies) — Dependencies for build scripts.
  * [`[target]`](specifying-dependencies.md#platform-specific-dependencies) — Platform-specific dependencies.
* [`[badges]`](#the-badges-section) — Badges to display on [crates.io].
* [`[features]`](features.md) — Conditional compilation features.
* [`[patch]`](overriding-dependencies.md#the-patch-section) — Override dependencies.
* [`[replace]`](overriding-dependencies.md#the-replace-section) — Override dependencies (deprecated).
* [`[profile]`](profiles.md) — Compiler settings and optimizations.
* [`[workspace]`](workspaces.md) — The workspace definition.

<a id="package-metadata"></a>
### The `[package]` section

The first section in a `Cargo.toml` is `[package]`.

```toml
[package]
name = "hello_world" # the name of the package
version = "0.1.0"    # the current version, obeying semver
authors = ["Alice <a@example.com>", "Bob <b@example.com>"]
```

The only fields required by Cargo are [`name`](#the-name-field) and
[`version`](#the-version-field). If publishing to a registry, the registry may
require additional fields. See the notes below and [the publishing
chapter][publishing] for requirements for publishing to [crates.io].

#### The `name` field

The package name is an identifier used to refer to the package. It is used
when listed as a dependency in another package, and as the default name of
inferred lib and bin targets.

The name must use only [alphanumeric] characters or `-` or `_`, and cannot be empty.
Note that [`cargo new`] and [`cargo init`] impose some additional restrictions on
the package name, such as enforcing that it is a valid Rust identifier and not
a keyword. [crates.io] imposes even more restrictions, such as
enforcing only ASCII characters, not a reserved name, not a special Windows
name such as "nul", is not too long, etc.

[alphanumeric]: ../../std/primitive.char.html#method.is_alphanumeric

#### The `version` field

Cargo bakes in the concept of [Semantic
Versioning](https://semver.org/), so make sure you follow some basic rules:

* Before you reach 1.0.0, anything goes, but if you make breaking changes,
  increment the minor version. In Rust, breaking changes include adding fields to
  structs or variants to enums.
* After 1.0.0, only make breaking changes when you increment the major version.
  Don’t break the build.
* After 1.0.0, don’t add any new public API (no new `pub` anything) in patch-level
  versions. Always increment the minor version if you add any new `pub` structs,
  traits, fields, types, functions, methods or anything else.
* Use version numbers with three numeric parts such as 1.0.0 rather than 1.0.

<a id="the-authors-field-optional"></a>
#### The `authors` field

The `authors` field lists people or organizations that are considered the
"authors" of the package. The exact meaning is open to interpretation — it may
list the original or primary authors, current maintainers, or owners of the
package. These names will be listed on the crate's page on
[crates.io]. An optional email address may be included within angled
brackets at the end of each author.

> **Note**: [crates.io] requires at least one author to be listed.

<a id="the-edition-field-optional"></a>
#### The `edition` field

You can opt in to a specific [Rust Edition] for your package with the
`edition` key in `Cargo.toml`. If you don't specify the edition, it will
default to 2015.

```toml
[package]
# ...
edition = '2018'
```

The `edition` key affects which edition your package is compiled with. Cargo
will always generate packages via [`cargo new`] with the `edition` key set to the
latest edition. Setting the `edition` key in `[package]` will affect all
targets/crates in the package, including test suites, benchmarks, binaries,
examples, etc.

#### The `description` field

The description is a short blurb about the package. [crates.io] will display
this with your package. This should be plain text (not Markdown).

```toml
[package]
# ...
description = "A short description of my package"
```

> **Note**: [crates.io] requires the `description` to be set.

<a id="the-documentation-field-optional"></a>
#### The `documentation` field

The `documentation` field specifies a URL to a website hosting the crate's
documentation. If no URL is specified in the manifest file, [crates.io] will
automatically link your crate to the corresponding [docs.rs] page.

```toml
[package]
# ...
documentation = "https://docs.rs/bitflags"
```

#### The `readme` field

The `readme` field should be the path to a file in the package root (relative
to this `Cargo.toml`) that contains general information about the package.
This file will be transferred to the registry when you publish. [crates.io]
will interpret it as Markdown and render it on the crate's page.

```toml
[package]
# ...
readme = "README.md"
```

If no value is specified for this field, and a file named `README.md`,
`README.txt` or `README` exists in the package root, then the name of that
file will be used. You can suppress this behavior by setting this field to
`false`. If the field is set to `true`, a default value of `README.md` will
be assumed.

#### The `homepage` field

The `homepage` field should be a URL to a site that is the home page for your
package.

```toml
[package]
# ...
homepage = "https://serde.rs/"
```

#### The `repository` field

The `repository` field should be a URL to the source repository for your
package.

```toml
[package]
# ...
repository = "https://github.com/rust-lang/cargo/"
```

#### The `license` and `license-file` fields

The `license` field contains the name of the software license that the package
is released under. The `license-file` field contains the path to a file
containing the text of the license (relative to this `Cargo.toml`).

[crates.io] interprets the `license` field as an [SPDX 2.1 license
expression][spdx-2.1-license-expressions]. The name must be a known license
from the [SPDX license list 3.6][spdx-license-list-3.6]. Parentheses are not
currently supported. See the [SPDX site] for more information.

SPDX license expressions support AND and OR operators to combine multiple
licenses.[^slash]

```toml
[package]
# ...
license = "MIT OR Apache-2.0"
```

Using `OR` indicates the user may choose either license. Using `AND` indicates
the user must comply with both licenses simultaneously. The `WITH` operator
indicates a license with a special exception. Some examples:

* `MIT OR Apache-2.0`
* `LGPL-2.1-only AND MIT AND BSD-2-Clause`
* `GPL-2.0-or-later WITH Bison-exception-2.2`

If a package is using a nonstandard license, then the `license-file` field may
be specified in lieu of the `license` field.

```toml
[package]
# ...
license-file = "LICENSE.txt"
```

> **Note**: [crates.io] requires either `license` or `license-file` to be set.

[^slash]: Previously multiple licenses could be separated with a `/`, but that
usage is deprecated.

#### The `keywords` field

The `keywords` field is an array of strings that describe this package. This
can help when searching for the package on a registry, and you may choose any
words that would help someone find this crate.

```toml
[package]
# ...
keywords = ["gamedev", "graphics"]
```

> **Note**: [crates.io] has a maximum of 5 keywords. Each keyword must be
> ASCII text, start with a letter, and only contain letters, numbers, `_` or
> `-`, and have at most 20 characters.

#### The `categories` field

The `categories` field is an array of strings of the categories this package
belongs to.

```toml
categories = ["command-line-utilities", "development-tools::cargo-plugins"]
```

> **Note**: [crates.io] has a maximum of 5 categories. Each category should
> match one of the strings available at <https://crates.io/category_slugs>, and
> must match exactly.

<a id="the-workspace--field-optional"></a>
#### The `workspace` field

The `workspace` field can be used to configure the workspace that this package
will be a member of. If not specified this will be inferred as the first
Cargo.toml with `[workspace]` upwards in the filesystem. Setting this is
useful if the member is not inside a subdirectory of the workspace root.

```toml
[package]
# ...
workspace = "path/to/workspace/root"
```

This field cannot be specified if the manifest already has a `[workspace]`
table defined. That is, a crate cannot both be a root crate in a workspace
(contain `[workspace]`) and also be a member crate of another workspace
(contain `package.workspace`).

For more information, see the [workspaces chapter](workspaces.md).

<a id="package-build"></a>
<a id="the-build-field-optional"></a>
#### The `build` field

The `build` field specifies a file in the package root which is a [build
script] for building native code. More information can be found in the [build
script guide][build script].

[build script]: build-scripts.md

```toml
[package]
# ...
build = "build.rs"
```

The default is `"build.rs"`, which loads the script from a file named
`build.rs` in the root of the package. Use `build = "custom_build_name.rs"` to
specify a path to a different file or `build = false` to disable automatic
detection of the build script.

<a id="the-links-field-optional"></a>
#### The `links` field

The `links` field specifies the name of a native library that is being linked
to. More information can be found in the [`links`][links] section of the build
script guide.

[links]: build-scripts.md#the-links-manifest-key

```toml
[package]
# ...
links = "foo"
```

<a id="the-exclude-and-include-fields-optional"></a>
#### The `exclude` and `include` fields

You can explicitly specify that a set of file patterns should be ignored or
included for the purposes of packaging. The patterns specified in the
`exclude` field identify a set of files that are not included, and the
patterns in `include` specify files that are explicitly included.

The patterns should be [gitignore]-style patterns. Briefly:

- `foo` matches any file or directory with the name `foo` anywhere in the
  package. This is equivalent to the pattern `**/foo`.
- `/foo` matches any file or directory with the name `foo` only in the root of
  the package.
- `foo/` matches any *directory* with the name `foo` anywhere in the package.
- Common glob patterns like `*`, `?`, and `[]` are supported:
  - `*` matches zero or more characters except `/`.  For example, `*.html`
    matches any file or directory with the `.html` extension anywhere in the
    package.
  - `?` matches any character except `/`. For example, `foo?` matches `food`,
    but not `foo`.
  - `[]` allows for matching a range of characters. For example, `[ab]`
    matches either `a` or `b`. `[a-z]` matches letters a through z.
- `**/` prefix matches in any directory. For example, `**/foo/bar` matches the
  file or directory `bar` anywhere that is directly under directory `foo`.
- `/**` suffix matches everything inside. For example, `foo/**` matches all
  files inside directory `foo`, including all files in subdirectories below
  `foo`.
- `/**/` matches zero or more directories. For example, `a/**/b` matches
  `a/b`, `a/x/b`, `a/x/y/b`, and so on.
- `!` prefix negates a pattern. For example, a pattern of `src/**.rs` and
  `!foo.rs` would match all files with the `.rs` extension inside the `src`
  directory, except for any file named `foo.rs`.

If git is being used for a package, the `exclude` field will be seeded with
the `gitignore` settings from the repository.

```toml
[package]
# ...
exclude = ["build/**/*.o", "doc/**/*.html"]
```

```toml
[package]
# ...
include = ["src/**/*", "Cargo.toml"]
```

The options are mutually exclusive: setting `include` will override an
`exclude`. Note that `include` must be an exhaustive list of files as otherwise
necessary source files may not be included. The package's `Cargo.toml` is
automatically included.

The include/exclude list is also used for change tracking in some situations.
For targets built with `rustdoc`, it is used to determine the list of files to
track to determine if the target should be rebuilt. If the package has a
[build script] that does not emit any `rerun-if-*` directives, then the
include/exclude list is used for tracking if the build script should be re-run
if any of those files change.

[gitignore]: https://git-scm.com/docs/gitignore

<a id="the-publish--field-optional"></a>
#### The `publish` field

The `publish` field can be used to prevent a package from being published to a
package registry (like *crates.io*) by mistake, for instance to keep a package
private in a company.

```toml
[package]
# ...
publish = false
```

The value may also be an array of strings which are registry names that are
allowed to be published to.

```toml
[package]
# ...
publish = ["some-registry-name"]
```

If publish array contains a single registry, `cargo publish` command will use
it when `--registry` flag is not specified.

<a id="the-metadata-table-optional"></a>
#### The `metadata` table

Cargo by default will warn about unused keys in `Cargo.toml` to assist in
detecting typos and such. The `package.metadata` table, however, is completely
ignored by Cargo and will not be warned about. This section can be used for
tools which would like to store package configuration in `Cargo.toml`. For
example:

```toml
[package]
name = "..."
# ...

# Metadata used when generating an Android APK, for example.
[package.metadata.android]
package-name = "my-awesome-android-app"
assets = "path/to/static"
```

There is a similar table at the workspace level at
[`workspace.metadata`][workspace-metadata]. While cargo does not specify a
format for the content of either of these tables, it is suggested that
external tools may wish to use them in a consistent fashion, such as referring
to the data in `workspace.metadata` if data is missing from `package.metadata`,
if that makes sense for the tool in question.

[workspace-metadata]: workspaces.md#the-workspacemetadata-table

#### The `default-run` field

The `default-run` field in the `[package]` section of the manifest can be used
to specify a default binary picked by [`cargo run`]. For example, when there is
both `src/bin/a.rs` and `src/bin/b.rs`:

```toml
[package]
default-run = "a"
```

### The `[badges]` section

[crates.io] can display various badges for build status, test coverage, etc. for
each crate. All badges are optional.

- The badges pertaining to build status that are currently available are
  Appveyor, CircleCI, Cirrus CI, GitLab, Azure DevOps, Travis CI and Bitbucket
  Pipelines.
- Available badges pertaining to code test coverage are Codecov and Coveralls.
- There are also maintenance-related badges based on isitmaintained.com
  which state the issue resolution time, percent of open issues, and future
  maintenance intentions.

Most badge specifications require a `repository` key. It is expected to be in
`user/repo` format.

```toml
[badges]

# Appveyor: `repository` is required. `branch` is optional; default is `master`
# `service` is optional; valid values are `github` (default), `bitbucket`, and
# `gitlab`; `id` is optional; you can specify the appveyor project id if you
# want to use that instead. `project_name` is optional; use when the repository
# name differs from the appveyor project name.
appveyor = { repository = "...", branch = "master", service = "github" }

# Circle CI: `repository` is required. `branch` is optional; default is `master`
circle-ci = { repository = "...", branch = "master" }

# Cirrus CI: `repository` is required. `branch` is optional; default is `master`
cirrus-ci = { repository = "...", branch = "master" }

# GitLab: `repository` is required. `branch` is optional; default is `master`
gitlab = { repository = "...", branch = "master" }

# Azure DevOps: `project` is required. `pipeline` is required. `build` is optional; default is `1`
# Note: project = `organization/project`, pipeline = `name_of_pipeline`, build = `definitionId`
azure-devops = { project = "...", pipeline = "...", build="2" }

# Travis CI: `repository` in format "<user>/<project>" is required.
# `branch` is optional; default is `master`
travis-ci = { repository = "...", branch = "master" }

# Bitbucket Pipelines: `repository` is required. `branch` is required
bitbucket-pipelines = { repository = "...", branch = "master" }

# Codecov: `repository` is required. `branch` is optional; default is `master`
# `service` is optional; valid values are `github` (default), `bitbucket`, and
# `gitlab`.
codecov = { repository = "...", branch = "master", service = "github" }

# Coveralls: `repository` is required. `branch` is optional; default is `master`
# `service` is optional; valid values are `github` (default) and `bitbucket`.
coveralls = { repository = "...", branch = "master", service = "github" }

# Is it maintained resolution time: `repository` is required.
is-it-maintained-issue-resolution = { repository = "..." }

# Is it maintained percentage of open issues: `repository` is required.
is-it-maintained-open-issues = { repository = "..." }

# Maintenance: `status` is required. Available options are:
# - `actively-developed`: New features are being added and bugs are being fixed.
# - `passively-maintained`: There are no plans for new features, but the maintainer intends to
#   respond to issues that get filed.
# - `as-is`: The crate is feature complete, the maintainer does not intend to continue working on
#   it or providing support, but it works for the purposes it was designed for.
# - `experimental`: The author wants to share it with the community but is not intending to meet
#   anyone's particular use case.
# - `looking-for-maintainer`: The current maintainer would like to transfer the crate to someone
#   else.
# - `deprecated`: The maintainer does not recommend using this crate (the description of the crate
#   can describe why, there could be a better solution available or there could be problems with
#   the crate that the author does not want to fix).
# - `none`: Displays no badge on crates.io, since the maintainer has not chosen to specify
#   their intentions, potential crate users will need to investigate on their own.
maintenance = { status = "..." }
```

### Dependency sections

See the [specifying dependencies page](specifying-dependencies.md) for
information on the `[dependencies]`, `[dev-dependencies]`,
`[build-dependencies]`, and target-specific `[target.*.dependencies]` sections.

### The `[profile.*]` sections

The `[profile]` tables provide a way to customize compiler settings such as
optimizations and debug settings. See [the Profiles chapter](profiles.md) for
more detail.



[`cargo init`]: ../commands/cargo-init.md
[`cargo new`]: ../commands/cargo-new.md
[`cargo run`]: ../commands/cargo-run.md
[crates.io]: https://crates.io/
[docs.rs]: https://docs.rs/
[publishing]: publishing.md
[Rust Edition]: ../../edition-guide/index.html
[spdx-2.1-license-expressions]: https://spdx.org/spdx-specification-21-web-version#h.jxpfx0ykyb60
[spdx-license-list-3.6]: https://github.com/spdx/license-list-data/tree/v3.6
[SPDX site]: https://spdx.org/license-list

<script>
(function() {
    var fragments = {
        "#the-project-layout": "../guide/project-layout.html",
        "#examples": "cargo-targets.html#examples",
        "#tests": "cargo-targets.html#tests",
        "#integration-tests": "cargo-targets.html#integration-tests",
        "#configuring-a-target": "cargo-targets.html#configuring-a-target",
        "#target-auto-discovery": "cargo-targets.html#target-auto-discovery",
        "#the-required-features-field-optional": "cargo-targets.html#the-required-features-field",
        "#building-dynamic-or-static-libraries": "cargo-targets.html#the-crate-type-field",
        "#the-workspace-section": "workspaces.html#the-workspace-section",
        "#virtual-manifest": "workspaces.html",
        "#package-selection": "workspaces.html#package-selection",
        "#the-features-section": "features.html#the-features-section",
        "#rules": "features.html#rules",
        "#usage-in-end-products": "features.html#usage-in-end-products",
        "#usage-in-packages": "features.html#usage-in-packages",
        "#the-patch-section": "overriding-dependencies.html#the-patch-section",
        "#using-patch-with-multiple-versions": "overriding-dependencies.html#using-patch-with-multiple-versions",
        "#the-replace-section": "overriding-dependencies.html#the-replace-section",
    };
    var target = fragments[window.location.hash];
    if (target) {
        var url = window.location.toString();
        var base = url.substring(0, url.lastIndexOf('/'));
        window.location.replace(base + "/" + target);
    }
})();
</script>
