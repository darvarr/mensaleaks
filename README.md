# MensaLeaks

_Fughe di notizie dal refettorio._ PWA offline

App web installabile (Android, iPhone, PC/Mac) che mostra il menù della mensa del giorno.
Nessun backend, nessuna chiave: i dati restano sul dispositivo.

## Pubblicazione su GitHub Pages (una volta)
1. Crea un repository `mensaleaks` e carica tutti i file di questa cartella.
2. Settings → Pages → Source: "Deploy from a branch" → `main` / root → Save.
3. Dopo un minuto l'app è su `https://<utente>.github.io/mensaleaks/`.

## Installazione
- **Android**: apri il link in Chrome → "Installa app" (o menu ⋮ → Aggiungi a schermata Home).
- **iPhone**: apri il link in **Safari** → Condividi → "Aggiungi alla schermata Home".

## Privacy
Il repository contiene solo il codice: nessun menù, nessun dato. Il menù importato resta nella memoria del telefono.
Su iPhone importa il menù dall'app aperta dalla schermata Home (Safari e l'app hanno memorie separate).

## Nuovo menù (due volte l'anno)
1. Fai la foto della tabella.
2. Mandala a un assistente AI con il prompt qui sotto, controlla il JSON.
3. Nell'app: ⚙ → Importa un nuovo menù → scegli il file o incolla il testo → indica in che settimana siamo.

Altri telefoni: ⚙ → "Condividi con un altro telefono" → invia il messaggio (WhatsApp, Messaggi…).
Sull'altro telefono: apri MensaLeaks → ⚙ → Importa → incolla tutto il messaggio. La settimana è già impostata.

Se una vacanza fa slittare la rotazione: ⚙ → Settimana corrente → − / +.

### Prompt
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

## Aggiornare l'app
Se modifichi i file, incrementa `VERSION` in `sw.js`: i telefoni scaricheranno la nuova versione alla riapertura.
