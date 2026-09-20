## Live Demo Link
https://consult-ready.vercel.app/

# ConsultReady
Cross-platform healthcare app for patient-controlled, time-limited medical record sharing during doctor consultations — access is granted and revoked via a secure QR code, not a permanent login.

## What it does

ConsultReady lets a **patient** pick which medical records are relevant to an upcoming consultation, generate a temporary QR code for that session, and share it with a **doctor**, who scans the code to request and view only those records — with no standing access once the session ends.

### Patient flow
- **Welcome** – choose to start a new consultation or view records
- **My Records** – browse uploaded records (lab reports, imaging, prescriptions, hospital records, vitals/history), upload new ones, view details in a record viewer modal
- **Create Consultation** – enter specialty and reason for visit
- **Smart Suggestions** – rule-based engine recommends which records are relevant to the chosen specialty/reason, with a clinical rationale for each suggestion
- **Review & Approve** – confirm the final set of records to share
- **Consultation QR** – a session QR code is generated (downloadable) with a live countdown until it expires
- **Incoming Access Request** – approve or deny a doctor's request to view the shared records
- **Profile** – patient details

### Doctor flow
- **Scan QR** – scan a patient's session QR via device camera (jsQR-based live scanner) or enter a session ID manually
- **Verify Access** – confirm the scanned session
- **Request Access** – send an access request to the patient
- **Waiting for Approval** – poll for the patient's decision
- **Patient Records** – view the approved records for that session only

### Session & security model
- QR codes encode only an opaque session ID (e.g. `CONSULT_SESSION_8F72A91C`) — no patient data or PHI is ever embedded in the code itself
- Sessions carry a status (`pending`, `requested`, `active`, `expired`, `revoked`, `denied`) and an expiry timestamp; a countdown timer enforces time-limited access
- Access is scoped per-session to only the records the patient selected

The app also includes a one-click **demo mode** that seeds a full sample flow (patient → suggestions → QR → doctor scan) for quickly demoing the product end to end, plus a device-preview toggle (mobile / tablet / responsive) for reviewing the UI at different breakpoints.

## Tech stack

- **React 19** + **TypeScript**, built with **Vite**
- **Zustand** for state management (`authStore`, `sessionStore`, `recordsStore`, `consultationStore`)
- **Tailwind CSS v4** (via `@tailwindcss/vite`) for styling
- **lucide-react** for icons, **motion** for animation, **canvas-confetti** for success moments
- **qrcode** for QR generation, **jsqr** for camera-based QR scanning

## Project structure

```
src/
├── App.tsx                    # Root component: role switching, navigation, toasts
├── components/
│   ├── common/                 # Shared UI: header, nav, cards, modals, states, buttons
│   ├── doctor/                  # Camera QR scanner, access request/verify/waiting UI
│   └── patient/                 # Incoming access request modal, record upload modal
├── features/
│   ├── patient/                 # Welcome, Records, Create Consultation, Suggestions,
│   │                             # Review & Approve, Consultation QR, Profile
│   └── doctor/                  # Scan QR, Verify, Patient Records screens
├── services/
│   ├── qrService.ts             # Session ID + QR generation, countdown formatting
│   └── smartSuggestions.ts      # Rule-based record-relevance engine per specialty
├── store/                       # Zustand stores (auth, session, records, consultation)
├── constants/                   # Demo users, specialties, reasons, seed data
└── types/                       # Shared TypeScript types
```

## Getting started

```bash
# install dependencies
npm install   # or bun install (a bun.lock is included)

# start the dev server (http://localhost:3000)
npm run dev

# type-check
npm run lint

# production build
npm run build
npm run preview
```

Camera-based QR scanning requires the browser to have camera permission; a manual session-ID entry fallback is available in the doctor scan screen.


