# Snipe-IT HTTP opt-in design

## Goal

Allow deployments whose Snipe-IT server uses plain HTTP to opt in through
`SNIPEIT_ALLOW_HTTP`, using the `allow_http` argument provided by the forked
`snipeit-python-api` client.

## Design

- Add a small environment resolver in `snipeit_mcp.client` that reads the
  variable on every client creation, matching the existing URL and token
  behavior.
- Treat `1`, `true`, `yes`, and `on` as true, case-insensitively and with
  surrounding whitespace ignored. Missing values and every other value are
  false.
- Pass the resolved boolean to
  `SnipeIT(url=url, token=creds, allow_http=allow_http)`.
- Leave `SnipeITDirectAPI` unchanged. It does not use the library constructor
  and already accepts the configured URL through `requests`.

The default remains secure: non-local plain HTTP is rejected unless the
environment variable explicitly contains a recognized true value.

## Testing

Add focused unit tests that mock `SnipeIT` and verify:

- the variable is absent or false-like: `allow_http=False`;
- each supported true spelling enables `allow_http=True`;
- the value is read for every client creation rather than at module import.

Run the focused client tests, followed by the full test suite.
