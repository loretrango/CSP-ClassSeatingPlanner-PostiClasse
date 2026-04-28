# Disposizione posti in classe — v8

Applicazione web (single-file HTML) per generare automaticamente la disposizione degli alunni in aula rispettando vincoli di separazione e vicinanza.

Ad esempio, è possibile definire un gruppo di alunni che non devono, per qualche motivo, stare vicini. In modo analogo, è possiblie definire che un alunno debba stare obbligatoriamente vicino ad alcuni alunni (potrebbe essere il caso di un alunno straniero, che deve essere affiancato ad alunni che parlano la sua stessa lingua).

Autore: **Lorenzo Antiga** — lorenzo.antiga@gmail.com

![Alt text](assets/image4.png)
---

## Funzionalità principali

- **Generazione automatica** con algoritmo ibrido (shuffle casuale + backtracking)
- **Gruppi non ammessi** — definisce insiemi di alunni che non possono stare vicini tra loro; le coppie vietate vengono generate automaticamente da ogni gruppo
- **Vicinanze obbligatorie** — forza un alunno a sedersi accanto ad almeno uno dei compagni indicati
- **Layout banchi personalizzabile** — griglia con celle assegnabili (`1`), non assegnabili (`0`) e corridoi (`c`)
- **Lista alunni** opzionale con nomi visualizzati in anteprima e nei builder
- **Scambio manuale** — click su due banchi nell'anteprima per scambiare gli alunni
- **Navigazione cronologia** — pulsanti Precedente / Successiva per tornare alle disposizioni precedenti
- **Esportazione** in file `.txt` con tutti gli input e l'output (numerico e con nomi)
- **Importazione** da file `.txt` esportato in precedenza (con compatibilità verso i vecchi file con "Coppie non permesse")

---

## Scaricare e avviare

### Scaricare da GitHub

**Metodo 1 — Scarica solo il file HTML (consigliato)**

1. Vai su [github.com/loretrango/PostiClasse-SeatingPlanner](https://github.com/loretrango/PostiClasse-SeatingPlanner)
2. Clicca su `postiClasse.html`
3. Clicca sul pulsante **Download raw file** (icona ↓ in alto a destra nel viewer del file)
4. Il file `.html` viene salvato sul tuo computer

In alternativa, scarica il file raw direttamente con `wget` o `curl`:
```bash
wget https://raw.githubusercontent.com/loretrango/PostiClasse-SeatingPlanner/main/postiClasse.html
```

**Metodo 2 — Clona l'intera repository**

```bash
git clone https://github.com/loretrango/PostiClasse-SeatingPlanner.git
```

### Avviare l'applicazione

Dopo aver scaricato il file:

- **Windows**: doppio clic su `postiClasse.html` → si apre nel browser predefinito
- **macOS**: doppio clic su `postiClasse.html` → si apre in Safari (o browser predefinito)
- **Linux**: `xdg-open postiClasse.html` oppure trascina il file nel browser

Non è necessario installare nulla, avviare un server o avere una connessione internet.

> **Nota**: se il browser chiede conferma per eseguire script locali (raro), concedi il permesso oppure usa Firefox che non pone questa restrizione.

---

## Come usare

### 1. Aprire il file

Aprire `postiClasse.html` direttamente nel browser (nessun server necessario).

### 2. Configurare gli input

| Campo | Formato testuale | Esempio |
|---|---|---|
| **Numero di alunni** | Intero positivo | `25` |
| **Gruppi non ammessi** | `A,B,C` — un gruppo per riga | `1,3,5` |
| **Vicinanze obbligatorie** | `A:B,C` — A deve stare vicino ad almeno uno tra B e C | `1:3,6` |
| **Layout banchi** | Righe di `1`, `0`, `c` (separate da newline o `;`) | vedi sotto |
| **Lista alunni** | `numero[TAB]Nome Cognome` per riga | `1	Mario Rossi` |

#### Formato layout banchi

Ogni riga rappresenta una fila di banchi:
- `1` = posto assegnabile
- `0` = posto non assegnabile (banco vuoto o cattedra)
- `c` = corridoio (non è un posto)

Esempio — 3 file, 2 corridoi verticali:
```
11c10cc11
11c111c11
11c111c11
```

> Tutte le righe devono avere lo stesso numero di caratteri.

---

### 3. Gruppi non ammessi

Ogni riga del campo testuale definisce un gruppo: tutti i membri non potranno essere seduti vicini tra loro. Un gruppo di N alunni genera automaticamente tutte le N·(N−1)/2 coppie vietate.

**Esempio:** il gruppo `1,3,5` genera i divieti 1↔3, 1↔5, 3↔5.

#### Builder visuale

Sotto il campo testuale è disponibile un builder interattivo:

- **Lista checkbox scrollabile** — mostra tutti gli alunni (con nome se disponibile)
  - Click singolo su un alunno → selezione singola
  - Click su più alunni → selezione multipla (nessun Ctrl+click necessario)
  - Gli alunni già nel gruppo corrente appaiono **disabilitati** con il badge "nel gruppo"
- **+ Aggiungi selezionati** — aggiunge gli alunni spuntati al gruppo in costruzione
- Le pillole nella riga "gruppo corrente" mostrano chi è stato aggiunto; click **×** per rimuovere singolarmente
- **Salva gruppo** — salva il gruppo corrente (attivo con ≥ 2 alunni)
- **Annulla** — svuota il gruppo in costruzione

I gruppi salvati appaiono come blocchi rossi con nome e membri. Da ciascun blocco è possibile:
- **Modifica** — carica i membri nel builder per modificarli; il bottone diventa "Aggiorna gruppo"
- **×** — elimina il gruppo

Il campo testuale e il builder sono **sempre sincronizzati**: si può modificare il testo direttamente oppure usare il builder, a piacere.

---

### 4. Vicinanze obbligatorie

Ogni riga del campo testuale ha il formato `A:B,C,...` — lo studente A deve trovarsi accanto ad almeno uno tra B, C, ecc.

#### Builder visuale

- Seleziona lo **studente** e il **vicino richiesto**, poi **+ Aggiungi**
- I gruppi salvati mostrano, per ogni studente, i vicini accettabili come pillole arancioni rimuovibili con **×**
- Rimuovere l'ultimo vicino di uno studente elimina automaticamente il vincolo

> Le vicinanze obbligatorie usano solo adiacenza orizzontale diretta; il corridoio separa.

---

### 5. Opzioni di controllo

- **Mostra nomi in anteprima** — mostra nome e numero nelle celle dell'anteprima
- **Controlla anche tra file** — i gruppi non ammessi vengono verificati anche tra banchi davanti/dietro/diagonali (oltre che nella stessa fila)
- **Corridoio conta come vicino** — considera vicini due alunni separati da un `c` (schema 1-c-1); vale solo per i gruppi non ammessi

---

### 6. Generare la disposizione

Premere **Genera disposizione**. Durante la ricerca è visibile un contatore di progresso.

Se i vincoli sono stringenti il motore passa automaticamente da shuffle casuale a backtracking. È possibile interrompere con **Annulla**.

---

### 7. Interagire con l'anteprima

- **Click su un banco** → lo seleziona (bordo rosso)
- **Click su un secondo banco** → scambia i due alunni
- Lo stato dei vincoli si aggiorna automaticamente dopo ogni scambio
- Banchi con vincoli violati evidenziati in **rosso** (gruppi non ammessi) o **arancione** (vicinanze obbligatorie)

---

### 8. Esportare e importare

- **Esporta (testo)** → scarica `disposizione_classe.txt` con tutti gli input e la disposizione
- **Importa…** → ricarica un file esportato in precedenza, ripristinando input e disposizione
- I vecchi file esportati con "Coppie non permesse" vengono convertiti automaticamente in gruppi da 2 al momento dell'importazione

---

## Algoritmo

La generazione avviene in un **Web Worker** separato (non blocca il browser) in due fasi:

1. **Shuffle casuale** — mescola gli studenti e verifica i vincoli; veloce per vincoli laschi (fino a 500.000 tentativi o 60% del timeout)
2. **Backtracking con random restart** — esplora sistematicamente le assegnazioni; gli studenti con più vincoli vengono piazzati per primi; include forward checking per le vicinanze obbligatorie

Timeout: **15 secondi** totali. Se non si trova una soluzione viene mostrato un messaggio con suggerimenti per allentare i vincoli.

---

## Versioni

| File | Descrizione |
|---|---|
| `postiClasse_02.html` | Prima versione web con layout 1/0/c |
| `postiClasse_03.html` | Aggiunta anteprima cliccabile e opzioni tra file / corridoio |
| `postiClasse_04.html` | UI minimale, export/import testo |
| `postiClasse_05.html` | Checkbox riorganizzate, textarea antipatie su righe separate |
| `postiClasse_06.html` | Motore ibrido shuffle + backtracking in Web Worker |
| `postiClasse.html` | Versione attuale (vedi sotto) |

**`postiClasse.html`** è la versione attiva e aggiornata. Rispetto alla v6 aggiunge:
- Gruppi non ammessi con builder visuale a checkbox (sostituisce "coppie non permesse")
- Builder visuale per le vicinanze obbligatorie
- Fix bug parser: `parseVicinanze` gestiva correttamente solo il separatore `;`, ignorando le righe separate da newline
- Compatibilità import file vecchi (coppie → gruppi da 2)

---

## Requisiti

- Browser moderno con supporto a **Web Workers** e **Blob URL** (Chrome, Firefox, Safari, Edge recenti)
- Nessuna dipendenza esterna, nessun server, nessuna installazione

---

## Licenza — GNU GPL v3

Questo software è distribuito sotto **GNU General Public License versione 3** (GPL-3.0-or-later).

### Cosa puoi fare

- **Usare** il programma liberamente, per qualsiasi scopo (personale, scolastico, professionale)
- **Copiare e distribuire** il file HTML a chiunque
- **Modificare** il codice sorgente per adattarlo alle tue esigenze
- **Distribuire le tue versioni modificate**

### Obblighi se distribuisci il software (originale o modificato)

1. **Includi sempre il codice sorgente** (o un'offerta scritta per ottenerlo) — poiché è un file HTML single-file, il sorgente coincide con il file stesso.
2. **Mantieni la nota di copyright** e il testo della licenza originale.
3. **Rilascia le tue modifiche sotto la stessa licenza GPL** — non puoi rendere proprietaria una versione derivata.
4. **Indica chiaramente le modifiche apportate** rispetto all'originale.

### Cosa non puoi fare

- Distribuire il software (o versioni modificate) sotto una licenza che ne limiti la libertà d'uso, copia o studio
- Rimuovere o oscurare gli avvisi di copyright e licenza esistenti
- Integrare il codice in un prodotto proprietario senza rispettare i termini GPL (copyleft)

### In sintesi

La GPL garantisce che questo software — e qualsiasi sua derivazione — rimanga libero per tutti. Se lo distribuisci, devi farlo alle stesse condizioni con cui l'hai ricevuto.

Testo completo della licenza: <https://www.gnu.org/licenses/gpl-3.0.html>
