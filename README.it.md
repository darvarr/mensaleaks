# SchoolPuzzle

[English](README.md) · **Italiano**

_Il puzzle della scuola, risolto ogni mattina._

SchoolPuzzle è una piccola Progressive Web App offline che risponde alle tre domande di ogni genitore:
**cosa mangia oggi? Domani c'è scuola? Quando c'è la riunione?**

Funziona su Android, iPhone e qualsiasi browser da computer. Non ha backend, account o chiavi API.
Il repository contiene solo il codice dell'app: menù della mensa e calendario scolastico si importano su ogni
telefono e non lasciano mai il dispositivo.

## Funzioni

- **Giorno**: il menù di oggi (nel weekend quello di lunedì), con allergeni e prodotti surgelati.
  Sopra il menù compaiono gli eventi del giorno (riunioni, feste, uscita anticipata). Nei giorni di chiusura
  il menù lascia il posto a "Scuola chiusa". In fondo, il riquadro **In arrivo** mostra le prossime chiusure,
  uscite anticipate e riunioni.
- **Settimana**: i cinque giorni di scuola insieme, con le chiusure in evidenza.
- **Calendario**: chiusure, uscite anticipate, riunioni, colloqui, feste ed eventi raggruppati per mese, con filtri.
  Gli eventi passati sono nascosti.
- **Rotazione del menù**: il menù si ripete su N settimane (di solito 4). All'import indichi in che settimana siete,
  al resto pensa l'app. Con −/+ correggi la rotazione se la mensa salta una settimana.
- **Profilo bambino** (⚙): scegli gruppo e sezione (es. "Piccoli · Gialli") per nascondere colloqui e avvisi
  delle altre classi.
- **Calendario del telefono** (⚙ → .ics): esporta gli eventi futuri, con un promemoria la sera prima di chiusure,
  uscite anticipate e riunioni. Su iPhone il file si apre direttamente; su Android conviene importarlo da
  Google Calendar sul web (Impostazioni → Importa).
- **Condividi con un altro telefono** (⚙): invia in un solo messaggio (WhatsApp, SMS, email…) menù, settimana
  corrente, calendario e profilo. Sull'altro telefono: ⚙ → Importa → incolla tutto il messaggio.
- **Offline**: una volta installata, l'app funziona senza connessione. Tema chiaro e scuro.

## Privacy

Menù, calendario e impostazioni restano nella memoria del browser di ogni dispositivo (`localStorage`).
Niente viene inviato a server. GitHub Pages serve solo i file statici dell'app. Tieni i file JSON della tua scuola
**fuori da questo repository**: di solito contengono nome e indirizzo della scuola.

## Pubblicazione su GitHub Pages

1. Metti `index.html`, `sw.js`, `manifest.webmanifest` e `icons/` nella **radice** del repository.
2. Settings → Pages → Source: **Deploy from a branch** → `main` / `(root)` → Save.
3. Usa l'indirizzo indicato in Settings → Pages ("Your site is live at `https://<utente>.github.io/<repo>/`").

**Aggiornamenti:** ogni volta che modifichi un file, aumenta `VERSION` in `sw.js`. Le app installate scaricano
la nuova versione alla successiva apertura e la usano dall'avvio seguente.

## Installazione sul telefono

- **Android**: apri il link in Chrome → "Installa app".
- **iPhone**: apri il link in **Safari** → Condividi → "Aggiungi alla schermata Home".
  Importa i dati **dall'app aperta dalla Home**, non dalla scheda di Safari: su iOS hanno memorie separate.

## Importare i dati

⚙ → **Importa**, poi scegli un file o incolla il testo. L'app riconosce da sola il contenuto:

| Contenuto | Riconosciuto da | Dopo l'import |
|---|---|---|
| Menù della mensa | `settimane` | chiede in quale settimana della rotazione siete |
| Calendario scolastico | `eventi` | apre le impostazioni per scegliere gruppo e sezione |
| Messaggio da un altro telefono, o backup | `mensaleaks` | ripristina tutto com'era |

Il testo attorno al JSON (un messaggio di chat, i ``` del Markdown) viene ignorato: puoi incollare la risposta
di un'AI così com'è.

Il flusso consigliato: fotografa il menù o il calendario cartaceo, mandalo a un assistente AI con uno dei
prompt qui sotto, controlla il JSON e importalo. Due volte l'anno per il menù, una volta l'anno per il calendario.

### Prompt: menù della mensa (allega la foto)

```
Trascrivi questo menù scolastico in JSON. Restituisci solo il JSON, con esattamente questo schema:
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

### Prompt: calendario scolastico (allega PDF o foto)

```
Trascrivi questo calendario scolastico in JSON. Restituisci solo il JSON, con questo schema:
{
  "tipo": "calendario",
  "versione": 1,
  "nome": "<titolo, anno scolastico e scuola>",
  "periodo": {"inizio": "AAAA-MM-GG", "fine": "AAAA-MM-GG"},   // primo e ultimo giorno di scuola
  "gruppi": ["Piccoli", "Mezzani", "Grandi"],                  // solo se il calendario li nomina
  "sezioni": ["Verdi", "Gialli", "Blu"],                       // solo se il calendario le nomina
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
  anche su feste o altri eventi), "gruppo" e "sezione" (stringa o lista, solo se l'evento riguarda alcuni gruppi
  o sezioni), "note", "da_confermare": true se il calendario dice "da decidere" o "da confermare".
Regole: "Ultimo giorno di scuola ... uscita ore 13" diventa tipo "uscita" con "uscita": "13:00".
I periodi di vacanza ("dal 23 dicembre al 6 gennaio") diventano un solo evento "chiusura" con "data" e "fine".
Controlla che il giorno della settimana scritto corrisponda alla data; se non corrisponde, segnalalo nelle "note".
Ordina gli eventi per data.
```

## Il formato dei dati in breve

**Menù**: `settimane[]` → `giorni` (`lun`…`ven`) → portate `{nome, allergeni[], surgelato, oppure?}`,
più `allergeni` (legenda) e `note`.

**Calendario**: `eventi[]` → `{data, fine?, mese?, titolo, tipo, ora?, uscita?, gruppo?, sezione?, note?, da_confermare?}`,
più `periodo`, `gruppi`, `sezioni`. I giorni fuori da `periodo` risultano chiusi.

## Note tecniche

- Un solo `index.html` (JavaScript puro, senza build né dipendenze), un service worker cache-first (`sw.js`)
  e un web app manifest.
- La chiave di memoria `menuMensa.v1` e il marcatore di condivisione `mensaleaks` vengono dalla prima versione
  dell'app (MensaLeaks): restano così perché le installazioni esistenti e i messaggi già inviati continuino a funzionare.
- Per rinominare l'app cambia `APP` in `index.html`, i tag `<title>` e `apple-mobile-web-app-title`, e
  `manifest.webmanifest`.

## Licenza

MIT, vedi [LICENSE](LICENSE).
