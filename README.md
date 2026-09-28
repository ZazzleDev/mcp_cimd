# mcp_cimd — Client ID Metadata Document for the Zazzle API live tests

`mcp-live-test.json` is the OAuth Client ID Metadata Document (MCP authorization spec 2025-11-25,
draft-ietf-oauth-client-id-metadata-document) that the ApiLib2 contract test
`McpEndpointTests.ClientIdMetadata_UrlClientId_IsAcceptedByAuthorizeAndTokenGet_WhenAdvertised` presents
as its `client_id` (api_plan §11.252). It must be served from a host that is **not** a Zazzle domain: the
URL is the client's identity on the consent page, and a Zazzle host is refused as a third party's identity.

## Publish (GitHub Pages)

1. Push this folder to the `zazzle/mcp_cimd` repository and enable Pages on the default branch (root).
2. The document is then at `https://zazzle.github.io/mcp_cimd/mcp-live-test.json`. **`client_id` inside the
   file must equal that URL byte for byte** — if the repository or path differs, edit the value first.
3. GitHub Pages serves `.json` as `application/json` with `Cache-Control: max-age=600`; Zazzle clamps that
   to its 1-hour minimum, so an edit is picked up within an hour (or at once via the CS tool's
   "Refetch on next use").

## Use in the live suite

- `ApiLibTests/Endpoints/env/<env>.local.env`: `ZAPI_CIMD_CLIENT_ID=https://zazzle.github.io/mcp_cimd/mcp-live-test.json`
  and `ZAPI_CIMD_REDIRECT_URI=http://127.0.0.1:6274/oauth/callback` (one of the `redirect_uris`).
- The target host must have DBConfig `ApiOAuth/ClientIdMetadataEnabled = true` (seeded false everywhere —
  flip it on dev/QA) and `zazzle.github.io` on `ApiOAuth/ClientIdMetadataTrustedHosts` (dev/QA seed) or
  `ClientIdMetadataAutoApprove = true`, otherwise the client is created pending and the test is Inconclusive.
- Run: `Endpoints/run-endpoint-tests.ps1 -Env dev -Filter "FullyQualifiedName~ClientIdMetadata"`.

Only loopback redirect URIs are listed on purpose: the strict rule requires every non-loopback redirect to
be on the document's own host, and nothing runs on `zazzle.github.io`.
