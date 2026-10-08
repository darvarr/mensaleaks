# SchoolPuzzle

_Il puzzle della scuola, risolto ogni mattina._ Una PWA offline che risponde alle tre domande di ogni genitore:
**cosa mangia oggi? domani c'è scuola? quando c'è la riunione?**

Funziona su Android, iPhone e PC. Non ha backend e non usa chiavi: il repository contiene solo il codice,
mentre menù e calendario restano nella memoria del telefono.

## Cosa fa
- **Giorno**: menù di oggi (nel weekend quello di lunedì), con allergeni e surgelati. Sopra il menù compaiono gli eventi del giorno
  (riunioni, feste, uscita anticipata). Nei giorni di chiusura il menù lascia il posto a "Scuola chiusa".
  Sotto c'è "In arrivo", con le prossime chiusure, uscite e riunioni.
- **Settimana**: i 5 giorni insieme, con le chiusure in evidenza.
- **Calendario**: l'elenco per mese di chiusure, uscite anticipate, riunioni, colloqui, feste ed eventi, con i filtri.
- **Profilo bambino** (⚙): scegli gruppo e sezione, ad esempio Piccoli · Gialli, e vedi solo i colloqui che ti riguardano.
- **Calendario del telefono** (⚙ → .ics): esporta gli eventi futuri con un promemoria la sera prima di chiusure,
  uscite anticipate e riunioni. Su iPhone il file si apre direttamente; su Android conviene importarlo da Google Calendar sul web
  (Impostazioni → Importa).
- **Condividi con un altro telefono** (⚙): invia in un messaggio menù, settimana corrente, calendario e profilo.
  Sull'altro telefono: ⚙ → Importa → incolla tutto il messaggio.

## Pubblicazione su GitHub Pages
1. Carica nella radice del repository `schoolpuzzle` i file di questa cartella: `index.html`, `sw.js`, `manifest.webmanifest`, `icons/`, `README.md`.
2. Settings → Pages → Source "Deploy from a branch" → `main` / `(root)` → Save.
3. Il link è quello indicato in Settings → Pages ("Your site is live at …").

Quando modifichi qualcosa, aumenta `VERSION` in `sw.js`: i telefoni si aggiornano alla riapertura.

## Installazione
- **Android**: apri il link in Chrome → "Installa app".
- **iPhone**: apri il link in **Safari** → Condividi → "Aggiungi alla schermata Home".
  Importa i dati **dall'app aperta dalla Home** (Safari e l'app hanno memorie separate).

## Importare i dati
⚙ → Importa: puoi scegliere un file o incollare il testo. L'app riconosce da sola se si tratta di un menù, di un calendario o di un messaggio condiviso.

- **Menù** (due volte l'anno): dopo l'import l'app ti chiede in che settimana siete.
- **Calendario** (una volta l'anno): dopo l'import si aprono le impostazioni per scegliere gruppo e sezione.

### Prompt per il menù (allega la foto)
```
Trascrivi questo menù scolastico in JSON, restituendo solo il JSON, con questo schema:
{
  "versione": 1,
  "nome": "<titolo del menù e scuola>",
  "allergeni": {"1": "Glutine", ...},          // legenda completa a piè di pagina
  "note": ["<note a piè di pagina>", ...],
  "settimane": [
    {"numero": 1, "giorni": {
      "lun": [{"nome": "Riso e prezzemolo", "allergeni": [3, 7], "surgelato": false}, ...],
      "mar": [...], "mer": [...], "gio": [...], "ven": [...]
    }}, ...
  ]
}
Regole: una voce per ogni portata, nell'ordine della tabella, frutta inclusa.
Nome con sola iniziale maiuscola e senza asterisco; "surgelato": true se il piatto ha l'asterisco.
"allergeni": i numeri riportati sotto il piatto (lista vuota se assenti).
Se un piatto ha un'alternativa ("O ..."), aggiungi "oppure": {"nome": "...", "allergeni": [...]}.
```

### Prompt per il calendario scolastico (allega PDF o foto)
```
Trascrivi questo calendario scolastico in JSON, restituendo solo il JSON, con questo schema:
{
  "tipo": "calendario",
  "versione": 1,
  "nome": "<titolo, anno scolastico e scuola>",
  "periodo": {"inizio": "AAAA-MM-GG", "fine": "AAAA-MM-GG"},   // primo e ultimo giorno di scuola
  "gruppi": ["Piccoli", "Mezzani", "Grandi"],                  // se il calendario li nomina
  "sezioni": ["Verdi", "Gialli", "Blu"],                       // se il calendario le nomina
  "eventi": [
    {"data": "AAAA-MM-GG", "titolo": "...", "tipo": "..."}
  ]
}
Campi di ogni evento:
- "data" (obbligatoria) e "fine" (solo per eventi di più giorni, inclusa), formato AAAA-MM-GG.
  Se la data non è ancora fissata usa "data": null e "mese": "AAAA-MM".
- "tipo", uno tra:
  "chiusura"  = scuola chiusa (vacanze, festività, ponti, festa patronale)
  "uscita"    = giorno di scuola con uscita anticipata
  "riunione"  = riunioni genitori, di sezione, sportello genitori
  "colloquio" = colloqui individuali
  "festa"     = feste e ricorrenze con i bambini
  "evento"    = open day, giornate a tema, eventi esterni, rientri
  "orario"    = inserimenti e cambi di orario
- opzionali: "ora" (es. "17:00", "9:30-11:30", "dalle 13:00"), "uscita" (orario di uscita anticipata, es. "13:00",
  anche su feste o altri eventi), "gruppo" e "sezione" (stringa o lista, solo se l'evento riguarda alcuni gruppi o sezioni),
  "note", "da_confermare": true se il calendario dice "da decidere" o "da confermare".
Regole: "Ultimo giorno di scuola ... uscita ore 13" diventa tipo "uscita" con "uscita": "13:00".
I periodi di vacanza ("dal 23 dicembre al 6 gennaio") diventano un solo evento "chiusura" con "data" e "fine".
Controlla che il giorno della settimana scritto corrisponda alla data; se non corrisponde, segnalalo nelle "note".
Ordina gli eventi per data.
```
