# Fuffaguru_AVS

**Simulazione di un Fondo Popolare Sovrano italiano — un esercizio di *steelman*.**

Il progetto prende sul serio l'idea, circolata nel dibattito politico, di un fondo sovrano
alimentato da tre fonti fiscali (recupero dell'evasione, imposta patrimoniale, imposta di
successione sulle grandi imprese) che investe in partecipate pubbliche e BTP, e che — se le cose
vanno meglio del previsto — distribuisce un **dividendo sociale**.

`AVS` sta per **Alleanza Verdi Sinistra**: il nome è uno sfottò affettuoso, ma il contenuto è
serio. La proposta viene implementata nella versione **più generosa e più competente possibile**,
e poi sono i numeri a dire cosa regge. Non è il programma di AVS: è una sua ricostruzione
stilizzata, con parametri dichiarati e modificabili.

## Cosa c'è dentro

Un unico notebook autosufficiente, `notebooks/01_fondo_sovrano.ipynb`, in 17 sezioni:

| § | Contenuto |
|---|---|
| 1–2 | Setup, pannello di controllo (~80 parametri), architettura, glossario |
| 3 | Le tre fonti: **RCF**, **IPP**, **ISA**, con i conti in chiaro |
| 4 | Dinamica delle entrate: shock ±20 %, catena di Markov dei regimi politici |
| 5–6 | Paniere di 8 partecipate, correlazioni, BTP con duration |
| 7 | La ricorsione del fondo, anno per anno |
| 8–9 | **MCMC** (`emcee`) e risultati a 10/20/30/50 anni |
| 10 | **Benchmark di nazionalizzazione**: ENI, ENEL, Poste, TIM rete, Snam, Terna, Leonardo, FS |
| 11 | **Dividendo sociale**: quanto vale, a chi va, cosa costa |
| 12–13 | Sensibilità (tornado), scenari |
| 14–15 | Limiti, onestà contabile, export |
| 16–17 | Riassunto in una schermata, conclusioni |

## Come si esegue

```bash
pip install -r requirements.txt
cd notebooks
jupyter lab 01_fondo_sovrano.ipynb     # oppure: jupyter notebook
```

Oppure da riga di comando, senza aprire l'interfaccia:

```bash
cd notebooks
MPLBACKEND=Agg jupyter nbconvert --to notebook --execute --inplace 01_fondo_sovrano.ipynb
```

Il notebook funziona anche lanciato dalla root del progetto: i percorsi vengono risolti
automaticamente. Tutte le manopole stanno in un solo blocco (la dataclass `Config`, §1): cambia un
numero, riesegui da lì in giù, e ogni tabella e grafico si aggiorna.

## Cosa produce

- `figures/` — 12 PNG (ventagli, tornado, benchmark, dividendo, scenari…)
- `results/` — 7 CSV (quantili della dotazione, dividendo, costi dei benchmark, parametri,
  diagnostica MCMC, scenari)
- `data/mkt_snapshot.csv` — la serie storica di rendimenti usata come likelihood. Sono
  ricostruzioni **rozze e dichiarate**: sostituiscile con dati veri e il modello migliora gratis.

## Numeri chiave (configurazione di default)

| Grandezza | Valore |
|---|---|
| Dote iniziale `F₀` | ≈ 17,4 mld €/anno (RCF 10,0 · IPP 5,8 · ISA 1,5) |
| Dotazione mediana, € reali 2026 | ≈ 205 mld (10 anni) · ≈ 435 (20) · ≈ 715 (30) |
| Pacchetto energia (ENI+ENEL+Snam+Terna) | accessibile all'anno ≈ 7 |
| Tutti e otto gli obiettivi | accessibile all'anno ≈ 11 |
| Dividendo sociale pro capite al 30° anno | media ≈ 150 €/anno, **mediana 0 €** |
| Valore netto creato / attivo lordo | ≈ 33 % |

L'ultima riga è il punto del progetto: **due terzi del numero grande non sono ricchezza nuova**,
sono gettito dirottato e asset comprati da privati.

## Come leggerlo

- Tutti gli importi sono in **miliardi di euro (mld €)**, salvo dove indicato.
- Il risultato principale è in **euro reali 2026**; il nominale è sempre disponibile come secondo
  pannello, perché a 30 anni la differenza è enorme.
- Ogni risultato è una **distribuzione**, non un numero: si riportano mediana e intervalli 5-95 %.
- L'MCMC gira sui parametri di mercato (dove ci sono dati); i parametri fiscali sono campionati
  dal **prior dichiarato** (dove non ci sono dati). La distinzione è esplicita nella §8.

## Requisiti

Vedi `requirements.txt`. Dipendenze minime: `numpy`, `pandas`, `matplotlib`, `scipy`.
`emcee` è opzionale: se manca, il notebook usa un Metropolis-Hastings adattivo interno con la
stessa interfaccia.

Testato con Python 3.14, numpy 2.5, pandas 3.1, matplotlib 3.11, scipy 1.18, emcee 3.1.6.

## Avvertenze

- **Non è una previsione** né una consulenza finanziaria. È un modello didattico con parametri
  rozzi, ognuno etichettato con la propria fonte e con un `TODO: raffinare`.
- Le stime di gettito fiscale sono **ordini di grandezza**, non stime ufficiali.
- Il modello **non** tiene conto delle regole UE su aiuti di Stato e bilancio, che nella realtà
  morderebbero parecchio (§14).
- Se i numeri sembrano troppo belli, controlla di aver lasciato `mercati.alpha_mode = "t_student"`.
