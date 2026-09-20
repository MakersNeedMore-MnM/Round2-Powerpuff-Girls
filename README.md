# ConsultReady

Cross-platform healthcare app for patient-controlled, time-limited medical record sharing during doctor consultations. Access is granted and revoked through a secure, expiring QR code — never a permanent login.

## Problem Statement

When a patient sees a new doctor or specialist, relevant medical history (lab reports, imaging, prescriptions, past diagnoses) is usually scattered across paper files, WhatsApp chats, or apps the doctor has no access to. Patients either overshare an entire medical history or arrive without the one report the doctor actually needs, and there's no clean way to grant a doctor temporary, scoped access to records for a single consultation and have that access automatically expire afterward.

ConsultReady solves this by letting patients curate exactly which records apply to a specific visit, share them via a single QR scan, and have that access expire on its own — no permanent data exposure, no manual revocation required.

## Key Features

- **Patient-controlled sharing** – patients choose exactly which records to share for each consultation, not their entire history
- **Smart record suggestions** – a rule-based engine recommends relevant records based on the chosen specialty and reason for visit, with a clinical rationale for each suggestion
- **QR-based, time-limited sessions** – a session QR code (downloadable) is generated per consultation, with a live countdown until it automatically expires
- **No PHI in the QR code** – the code encodes only an opaque session ID (e.g. `CONSULT_SESSION_8F72A91C`); no patient data ever lives in the code itself
- **Doctor-side scan & request flow** – doctors scan the QR (live camera scanner) or enter a session ID manually, then request access, which the patient must explicitly approve or deny
- **Session status tracking** – sessions move through `pending`, `requested`, `active`, `expired`, `revoked`, and `denied` states, so access is always scoped and time-boxed
- **Record management** – patients can upload, browse, and view full record details (lab reports, imaging, prescriptions, hospital records, vitals/history) in-app
- **One-click demo mode** – seeds a complete sample flow (patient → suggestions → QR → doctor scan) for quickly demoing the product end to end
- **Responsive device preview** – toggle between mobile, tablet, and responsive views to review the UI at different breakpoints

## Live Demo
https://consult-ready.vercel.app/

## Tech Stack

- **React 19** + **TypeScript**, built with **Vite**
- **Zustand** for state management (`authStore`, `sessionStore`, `recordsStore`, `consultationStore`)
- **Tailwind CSS v4** (via `@tailwindcss/vite`) for styling
- **lucide-react** for icons, **motion** for animation, **canvas-confetti** for success moments
- **qrcode** for QR generation, **jsqr** for camera-based QR scanning

## How to Run

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

Camera-based QR scanning requires the browser to have camera permission; a manual session-ID entry fallback is available on the doctor scan screen.

### How to use it

1. **As a patient**: start on the Welcome screen → select a specialty and reason for visit → review the smart-suggested records → approve the final set → a QR code is generated with a countdown timer.
2. **As a doctor**: switch roles (or use the built-in "simulate doctor scan" demo shortcut) → scan the patient's QR, or enter the session ID manually → request access → once the patient approves, view the shared records only, only until the session expires.

## Project Structure

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

## Team Members

| Name |
|------|
|Anjali Bisht |
|Akansha Bisht |
|Mukti Pandit |
|Aashi|


