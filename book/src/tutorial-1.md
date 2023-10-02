# Add `bindgen` as a Build Dependency

First we need to declare a build-time dependency on `bindgen` by adding it to
the `[build-dependencies]` section of our crate's `Cargo.toml` file.

Please make sure that the dependency resolver always use the latest
version of `bindgen`, as it has the most fixes and best compatibility.
Unless you have a very good reason to do so, use only the
`<major>.<minor>` syntax.

```toml
[build-dependencies]
bindgen = "0.49"
```

Explicitly requesting a given patchlevel, however, will *prevent* cargo
from using a recent version.

> ⚠️ **Warning**
>
> Specifying `bindgen = "0.64.1"` will forbid the use of 0.65 and later


As a number of crates currently specify a patchlevel, those will force
app crates to use old versions of bindgen (whose dependencies can be
mutually exclusive), so you should consider specifying an old-enough
minimum version to avoid unnecessary conflicts (unless, obviously, if
your crate does require a recent `bindgen`).

> ⚠️ **Warning**
> As an illustration, take the following `Cargo.toml`, pinning
> packages to a specific point in time of September 2023:
>
> ```
> [package]
> name = "bindgen-conflict"
> version = "0.1.0"
> edition = "2021"
> 
> [dependencies]
> xenctrl-sys = "=0.1.1"    # bindgen = "^0.49.2"
> xenstore-sys = "=0.1.2"   # bindgen = "^0.68.1"
> ```
>
> This results in an unbuildable crate:
> ```
>     Updating crates.io index
> error: failed to select a version for `clang-sys`.
>     ... required by package `bindgen v0.68.1`
>     ... which satisfies dependency `bindgen = "^0.68.1"` of package `xenstore-sys v0.1.2`
>     ... which satisfies dependency `xenstore-sys = "=0.1.2"` of package `bingen-conflict v0.1.0 (/home/user/src/bingen-conflict)`
> versions that meet the requirements `^1` are: 1.6.1, 1.6.0, 1.4.0, 1.3.3, 1.3.2, 1.3.1, 1.3.0, 1.2.2, 1.2.1, 1.2.0, 1.1.1, 1.1.0, 1.0.3, 1.0.2, 1.0.1, 1.0.0
> 
> the package `clang-sys` links to the native library `clang`, but it conflicts with a previous package which links to `clang` as well:
> package `clang-sys v0.28.0`
>     ... which satisfies dependency `clang-sys = "^0.28.0"` of package `bindgen v0.49.2`
>     ... which satisfies dependency `bindgen = "^0.49.2"` of package `xenctrl-sys v0.1.1`
>     ... which satisfies dependency `xenctrl-sys = "=0.1.1"` of package `bingen-conflict v0.1.0 (/home/user/src/bingen-conflict)`
> Only one package in the dependency graph may specify the same links value. This helps ensure that only one copy of a native library is linked in the final binary. Try to adjust your dependencies so that only one package uses the links ='clang-sys' value. For more information, see https://doc.rust-lang.org/cargo/reference/resolver.html#links.
> 
> failed to select a version for `clang-sys` which could resolve this conflict
> ```

> ⚠️ **Warning**
>
> `bindgen` needs to be added to the `[build-dependencies]` section, not the normal
> `[dependencies]` section. If you add it as a regular dependency, you will get
> errors like the following: `` error[E0463]: can't find crate for `bindgen` ``
