# Status (statische fallback)

Kleine repository met een voorbeeld van een statische `status.json` en een GitHub Pages-deployment.

Deze repo bevat een klein statisch `status.json` dat je kunt hosten op een onafhankelijke CDN (GitHub Pages, Cloudflare Pages, S3+CloudFront, enz.) zodat je Next.js-apps het bij het laden client-side kunnen ophalen als een robuuste status/fallback.

Bestanden in deze repo:
- `docs/status.json` — voorbeeld van het status-payload
- `docs/index.html` — kleine demo-pagina die `status.json` ophaalt en toont
- `.nojekyll` — zorgt dat GitHub Pages bestanden onbehandeld serveert
- `.github/workflows/pages.yml` — GitHub Actions workflow om `docs/` naar GitHub Pages te publiceren

Hoe het werkt (globaal idee)
- Host een klein statisch JSON-bestand op een onafhankelijke CDN.
- Client-apps doen een client-side fetch bij het laden en tonen een banner/melding als `status` `warning`/`error`/`critical` is.
- Geen afhankelijkheid van backend-systemen zoals GraphQL, Prepr, Azure DB/Tables, enz. Alleen afhankelijk van de CDN (en dat de app bereikbaar is vanuit de browser).

Status bijwerken
- Bewerk `docs/status.json` en push naar `main`. De GitHub Actions workflow publiceert `docs/` naar GitHub Pages en je `status.json` wordt beschikbaar op `https://<owner>.github.io/<repo>/status.json` (of op je eigen domein als dat is ingesteld).

Korte Next.js client-snippet
Voeg een kleine client-side fetch toe (voorbeeld met React `useEffect`) om een banner te tonen wanneer de status wijst op een incident:

```
// client-side (bijv. in _app.tsx of een kleine Status-component)
import {useEffect, useState} from 'react'

export default function useStatus() {
  const [status, setStatus] = useState(null)

  useEffect(() => {
    fetch('/status.json', { cache: 'no-store' })
      .then(r => r.json())
      .then(data => {
        setStatus(data)
        // optioneel: laatste bekende status bewaren: localStorage.setItem('status', JSON.stringify(data))
      })
      .catch(() => {
        // fallback: probeer laatste bekende status uit localStorage
        try {
          const cached = localStorage.getItem('status')
          if (cached) setStatus(JSON.parse(cached))
        } catch (e) {}
      })
  }, [])

  return status
}
```

Fallback- en resilience-aanbevelingen
- Host `status.json` op een onafhankelijke CDN (Cloudflare Pages, GitHub Pages, Cloudflare R2 + CDN, S3 + CloudFront).
- Stel `Cache-Control`-headers in op de CDN zodat clients een korte TTL voor de laatste bekende status kunnen gebruiken.
- Bewaar de laatste bekende status in `localStorage` zodat de app een melding kan tonen als de CDN tijdelijk onbereikbaar is.
- Optioneel: voeg een kleine Service Worker toe om `status.json` te cachen voor robuuste offline fallback.

Als een downstream-onderdeel uitvalt
- Toon een banner in de app-UI met het `message` en de `severity` uit `status.json`.
- Voor kortdurende problemen: voeg `expiresAt` toe in de JSON zodat de UI automatisch kan verbergen of opnieuw kan controleren na expire.

Hosten met GitHub Pages
- De meegeleverde Actions-workflow publiceert automatisch de `docs/`-map naar GitHub Pages bij een push naar `main`.
- Controleer na de eerste push de Pages-instellingen van de repo (`Settings → Pages`) voor de publicatie-URL of om een custom domein te koppelen.

Vervolgstappen / opties
- Wil je dat ik een kleine React-component (banner) toevoeg die de status gebruikt en een dismissible UI rendert? Ik kan die toevoegen in `docs/` of als een losse component voor Next.js.

--

