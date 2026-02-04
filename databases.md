# Database
Un database è un sistema per la memorizzazione e la gestione dei dati, che comprende sia l'hardware fisico su cui sono memorizzati i dati, sia il software che organizza e controlla l'accesso ai dati.

## DBMS - DataBase Management System

Il DBMS è il sistema di gestione delle basi di dati (software).
Grazie a questo sistema è possibile interrogare, creare, manipolare gli oggetti del database. Un'esempio di DBMS è 
<span style="color:blue">**PostgreSQL**</span>.

Rispetto ad un *File System* il DBMS garantisce un'accesso più granulare ai dati contenuti in un sistema di archiviazione.
Un DBMS ideale rispetta le proprietà <span style="color:green">**ACID**</span>:

- **Atomicità**  
  Il processo deve essere suddivisibile in un numero finito di unità indivisibili, chiamate *transazioni*.  
  L’esecuzione di una transazione deve essere *o totale o nulla*: non sono ammesse esecuzioni parziali.

- **Consistenza**  
  Il database deve rispettare tutti i vincoli di integrità sia all’inizio sia alla fine della transazione.  
  Non devono verificarsi incoerenze o contraddizioni tra i dati memorizzati.

- **Isolamento**  
  Ogni transazione deve essere eseguita in modo isolato e indipendente dalle altre.  
  Il fallimento di una transazione non deve influire sulle altre transazioni in corso.

- **Durabilità**  
  Detta anche *persistenza*. Una volta eseguito il *commit*, le modifiche devono essere permanenti e non più perdibili.  
  Per tutelare i dati nel tempo che intercorre tra il commit e la scrittura fisica, il DBMS utilizza file di log che registrano tutte le operazioni effettuate.

## Modello 
Un modello è un'astrazione della realtà. E' fondamentale catturare le informazioni più importanti nel contesto che vogliamo astrarre.

Esistono 3 livelli di astrazione:
1. **Mondo reale** (ciò che vogliamo monitorare): quello che vogliamo fare, quello che ci chiede il cliente, sarebbero i requisiti che il nostro modello deve soddisfare rappresentati con il linguaggio naturale.
2. **Modello concettuale**: traduce il requisito in un modello, estrazione delle informazioni rilevanti, intregrazione di informazione utili per l'implementazione di una soluzione.
3. **Modello logico** (oggetti su DB)

## Modello Entità-Relazione
Si tratta di un modello concettuale, cioè la rappresentazione intermedia tra richiesta del cliente e soluzione elettronica. 

Le principali componenti di questo modello sono 4:
- **Entità**: una classe di oggetti con proprietà in comune. Una sua occorrenza è detta istanza. ogni entità ha un nome (+ una PK) per identificarla e degli attributi per descriverla. Si rappresenta con un rettangolo.
- **Relazioni**: rappresentno come le entità sono legate tra di loro e come possono interagire. In genere sono tra 2 entità (Relazioni binarie) ma possono essere coinvolte N entità (ad esempio si incontrano spesso relazione ternarie). Esistono relazioni "ad anello" cioè tra un entità e se stessa. Tra 2 entità possono esserci più relazioni. Vengono rappresentate tramite un rombo (identificato dal nome della relazione).
- **Attributi**: usati per descrivere un''entità / una relazione.
Traa gli attributi viene identificata una chiave primaria che identifica univocamente un'entità. Graficamente si rappresentano con un ellisse (nel caso delle relazioni) oppure come righe all'interno del rettagolo (nel caso delle entità).
- **Cardinalità**: utilizzata sia per gli attributi che per le relazioni. Nel caso degli attributi la cardinalità indica il numero di valori differenti che possono assumere. 

Nel caso delle relazioni indica quante volte un istanza può partecipare a quella relazione.

La cardinalità si indica con una coppia (numero minimo, numero massimo)

La cardinalità indica il tipo di relazione:
- *1-1*: se un'istanza di un'entità può essere legata tramite la relazione a un'istanza dell'altra entità
- *1-n*: se un'istanza di un'entità può essere legata tramite la relazione a n istanze dell'altra entità
- *n-n*: se n istanze di un'entità possono essere legate tramite la relazione a n istanze dell'altra entità


## Modello Relazionale

Traduzione del modello entità-relazione in un modello logico.

Le entità e le relazioni devono essere tradotte in tabelle.

Le entità sono facilmente traducibili, le relazioni meno.

1. **Caso Generico**: La relazione viene tradotta in una tabella che ha come campi le chiavi primarie delle entità coinvolte più eventuali attributi della relazione.

2. **Casi particolari**: 
  - Relazione con cardinalità 1-1 (anche solo da un lato): basta aggiungere la chiave primaria dell'altra entità a quella della relazione e aggiungere gli eventuali attributi della relazione.
  - Relazione con cardinalità 0-1: Nel caso in cui ci fosse una relazione in cui molte istanze di questa entità non partecipano alla relazione è meglio spezzare l'entità in due e quindi creare 2 tabelle. Questo viene fatto per avere un numero sistemicamente alto di null-values.
  - Attributi multivalore: valutare se creare diverse colonne o se conviene avere tutto nella stessa colonna (ad esempio se la cardinalità è troppo alta)

 

# Data type nei DBMS e in particolare in PostgreSQL (9/12/25)

## Linguaggi SQL
- **DDL – Data Definition Language**  
  Utilizzato per definire le strutture dati del database: creare, modificare o eliminare tabelle.

- **DML – Data Manipulation Language**  
  Permette di inserire, modificare e cancellare dati all’interno delle tabelle.

- **DQL – Data Query Language**  
  Linguaggio per interrogare i dati (es. `SELECT`).

- **DCL – Data Control Language**  
  Gestisce concessione e revoca dei permessi sulle tabelle.

Quando definiamo una tabella, specifichiamo il **tipo di dato** di ciascuna colonna.



# Data type di PostgreSQL

## Tipi numerici
- **NUMERIC(p, s)**  
  Rappresenta numeri con qualunque precisione.  
  - `p` = precisione (numero totale di cifre)  
  - `s` = scala (cifre decimali)

- **INTEGER**  
  Varianti:
  - `SMALLINT`
  - `INTEGER`
  - `BIGINT`

## Tipi seriali  
Usati spesso come chiavi surrogate auto-incrementali.

- `SERIAL`, `SMALLSERIAL`, `BIGSERIAL`  
  Il DBMS crea automaticamente una **sequence** e usa `nextval()` per popolare la colonna.  
  Vantaggi:
  - garantiscono unicità  
  - operazioni su numeri interi sono più performanti rispetto alle stringhe  
  - utili per chiavi primarie e relazioni tra tabelle


## Tipi testuali
- **VARCHAR(n)**  
  Testo con lunghezza massima `n`.  
  Se non specifichiamo `n`, PostgreSQL lo tratta come `TEXT`.

- **CHAR(n)**  
  Lunghezza fissa con padding: se inseriamo meno di `n` caratteri, il resto viene riempito con spazi.
  Se non specifichiamo `n` rappresenta un solo carattere.  
  È confrontabile ma generalmente **sconsigliato**.

- **TEXT**  
  Testo a lunghezza illimitata.


## Tipi temporali

- **DATE**  
   Memorizza una data (anno, mese, giorno).

- **TIME** / **TIME WITH TIME ZONE**  
   Memorizza un orario (con o senza fuso orario).

- **TIMESTAMP** / **TIMESTAMP WITH TIME ZONE**  
   Memorizza data e ora.  
   Si consiglia **TIMESTAMPTZ** (con time zone).

- **INTERVAL**  
   Rappresenta durate o intervalli temporali.  
   L’attributo *field* può specificare il formato dell’intervallo (anni, mesi, giorni, ore, …).

### Gestione del fuso orario  
- PostgreSQL salva i valori **in UTC**.  
- Quando si estraggono i dati, li converte automaticamente nel **fuso orario del server** (non del client).  
- Esempio: server in Italia, client in USA → i dati vengono visualizzati comunque con la timezone italiana.

Possiamo usare gli intervalli per manipolare date e timestamp, ad esempio castando una stringa a `INTERVAL`.

### Valori speciali temporali

| Placeholder       | Significato                               |
|-------------------|--------------------------------------------|
| today             | La data di oggi                            |
| yesterday         | La data di ieri                            |
| tomorrow          | La data di domani                          |
| now               | Data e ora attuali (CURRENT_TIMESTAMP)     |
| current_date      | Data corrente                              |
| current_time      | Ora corrente                               |
| current_timestamp | Timestamp corrente                         |
| localtime         | Ora locale                                 |
| localtimestamp    | Timestamp locale                           |
| +infinity         | Data massima rappresentabile               |
| -infinity         | Data minima rappresentabile                |


La stringa `"today"` rappresenta:
- come `DATE`: la data di oggi  
- come `TIMESTAMP`: la mezzanotte di oggi (`00:00`)


### Conversioni di tipo
- `TO_DATE(string)`  
- `TO_TIMESTAMP(string)`  
- `TO_CHAR(date, formato string)` (restituisce un TEXT)

Le stringhe sono ambigue, mentre `DATE` e `TIMESTAMP` hanno formato standard.

Sintassi generale:
```sql
CAST(valore AS data_type)
```

## Boolean

Tipo logico con valori:

- **TRUE / FALSE**
- **ON / OFF**
- **1 / 0**
- **NULL**


# DDL – Data Definition Language (16/12/2025)

Il **DDL (Data Definition Language)** è il sottoinsieme del linguaggio SQL utilizzato per **definire e modificare la struttura del database**.  
Consente di creare, alterare ed eliminare oggetti come tabelle, indici e vincoli.

---

## Operazioni principali

### CREATE
Utilizzato per creare nuovi oggetti nel database, in particolare le tabelle.

```sql
CREATE TABLE nome_tabella (
    campo1 tipo,
    campo2 tipo,
    campo3 tipo
);
```

È possibile creare una tabella **a partire dal risultato di una query**:

```sql
CREATE TABLE nuova_tabella AS
SELECT ...
```
In questo caso vengono copiate solo le colonne e i dati risultanti dalla query, non i vincoli.

### DROP

Elimina definitivamente una tabella (struttura + dati).

```sql
DROP TABLE nome_tabella;
```

Per evitare errori nel caso la tabella non esista, si utilizza:

```sql
DROP TABLE IF EXISTS nome_tabella;
```

In questo modo il DBMS non solleva un errore ma solo un warning.

### TRUNCATE

Rimuove tutti i record da una tabella mantenendone la struttura.

```sql
TRUNCATE TABLE nome_tabella;
```

Caratteristiche principali:

- operazione molto veloce

- non attiva i trigger

- non è selettiva (non supporta WHERE)

- resetta eventuali contatori (es. SERIAL)

### ALTER

Utilizzato per modificare la struttura di una tabella esistente
(es. aggiungere, rimuovere o modificare colonne e vincoli).

```sql
ALTER TABLE nome_tabella
ADD COLUMN nuova_colonna tipo;
```
## Valori di default

È possibile specificare un valore di default per una colonna, che viene utilizzato quando in fase di inserimento non viene fornito alcun valore.
```sql
campo tipo DEFAULT valore
```

Il valore di default viene valutato automaticamente dal DBMS.

## Constraints (Vincoli)

I constraints servono a garantire correttezza e coerenza dei dati all’interno del database.

Possono essere:

- di colonna

- di tabella

### Tipologie principali
### NOT NULL

Impedisce che una colonna assuma valori NULL.

```sql
campo tipo NOT NULL
```

Una colonna nullable è una colonna che può assumere valori NULL.

### UNIQUE

Garantisce l’unicità dei valori in una colonna (o insieme di colonne).

```sql
campo tipo UNIQUE
```

Per implementare l’unicità in modo efficiente, PostgreSQL utilizza un indice B-Tree.
L’indice consente ricerche molto più veloci rispetto a una scansione completa della tabella.

È possibile creare manualmente un indice:

```sql
CREATE INDEX nome_indice
ON nome_tabella(campo);
```
### CHECK

Consente di imporre una condizione logica sui valori di una colonna.
```sql
CHECK (condizione)
```

### ⚠️ Attenzione:
I valori NULL non vengono valutati nei vincoli CHECK.
Se necessario, occorre gestirli esplicitamente (ad esempio con IS NOT NULL).

## Chiavi
### Chiave primaria (PRIMARY KEY)

Identifica univocamente ogni record di una tabella.

Può essere:

- semplice (una sola colonna)

- composta (più colonne)
```sql
PRIMARY KEY (campo1, campo2)
```

### Chiave esterna (FOREIGN KEY)

Garantisce l’integrità referenziale tra tabelle.

I valori della colonna referente devono essere presenti nella colonna referenziata.
```sql
FOREIGN KEY (campo_referente)
REFERENCES tabella_riferita(colonna_referenziata)
```

Serve a mantenere la coerenza tra i dati collegati in tabelle diverse.


### Lezione 13/01

## ALTER TABLE 

Il comando `ALTER TABLE` permette di **modificare la struttura di una tabella esistente**:  
aggiungere o rimuovere colonne, modificare tipi di dato e aggiungere vincoli.

---

#### Aggiungere una colonna

```sql
ALTER TABLE table_name
ADD column_name datatype;
```

La colonna aggiunta viene popolata con:
- `NULL` di default

- oppure con un valore esplicito se viene specificato un `DEFAULT`

#### Impostare un valore di default
```sql
ALTER TABLE table_name
ADD column_name datatype DEFAULT valore;
```

#### Oppure su una colonna già esistente:
```sql
ALTER TABLE table_name
ALTER COLUMN column_name SET DEFAULT valore;
```
#### Rimuovere una colonna
```sql
ALTER TABLE table_name
DROP column_name;
```
La colonna viene eliminata da tutte le righe della tabella.

Attenzione ai vincoli di foreign key
Se la colonna è coinvolta in un vincolo (es. foreign key), il comando fallisce.

#### È possibile forzare la rimozione con:

```sql
ALTER TABLE table_name
DROP column_name CASCADE;
```
Con `CASCADE`:

- vengono eliminati anche i vincoli che dipendono dalla colonna

- non viene eliminata la colonna nella tabella referenziata

- si elimina solo il vincolo, non i dati dell’altra tabella

### Operazione potenzialmente pericolosa.
--- 
### Aggiungere vincoli (CONSTRAINT)
È possibile aggiungere vincoli anche dopo la creazione della tabella.

#### Vincolo generico
```sql
ALTER TABLE table_name
ADD CONSTRAINT nome_vincolo tipo_vincolo;
```

#### Per i vincoli NULL / NOT NULL si usa una sintassi diversa:
```sql
ALTER TABLE table_name
ALTER COLUMN column_name SET NOT NULL;
```
#### Per rimuovere il vincolo:
```sql
ALTER TABLE table_name
ALTER COLUMN column_name DROP NOT NULL;
```
### Tabelle di catalogo (metadati)
PostgreSQL mantiene i metadati del database nello schema:
- pgsql
- pg_catalog

#### Per visualizzare i vincoli:
```sql
SELECT *
FROM pg_catalog.pg_constraint;
```
Queste tabelle descrivono:

- tabelle

- colonne

- indici

- vincoli

- tipi di dato

Sono utilizzate internamente dal DBMS e per interrogazioni di sistema.

#### Modifica del tipo di una colonna
È possibile cambiare il tipo di una colonna:

```sql
ALTER TABLE table_name
ALTER COLUMN column_name TYPE nuovo_tipo;
```

Alcune conversioni sono automatiche:

`INTEGER → TEXT` : OK

Altre richiedono conversione esplicita:

`TEXT → INTEGER` : serve cast

```sql
ALTER TABLE table_name
ALTER COLUMN column_name TYPE INTEGER
USING column_name::INTEGER;
```
Se la conversione non è possibile, PostgreSQL solleva un errore.

# DML - Data Manipulation Language 

Il **DML (Data Manipulation Language)** è la parte del linguaggio SQL che consente di:

- inserire dati nelle tabelle
- modificare dati esistenti
- eliminare record

I principali comandi sono:

- `INSERT`
- `UPDATE`
- `DELETE`

Queste operazioni agiscono **sui record**, non sulla struttura delle tabelle  
(a differenza di `DROP` o `ALTER`, che sono comandi DDL).

---

## INSERT — Inserimento dei dati

L’`INSERT` può essere eseguito:

- in modo **hard-coded** (valori scritti esplicitamente)
- tramite una **query** che legge i dati da un’altra tabella

#### Inserimento hard-coded

```sql
INSERT INTO nome_tabella (col1, col2, col3)
VALUES (val1, val2, val3);
```
##### Se si specificano tutte le colonne nello stesso ordine della tabella:
```sql
INSERT INTO nome_tabella
VALUES (val1, val2, val3);
```
Le colonne non specificate assumono:

- il valore di DEFAULT, se definito

- altrimenti NULL

#### INSERT con SELECT (da un’altra tabella)
```sql
INSERT INTO tabella_destinazione (col1, col2)
SELECT campoA, campoB
FROM tabella_sorgente
WHERE condizione;
```
In questo caso:

- il numero di colonne deve coincidere

- i tipi devono essere compatibili

## UPDATE — Modifica dei dati
Serve per aggiornare uno o più record esistenti.

```sql
UPDATE nome_tabella
SET col1 = nuovo_valore1,
    col2 = nuovo_valore2
WHERE condizione;
```
### Attenzione!!
Se si omette la clausola WHERE, tutti i record della tabella vengono modificati:

```sql
UPDATE nome_tabella
SET col1 = valore;
```
## DELETE — Eliminazione dei dati

Serve per cancellare record da una tabella
(diverso da `DROP`, che elimina l’intera tabella o una colonna).

```sql
DELETE FROM nome_tabella
WHERE condizione;
```
### Attenzione!!
Se non si specifica `WHERE`, vengono eliminati tutti i record:

```sql
DELETE FROM nome_tabella;
```
La struttura della tabella resta invariata.

#### DELETE vs TRUNCATE
Entrambi rimuovono i dati, ma:

#### DELETE
- può avere WHERE

- attiva trigger

- può essere rollbackato

- più lento

#### TRUNCATE
```sql
TRUNCATE TABLE nome_tabella;
```
- non ha WHERE

- non attiva trigger

- resetta contatori SERIAL

- molto veloce

- operazione DDL, non DML

---

# JOIN 

Una JOIN serve a combinare righe di due (o più) tabelle usando una colonna in comune (tipicamente una chiave primaria in una tabella e una chiave esterna nell’altra).

Esistono diversi tipi di Join:

## CROSS JOIN

Fa il prodotto cartesiano delle righe delle due tabelle 

```sql
SELECT
    c.nome AS colore,
    t.nome AS taglia
FROM colori AS c
CROSS JOIN taglie AS t;
```

il risultato è una tabella che ha come righe tutte le combinazioni possibili tra colori e taglie

La cross join è utile per generare dati ma può esplodere 

## INNER JOIN

Restituisce solo le righe che hanno corrispondenza in entrambe le tabelle.

```sql
SELECT lista_delle_colonne
FROM table_a a
INNER JOIN table_b b
ON a.key = b.key
```
`nota`: `JOIN` e `INNER JOIN` si equivalgono in postgres
`nota`: se la chiave di join ha lo stesso nome nelle tabelle si può usare questa forma

```sql
...
USING (key)
```

## LEFT JOIN e RIGHT JOIN

LEFT: Restituisce tutte le righe della tabella di sinistra e, se esiste, la corrispondenza della tabella di destra.
Se non c’è corrispondenza, i campi della tabella destra sono NULL.

RIGHT: Restituisce tutte le righe della tabella di destra e, se esiste, la corrispondenza della tabella di sinistra.
Se non c’è corrispondenza, i campi della tabella sinistra sono NULL.

```sql
SELECT lista_delle_colonne
FROM table_a a
LEFT JOIN table_b b
ON a.key = b.key
```

```sql
SELECT lista_delle_colonne
FROM table_a a
RIGHT JOIN table_b b
ON a.key = b.key
```

## LEFT ANTI JOIN e RIGHT ANTI JOIN

LEFT: le righe della tabella di sinistra che NON hanno alcuna corrispondenza nella tabella di destra, i campi della tabella destra sono NULL.

RIGHT: le righe della tabella di destra che NON hanno alcuna corrispondenza nella tabella di sinistra, i campi della tabella sinistra sono NULL.

```sql
SELECT lista_delle_colonne
FROM table_a a
LEFT JOIN table_b b
ON a.key = b.key
WHERE b.key IS NULL
```

```sql
SELECT lista_delle_colonne
FROM table_a a
RIGHT JOIN table_b b
ON a.key = b.key
WHERE a.key IS NULL
```

## FULL JOIN e FULL ANTI JOIN

FULL JOIN (FULL OUTER JOIN): Restituisce tutte le righe di entrambe le tabelle:

- righe che combaciano → unite

- righe senza match → con NULL dalla parte mancante

```sql
SELECT lista_delle_colonne
FROM table_a a
FULL OUTER JOIN table_b b
ON a.key = b.key
```

FULL ANTI JOIN: tutte le righe che non hanno corrispondenza tra le due tabelle, da entrambe le parti. In pratica l'opposto dell'inner join

```sql
SELECT lista_delle_colonne
FROM table_a a
FULL OUTER JOIN table_b b
ON a.key = b.key
WHERE a.key IS NULL OR b.key IS NULL
```

`nota`: si possono aggiungere condizioni dop la keyword ON, la condizione viene applicata PRIMA della join

self join 