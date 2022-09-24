Here you can read about the best practices you can use in your GHA workflow. And don't forget to contribute yours!

---

# Too Long, need a shortcut!

### Good examples of GHA workflows that are using the best practices and you can copypaste them

(don't forget to update the actions' versions though)

#### Rust projects

https://github.com/paritytech/jsonrpsee/blob/b2553e0e6b8b435d1fb335d7d86043862a9f74a0/.github/workflows/ci.yml

---

# Best practices

## Pipelines optimization

### cancel workflow action

#### Description

[repo](https://github.com/styfle/cancel-workflow-action)

Main features:

Cancels any previous runs that are not `completed` for a given workflow.

- helpful i.e. if you commit more often than workflow executes
- prevents hitting a [maximum concurrent macOS jobs](https://docs.github.com/en/actions/learn-github-actions/usage-limits-billing-and-administration#usage-limits) or simply wasting time
- [cancels](https://github.com/styfle/cancel-workflow-action#advanced-pull-requests-from-forks) workflows on the PRs created from forks

#### [Usage example](https://github.com/paritytech/parity-bridges-ui/blob/80fcb34605d1d8cf9a99a52355d1a44246b0bede/.github/workflows/js.yml#L41-L44)

(don't forget to update the actions' versions)

### cargo nextest

#### Description

[doc](https://nexte.st)

[repo](https://github.com/nextest-rs/nextest)

Is a better alternative to `cargo test`.

Main features:

- can run tests in parallel, `cargo test` runs them consecutively
- can divide tests into several CI jobs. This is useful when your tests take too much time and not much if your compile time is also long (see anti-features), the tradeoff should be solved with proper caching.
- can re-run and point to flaky tests

Anti-features:

- [doesn't support](https://github.com/nextest-rs/nextest/issues/16) `doctest`, make sure you run them in a separate CI job (it won't be a long job as doctests do not usually require building much)
- [doesn't support](https://github.com/nextest-rs/nextest/issues/44) `--no-run`. This means you can't compile a test binary in one job and pass the results to the next jobs.
- [issue](https://github.com/nextest-rs/nextest/issues/39) with `trybuild`
- [may leak pipes](https://github.com/nextest-rs/nextest/issues/15)

#### [Usage example](https://github.com/paritytech/jsonrpsee/blob/b2553e0e6b8b435d1fb335d7d86043862a9f74a0/.github/workflows/ci.yml#L86-L96)

(don't forget to update the actions' versions)

### cargo-install action

[Marketplace](https://github.com/marketplace/actions/cargo-install)

#### Description

Caches your `cargo install` between runs saving time on compiling it over and over.

#### [Usage example](https://github.com/paritytech/jsonrpsee/blob/b2553e0e6b8b435d1fb335d7d86043862a9f74a0/.github/workflows/ci.yml#L86-L90)

(don't forget to update the actions' versions)

### Swatinem/rust-cache

[Repo](https://github.com/Swatinem/rust-cache)

#### Description

Much easier and more effective for Rust than a regular cache action

#### Usage examples

[default](https://github.com/paritytech/jsonrpsee/blob/b2553e0e6b8b435d1fb335d7d86043862a9f74a0/.github/workflows/ci.yml#L92-L93)

[when Rust code is not in root dir](https://github.com/paritytech/parity-signer/blob/33f6945681e4f7928fdc90ef84c61f25d449d065/.github/workflows/rust-test-android.yml#L45-L48)

(don't forget to update the actions' versions)

## User-doc and rustdoc hosted on GitHub Pages

#### Description

- relative links from user-docs to rustdocs are tested by `mdbook`
- cached
- `gh-pages` branch is orphaned and minimalistic (otherwise it tends to take up a lot of space)

Tree

```bash
./index.html                        # user-docs
./rustdocs/[crate-name]/index.html  # rustdocs
```

The idea is to generate rustdocs in the same workflow so they can be passed to `mdbook`. Which, in turn, can test the relative links from user-docs to rust-docs. Then both are published to the GitHub pages. This is achieved by `mv ./target/doc/ ../docs/src/rustdocs/` moving the rustdoc to the `src` dir in the `mdbook` structure. The contents of this dir are [getting into](https://rust-lang.github.io/mdBook/guide/creating.html#source-files) the generated book.

In order to cache this workflow you should not remove everything (as it was popular previously) from the workspace. Thanks to `peaceiris/actions-gh-pages` you can choose `publish_dir:` (i.e. `./docs/book/html` for `mdbook` or `./target/doc/` for rustdocs) that only gets pushed to `gh-pages`, hence published. This also keeps the `gh-pages` branch lightweight. Also note `force_orphan:` option, so you can keep only latest version in the `gh-pages` branch (it tends to be very fat otherwise).

If you want to host multiple versions of your docs (i.e. on tags), you can publish them to the [variable dirs](https://github.com/peaceiris/actions-gh-pages/issues/324#issuecomment-665441440).

#### Examples

https://github.com/paritytech/parity-signer/blob/fcea43ec26bda3c9c2e1f9a4fb1074a3b03b04f4/.github/workflows/docs.yml

## GitHub Actions Usage Policies

Moved [here](https://github.com/paritytech/ci_cd/wiki/Policies-and-regulations:-GitHub-Actions-usage-policies)
