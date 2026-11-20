# Changelog

Every release of apogee-testing, newest first. Each is a `vX.Y.Z` tag with a GitHub
release carrying its section; the `grav` line in `grav.toml` says which
compiler versions it builds with.

## Unreleased

The first release. Needs grav 1.10.0 or newer.

- `TestClient`: requests into an HTTP kernel in the same process, with form
  and JSON bodies, headers, tokens, cookies kept between requests, redirects
  followed, and requests made as a signed-in user.
- `TestResponse`: status, body, header, cookie, JSON and validation
  assertions that chain, failing with `AssertionFailed`.
