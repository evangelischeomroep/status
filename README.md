# Status JSON — uitleg en gebruik

Deze README beschrijft het `status.json`-bestand dat als statische fallback gebruikt kan worden voor Next.js- en andere webapplicaties.

**Doel**
- Een klein, statisch JSON-bestand hosten op een onafhankelijke CDN (GitHub Pages, Cloudflare Pages, S3+CloudFront) zodat clients bij het laden snel kunnen controleren of er een incident is en een banner/alert tonen — ook als backend-systemen  uitvallen.

**Schema (veld-uitleg)**
- `version` (string, ISO 8601 timestamp): tijdstempel of versie van deze status. Gebruik UTC (bv. `2025-12-01T10:30:00Z`).
- `status` (string): korte label, bijvoorbeeld `info`, `warning`, `error`.
- `message` (string): korte, menselijke leesbare verklaring die in de UI wordt getoond.
- `affectedServices` (array[string]): lijst met servicenaam(en) intern gebruikt (bv. `checkout`, `content`, `media`, `auth`, `interaction`, `crm`).
- `expiresAt` (string, ISO 8601, optioneel): tijdstip waarop de melding verloopt — clients kunnen hierop opnieuw checken of auto-dismissen.

**Categorieën en tagging**
- `checkout`: betaal- en bestelstromen, inclusief betalingsproviders. Gebruik deze categorie bij issues die de financiële flow raken.
- `contact`: Gebruik deze categorie bij issues in het afhandelen van Calls. Bijv. issues in contact-formulieren die een call aanmaken via het crm of capaciteitsproblemen bij KCC.
- `content`: rendering of CMS-gerelateerde assets (pagina’s, media, video). Als de site/ui traag is of statische content faalt, gebruik je deze categorie.
- `auth`: inlog/account (auth-provider).
- `interaction`: chat, notificaties en andere real-time communicatie.
- `media`: streaming, videospeler of uitzending-specifieke services die losstaan van de algemene content-ervaring.

Je kunt meerdere categorieën tegelijk toewijzen via `affectedServices` (bijv. `["crm","checkout"]` wanneer een CRM-issue ook checkout-devices raakt).

**Voorbeeld**
```
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

**Aanbevelingen & semantiek**
- Gebruik ISO 8601 timestamps voor `version` en `expiresAt` (UTC) zodat clients eenvoudig kunnen vergelijken.
- Houd `message` kort en actiegericht: wat is er niet beschikbaar, en wat kan de gebruiker verwachten?
- Stel `status` consistent in: `info` = informatief, `warning` = beperkte degradatie, `error` = functionaliteit kapot.
