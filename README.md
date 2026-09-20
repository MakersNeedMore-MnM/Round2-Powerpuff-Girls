# ConsultReady

A cross-platform healthcare front-end for **patient-controlled, time-limited medical record sharing via QR code**. A patient picks which records to share for a specific consultation, generates a QR code carrying an opaque session ID, and a doctor scans it to request time-boxed access.

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

## License

Not specified in the project — add one if you intend to distribute this.
