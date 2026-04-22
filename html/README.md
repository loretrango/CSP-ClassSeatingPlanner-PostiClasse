# Disposizione posti in classe — v6 Beta

Applicazione web (single-file HTML) per generare automaticamente la disposizione degli alunni in aula rispettando vincoli di separazione e vicinanza.

**Autore:** Lorenzo Antiga — lorenzo.antiga@gmail.com

---

## Funzionalità principali

- **Generazione automatica** della disposizione con algoritmo ibrido (shuffle casuale + backtracking)
- **Coppie non permesse** — impedisce che due alunni indicati si siedano vicini
- **Vicinanze obbligatorie** — forza un alunno a sedersi accanto ad almeno uno dei compagni indicati
- **Layout banchi personalizzabile** — griglia con celle assegnabili (`1`), non assegnabili (`0`) e corridoi (`c`)
- **Lista alunni** opzionale con nomi visualizzati in anteprima
- **Scambio manuale** — click su due banchi nell'anteprima per scambiare gli alunni
- **Navigazione cronologia** — pulsanti Precedente / Successiva per tornare alle disposizioni precedenti
- **Esportazione** in file `.txt` con input e output (numerico e con nomi)
- **Importazione** da file `.txt` esportato in precedenza

---

## Come usare

### 1. Aprire il file

Aprire `postiClasse_beta.html` direttamente nel browser (nessun server necessario).

### 2. Configurare gli input

| Campo | Formato | Esempio |
|---|---|---|
| **Numero di alunni** | Intero positivo | `25` |
| **Coppie non permesse** | `A-B` separati da `;` o a capo | `1-2; 5-6` |
| **Vicinanze obbligatorie** | `A:B,C` — A deve stare vicino ad almeno uno tra B, C | `1:3,6` |
| **Layout banchi** | Righe di `1`, `0`, `c` (separati da newline o `;`) | vedi sotto |
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

### 3. Opzioni di controllo

- **Mostra nomi in anteprima** — mostra cognome e nome nelle celle dell'anteprima
- **Controlla anche tra file** — le coppie non permesse vengono verificate anche tra banchi davanti/dietro/diagonali (oltre che nella stessa fila)
- **Corridoio conta come vicino** — considera vicini due alunni separati da un `c` (schema 1-c-1); vale solo per le coppie non permesse

> Le vicinanze obbligatorie usano sempre solo adiacenza orizzontale diretta; il corridoio separa.

### 4. Generare la disposizione

Premere **Genera disposizione**. Durante la generazione è visibile un contatore di progresso.

Se i vincoli sono molto stringenti, il motore passa automaticamente da shuffle casuale a backtracking. È possibile annullare la ricerca con il pulsante **Annulla**.

### 5. Interagire con l'anteprima

- **Click su un banco** → lo seleziona (bordo rosso)
- **Click su un secondo banco** → scambia i due alunni
- Lo stato dei vincoli si aggiorna automaticamente dopo ogni scambio
- I banchi con vincoli violati sono evidenziati in rosso (antipatie) o arancione (vicinanze)

### 6. Esportare e importare

- **Esporta (testo)** → scarica `disposizione_classe.txt` con tutti gli input e la disposizione
- **Importa…** → ricarica un file esportato in precedenza, ripristinando input e disposizione

---

## Algoritmo

La generazione avviene in un **Web Worker** separato (non blocca il browser) in due fasi:

1. **Shuffle casuale** — mescola gli studenti e verifica i vincoli; veloce per vincoli laschi (fino a 500.000 tentativi o 60% del timeout)
2. **Backtracking con random restart** — esplora sistematicamente le assegnazioni; gli studenti con più vincoli vengono piazzati per primi; include forward checking per le vicinanze obbligatorie

Timeout: **15 secondi** totali. Se non si trova una soluzione, viene mostrato un messaggio con suggerimenti per allentare i vincoli.

---

## Versioni precedenti

Le versioni da `postiClasse_02.html` a `postiClasse_06.html` sono archivi dello sviluppo iterativo.
La versione attiva e aggiornata è **`postiClasse_beta.html`**.

---

## Requisiti

- Browser moderno con supporto a **Web Workers** e **Blob URL** (Chrome, Firefox, Safari, Edge recenti)
- Nessuna dipendenza esterna, nessun server, nessuna installazione
