# HEALTH-024 — Offline POST error handling

| Field | Value |
|-------|-------|
| **Epic** | E5 — Dashboard, history, export |
| **Priority** | P2 |
| **Estimate** | S (~2h) |
| **Status** | backlog |
| **Spec** | [05-dashboard-history.md](../specs/05-dashboard-history.md) |
| **Depends on** | HEALTH-008 |
| **Blocks** | — |

## User story

As a user submitting data while offline, I want a clear error message, so that I know my log was not saved.

## Summary

Client-side detection on weight and diary forms when offline or fetch fails; show toast/banner.

## Technical approach

### JS module: `resources/js/offline-guard.js`

```js
export function guardForm(formEl) {
  formEl.addEventListener('submit', (e) => {
    if (!navigator.onLine) {
      e.preventDefault();
      dispatchToast('You are offline. Changes were not saved.');
    }
  });
}
```

For Alpine fetch adds in diary:

```js
catch (e) {
  if (!navigator.onLine) showToast('Offline — try again when connected');
}
```

### Toast component

- Alpine `x-data="{ show: false, message: '' }"` in app layout
- Auto-hide 5s

### Forms to wire

- Weight log form
- Diary add item (fetch)
- Manual food POST
- Optional: copy yesterday

### Implementation tasks

- [ ] offline-guard.js imported in app.js
- [ ] Toast blade component
- [ ] `window.addEventListener('offline', ...)` optional banner

## Acceptance criteria

- [ ] Submit weight offline → no silent fail, toast shown
- [ ] Add food via fetch offline → toast shown
- [ ] Online submit still works

## QA

- [ ] DevTools → Network → Offline → submit form

## Definition of done

- [ ] Documented in PWA QA checklist
