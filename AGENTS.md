# status

The status file EO websites fetch to show incident banners: GitHub Pages serves `status.json` from `main`, and the Next.js sites in `next-monorepo` read it. Editors and developers post a message here when a service has an outage or planned maintenance.

## Ways to hurt yourself

1. **An invalid file hides every banner.** The websites validate the whole file: broken JSON, a `status` other than `info`, `warning` or `error`, or a service outside the six categories in the README makes them drop the file and show no message at all. Use only the listed values and check the file parses (`jq . status.json`) before you push.
2. **A push to `main` is live.** GitHub Pages publishes `main` directly and websites show the change within a minute; nothing reviews it first. No website reads `status-test.json`, so it is not a test environment.

## How it works

GitHub Pages serves the repo root of `main` as-is. In `next-monorepo`, `@eo/data-utils` fetches `status.json` with a 60-second revalidation, drops expired messages, filters by the services a page asks for and sorts by severity, then by `version`, newest first. `MultiSystemStatusBanner` in `@eo/site-fragments` renders the result. The categories are defined in `packages/data-utils/src/status/types.ts` there, so a new category starts in `next-monorepo`, and its `packages/site-fragments/src/status/README.md` describes which category fits which incident.
