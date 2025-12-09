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
- **Cardinalità**: utilizzata sia per gli attributi che per le relazioni. Nel caso degli attributi la cardinalità indica il numero di valori differenti che possono assumere. Nel caso delle relazioni 

da finire
--- 



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