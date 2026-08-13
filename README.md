# Calcolatore Stipendio Netto — Prototipo (Jet HR · Product Builder Task)

Prototipo funzionante che simula la proiezione della **retribuzione netta annuale** a partire da una **RAL** (retribuzione annua lorda), mostrando tutte le voci trattenute al lordo.

- **Input:** RAL annuale (€)
- **Output:** netto annuale, netto mensile, dettaglio di tutte le trattenute, IRPEF per scaglioni

## Come usarlo

Aprire `index.html` in qualsiasi browser (nessuna dipendenza, nessun build step — HTML/CSS/JS vanilla). Il calcolo parte automaticamente con RAL = 30.000 €.

## Logica di calcolo (anno d'imposta di riferimento: 2025)

1. **Contributi INPS a carico del dipendente**
   - 9,19% sull'imponibile previdenziale fino a 55.448 € (soglia 2025)
   - 10,19% (9,19% + 1%) sulla parte eccedente
2. **Imponibile IRPEF** = RAL − contributi INPS
3. **IRPEF lorda** — scaglioni 2025:
   | Reddito imponibile | Aliquota |
   |---|---|
   | fino a 28.000 € | 23% |
   | 28.001 – 50.000 € | 6.440 € + 35% sulla parte oltre 28.000 € |
   | oltre 50.000 € | 14.140 € + 43% sulla parte oltre 50.000 € |
4. **Detrazione lavoro dipendente** (rapportata all'anno pieno):
   - ≤ 15.000 € → 1.955 € (comunque non superiore all'imposta lorda — no tax area)
   - 15.001 – 28.000 € → 1.910 + 1.190 × (28.000 − reddito) / 13.000
   - 28.001 – 50.000 € → 1.910 × (50.000 − reddito) / 22.000, più **65 €** se reddito tra 25.001 e 35.000 €
   - oltre 50.000 € → 0
5. **Ulteriore detrazione (cuneo fiscale, LdB 2025):** 1.000 € fissi per redditi 20.001 – 32.000 €; decrescente 1.000 × (40.000 − reddito) / 8.000 per 32.001 – 40.000 €
6. **IRPEF netta** = IRPEF lorda − detrazione lavoro dipendente − ulteriore detrazione (mai negativa)
7. **Addizionale regionale Lombardia:** 1,23% sull'imponibile IRPEF
8. **Addizionale comunale Milano:** 0,8% sull'imponibile IRPEF
9. **Bonus cuneo fiscale (esente, redditi ≤ 20.000 €):** 7,1% fino a 8.500 € · 5,3% fino a 15.000 € · 4,8% fino a 20.000 €
10. **Trattamento integrativo:** 1.200 €/anno se reddito ≤ 15.000 € e IRPEF netta positiva
11. **Netto annuale** = RAL − (INPS + IRPEF netta + addizionali) + bonus + trattamento
12. **Netto mensile** = netto annuale / **13** mensilità (tredicesima inclusa)

## Assunzioni e semplificazioni

Profilo simulato (come richiesto dal task): impiegato a tempo indeterminato, residente a Milano, nessuna agevolazione particolare.

Semplificazioni adottate (discutibili in sede di colloquio):

| Semplificazione | Motivazione |
|---|---|
| Normativa di riferimento: anno 2025 | Regole consolidate e verificate; il prototipo non insegue variazioni annuali |
| Nessuna detrazione per familiari a carico | Il profilo "non ha nessun tipo di agevolazione particolare" |
| Addizionali in quota fissa (1,23% / 0,8%) | Ignorate soglie di esenzione e arrotondamenti comunali; Milano applica lo 0,8% ordinario |
| Detrazioni non rapportate ai giorni lavorati | Ipotesi di lavoro a tempo pieno per l'intero anno |
| TFR, contributi datoriali, ferie/permessi esclusi | Non incidono sul netto in busta; il focus è sulle trattenute |
| Bonus e detrazioni del cuneo fiscale inclusi | Sono misure generali 2025, non "agevolazioni particolari"; implementate perché impattano sensibilmente il netto nelle fasce comuni |
| Arrotondamenti al centesimo | Come da prassi |

## Fonti

- **Agenzia delle Entrate** — aliquote e calcolo IRPEF: [agenziaentrate.gov.it](https://www.agenziaentrate.gov.it/portale/web/english/personal-income-tax-irpef-rates-and-calculation)
- **Circolare Agenzia delle Entrate 4/E (16/05/2025)** — novità redditi lavoro dipendente, Legge di Bilancio 2025 (scaglioni, detrazioni, cuneo): sintesi su [Il Sole 24 Ore](https://en.ilsole24ore.com/art/irpef-circular-circular-revenue-deductions-here-is-all-the-news-on-irpef-AHJvbFq)
- **Legge di Bilancio 2025** (L. 30/12/2024, n. 207) — analisi individuali: [Lexology](https://www.lexology.com/library/detail.aspx?g=45c95802-c963-44ba-87f9-bbed03803b64)
- **INPS** — aliquota contributiva dipendente settore privato 9,19% e soglia prima fascia pensionabile: [TaxRavens — Social Contributions Italy](https://taxravens.com/en/italy/social-contributions), [Teamd — Italy Tax and Payroll](https://www.teamed.global/country-hiring-guides/italy/tax-and-payroll)
- **Addizionali locali** — quadro aliquote: [TaxAtlas — Italy Personal Income Tax Rates](https://taxatlas.io/country/italy/income-tax)

## Esempi di output (verifica)

| RAL | INPS | IRPEF netta | Totale trattenute | Netto annuale | Netto mensile (×13) |
|---|---|---|---|---|---|
| 20.000 € | 1.838 € | 1.367 € | 3.573 € | 17.298 € | 1.331 € |
| 30.000 € | 2.757 € | 3.287 € | 6.597 € | 23.403 € | 1.800 € |
| 40.000 € | 3.676 € | 7.707 € | 12.120 € | 27.880 € | 2.145 € |
| 60.000 € | 5.560 € | 16.049 € | 22.714 € | 37.286 € | 2.868 € |

## Nota

Prototipo didattico: non sostituisce il calcolo di una busta paga reale. Ogni semplificazione è voluta ed esplicitata qui sopra; in produzione il calcolo andrebbe parametrizzato per regione, comune, CCNL, giorni lavorati e normative dell'anno corrente.
