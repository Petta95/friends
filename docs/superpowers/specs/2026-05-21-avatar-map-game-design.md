# Avatar Map Game — Design Spec

**Date:** 2026-05-21  
**Status:** Approved

---

## Overview

Un mini-gioco a singola pagina HTML (no framework, no dipendenze esterne) con:
1. Schermata di selezione avatar
2. Mappa interattiva con avatar mobile
3. 4 popup tematici da esplorare
4. Schermata finale al completamento

---

## File Structure

```
index.html          ← unico file con HTML + CSS + JS embedded
assets/
  angela.png        ← avatar gorilla
  alex.png          ← avatar maiale
  iuri.png          ← avatar asino
  alessia.png       ← avatar struzzo
  piedi.jpg         ← immagine piedi (popup Paura step 3)
  celeb1.jpg        ← celebrity 1 (popup Amore)
  celeb2.jpg        ← celebrity 2
  celeb3.jpg        ← celebrity 3
  celeb4.jpg        ← celebrity 4
  celeb5.jpg        ← celebrity 5
  video-sport.mp4   ← video salto muretto (popup Sport)
  gif-urlo.gif      ← sticker urlo (popup Paura step 3)
  gif-malizioso.gif ← sticker malizioso (popup Amore)
  gif-atleta.gif    ← sticker atleta (popup Sport)
  gif-povero.gif    ← sticker povero (popup Soldi)
  gif-piange.gif    ← sticker pianto (schermata finale)
```

---

## Screen 1 — Selezione Avatar

- Sfondo scuro con stelle animate in CSS
- Titolo: "Scegli il tuo personaggio"
- 4 card disposte in griglia 2×2 (desktop) / 1 colonna (mobile)
- Ogni card: PNG animale + nome personaggio + sottotitolo ironico
  - Angela → gorilla → "La più elegante"
  - Alex → maiale → "Il più profumato"
  - Iuri → asino → "Il più testardo"
  - Alessia → struzzo → "La più veloce (in fuga)"
- Hover: bordo colorato pulsante + leggero scale-up
- Click: fade-out e transizione alla mappa

---

## Screen 2 — Mappa

### Layout
- Mappa disegnata in CSS top-down style: erba verde, strade grigie, alberi decorativi
- Dimensione canvas: 800×600px (centrato, scrollabile su mobile)
- Avatar: immagine PNG selezionata, 48×48px, si muove liberamente sulla mappa

### Controlli
- Arrow keys o WASD
- Velocità: 3px per frame a 60fps

### Hotspot (zone interattive)
4 icone fisse sulla mappa, ognuna con etichetta flottante:
| ID | Icona | Posizione |
|----|-------|-----------|
| paura | 👻 | top-left area |
| amore | ❤️ | top-right area |
| sport | ⚽ | bottom-left area |
| soldi | 💰 | bottom-right area |

- Raggio di attivazione: 50px dal centro hotspot
- Quando l'avatar entra nel raggio: hotspot pulse + tasto "Apri [tema]" compare
- Hotspot completato: cambia colore (grigio con checkmark ✓), non riattivabile

### Completamento
- Quando tutti e 4 gli hotspot sono stati completati → fade-out → Screen 3

---

## Popup Sistema

Ogni popup è un modal centrato con overlay scuro semi-trasparente.  
Chiudibile solo completando il flusso (no X di chiusura per i popup "seri").

---

### Popup: PAURA 👻
**Step 1**
- Sfondo: rosso scuro
- Testo: "È molto spaventoso, sei sicuro?"
- Bottone NO: grande, prominente (verde)
- Bottone SÌ: piccolo (font-size 0.7em, colore grigio)
- NO → chiude popup senza completarlo (il giocatore può rientrare nel raggio e riprovare) | SÌ → step 2

**Step 2**
- Testo: "⚠️ Attenzione: le immagini seguenti sono riservate ai 18+"
- Bottone NO: grande (verde)
- Bottone SÌ: ancora più piccolo (font-size 0.5em, quasi invisibile)
- NO → chiude popup senza completarlo | SÌ → step 3

**Step 3**
- Mostra: `assets/piedi.jpg` (immagine piedi)
- Mostra: `assets/gif-urlo.gif`
- Bottone "CHIUDI" → segna hotspot completato

---

### Popup: AMORE ❤️
- Sfondo: rosa/fucsia
- Titolo: "Decidi la fidanzata di Petta 💕"
- Griglia 5 immagini celebrity (celeb1.jpg … celeb5.jpg)
- Click su una celebrity:
  - Testo "Buona scelta! 😏"
  - Mostra `assets/gif-malizioso.gif`
  - Bottone "Conferma" → segna hotspot completato

---

### Popup: SPORT ⚽
- Sfondo: arancio
- Titolo: "La mossa leggendaria"
- Video player con `assets/video-sport.mp4` (autoplay muted, controlli visibili)
- Testo: "Alex e Iuri non ci sono riusciti 😂"
- Mostra `assets/gif-atleta.gif`
- Bottone "Chiudi" → segna hotspot completato

---

### Popup: SOLDI 💰
- Sfondo: verde banconota
- Testo grande: "Ti piacerebbe scemoooooo 🤑"
- Mostra `assets/gif-povero.gif`
- Bottone "Chiudi" → segna hotspot completato

---

## Screen 3 — Fine

- Sfondo: nero
- Testo grande: "Gioco finito 🎮"
- Sottotitolo: "Ora si può tornare a lavorare"
- Mostra `assets/gif-piange.gif`
- Nessun bottone (fine del gioco)

---

## State Management

```js
// Stato globale in JS
const state = {
  selectedAvatar: null,        // 'angela' | 'alex' | 'iuri' | 'alessia'
  completedPopups: new Set(),  // es. Set(['paura', 'amore'])
  avatarPos: { x: 400, y: 300 }
}
// Quando completedPopups.size === 4 → mostra schermata finale
```

---

## Technical Notes

- Tutto in un singolo `index.html` (CSS e JS embedded nei tag `<style>` e `<script>`)
- Movimento avatar: `requestAnimationFrame` loop con `keydown`/`keyup` listener
- Collisione hotspot: distanza euclidea tra posizione avatar e centro hotspot
- Popup: `display: none` / `display: flex` toggle con classe CSS `.active`
- Mobile: layout responsivo, swipe gesture per movimento (opzionale, low priority)
- Placeholder fallback: se un asset non è presente, mostra un box colorato con testo descrittivo

---

## Out of Scope

- Salvataggio progressi (no localStorage)
- Multiplayer
- Suono / musica di sottofondo
- Animazioni sprite walk cycle
