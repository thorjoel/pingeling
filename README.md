# Pingeling

A single-file DNS propagation monitor with CAA validation. Open `pingeling.html` in any browser — no installation required.

## Features

- **Dual resolver** — checks Cloudflare (1.1.1.1) and Google (8.8.8.8) simultaneously
- **CAA validation** — detects whether DigiCert (required by Azure App Service) is allowed to issue SSL certificates
- **TTL display** — shows time-to-live for each DNS response
- **Propagation timestamp** — records exactly when DNS matched on both resolvers
- **Check counter** — tracks how many polls have been made per domain
- **Desktop notifications + sound** — alerts you the moment DNS propagates
- **Export / Import** — save and reload your domain list as JSON
- **Persistent** — domains are remembered via localStorage between sessions

## Usage

1. Open `pingeling.html` in a browser — no server, no install
2. Enter a domain and optional expected value (IP address or CNAME target)
3. Select record type (A, AAAA, CNAME, MX, TXT)
4. Click **+ Legg til**
5. Pingeling polls automatically and alerts you when DNS propagates on both resolvers

## Export / Import

Click **⬇ Eksport** to download your current domain list as a `.json` file. Click **⬆ Import** to load it back. The format is simple and human-editable:

```json
{
  "pingeling": true,
  "version": 1,
  "monitors": [
    { "domain": "example.com", "expected": "20.100.3.9", "type": "A" },
    { "domain": "www.example.com", "expected": "myapp.azurewebsites.net", "type": "CNAME" },
    { "domain": "asuid.example.com", "expected": "AZURE_VERIFICATION_CODE", "type": "TXT" }
  ]
}
```

## Azure App Service

When activating a custom domain in Azure App Service, add these DNS records:

| Type  | Host       | Value                           |
|-------|------------|---------------------------------|
| A     | @          | Azure IP                        |
| CNAME | www        | `yourapp.azurewebsites.net`     |
| TXT   | asuid      | Azure domain verification code  |
| TXT   | asuid.www  | Azure domain verification code  |

Pingeling automatically checks for a **CAA record** and warns you if DigiCert is not listed — which would prevent Azure from issuing an SSL certificate.

## Tech

Pure HTML/CSS/JavaScript — no dependencies, no build step. DNS lookups use DNS-over-HTTPS (DoH) from Cloudflare and Google.

## License

MIT

---

## Changelog

### v1.4
- Export domain list to JSON file
- Import JSON file to quickly populate monitors (duplicates skipped)

### v1.3
- Renamed to **Pingeling** with custom animated SVG logo
- Renamed file to `pingeling.html`

### v1.2
- Added CAA record lookup per domain
- Azure/DigiCert validation — warns if DigiCert not listed in CAA records
- CAA checks root domain automatically if subdomain has no records
- CAA re-checks every 5 minutes

### v1.1
- Fixed notification permission dialog appearing on every page reload
- `done` state and propagation timestamp now saved to localStorage
- CNAME values no longer overflow resolver boxes

### v1.0
- Dual resolver: Cloudflare and Google checked simultaneously, side by side
- TTL displayed per resolver (highlighted when under 60 seconds)
- Propagation timestamp shown when DNS matches on both resolvers
- Check counter per domain
- localStorage persistence — domains survive page reload
- Three-tone audio alert on propagation

### v0.1 — Initial release
- Single-file DNS monitor (no dependencies)
- Poll a domain against a single DNS resolver
- Configurable poll interval (5 sec – 5 min)
- Desktop notification on propagation
- Basic activity log
