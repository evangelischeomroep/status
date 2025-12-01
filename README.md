# Status JSON — uitleg en gebruik

Deze README beschrijft het `status.json`-bestand dat als statische fallback gebruikt kan worden voor Next.js- en andere webapplicaties.

**Doel**
- Een klein, statisch JSON-bestand hosten op een onafhankelijke CDN (GitHub Pages, Cloudflare Pages, S3+CloudFront) zodat clients bij het laden snel kunnen controleren of er een incident is en een banner/alert tonen — ook als backend-systemen  uitvallen.

**Schema (veld-uitleg)**
- `version` (string, ISO 8601 timestamp): tijdstempel of versie van deze status. Gebruik UTC (bv. `2025-12-01T10:30:00Z`).
- `status` (string): korte label, bijvoorbeeld `info`, `warning`, `error`.
- `message` (string): korte, menselijke leesbare verklaring die in de UI wordt getoond.
- `affectedServices` (array[string]): lijst met servicenaam(en) intern gebruikt (bv. `checkout`, `content`, `media`, `auth`, `interaction`).
- `expiresAt` (string, ISO 8601, optioneel): tijdstip waarop de melding verloopt — clients kunnen hierop opnieuw checken of auto-dismissen.

**Voorbeeld**
```
[
    {
        "version": "2024-11-18T10:30:00Z",
        "status": "warning",
        "message": "Storing in video-afspeel functionaliteit. We werken aan een oplossing.",
        "affectedServices": ["media"],
        "expiresAt": "2024-11-18T18:00:00Z"
    }
]
```

**Aanbevelingen & semantiek**
- Gebruik ISO 8601 timestamps voor `version` en `expiresAt` (UTC) zodat clients eenvoudig kunnen vergelijken.
- Houd `message` kort en actiegericht: wat is er niet beschikbaar, en wat kan de gebruiker verwachten?
- Stel `status` consistent in: `info` = informatief, `warning` = beperkte degradatie, `error` = functionaliteit kapot.
