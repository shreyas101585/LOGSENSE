# WASP hero

The active site is one WASP hero plus a plain white continuation. It runs in the existing Next.js App Router project.

## Active file tree

- `src/app/page.tsx` — server route, PostgreSQL connectivity check, optional GLB detection.
- `src/app/layout.tsx` — metadata, self-hosted Oxanium 300 and Inter 400/600, Tailwind/global CSS.
- `src/app/globals.css` — light-mode artboard, responsive layout, thin SVG frame styling, chamfered controls.
- `src/components/Hero.tsx` — navbar, pixel wordmark, React-owned scramble headline, GSAP intro, animated statistics, compact navigation panels and contact form.
- `src/components/WaspScene.tsx` — R3F canvas, procedural wasp, GLB loading, studio reflection environment, wing animation, reduced-motion handling and visibility-paused demand rendering.
- `src/components/FrameLines.tsx` — one responsive SVG containing desktop/mobile linework.
- `src/app/api/connect/route.ts` — validated public inquiry endpoint.
- `src/app/api/health/route.ts` — database-backed preview health check.
- `src/db/index.ts` — existing Drizzle/PostgreSQL client.
- `src/db/schema.ts` — WASP inquiries table.
- `public/models/README.md` — production-model instructions.

Legacy LogSense modules remain in the project but are not imported by the WASP route or layout.

## Packages

Install with: `npm install three @types/three @react-three/fiber @react-three/drei gsap @fontsource/oxanium @fontsource/inter`.

All required packages are already installed in this project. Tailwind 4 remains wired through the existing PostCSS configuration and `@import "tailwindcss"` in globals.css; `@theme inline` exposes the WASP palette and font utilities.

## Model

Drop the actual asset at `public/models/wasp.glb`. It is automatically selected instead of the procedural wasp. No file edit is necessary. For recommended object names and export orientation, see `public/models/README.md`.

The studio reflection environment is built locally with Drei Environment + Lightformer rather than a remote HDR preset. This avoids production CDN dependencies and keeps the background transparent and flat grey.

## Motion and accessibility

- The intro uses a scoped GSAP context with cleanup. React owns every headline character; no splitting, cloning, or DOM injection occurs.
- Reduced-motion visitors skip the intro/scramble and rapid wing flap. Only a small, slow body sway remains.
- The transparent R3F canvas uses `frameloop="demand"`, requests frames only while the page is visible, and caps DPR at 2.
- No ground plane or contact shadow is used.
- A static SVG silhouette is supplied when WebGL2 is unavailable.
- The mobile navigation exposes its expanded state. Contextual panels support Escape, focus trapping and focus restoration.

## Inquiry storage

The CONNECT and START NOW actions open the same contact form. Valid submissions are persisted to the `wasp_inquiries` PostgreSQL table using Drizzle. No external email delivery service is configured or implied.

Apply the schema with `npx drizzle-kit push` after the managed environment/database is running.
