# Fuffaguru_AVS

**Simulazione di un Fondo Popolare Sovrano italiano che diventa una holding di controllo —
un esercizio di *steelman*.**

Il progetto prende sul serio l'idea, circolata nel dibattito politico, di un fondo sovrano
alimentato da tre fonti fiscali (recupero dell'evasione, imposta patrimoniale, imposta di
successione sulle grandi imprese) con un obiettivo esplicito: **portare sotto controllo pubblico
il 51 % delle principali partecipate italiane**, e — solo dove conviene — il 100 %.

`AVS` sta per **Alleanza Verdi Sinistra**: il nome è uno sfottò affettuoso, ma il contenuto è
serio. La proposta viene implementata nella versione **più generosa e più competente possibile**,
e poi sono i numeri a dire cosa regge. Non è il programma di AVS: è una sua ricostruzione
stilizzata, con parametri dichiarati e modificabili.

## La tesi in una riga

**Il controllo si paga, non si moltiplica.** A 30 anni la stessa cassa, tenuta in BTP e senza
comprare niente, vale **620 mld**; la holding che compra il 51 % di tutto vale **514 mld**. La
differenza (−106 mld, −17 %) è il prezzo del controllo: premio pagato ai privati, dividendi non
incassati, rischio sopportato. Il notebook è l'unico posto dove questa frase ha un numero accanto.

## Cosa c'è dentro

Un unico notebook autosufficiente, `notebooks/01_fondo_sovrano.ipynb`, in 17 sezioni:

| § | Contenuto |
|---|---|
| 1–2 | Setup, pannello di controllo (88 parametri), architettura, glossario |
| 3 | Le tre fonti: **RCF**, **IPP**, **ISA**, con i conti in chiaro |
| 4 | Dinamica delle entrate: shock ±20 %, catena di Markov dei regimi politici |
| 5 | **L'universo di acquisto**: 8 target, chi comprare, quanto costa, chi paga il premio |
| 6 | La riserva liquida: **curva dei tassi** a 4 scadenze, tetto di duration |
| 7 | **La macchina della holding**: stato, regole di acquisto, percorso anno per anno |
| 8–9 | **MCMC** (`emcee`) e risultati a 10/20/30/50 anni |
| 10 | **Il calendario del controllo**: anno mediano di acquisizione, costo di ILVA |
| 11 | **Dividendo sociale**: quanto vale, a chi va, cosa costa |
| 12–13 | Sensibilità (tornado), scenari |
| 14–15 | Limiti, onestà contabile, export |
| 16–17 | Riassunto in una schermata, conclusioni |

Il fondo compra **solo il pezzo che manca** (target − quota già pubblica), **una società all'anno**,
e solo se dopo l'acquisto resta almeno metà del NAV liquido. Il premio di controllo è contabilizzato
come **perdita immediata**: l'attivo entra a valore di mercato, non a prezzo di acquisto.

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
automaticamente. Tutte le manopole stanno in un solo blocco (le dataclass in §3): cambia un numero,
riesegui da lì in giù, e ogni tabella e grafico si aggiorna.

## Cosa produce

- `figures/` — 12 PNG (ventagli, curva dei tassi, calendario del controllo, tornado, scenari…)
- `results/` — 14 CSV (target e costi, calendario, cascata degli acquisti, ILVA, quantili della
  dotazione, dividendo, tornado, payoff, parametri, diagnostica MCMC, scenari)
- `data/mkt_snapshot.csv` — la serie storica di rendimenti usata come likelihood. Sono
  ricostruzioni **rozze e dichiarate**: sostituiscile con dati veri e il modello migliora gratis.

## Numeri chiave (configurazione di default)

| Grandezza | Valore |
|---|---|
| Dote iniziale `F₀` | ≈ 17,4 mld €/anno (RCF 10,0 · IPP 5,8 · ISA 1,5) |
| NAV mediano, € reali 2026 | ≈ 158 mld (10 anni) · ≈ 319 (20) · ≈ 514 (30) |
| Controfattuale «stesse entrate, solo BTP» | ≈ 620 mld a 30 anni → **−106 mld (−17 %)** |
| Costo del perimetro 51 % (prezzi di oggi) | **104 mld** (26 % della capitalizzazione delle quotate) |
| Perimetro completo | anno mediano **10** (P10 7 · P90 16), P(≤20 anni) 96 % |
| Fase 2 (residuo al 100 %) | anno mediano 26, P(entro 30 anni) 59 % |
| Premi di controllo pagati in 30 anni | ≈ **100 mld** (valore trasferito ai privati) |
| Dividendo sociale pro capite al 30° anno | media ≈ 134 €/anno, **mediana 0 €** |
| Mondi in cui il dividendo paga almeno una volta | 88 % (cumulato mediano 1.181 €/ab in 30 anni) |

Le ultime righe sono il punto del progetto: il NAV che fa scena **non è ricchezza nuova**, è
ricchezza **pubblica** — e il prezzo per ottenerla si paga ai privati, in contanti, subito.

## Come leggerlo

- Tutti gli importi sono in **miliardi di euro (mld €)**, salvo dove indicato.
- Il risultato principale è in **euro reali 2026**; il nominale è sempre disponibile come secondo
  pannello, perché a 30 anni la differenza è enorme.
- Ogni risultato è una **distribuzione**, non un numero: si riportano mediana e intervalli 5-95 %.
- L'MCMC gira sui parametri di mercato (dove ci sono dati); i parametri fiscali sono campionati
  dal **prior dichiarato** (dove non ci sono dati). La distinzione è esplicita nella §8.
- I dividendi sulle quote **già pubbliche** restano al bilancio dello Stato (voce *memo*): non sono
  entrate del fondo, e contarle come tali sarebbe un doppio conteggio.

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
- Il caso ILVA è modellato come salvataggio (14 mld, 40 % recuperabile, zero dividendi per 5-7
  anni): è probabilmente la stima più incerta del notebook.
- Se i numeri sembrano troppo belli, controlla di aver lasciato `mercati.alpha_mode = "t_student"`.
