<img src="./.github/banner.svg" alt="status" width="100%">

<p align="center">The static file that tells EO websites which services have an incident.</p>

<p align="center">
  <a href="https://evangelischeomroep.github.io/status/status.json">Production</a>
</p>

`status.json` lists the current incidents and announcements, each tagged with the services it affects. GitHub Pages serves it, apart from the EO's own infrastructure, so a website can still warn its visitors when the backend is down. The EO's Next.js websites read it every minute and show a banner on the pages that use an affected service.

## What it does

- **A banner per service.** Each message names the services it affects, so a checkout page shows only checkout problems.
- **Messages that expire.** A message with an expiry time disappears by itself.
- **Reachable during an outage.** The file does not depend on any EO system, so it keeps working when they fail.

## Getting started

```bash
curl https://evangelischeomroep.github.io/status/status.json
```

To post or clear a message, change `status.json` on `main`. GitHub Pages publishes it, and websites pick it up within a minute.

## Format

The file is an array of messages; `[]` means no messages.

```json
[
  {
    "version": "2025-12-01T01:00:00Z",
    "status": "warning",
    "message": "Mededeling: Door een storing is onze website minder goed bereikbaar.",
    "affectedServices": ["content"],
    "expiresAt": "2025-12-02T18:00:00Z"
  }
]
```

- `version` (string, ISO 8601 timestamp): the time or version of this status. Use UTC, for example `2025-12-01T10:30:00Z`.
- `status` (string): `info` is informational, `warning` is limited degradation, `error` is broken functionality. Set it consistently.
- `message` (string): a short, human-readable explanation shown in the UI. Keep it short and actionable: what is unavailable, and what can the visitor expect?
- `affectedServices` (array of strings): one or more of the categories below.
- `expiresAt` (string, ISO 8601 timestamp, optional): when the message expires. Clients can check again at that moment or dismiss it automatically.

Use UTC ISO 8601 timestamps for `version` and `expiresAt`, so clients can compare them easily.

### Categories

- `checkout`: payment and order flows, including payment providers. Use it for issues that affect the financial flow.
- `contact`: handling calls, for example contact forms that create a call in the CRM, or capacity problems at customer service.
- `content`: rendering or CMS-related assets (pages, media, video). Use it when the site or UI is slow or static content fails.
- `auth`: login and accounts (the auth provider).
- `interaction`: chat, notifications and other real-time communication.
- `media`: streaming, the video player or broadcast-specific services that stand apart from the general content experience.

A message can name several categories, for example `["contact","checkout"]` when a CRM issue also affects checkout.

## Contributing

Working on this repo? [AGENTS.md](./AGENTS.md) has the setup, conventions and checks.
