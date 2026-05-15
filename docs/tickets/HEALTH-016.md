# HEALTH-016 — Barcode scan UI

| Field | Value |
|-------|-------|
| **Epic** | E4 — Diet & nutrition |
| **Priority** | P1 |
| **Estimate** | M (~1 day) |
| **Status** | backlog |
| **Spec** | [04-diet-nutrition.md](../specs/04-diet-nutrition.md) |
| **Depends on** | HEALTH-015 |
| **Blocks** | — |

## User story

As a user, I want to scan a product barcode with my phone camera, so that I can log packaged food quickly.

## Summary

Add barcode scanning to diary add-food flow using `@zxing/browser`, with manual barcode entry fallback.

## Technical approach

### Install

```bash
npm i @zxing/browser
```

### Alpine component: `barcodeScanner.js`

- Request `navigator.mediaDevices.getUserMedia({ video: { facingMode: 'environment' } })`
- `BrowserMultiFormatReader.decodeFromVideoDevice`
- On decode → call `/api/foods/barcode/{code}` → open quantity step

### UI

- “Scan barcode” in FAB menu
- Modal with `<video>` preview + cancel
- Manual input: numeric field + “Look up” button
- Error states: permission denied, not found, unsupported browser

### HTTPS dev

Document in README:

```bash
# Option: vite basic ssl plugin, or ngrok, or laravel valet secure
```

### Implementation tasks

- [ ] Scanner component integrated into add-food modal
- [ ] Stop camera stream on modal close
- [ ] Validate barcode: 8–14 digits
- [ ] Handle OFF null → “Product not found. Try manual entry.”

## Acceptance criteria

- [ ] Scan known test barcode (use OFF sample) → product prefilled
- [ ] Manual entry works without camera
- [ ] Camera stops when modal closed
- [ ] iOS Safari: graceful fallback message if scan unsupported

## QA checklist

- [ ] Test on Android Chrome HTTPS
- [ ] Test permission deny path
- [ ] Test invalid barcode

## Definition of done

- [ ] Scan flow documented; works on at least one mobile browser
