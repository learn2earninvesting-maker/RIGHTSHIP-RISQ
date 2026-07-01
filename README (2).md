# RightShip Ready — RISQ Pre-Inspection Checklist

A free, self-contained web app for running RightShip RISQ pre-inspection checklists on board — 827 verification points across all 10 ranks (Master, Chief Officer, 2nd/3rd Officer, Chief/2nd/3rd/4th Engineer, ETO, and joint Master & CE document/competency checks).

**Live app:** `https://<your-username>.github.io/risq-checklist/`

## How to use it (crew)

1. Open the link above on any phone, tablet, or laptop — no login, no install.
2. Enter a **Room Code** — use your vessel name or IMO number (e.g. `FEDERAL-FRONTIER`). Everyone who enters the same code shares the same live checklist.
3. Enter your name once — it gets tagged against anything you check off, so the rest of the crew can see who confirmed what.
4. Pick your rank, go through the items, mark **YES / NO / N/A**, and add a remark (e.g. the date you checked it or evidence reference).
5. Saves happen automatically and sync live to everyone else using the same room code.
6. Once a rank (or the whole set) is ready, tap **Generate report** to get a print-ready summary — use your browser's "Save as PDF" from the print dialog.

Each vessel/team should pick its own room code so checklists don't overlap with other ships using this same public tool.

## For maintainers

This is a single static `index.html` file — no build step, no server. To update it:

1. Edit `index.html` directly (or regenerate it from source).
2. Commit and push to `main`.
3. GitHub Pages redeploys automatically within about a minute.

### Backend

Answers are stored in **Firebase Firestore**, one document per room code under the `rsready_rooms` collection. The Firebase web config embedded in the page is not a secret — access is controlled entirely by the Firestore security rules below, published in the Firebase console under **Firestore → Rules**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /rsready_rooms/{roomCode} {
      allow read, write: if roomCode is string && roomCode.size() > 0 && roomCode.size() < 100;
    }
  }
}
```

There is no authentication — anyone who knows a room code can read or write that room's data. This is intentional, to keep the app zero-friction for crews, but it means room codes should be treated as shared secrets if privacy matters (e.g. append the IMO number rather than using a guessable vessel name alone).

### Checklist source

The checklist content (RISQ reference, question text, SMS reference, and inspector guidance) is based on RightShip RISQ questionnaires and each vessel's SMS cross-references. It's static data embedded in the page — updating the underlying checklist requires regenerating `index.html` from the source RISQ files.

## Disclaimer

This tool is provided as-is to help crews prepare for RightShip inspections. It is not an official RightShip product and is not affiliated with RightShip. Always refer to the current RightShip RISQ questionnaire and your company's SMS as the authoritative source.
