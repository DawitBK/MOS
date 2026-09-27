# ADR-0012: Frontend on the house Next.js 16 stack, plus TanStack Table and react-hook-form

- **Status:** Accepted (planning), 2026-09-26. **Confirmed in the Phase 6 session (2026-09-28, [ADR-0021](ADR-0021-stack-lock-in.md))** over a Vite single-page app.

## Context
The owner asked for the house stack. An ERP frontend is dominated by data grids and line-item forms. The house systems have no grid or form library.

## Decision
- **House base:** Next.js 16 (App Router), React 19, TanStack Query 5, Zustand, Tailwind 3.4, Radix primitives, lucide-react.
- **Additions:**
  - **TanStack Table** for server-driven grids;
  - **react-hook-form + Zod** (schemas shared through `packages/contracts`) for header and line-item forms.
- **UI kit:** MOS keeps its own component kit, not shared with other systems (house rule).
- **House Next 16 rules apply:** the build-env fingerprint (config is baked at build time), a pinned `turbopack.root`, native ESLint flat configs, React Compiler hook rules, and the `useResetOnChange` pattern with stable dependencies.
- **i18n hooks from day one:** a message catalog and a single date formatter with a calendar adapter.

## Consequences
- Consistent with the platform's other frontends, while fit for heavy data entry.
- Tablet and scan-first stations (gate, lab, warehouse) are views inside the same app, not separate apps, until a Later release evaluates an offline PWA.
