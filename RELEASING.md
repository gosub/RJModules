# Releasing

CI does the work. Pushing a tag named `v<version>` builds win-x64,
lin-x64, mac-x64 and mac-arm64, creates the GitHub release and attaches
one `.vcvplugin` per platform.

## The one rule

`plugin.json`'s `"version"` and the tag must match exactly, tag prefixed
with `v`: version `2.0.1` is tag `v2.0.1`. The publish job compares them
and fails the release if they differ, so a mismatch costs a re-tag rather
than a bad release.

## Steps

1. Check the build for the commit you intend to tag. Every push builds
   all four platforms, so a green run already exists.
2. Bump `"version"` in `plugin.json` and commit.
3. Tag and push:

   ```
   git tag v<version>
   git push && git push --tags
   ```

4. Check the release page once the run finishes.

Builds on a branch or an untagged push get `-<short sha>` appended to the
version so they are distinguishable from a real release; that rewrite
happens in CI only and never touches the committed `plugin.json`.

## Rack SDK version

Pinned as `rack-sdk-version` at the top of
`.github/workflows/build-plugin.yml`. The Linux and Windows jobs run in
the `rack-plugin-toolchain` container for its cross-compilers but
download the pinned SDK over whatever the image bundles, so bumping that
one value moves every platform at once.

## Local builds

```
RACK_DIR=/path/to/Rack-SDK make dist
```

`make dist` produces `dist/RJModules-<version>-<platform>.vcvplugin`,
the same artifact CI uploads.
