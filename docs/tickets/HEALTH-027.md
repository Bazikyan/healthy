# HEALTH-027 — Medical disclaimer footer component

| Field | Value |
|-------|-------|
| **Epic** | E6 — Quality & launch |
| **Priority** | P1 |
| **Estimate** | S (~1h) |
| **Status** | backlog |
| **Spec** | [00-overview.md](../specs/00-overview.md) |
| **Depends on** | HEALTH-007 |
| **Blocks** | — |

## User story

As a user, I want to see that the app is not medical advice, so that I understand its limitations.

## Summary

Blade component rendered in app layout footer on all authenticated pages.

## Technical approach

### Component: `resources/views/components/medical-disclaimer.blade.php`

```blade
<p class="text-xs text-gray-500 text-center px-4 py-2">
  This app does not provide medical advice. Always consult a qualified healthcare professional
  before changing your diet or exercise routine.
</p>
```

### Placement

In `layouts/app.blade.php` above bottom nav (inside main, or between main and nav):

```blade
<x-medical-disclaimer />
<x-bottom-nav />
```

### Styling

- Small, muted text
- Does not overlap bottom nav
- `pb` adjusted if needed

### Implementation tasks

- [ ] Create component
- [ ] Include in app layout
- [ ] Exclude from guest/auth pages (login) — optional one line on register

## Acceptance criteria

- [ ] Visible on dashboard, diary, weight, settings
- [ ] Not hidden behind bottom nav
- [ ] Readable on mobile (contrast WCAG AA for small text)

## Definition of done

- [ ] Component merged; visual check on 375px viewport
