# Domain Intelligence Scraper

Bulk domain audit — DNS records, WHOIS, SSL certificates, HTTP status, email authentication (SPF/DMARC/DKIM) and blacklist checks, with a 0-100 security score and change monitoring.

**[Domain Intelligence Scraper on Apify →](https://apify.com/blackfalcondata/domain-intelligence-scraper?fpr=1h3gvi)**

---

## 🚀 How to use this actor

> ### 💚 $5 free Apify credits — every month
> No credit card required. No commitment. Cancel anytime.

### 👉 [Sign up free on Apify →](https://console.apify.com/sign-up?fpr=1h3gvi)

1. **Click sign up** — pick GitHub, Google, or email; takes ~30 seconds
2. **Open this actor** — input is pre-filled with a working example
3. **Click Start** — export results as JSON, CSV, or Excel

Your **$5 monthly platform credit** is enough to run this actor right away — and again every month — scraping typically several hundred to several thousand results per run, depending on your input.

## Key features

**Incremental mode** — Only get new or changed listings since your last run. Content hash per listing — no duplicates, no re-processing.

**Change classification** — Track unchanged across runs. Build audit trails of how listings evolve over time.

**Compact output** — Emit core fields only (AI-agent / MCP-friendly). Keeps response size small for LLM workflows.

**Export anywhere** — Download as JSON, CSV, or Excel. Stream via Apify API, webhooks, or integrations with Make, Zapier, Airbyte, Keboola.

**Structured data** — Every listing returns the same schema with consistent field naming. All fields always present — `null` when unavailable, never omitted.

---

## Use cases

**Data pipeline automation**
Integrate with your ETL pipeline to collect structured listings from console.apify.com on a schedule. Export to CSV, JSON, or directly to your database. Use compact mode to control output size.

**Market research**
Monitor listings, track trends, and analyze market dynamics with structured, deduplicated data from console.apify.com.

**Change monitoring**
Run daily or hourly in incremental mode to capture only new, updated, or expired listings. Perfect for price-tracking, churn analysis, and alerting pipelines.

**AI / LLM training data**
Structured JSON per listing is ready for RAG pipelines, embeddings, and agent workflows. Compact mode trims tokens for LLM context windows.

---

## Quick start

```json
{
  "domains": [
    "example.com",
    "github.com"
  ],
  "checks": [
    "dns",
    "whois",
    "ssl",
    "http",
    "emailAuth",
    "blacklist"
  ]
}
```

---

## Input parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `domains` | array | — | Domains or full URLs to inspect — one per line, or paste a JSON array. Full URLs are auto-cleaned to the apex domain (https://www.example.com/path → example.com). |
| `checks` | array | `["dns","whois","ssl","http","emailAuth","blacklist"]` | Which inspections to run per domain. Leave all selected for a full audit; deselect to run faster / cheaper on just what you need. |
| `recordTypes` | array | `["A","AAAA","MX","TXT","CNAME","NS","SOA","CAA"]` | Which DNS record types to resolve when the DNS check is enabled. |
| `dkimSelectors` | array | `[]` | DKIM cannot be enumerated, so common selectors (default, google, selector1/2, k1…) are probed automatically. Add your ESP's selector here to catch custom setups. |
| `timeoutMs` | integer | `8000` | Timeout for each individual DNS/WHOIS/SSL/HTTP lookup. |
| `compact` | boolean | `false` | At-a-glance verdict only (domain, security score, email grade, flags) — drops the raw record blocks. Ideal for AI-agent / MCP workflows. |
| `excludeEmptyFields` | boolean | `false` | Drop null, empty-string, and empty-array fields from each record before push. Smaller payloads for AI agents and dashboards. |
| `incrementalMode` | boolean | `false` | Compare each domain against the previous run and flag changes (NEW / UPDATED / UNCHANGED). Detects cert renewals, WHOIS expiry, DNS changes, email-auth drift and blacklist appearances over time. stateKey is optional — defaults to a stable key derived from the domain list + checks. |
| `stateKey` | string | — | Optional. Stable identifier for the monitored domain set. Leave empty to auto-generate from the domain list + checks. |
| `emitUnchanged` | boolean | `false` | When monitoring, also output domains whose signals did not change since the last run. Off = only NEW and UPDATED domains are emitted. |
| `telegramToken` | string | — | Telegram bot token (from @BotFather). Required for Telegram notifications. |
| `telegramChatId` | string | — | Telegram chat or channel ID (e.g. "-100123456789"). Required when telegramToken is set. |
| `discordWebhookUrl` | string | — | Discord incoming webhook URL. Server Settings → Integrations → Webhooks → New Webhook. |
| `slackWebhookUrl` | string | — | Slack incoming webhook URL. api.slack.com/messaging/webhooks. |
| `notificationLimit` | integer | `5` | Maximum number of domains included in each notification message (1–20). |
| `notifyOnlyChanges` | boolean | `false` | When monitoring is on, only send notifications for NEW and UPDATED domains. No effect otherwise. |
| `whatsappAccessToken` | string | — | WhatsApp Cloud API permanent access token (System User token from Meta Business). Recipient must have messaged the business number within the last 24h (service-conversation window — free since Nov 2024). |
| `whatsappPhoneNumberId` | string | — | Your WhatsApp Business phone-number ID (numeric, from Meta dashboard). Required when whatsappAccessToken is set. |
| `whatsappTo` | string | — | Recipient phone in E.164 format without + (e.g. "436641234567"). Recipient must have messaged your business number within last 24h. |
| `webhookUrl` | string | — | Receives a JSON POST with {metadata, items} after each run. Universal escape hatch for n8n / Make / Zapier / custom backends. |
| `webhookHeaders` | object | — | Optional JSON object of custom headers (e.g. {"Authorization":"Bearer ..."}). |
| `appConnector` | string | — | Optional. Pick a connected app under Settings → API & Integrations to receive your results. Best-effort across MCP connectors as Apify expands its catalog. |
| `mcpIssueTeam` | string | — | Only when the connected app is an issue tracker: the team (name or ID) the summary issue is created under, if that app requires one. |

---

## Output fields

Every listing returns the same 14-field schema. Missing values are `null` — never omitted.

- `domain`
- `inputUrl`
- `securityScore`
- `emailGrade`
- `flags`
- `dns`
- `whois`
- `ssl`
- `http`
- `emailAuth`
- `blacklist`
- `checkedAt`
- `source`
- `changeType`

---

## Sample output

One object per listing. Here is a real example from a production run:

```json
{
  "domain": "github.com",
  "inputUrl": "github.com",
  "securityScore": 96,
  "emailGrade": "A",
  "flags": [],
  "dns": {
    "records": {
      "A": [
        "140.82.121.4"
      ],
      "NS": [
        "dns4.p08.nsone.net",
        "ns-421.awsdns-52.com",
        "ns-520.awsdns-01.net",
        "ns-1283.awsdns-32.org",
        "ns-1707.awsdns-21.co.uk",
        "dns1.p08.nsone.net",
        "dns2.p08.nsone.net",
        "dns3.p08.nsone.net"
      ],
      "SOA": [
        {
          "nsname": "dns1.p08.nsone.net",
          "hostmaster": "hostmaster.nsone.net",
          "serial": 1656468023,
          "refresh": 43200,
          "retry": 7200,
          "expire": 1209600,
          "minttl": 3600
        }
      ],
      "MX": [
        {
          "priority": 0,
          "exchange": "github-com.mail.protection.outlook.com"
        }
      ],
      "CAA": [
        {
          "critical": 0,
          "issue": "digicert.com"
        },
        {
          "critical": 0,
          "issue": "globalsign.com"
        },
        {
          "critical": 0,
          "issue": "letsencrypt.org"
        },
        {
          "critical": 0,
          "issue": "sectigo.com"
        },
        {
          "critical": 0,
          "issuewild": "digicert.com"
        },
        {
          "critical": 0,
          "issuewild": "letsencrypt.org"
        },
        {
          "critical": 0,
          "issuewild": "sectigo.com"
        }
      ],
      "TXT": [
        "shopify-verification-code=t1YPwcmvnxZyBycaCpk1MPyWoFs72o",
        "krisp-domain-verification=ZlyiK7XLhnaoUQb2hpak1PLY7dFkl1WE",
        "miro-verification=d2e174fdb00c71e0bcf58f8e58c3da2dd80dcfa9",
        "facebook-domain-verification=39xu4jzl7roi7x0n93ldkxjiaarx50",
        "serval-domain-verification-ydryhj=qbkiEakpwEpTvHh5fIiCqtaue",
        "anthropic-domain-verification-4az7qn=if8YWuRRqwLycGJDooumzHtxm",
        "calendly-site-verification=at0DQARi7IZvJtXQAWhMqpmIzpvoBNF7aam5VKKxP",
        "google-site-verification=82Le34Flgtd15ojYhHlGF_6g72muSjamlMVThBOJpks",
        "google-site-verification=UTM-3akMgubp6tQtgEuAkYNYLyYAvpTnnSrDMWoDR3o",
        "stripe-verification=f88ef17321660a01bab1660454192e014defa29ba7b8de9633c69d6b4912217f",
        "adobe-idp-site-verification=b92c9e999aef825edc36e0a3d847d2dbad5b2fc0e05c79ddd7a16139b48ecf4b",
        "atlassian-domain-verification=jjgw98AKv2aeoYFxiL/VFaoyPkn3undEssTRuMg6C/3Fp/iqhkV4HVV7WjYlVeF8",
        "v=spf1 ip4:192.30.252.0/22 include:spf.protection.outlook.com include:_netblocks.google.com include:_netblocks2.google.com include:mail.zendesk.com include:_spf.salesforce.com include:servers.mcsv.net include:mktomail.com include:sendgrid.net ip4:62.253.227.114 ip4:166.78.69.169 ip4:166.78.69.170 ip4:166.78.71.131 ~all",
        "MS=ms44452932",
        "MS=ms58704441",
        "TAILSCALE-xOzoDvFUzZr5YYVCQFuD",
        "00Dd0000000hHE0=1TBKg000000TN2r",
        "apple-domain-verification=RyQhdzTl6Z6x8ZP4",
        "MS=6BF03E6AF5CB689E315FB6199603BABF2C88D805",
        "docusign=087098e3-3d46-47b7-9b4e-8a23028154cd",
        "jamf-site-verification=XtaPNIYghF_e_xRDI8CjgQ",
        "loom-site-verification=f3787154f1154b7880e720a511ea664d"
      ]
    },
    "dnssec": false,
    "errors": {
      "AAAA": "ENODATA",
      "CNAME": "ENODATA"
    }
  },
  "whois": {
    "raw": "Domain Name: github.com\nRegistry Domain ID: 1264983250_DOMAIN_COM-VRSN\nRegistrar WHOIS Server: whois.markmonitor.com\nRegistrar URL: http://www.markmonitor.com\nUpdated Date: 2024-09-07T09:16:33+0000\nCreation Date: 2007-10-09T18:20:50+0000\nRegistrar Registration Expiration Date: 2026-10-09T00:00:00+0000\nRegistrar: MarkMonitor, Inc.\nRegistrar IANA ID: 292\nRegistrar Abuse Contact: https://corp.markmonitor.com/domain/ui/abuse-report\nRegistrar Abuse Contact Phone: +1.2086851750\nDomain Status: clientUpdateProhibited (https://www.icann.org/epp#clientUpdateProhibited)\nDomain Status: clientTransferProhibited (https://www.icann.org/epp#clientTransferProhibited)\nDomain Status: clientDeleteProhibited (https://www.icann.org/epp#clientDeleteProhibited)\nRegistrant Organization: GitHub, Inc.\nRegistrant Country: US\nRegistrant Email: Select Request Email Form at https://domains.markmonitor.com/whois/github.com\nTech Email: Select Request Email Form at https://domains.markmonitor.com/whois/github.com\nName Server: dns3.p08.nsone.net\nName Server: ns-520.awsdns-01.net\nName Server: ns-1707.awsdns-21.co.uk\nName Server: ns-1283.awsdns-32.org\nName Server: dns4.p08.nsone.net\nName Server: dns1.p08.nsone.net\nName Server: dns2.p08.nsone.net\nName Server: ns-421.awsdns-52.com\nDNSSEC: unsigned\nURL of the ICANN WHOIS Data Problem Reporting System: http://wdprs.internic.net/\n>>> Last update of WHOIS database: 2026-07-18T10:33:50+0000 <<<\n\nFor more information on WHOIS status codes, please visit:\n  https://www.icann.org/resources/pages/epp-status-codes\n\nIf you wish to contact this domain’s Registrant or Technical\ncontact, and such email address is not visible above, you may do so via our web\nform, pursuant to ICANN’s Temporary Specification. To verify that you are not a\nrobot, please enter your email address to receive a link to a page that\nfacilitates email communication with the relevant contact(s).\n\nWeb-based WHOIS:\n  https://domains.markmonitor.com/whois/contact/github.com\n\nIf you have a legitimate interest in viewing the non-public WHOIS details, send\nyour request and the reasons for your request to whoisrequest@markmonitor.com\nand specify the domain name in the subject line. We will review that request and\nmay ask for supporting documentation and explanation.\n\nThe data in MarkMonitor’s WHOIS database is provided for information purposes,\nand to assist persons in obtaining information about or related to a domain\nname’s registration record. While MarkMonitor believes the data to be accurate,\nthe data is provided \"as is\" with no guarantee or warranties regarding its\naccuracy.\n\nBy submitting a WHOIS query, you agree that you will use this data only for\nlawful purposes and that, under no circumstances will you use this data to:\n  (1) allow, enable, or otherwise support the transmission by email, telephone,\nor facsimile of mass, unsolicited, commercial advertising, or spam; or\n  (2) enable high volume, automated, or electronic processes that send queries,\ndata, or email to MarkMonitor (or its systems) or the domain name contacts (or\nits systems).\n\nMarkMonitor reserves the right to modify these terms at any time.\n\nBy submitting this query, you agree to abide by this policy.\n\nMarkMonitor Domain Management(TM)\nProtecting companies and consumers in a digital world.\n\nVisit MarkMonitor at https://www.markmonitor.com\nContact us at +1.8007459229\nIn Europe, at +44.02032062220\n--\n",
    "server": "whois.markmonitor.com",
    "registrar": "MarkMonitor, Inc.",
    "createdDate": "2007-10-09T18:20:50+0000",
    "updatedDate": "2024-09-07T09:16:33+0000",
    "expiryDate": "2026-10-09T00:00:00+0000",
    "nameServers": [
      "dns3.p08.nsone.net",
      "ns-520.awsdns-01.net",
      "ns-1707.awsdns-21.co.uk",
      "ns-1283.awsdns-32.org",
      "dns4.p08.nsone.net",
      "dns1.p08.nsone.net",
      "dns2.p08.nsone.net",
      "ns-421.awsdns-52.com"
    ],
    "status": [
      "clientUpdateProhibited (https://www.icann.org/epp#clientUpdateProhibited)",
      "clientTransferProhibited (https://www.icann.org/epp#clientTransferProhibited)",
      "clientDeleteProhibited (https://www.icann.org/epp#clientDeleteProhibited)"
    ],
    "registrantEmails": [
      "whoisrequest@markmonitor.com"
    ],
    "daysToExpiry": 83
  },
  "ssl": {
    "ok": true,
    "error": null,
    "subject": "github.com",
    "issuer": "Sectigo Limited",
    "altNames": [
      "github.com",
      "www.github.com"
    ],
    "validFrom": "Jul  3 00:00:00 2026 GMT",
    "validTo": "Sep 30 23:59:59 2026 GMT",
    "daysToExpiry": 75,
    "expired": false,
    "certSha256": "17:F8:FD:2E:3F:D2:C1:13:FC:B9:77:2D:8A:4B:AB:B8:52:2D:D0:6D:D0:79:49:15:A4:FF:98:B1:B6:86:3A:00",
    "protocol": "TLSv1.3"
  },
  "http": {
    "http": {
      "status": 200,
      "finalUrl": "https://github.com/",
      "redirected": true,
      "server": "github.com",
      "error": null
    },
    "https": {
      "status": 200,
      "finalUrl": "https://github.com/",
      "redirected": false,
      "server": "github.com",
      "error": null
    },
    "upgradesToHttps": true
  },
  "emailAuth": {
    "hasMx": true,
    "spf": {
      "found": true,
      "record": "v=spf1 ip4:192.30.252.0/22 include:spf.protection.outlook.com include:_netblocks.google.com include:_netblocks2.google.com include:mail.zendesk.com include:_spf.salesforce.com include:servers.mcsv.net include:mktomail.com include:sendgrid.net ip4:62.253.227.114 ip4:166.78.69.169 ip4:166.78.69.170 ip4:166.78.71.131 ~all",
      "all": "~all",
      "includes": [
        "spf.protection.outlook.com",
        "_netblocks.google.com",
        "_netblocks2.google.com",
        "mail.zendesk.com",
        "_spf.salesforce.com",
        "servers.mcsv.net",
        "mktomail.com",
        "sendgrid.net"
      ],
      "verdict": "pass"
    },
    "dmarc": {
      "found": true,
      "record": "v=DMARC1; p=quarantine; sp=reject; pct=100; rua=mailto:dmarc@github.com; ruf=mailto:dmarc@github.com; fo=1",
      "policy": "quarantine",
      "pct": 100,
      "rua": [
        "mailto:dmarc@github.com"
      ],
      "verdict": "pass"
    },
    "dkim": {
      "selectorsChecked": [
        "default",
        "google",
        "selector1",
        "selector2",
        "k1",
        "s1",
        "s2",
        "dkim",
        "mail",
        "mandrill",
        "k2",
        "zoho",
        "mxvault"
      ],
      "found": [
        "google",
        "selector1",
        "k2"
      ],
      "verdict": "pass"
    },
    "dnssec": false,
    "score": 90,
    "grade": "A"
  },
  "blacklist": {
    "ip": "140.82.121.4",
    "listedOn": [],
    "checked": [
      "zen.spamhaus.org",
      "b.barracudacentral.org",
      "dnsbl.sorbs.net",
      "dbl.spamhaus.org"
    ],
    "note": "best-effort; cloud resolvers may be refused by some zones"
  },
  "checkedAt": "2026-07-18T10:38:24.412Z"
}
```

*Truncated — full records contain 14 fields. See Output fields for the complete schema.*

**[Try Domain Intelligence Scraper now — $5 free credit, no credit card →](https://apify.com/blackfalcondata/domain-intelligence-scraper?fpr=1h3gvi)**

---

## Pricing

Pay only for what you extract. No subscription required — Apify's free $5 credit covers thousands of results.

| Event | Price (USD) |
| --- | --- |
| Actor Start | $0.00005 |
| Result | $0.001 |

See the [actor on Apify](https://apify.com/blackfalcondata/domain-intelligence-scraper?fpr=1h3gvi) for current pricing.

---

## FAQ

**How do I scrape console.apify.com?**
Use this actor on Apify to extract structured data from console.apify.com. Configure your search query and filters in the input, then click Start — no coding required.

**How do I get console.apify.com data as JSON, CSV, or Excel?**
The actor writes each listing to Apify's dataset. Download as JSON, CSV, or Excel from the Console, stream via the API, or push to Make, Zapier, Airbyte, or Keboola.

**Is it legal to scrape console.apify.com?**
Web scraping of publicly available data is generally legal. This actor only accesses publicly visible information. Always check console.apify.com's terms of service for your specific use case.

**How much does it cost?**
Pay-per-event pricing — you only pay for listings extracted. Apify's free $5 credit is enough to run thousands of results before you pay anything.

**How does incremental mode work?**
Each listing gets a content hash. On subsequent runs, only new or changed listings are emitted — saving time, compute, and storage. Expired listings can be tracked separately.

**Do I need an API key or credentials?**
No. Just sign up for Apify, paste your input, and click Start. No credit card required.

---

## Related products by Black Falcon Data

[Browse all Black Falcon Data actors →](https://apify.com/blackfalcondata?fpr=1h3gvi)

---

## Getting started with Apify

New to Apify? [Create a free account with $5 credit](https://console.apify.com/sign-up?fpr=1h3gvi) — no credit card required.

1. Sign up — $5 platform credit included
2. Open [Domain Intelligence Scraper](https://apify.com/blackfalcondata/domain-intelligence-scraper?fpr=1h3gvi) and configure your input
3. Click **Start** — export results as JSON, CSV, or Excel

Need more later? [See Apify pricing](https://apify.com/pricing?fpr=1h3gvi).

---

## About Black Falcon Data

Black Falcon Data builds production-grade web scrapers for job boards and marketplace data. Browse our full actor catalog at [www.blackfalcondata.com](https://www.blackfalcondata.com).

---

*Last updated: 2026 07*
