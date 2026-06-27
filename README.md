# Kalkulator Folii 3M 2080

Kalkulator ceny folii samochodowej 3M seria 2080 dla sprzedaży na Allegro / eBay.

**Live:** https://artprintgg.github.io/kalkulator-3m-2080/

---

## Co robi

Klient wybiera kolor folii i długość → kalkulator wylicza cenę i liczbę jednostek Allegro → klient kopiuje dane zamówienia i przechodzi do oferty.

---

## Logika cenowa

| Parametr | Wartość |
|---|---|
| Szerokość rolki | 152 cm (stała) |
| Cięcie | co 10 cm |
| VAT | 23% |
| Jednostka Allegro | 3,00 PLN |

**Formuła:**
```
cena_netto = priceM2_netto × 1,524 m × (długość_cm / 100)
cena_brutto = cena_netto × 1,23
jednostki = ceil(cena_brutto / 3,00)
```

Klient zawsze płaci za pełne mb przy szerokości 152 cm — niezależnie od wybranej szerokości.

---

## Grupy cenowe (Integart cennik)

| Grupa | Kody | Cena netto/m² |
|---|---|---|
| Standard | G\*, M\*, S\* (pozostałe) | 81,58 zł |
| Pearl Premium | GP240, GP272, GP282, DM12, S271, S327, S335, S347, S363, SP10, SP240, SP242, SP273 | 97,51 zł |
| High Gloss | HG\* | 118,28 zł |
| 1080 Pearl | GP258 (seria 1080) | 104,64 zł |
| Flip | SP236, SP280, SP281, GP278, GP281 | 157,19 zł |
| Carbon / Texture / Brushed | CFS12, CFS201, SB12, MX12, BR120, BR201, BR212, BR230 | 160,20 zł |

---

## Przepływ zamówienia

```
1. Klient wybiera kolor (strzałki ← →  lub "Przeglądaj wszystkie kolory")
2. Klient wpisuje długość (lub suwak / przyciski preset)
3. Kalkulator wyświetla: cena brutto + liczba jednostek
4. Niebieski box pokazuje tekst do skopiowania:
   "3M 2080 Blue Metallic G363 Połysk 140x152 cm · 47 szt."
5. Klient klika ikonę kopiowania → tekst trafia do schowka
6. Klient klika "Zamów" → otwiera się oferta Allegro
7. W koszyku Allegro:
   - Pole "Ilość" → wpisuje liczbę jednostek (widoczna w kalkulatorze)
   - Pole "Uwagi do zakupu" → wkleja skopiowany tekst
```

---

## Oferta Allegro

https://allegro.pl/oferta/folia-samochodowa-3m-2080-na-wymiar-18688557855

---

## Struktura pliku

Jeden samodzielny plik HTML (`index.html`) — brak zależności npm, brak build step.

Bazuje na bundlze [Claude Design](https://claude.ai/design) z frameworkiem PetiteWue (reactive bindings `{{ }}`).

### Kluczowe metody JS

| Metoda | Co robi |
|---|---|
| `calculatePrice(code, length_cm, currency)` | Zwraca `{ units, price, unitPrice }` — formuła liniowa |
| `buildClipboardText()` | Buduje tekst do schowka: kolor + kod + wykończenie + wymiar + szt. |
| `copyInfo()` | Kopiuje tekst do schowka + toast "Skopiowano" |
| `confirmOrder()` | Otwiera ofertę Allegro w nowej karcie |
| `getGroupForCode(code)` | Mapuje kod koloru → grupę cenową |
| `marketplaceUrlFor()` | Zwraca stały URL oferty Allegro |

### Zmienne konfiguracyjne

```javascript
UNIT_PRICE_PLN = 3.00      // cena 1 jednostki Allegro
VAT_RATE = 0.23            // 23% VAT
ROLL_WIDTH_M = 1.524       // szerokość rolki w metrach
MIN_LENGTH_CM = 10         // minimalne cięcie
MAX_LENGTH_CM = 2286       // pełna rolka (22,86 m)
```

---

## Aktualizacja cen

Edytuj `GROUPS` w `index.html` (lub uruchom `fix_bundle.js`):

```javascript
GROUPS = {
  STANDARD:               { label:'Standard',                  mult:1.000, priceM2:81.58  },
  PEARL_PREMIUM:          { label:'Pearl Premium',             mult:1.195, priceM2:97.51  },
  HG:                     { label:'High Gloss',                mult:1.450, priceM2:118.28 },
  FLIP:                   { label:'Flip',                      mult:1.927, priceM2:157.19 },
  CARBON_TEXTURE_BRUSHED: { label:'Carbon / Texture / Brushed',mult:1.963, priceM2:160.20},
  WRAP_1080_PEARL:        { label:'1080 Pearl',                mult:1.283, priceM2:104.64 },
};
```

---

## Deployment

### GitHub Pages (obecny)

```bash
# po zmianach w index.html:
git add index.html
git commit -m "opis zmian"
git push
# strona aktualizuje się po ~2 minutach
```

### Docelowy serwer (vinyl.print.gg)

1. Utwórz subdomenę `vinyl.print.gg` w panelu DNS
2. Skieruj na folder publiczny
3. Wgraj `index.html`
4. Włącz SSL/HTTPS

---

## Narzędzia

- [`fix_bundle.js`](fix_bundle.js) — skrypt Node.js do aktualizacji bundla (ceny, layout, logika) bez ręcznej edycji zakodowanego JSON
- Źródło cennika: `3M 2080 Integart cennik - Arkusz1.csv`
- Baza wizualna: Claude Design `Konfigurator Folii 3M 2080.dc.html`

---

## Planowane

- [ ] Weryfikacja zamówień (BaseLinker webhook → Cloudflare Worker)
- [ ] Wdrożenie na vinyl.print.gg
- [ ] Wersja eBay (DE/EN)
