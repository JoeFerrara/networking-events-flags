# networking-events-flags

Public companion repo for the (private) NetworkingEvents app. Holds a single
`flags.json` document the app fetches at launch and on each foreground
transition to decide which features are exposed.

This repo is intentionally tiny: no source code, no secrets, just the flag
document. The values inside are not PII — `allowListSHA256` is a list of
SHA-256 hex digests of `UIDevice.identifierForVendor` UUIDs. The raw UUID
never leaves the device.

## Schema

```json
{
  "fullCardScanning": {
    "global": false,
    "allowListSHA256": ["<sha256-hex-digest>"]
  }
}
```

- `global`: when `true`, the feature is on for everyone regardless of allow-list.
- `allowListSHA256`: lowercase SHA-256 hex digests of the device's
  `identifierForVendor` UUID string (uppercase, dashed — the canonical
  `uuidString` form); devices whose hashed ID matches any entry get the
  feature even when `global` is `false`.

## Flags

- `fullCardScanning` — the experimental LinkedIn-card scanner: portrait
  viewfinder, OCR of name/headline, and an editable review sheet before the
  contact is saved. When **off** (the default), the scanner runs in QR-only
  mode: square viewfinder, the capture photo is saved, the URL lands on the
  contact, and the app jumps straight to the scanned link.

## Finding a device's hash

The app logs its own allow-list hash on every flag refresh (Console.app,
subsystem `com.joeferrara.NetworkingEvents`, category `featureFlags`). Copy
that digest into `allowListSHA256` to enable a flag for that device only.

## Editing

Push a commit to `main` with the desired changes. The app refetches on its
next launch / foreground transition (no app update required). When the
document is unreachable or malformed the app keeps the last fetched values
(cached on device) and otherwise defaults every flag to off.
