# Unfinished registration

How a user leaves a registration and comes back to it. The prototype keeps everything in memory
(`S.drafts`); in production a "draft" is a server-side record tied to the user account.

## Rules

- **Saving starts at step 2.** Until the user account exists (step 1 and the account modal) nothing is
  stored. Leaving there asks for confirmation and discards the registration.
- **One draft per company.** A user can have up to **5** drafts (`MAX_DRAFTS`, easy to change).
- **A draft stores** the data of that registration only: IČO, company, contact, identity-check choice and
  uploads, consents, and the last opened step. It is saved on leave, not on every keystroke.
- **Loading screens are not resume points.** A draft saved on a loading or in-progress sub-screen reopens
  on the parent step (`STABLE` map, e.g. `bankLoading` → `bank`).
- **A draft is removed** when the registration is finished (step 5) or the user deletes it.

## Flows

**1. Leave** – close button, logo, "Dokončit registraci později", or sign-out inside a registration.
- Step 1 / account modal → dialog *"Opravdu chcete ukončit registraci?"* → leaving discards it.
- Step 2+ → dialog *"Registraci jsme uložili"* → leaving saves the draft (`saveDraft()`).

**2. Come back** – three entry points, all lead to sign-in (`login`) and then the hub:
- "Máte rozpracovanou registraci? Přihlásit se" on the account-creation step,
- "Přihlásit se" in the header,
- the **reminder e-mail** ("Dokončit registraci", link valid 30 days).

Sign-in is e-mail + password or a social account. Errors: invalid e-mail or empty password, wrong
credentials (same message for an unknown e-mail, so accounts are not revealed), temporary lock after
repeated failures (15 min), social account without a registration (links to sign-up).

**3. Hub – "Vaše registrace"** (`hub`) – one card per draft: company (or "Firma zatím nezadána"), IČO,
5-segment progress, *"Krok X z 5 · název · uloženo datum"*.
- **Pokračovat v registraci** → `loadDraft()` restores the draft and opens the last step.
- **Smazat** → confirm dialog → draft removed together with its data (not undoable).
- **Registrovat další firmu** → `newRegistration()`: fresh registration from step 1; a signed-in user
  skips account creation. Disabled at 5 drafts, with an explanatory hint.
- No drafts → empty state with "Registrovat firmu".

**4. Duplicate IČO** – on step 3, a company that already has a draft shows *"Registraci firmy X už máte
rozpracovanou"* with **Pokračovat v této registraci** (opens that draft) or **Zadat jiné IČO**. A company
that is already a customer shows a message with the phone number instead.

**5. Start at the limit** – "Registrovat se" on public pages with 5 drafts sends a signed-in user to the
hub with the limit notice instead of step 1.

**6. Where the user lands after leaving** – back to where the registration started (`S.origin`): the hub
if it was started from there, otherwise the landing page.

## User menu

"Rozpracované registrace (n)" appears in the user menu and on the mobile profile page only when n > 0.
Profile forms with unsaved changes ask *"Zahodit neuložené změny?"* before navigating away.

## Try it in the prototype

*Prototype scenarios* (bottom right):
- **Saved registrations** – none / 1 / 2 / 5 drafts (after sign-in),
- **Login result** – success, wrong e-mail or password, locked, social login without an account,
- jump to **L1 Přihlášení**, **L2 Vaše registrace** or the **reminder e-mail**.

## Open points for implementation

- Draft storage, retention and cleanup (the e-mail link is valid 30 days) are not defined here.
- Reminder e-mail trigger and timing; the prototype only shows the e-mail itself.
- Password reset is not part of the prototype ("Zapomenuté heslo" only shows a toast).
- Uploaded documents in a draft need server-side storage and the same retention rules.
