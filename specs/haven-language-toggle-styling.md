# Haven language-toggle styling — page-level i18n toggle

_Brand-spec for the `language-toggle-i18n` primitive — the canonical page-level language toggle. Visual treatment + rationale only; structure and CSS implementation live in [`Lab/haven-ui/packages/design-system/pattern-library/components/language-toggle-i18n.html`](../../haven-ui/packages/design-system/pattern-library/components/language-toggle-i18n.html) and [`packages/design-system/src/styles/tokens/components.css`](../../haven-ui/packages/design-system/src/styles/tokens/components.css)._

---

## 1. Context

The `language-toggle-i18n` primitive is the canonical page-level language toggle for haven-ui. v1 ships PAGE-LEVEL scope only — the toggle reads / writes its own localStorage key + applies the swap within the current page. APP-SHELL scope (where the toggle lives at the app-shell layer + the choice persists across route changes via a centralized language-choice stream + the choice propagates across different apps within the same shell) is DEFERRED to the next codification cycle.

The primitive earned codification when the cena-apps patient-app account.html hand-author (2026-06-17) introduced the Profile section's EN/ES segmented-control with inline i18n-swap JS. Haven Steward dispatch 2026-06-22 codified the page-level shape and deferred the app-shell shape; the deferral note is in the language-toggle-i18n PL fragment's `@component-meta` notes.

**Why a primitive vs. an inline composition.**

Composing the segmented-control + the i18n-swap JS inline on every page that needs a language toggle (a) re-derives the localStorage convention each time, (b) splits the storage-key contract across the codebase (the hand-author used 'cena.lang'; haven's pre-existing i18n.js used 'cena-lang' — confusion + drift waiting to land), (c) leaves the data-i18n-* attribute pattern undocumented at the haven-ui spec layer. Codification absorbs the shape so future pages inherit the contract.

**Brand intent.** The language toggle must read as **"a clean preference choice — pick the one that fits you."** No persuasion, no nudging, no default-language opinion. The two languages are visually equal (same chrome, same weight); the active state is a subtle ground-fill change (primary-50 / primary-700 text) inherited from the segmented-control canon. Brand restraint per the cena-health-brand tone: this is a routine preference, not a feature beat.

---

## 2. Brand frame for this primitive

The primitive composes the existing `.segmented-control` canon verbatim — same visual chrome as the meal-ordering view toggles, the calendar view picker, and other canonical segmented controls. The `.language-toggle-i18n` class is a SCOPE marker on the container (so JS hooks, manifests, and future invariant tripwires can pin "the canonical page-level language toggle"); it adds NO new visual chrome. The brand decision was authored once at the segmented-control layer.

Three brand choices follow:

- **Segmented-control, not dropdown / radio group / link list.** A dropdown would feel heavier for a binary EN/ES choice (the patient sees the full set without expanding). A radio group would feel form-y — radio buttons read as "fill out this question" rather than "set this preference." A link list would imply destination changes (clicking a link goes somewhere new). The segmented-control reads as a single self-contained "choose one" affordance — the right register for "this preference applies to the page I'm on right now."

- **Button labels are the language NAMES in the LANGUAGE itself, not language codes or flags.** "English" / "Español", not "en" / "es", and not 🇺🇸 / 🇪🇸. Language codes read as developer / power-user; the patient should not have to know what ISO-639-1 is. Flags carry political weight (the Cena Health patient population includes Spanish-speakers from many countries; the Mexican / Spanish / Argentine flag would all read as wrong-or-incomplete). Language-name-in-the-language is the inclusive, accurate choice — and it's the convention every major platform (Apple, Google, Microsoft) uses for the same reason.

- **No persistence-confirmation message.** When the patient clicks a language button, the page swap happens immediately + the choice persists silently. No "Language preference saved" toast, no "Are you sure?" confirm. The choice is reversible (click the other button), the consequence is purely visual (the page reads in the other language), and the patient gets the result they asked for. A confirmation message would be ceremony for a non-ceremonial action.

The "grew, not built" test on this primitive: an account-page Language row containing a Language label + helper + the segmented-control toggle should read identically to a hand-authored equivalent — same segmented-control chrome, same label register, same vertical rhythm. The codification's brand work is naming WHICH segmented-control instance is the canonical language toggle (via the `.language-toggle-i18n` class + the `[data-language-toggle]` attribute), not introducing new visual chrome.

---

## 3. Composition treatment per region

The primitive composes the existing `.segmented-control` + `.segmented-control-btn` canon. All visual decisions below are inherited from haven's existing canon; the codification's brand work is the scope marker + the JS contract.

### 3.1 Toggle container

- **Semantic intent.** Groups the two (or future N) language options into one mutually-exclusive choice.
- **Visual treatment.** Composes `.segmented-control` (inline-flex, rounded-lg, sand-200 border, overflow-hidden). The `.language-toggle-i18n` class is a scope marker only — it adds no visual chrome.
- **Container attributes.** `role="group"` + `aria-label="Language"` (or `aria-labelledby` pointing at an adjacent visible label). The `aria-label` should match the active language ("Language" when EN is active; "Idioma" when ES is active — the page's primary `data-i18n-*` attribute on the container's label provides this) — but v1 ships with a static English aria-label for simplicity; the data-i18n-aware aria-label is a candidate for the next slice.
- **JS hook.** `data-language-toggle` (attribute-based, preferred) OR `.language-toggle-i18n` (class-based, fallback). Either works; the attribute is the canonical contract.

### 3.2 Toggle buttons

- **Semantic intent.** Each button selects the corresponding language as the active page language. Toggle semantics (`aria-pressed`), not option-list semantics (`aria-selected`) — these are toggle buttons, not list-box options.
- **Visual treatment.** Composes `.segmented-control-btn` (px-3 py-1.5, text-sm, sand-600 text, sand-50 ground, focus ring primary-500, square corners with separator border). Active state: `.active` class adds primary-50 ground + primary-700 text + font-medium. Inherits the existing canon's hover (sand-700 text) and focus (ring-primary-500) chrome.
- **Button attributes.** `data-lang="en"` / `data-lang="es"` (canonical, language code per ISO-639-1); `aria-pressed="true"` / `aria-pressed="false"` reflecting the current state.
- **Button labels.** Language names in the language itself: "English" / "Español". Future N-language support would extend the pattern: "Français" / "中文" / "Tagalog" / etc.

### 3.3 Active-state register

- **Active button.** Primary-50 ground + primary-700 text + font-medium. The treatment carries enough visual weight to be recognizable at a glance (the patient should not need to read the labels to know which is active) but not so much that the inactive button reads as disabled.
- **Inactive button.** sand-600 text on sand-50 ground. Hover: sand-700 text. Focus: visible ring. The inactive button is still clearly interactive.
- **Transition.** The active-state change is instantaneous — no CSS transition on the ground fill or text color. Per brand restraint, an animated active-state change would add motion to a routine preference action.

---

## 4. Storage + JS contract

The primitive's behavior layer is owned by [`packages/design-system/src/scripts/components/language-toggle.js`](../../haven-ui/packages/design-system/src/scripts/components/language-toggle.js). Brand-visible contract:

- **localStorage key 'cena-lang'** (string, lowercase). MATCHES the existing i18n.js convention; language-toggle.js is the canonical successor.
- **Default 'en'** if unset or localStorage is unavailable.
- **Persistence is silent.** No toast, no confirm, no other visible chrome on language change.
- **Cross-page persistence.** The choice persists across page reloads + navigations within the same app (same localStorage scope). It does NOT persist across different apps unless the app-shell layer reads from the same localStorage key — but cross-app persistence is the deferred app-shell-scope work; v1 is page-level only.

### Migration from i18n.js

The pre-existing `i18n.js` script is the V0 ad-hoc language-swap script (no PL primitive, no codified contract). `language-toggle.js` supersedes it with:
- Same storage-key convention (`'cena-lang'`)
- Same data-i18n-en / data-i18n-es attribute pattern
- Same default-to-'en' behavior
- ADDED: scope-marker class hook (`.language-toggle-i18n`) + attribute hook (`[data-language-toggle]`)
- ADDED: saved-state skip (won't overwrite a label carrying `data-account-state="saved"`)
- ADDED: programmatic API (`_languageToggle.getLang()` / `setLang()`)
- ADDED: `language-change` CustomEvent for future app-shell coordination

The two scripts MUST NOT both load on the same page — they would double-bind click handlers on `[data-lang]` buttons. Consumers migrate to language-toggle.js when they next touch the page.

### Storage-key divergence note (cena-apps)

The cena-apps patient-app account.html hand-author currently uses localStorage key `'cena.lang'` (dot) inline, while haven-ui's existing i18n.js uses `'cena-lang'` (hyphen). This primitive adopts `'cena-lang'` as canonical (matches haven's existing convention + the broader consumer base — a one-line key change is cheaper than a vault-wide migration of haven's existing usage). A future cena-apps re-vendor + re-emit pass will need to normalize to `'cena-lang'`; patients who set their language preference via the hand-author would have a one-time language reset. Documented in the completion report.

---

## 5. What this brand spec does NOT cover

- **App-shell scope.** When the toggle lives at the app-shell layer + the choice propagates across route changes + across apps, the storage layer + JS contract changes. Deferred to the next codification cycle. The primitive's PL fragment notes name the migration path: persisted-choice reading moves to app-shell init; this module's storage layer becomes a fallback for pages that load outside an app-shell context.
- **More-than-binary language sets.** v1 supports EN + ES only. When a consumer needs a 3rd or Nth language, a slot-based variant earns codification: the `[data-lang]` attribute already takes any language code, so the JS contract extends naturally; what needs codification is the visual variant for a 3-or-more-button segmented-control (the active-state weight might need refinement when 4+ options are visible). PL fragment + brand spec at that time.
- **AT user testing on the lang-shift announcement.** The primitive updates `document.documentElement.lang`, which drives the browser's accessibility tree's language. v1 does NOT add a live-region announcement on language change (the document.lang update + the visible chrome change are the cues). If AT user testing surfaces a need, the next slice can add a polite live-region announcement.
- **Right-to-left language support.** v1 EN + ES are both LTR. When a Cena Health surface needs an RTL language (Arabic, Hebrew, Urdu), the primitive needs an `<html dir="rtl">` flip + the CSS chrome needs an RTL audit. Codify at that point.

---

## 6. Source incident

cena-apps patient-app account.html hand-author (2026-06-17). The Profile section introduced an EN/ES segmented-control with inline i18n-swap JS to support the bilingual UConn pilot patient population (a mix of English- and Spanish-dominant care recipients). The hand-author shape worked for the slice but left three things uncodified:
1. The segmented-control as the canonical language-toggle register (vs. dropdown / radio / etc.)
2. The data-i18n-en / data-i18n-es attribute pattern as the canonical bilingual-content marker
3. The localStorage persistence convention

Haven Steward dispatch 2026-06-22 codified all three at the page-level scope. App-shell scope is deferred because that work requires auditing all navigation entry points + coordinating with the app-shell author (Andrey) + ensuring the i18n choice persists across route changes and across different app contexts. That's structural work, not codification work; it earns its own slice when a second screen (login, menu, order-history) needs centralized i18n.
