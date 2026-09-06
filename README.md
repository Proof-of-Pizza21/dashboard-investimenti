# Dashboard Investimenti

Una dashboard per il portafoglio personale in **un solo file HTML**: nessun server, nessuna dipendenza, nessun account. Si apre con doppio clic e i dati restano nel browser.

Pensata per un investitore italiano: le stime fiscali seguono il **regime amministrato** (scarico dei costi in LIFO, zainetto delle minusvalenze, aliquote e bolli configurabili).

> Questo file parte **vuoto**: gli strumenti, i lotti e i valori li inserisci tu dall'interfaccia.

## Cosa fa

- **Composizione** — due grafici a ciambella: per singolo asset e per categoria, broker o classe fiscale.
- **Andamento titoli** — lo storico del portafoglio da snapshot giornalieri, con periodi 1M / 3M / 6M / 1A / tutto e il TIR (rendimento annualizzato che tiene conto di versamenti e prelievi).
- **Posizioni** — quantità, prezzo medio di carico, prezzo attuale, valore, P/L, peso e TER per ogni strumento, più la liquidità non investita.
- **Ribilanciamento** — scostamento di ogni voce dal target, con due strade: ribilanciando con vendite oppure comprando soltanto.
- **Fiscale — stima se vendessi oggi** — imponibile e imposta per strumento, distinguendo redditi diversi e redditi di capitale.
- **Realizzato e zainetto fiscale** — plus/minusvalenze già realizzate e il registro cronologico delle minusvalenze compensabili, separate nei due panieri (titoli e cripto), con quelle in scadenza.
- **Costi** — quanto costa il portafoglio ogni anno ai valori di oggi (TER, bolli, commissioni) e quanto è costato dall'inizio.
- **Simulatore what-if** — sposta i prezzi di uno o più strumenti e guarda l'effetto su patrimonio, P/L e imposte.
- **Registro operazioni** — acquisti e vendite, con annullamento di una vendita che rimette a posto i lotti consumati.
- **Dati e backup** — export JSON completo, CSV di posizioni, operazioni e storico, reimport del backup, diagnostica delle fonti prezzi.
- **Pulsante occhio** — nasconde tutti gli importi in euro con un clic, per aprirla senza scoprire il portafoglio a chi passa. Le soglie e i parametri fissi restano leggibili.
- **Tema chiaro/scuro** e layout adattivo per telefono.

## Come si usa

1. Scarica `index.html`.
2. Aprilo con un browser (Chrome, Firefox, Safari, Edge — va bene anche `file://`, senza server).
3. **+ Posizione** per aggiungere uno strumento, **+ Operazione** per registrare un acquisto o una vendita.
4. La liquidità e i fondi pensione si scrivono direttamente nei campi sotto i grafici.

Volendo si può pubblicare su GitHub Pages e usarla dal telefono: essendo un file solo, funziona così com'è.

## Dove finiscono i dati

Tutto nel **`localStorage` del browser**, sotto la chiave `investimenti_v1` — non nel file HTML e da nessun'altra parte. Nessun dato viene inviato a un server: le uniche chiamate in uscita sono quelle per le quotazioni.

Le conseguenze pratiche:

- ogni browser (e ogni profilo) ha i suoi dati, non si sincronizzano da soli;
- **cancellare i dati del sito o navigare in incognito cancella tutto**;
- aggiornare `index.html` con una versione nuova **non** tocca i dati salvati;
- l'unica rete di sicurezza è il **Backup JSON** dalla sezione *Dati e backup*. Fanne uno ogni tanto.

## Quotazioni: da dove arrivano i prezzi

Il pulsante *Aggiorna prezzi* legge i valori da fonti pubbliche e gratuite:

| Cosa | Fonti, nell'ordine in cui vengono provate |
|---|---|
| Cripto detenute direttamente | **CoinGecko**, poi **Coinbase** |
| ETF ed ETP | **onvista**, **justETF**, **TradingView** |
| Cambio BTC/EUR (per gli strumenti legati al bitcoin) | **Coinbase**, poi **CoinGecko** |
| Storico fino a 180 giorni, per le sparkline e i grafici | **CoinGecko** per le cripto, **justETF** per gli ETF |

Alcune di queste non permettono la chiamata diretta dal browser (non espongono CORS): in quei casi la richiesta passa da un **proxy pubblico gratuito** — nell'ordine `cors.lol`, `cors.workers.dev`, `allorigins`, `codetabs` — e la pagina ricorda quale ha funzionato l'ultima volta per ripartire da lì.

Cosa viaggia in quelle richieste: l'**identificativo dello strumento** (ISIN, ticker o id CoinGecko) e, come in qualunque richiesta web, il tuo indirizzo IP. Mai quantità, importi o controvalori.

È un tentativo *best effort*, non un servizio garantito: nessuna di queste fonti ha un contratto con te, possono rispondere lentamente, sbagliare o sparire. Se una non risponde, il prezzo si scrive a mano e la dashboard funziona lo stesso; il pulsante **Diagnostica prezzi** mostra strumento per strumento quale fonte ha risposto e quale no.

## Fiscalità: cosa assume

I parametri stanno in un unico punto del codice (`DEFAULT_STATE.settings`) e si cambiano da lì:

| Voce | Valore di partenza |
|---|---|
| Aliquota su titoli, ETF, ETP | 26% |
| Aliquota su cripto detenute direttamente | 33% |
| Bollo sul dossier titoli | 0,2% annuo |
| Bollo sul conto corrente | 34,20 € sopra i 5.000 € di giacenza |

Il costo delle vendite viene scaricato in **LIFO** sui lotti effettivamente presenti, come fa un intermediario in regime amministrato. Il P/L che mostra il broker nel dettaglio della posizione è spesso calcolato sul prezzo medio di carico dell'intera posizione: su una vendita parziale i due numeri non coincidono, e non è un errore.

## Privacy: cosa esce da qui

Niente account, niente backend, niente analytics, niente cookie di terze parti. Quello che inserisci resta nel `localStorage` del tuo browser, sul tuo computer, e da lì non parte da nessuna parte.

L'unica cosa che esce sono le richieste per le quotazioni, quando premi *Aggiorna prezzi*: vanno alle fonti pubbliche elencate sopra e ai proxy CORS che fanno da tramite. Contengono l'**identificativo dello strumento** (ISIN, ticker, id CoinGecko) e, come qualunque richiesta web, il tuo indirizzo IP. Non contengono quantità, importi, controvalori né altro del portafoglio: chi sta dall'altra parte può dedurre *quali* strumenti segui, non *quanto* ne hai. Se anche questo ti dà fastidio, non premere il pulsante e scrivi i prezzi a mano — la dashboard funziona lo stesso.

Un'ultima cosa sul dominio: il `localStorage` appartiene all'indirizzo da cui apri la pagina. La stessa dashboard aperta da file locale, da GitHub Pages e da un altro sito ha tre archivi separati, che non si vedono fra loro. E su un computer condiviso i dati restano a disposizione di chi lo usa dopo di te.

## Disclaimer

Non è consulenza finanziaria né fiscale, e non sostituisce i conteggi del tuo intermediario.

- **Le stime fiscali sono indicative.** Servono a farsi un'idea prima di vendere: fa fede quello che calcola il tuo intermediario. Aliquote e bolli cambiano nel tempo — controlla che i parametri impostati siano ancora quelli giusti.
- **I prezzi possono essere vecchi o sbagliati.** Arrivano da fonti pubbliche gratuite, senza alcuna garanzia di correttezza o di continuità: un proxy che smette di rispondere o un valore fuori scala sono possibili. Verifica sempre prima di decidere qualcosa.
- **I tuoi dati li custodisci tu.** Nessuno ne ha una copia. Se cancelli i dati del sito, cambi browser o formatti il computer, spariscono: l'unico backup è quello che fai tu.
- **Nessuna garanzia.** Il programma è fornito "così com'è", senza garanzia di alcun tipo, secondo le sezioni 15 e 16 della licenza AGPL-3.0.

In breve: non dovrebbero esserci problemi, ma **se decidi di usarla lo fai sotto la tua responsabilità**. L'autore non risponde di perdite di dati, errori di calcolo o decisioni di investimento prese sulla base di quello che questa pagina mostra.

## Licenza

**GNU AGPL-3.0** © 2026 Proof-of-Pizza21 — vedi [LICENSE](LICENSE).

In breve: puoi usarla, studiarla e modificarla liberamente, ma se distribuisci una versione modificata — **anche solo mettendola online perché altri la usino** — devi pubblicarne il codice sorgente con la stessa licenza.
