---
name: android-superapp
description: Test a Jira ticket on a web app running inside a SuperApp on an Android emulator — preflight the device, drive the WebView safely, record one clip per AC with touches shown, check every clip, upload to a Drive folder with name + size read-back, and draft the Jira comment. Wraps Helix testing-ticket-workflow. Use when the user says /android-superapp, "เทสบน emulator", "เทส SuperApp", "test on Android", or gives a ticket to test on an AVD. Do NOT use for desktop/browser-only runs (testing-ticket), retest after a fix (retest-bug), writing TCs (tc-fe-prep / tc-api-prep), or filing bugs (create-bug).
metadata:
  proactive_triggers:
    - /android-superapp
    - เทสบน emulator
    - เทส SuperApp
    - test on Android emulator
---

# Android SuperApp testing

One entry point for testing a ticket **through a SuperApp on an Android emulator** and delivering
the evidence clips to a Drive folder. It does not replace Helix: Helix `testing-ticket-workflow` runs
the intake, the confirm gate, AC/EC coverage, verdicts and the Jira step; this skill adds the device,
recording and upload rules in [references/android-superapp.md](references/android-superapp.md).

**Session start:** follow Helix `references/session-start-context.md` (installed with Helix).

## Step 1 — Prerequisites (check, do not assume)

| Check | Command / how | Missing → |
|---|---|---|
| Helix installed | the `testing-ticket-workflow` skill is listed | stop; point to the README install step |
| `adb` on PATH | `adb version` | stop |
| Emulator up | `adb devices` lists at least one `emulator-*` | ask the person to start the AVD; do not start one they did not name |
| Helpers pass | `node <this skill>/tools/superapp-test/superapp_test.test.js` exits 0 | stop — a helper that fails its known-answer test is not trusted |
| OCR / frame strip (macOS, optional) | compiled `ocr` and `frames` binaries exist | continue; say which checks will be BLOCKED |

## Step 2 — Intake

Run Helix `testing-ticket-workflow` Phase A, and in the **same** message also ask the extra fields
in [§1 of the rules](references/android-superapp.md#1-intake--extra-fields-ask-once-in-the-phase-a-message):
AVD name, account (never a password), evidence destination (Drive folder or `none`), duplicate policy,
clip style. Real values (package name, web host, folder) come from the person or their own workspace
guide — never write them into this repository.

## Step 3 — Plan and confirm

Follow Helix Phase B–C. The plan lists one clip per AC with its file name
(`{KEY}_AC_{nn}_{slug}.mp4`) and any AC that will be BLOCKED on this device (keyboard, DOM access).
Nothing is recorded before the person confirms.

## Step 4 — Run

Apply the rules in order: device (§2) → preflight (§3) → forbidden inputs (§4) → driving (§5, §5b)
→ recording and clip check (§6) → test data (§7). Use the helpers in `tools/superapp-test/` for every
decision they cover instead of re-deriving it.

## Step 5 — Upload and read-back

§8: one file per upload call, read back name + byte size against the local files, duplicates only as
intake decided (old file to trash, never hard-delete). Skip when the destination is `none`.

## Step 6 — Report

Close with the §10 review gate and Helix's report. Changes asked after delivery go through the
revision loop (§9). Defect-looking observations are listed for the owner, never filed by this skill;
the Jira comment is a draft the person posts.
