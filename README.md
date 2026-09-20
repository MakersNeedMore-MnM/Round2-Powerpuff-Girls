# ConsultReady

A cross-platform healthcare front-end for **patient-controlled, time-limited medical record sharing via QR code**. A patient picks which records to share for a specific consultation, generates a QR code carrying an opaque session ID, and a doctor scans it to request time-boxed access.

> **Status: prototype / demo.** This is a client-side React app built for AI Studio (Gemini). There is no real backend, no authentication, and no encryption of record contents — see [Limitations](#limitations--known-gaps) before treating this as anything beyond a UX proof of concept.

## How it works

1. **Patient** selects a specialty and reason for visit → the app runs a deterministic, keyword-based rules engine (`smartSuggestions.ts`) to suggest which existing records are clinically relevant.
2. **Patient** reviews/approves the suggested records and generates a QR code. The QR encodes only an opaque session ID (e.g. `CONSULT_SESSION_8F72A91C`) — no PHI is put in the QR itself.
3. **Doctor** scans the QR (camera-based, via `jsqr`) or enters the session ID manually, then submits an access request.
4. **Patient** approves or denies the request. On approval, the doctor gets read access to the selected records until the session expires.
5. Sessions carry a status (`pending`, `requested`, `active`, `expired`, `revoked`, `denied`) and an expiry timestamp; a countdown timer shows remaining access time.

Both roles are simulated in the same app — there's a "switch to Scanner Portal" shortcut and a one-click demo flow (`handleQuickDemoFlow`) that pre-loads a Cardiology / chest-pain scenario for fast walkthroughs.

## Tech stack

- **React 19** + **TypeScript**, bundled with **Vite 8**
- **Zustand** for state (auth, session, records, consultation stores)
- **Tailwind CSS 4** for styling
- **qrcode** (generation) + **jsqr** (camera scanning) for the QR flow
- **motion** for animation, **canvas-confetti** for success states, **lucide-react** for icons
- **@google/genai** — Gemini API dependency is present (per `metadata.json`'s `MAJOR_CAPABILITY_SERVER_SIDE_GEMINI_API`), but the record-suggestion logic actually shipped is a hand-written rules engine, not an LLM call
- A minimal **Express** dependency is present but there's no server code in `src/` — routing and logic are entirely client-side

## Project structure

```
src/
├── App.tsx                     # Root component: role switching, navigation state, toasts
├── components/
│   ├── common/                 # Shared UI: header, nav, cards, modals, QR card, toasts...
│   ├── doctor/                 # Doctor-only widgets (QR scanner, request/verify/waiting UI)
│   └── patient/                # Patient-only widgets (incoming request modal, upload modal)
├── features/
│   ├── patient/                # Welcome, Records, Create Consultation, Suggestions, Review, QR, Profile
│   └── doctor/                 # Scan QR, Verify, Records screens
├── services/
│   ├── qrService.ts            # Session ID generation, QR data URL, countdown formatting
│   └── smartSuggestions.ts     # Specialty/reason → relevant record rules engine
├── store/                      # Zustand stores: auth, session, records, consultation
├── types/                      # Shared TypeScript types
└── constants/                  # Demo data, seed patient, session duration, etc.
```

## Getting started

```bash
# install dependencies (bun.lock is present, so bun is the intended package manager)
bun install
# or: npm install

# run the dev server (http://localhost:3000)
bun run dev

# type-check
bun run lint

# production build
bun run build
bun run preview
```

### Environment variables

Copy `.env.example` to `.env` and fill in:

| Variable | Purpose |
|---|---|
| `GEMINI_API_KEY` | For Gemini API calls (AI Studio injects this automatically from the Secrets panel in that environment) |
| `APP_URL` | Self-referential URL used for links/callbacks (auto-injected by AI Studio's Cloud Run deployment) |

## Limitations / known gaps

Worth knowing before relying on this for anything real:

- **No real authentication.** The doctor role is explicitly reachable with "no login required" (see `handleSimulateDoctorScan` in `App.tsx`), and role switching lives in client state.
- **No backend or database.** "Sessions" persist via `localStorage` (`consultready_sessions_store_v1`) on the same device/browser, not a server — this cannot broker access between an actual patient's phone and an actual doctor's device.
- **No encryption or access control on record contents** beyond what the UI enforces; anyone with the session ID and local access to the store can, in principle, see the data.
- **Smart suggestions are keyword rules, not clinical judgment** — the "AI" framing in the product description is aspirational relative to what's implemented.
- Demo/seed data (a default patient and a pre-expiring demo session) ships baked into the store initialization, which is convenient for showing the flow but means first-run state isn't empty.

## License

Not specified in the project — add one if you intend to distribute this.
