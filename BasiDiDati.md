# Basi di dati 
All'interno di questo file markdown sono presenti appunti ed esercitazioni riguardanti il corso di Basi di Dati.

## Piccolo disclaimer
Poichè posseggo una discreta conoscenza dei database relazionali e linguaggi di query, all'interno del file sono presenti solo informazioni che per me risultano nuove o poco chiare, per cui conviene tenerne traccia negli appunti.

## Linguaggi di un DBMS
Un DBMS supporta diversi tipi di linguaggi:
- **Data Definition Language (DDL)**: permette di specificare e modificare lo schema della base di dati, lo schema delle viste e i vincoli di integrità. Agisce sul livello logico ed esterno.
- **Data Manipulation Language (DML)**: permette di creare, modificare e interrogare l'istanza della basi di dati. Agise su livello logico ed esterno.
- **Storage Definition Language (SDL)**: definisce lo schema fisico del DB. Agisce sul livello fisico.

In sintesi possiamo affermare che **DDL** e **DML** sono linguaggi **CRUD**, ovvero quei linguaggi che supportano operazioni di:
- Creation
- Read
- Update
- Delete
Entrambi agiscono allo stesso modo ma da punti di vista differenti:
- il DML agisce sulle istanze delle relazioni, i dati veri e propri
- il DDL agisce sullo schema delle relazioni, sulla "struttura"

Fra i vari linguaggi di interrogazione uno dei più famosi è **SQL (Structured Query Language)** che fornisce un approccio di interrogazione dichiarativa, poichè in modo esplicito dichiariamo cosa vogliamo ottenere dall'interrogazione.
SQL non è nato nè per la potenza computazionale ma per la potenza espressiva. Di fatti frài vari vantaggi troviamo:
- facilità di utilizzo anche per utenti poco esperti
- possibilità di attuare strategie di ottimizzazione (solitamente attuate dal DBMS che attua una serie di strategie per eseguire in modo ottimizzato le interrogazioni).

## Tipi di dato

Prima di introdurre il concetto di vincolo e le istruzioni SQL per DDL e DML elenchiamo velocemente i vari tipi di dato supportati nella maggior parte dei DBMS:
- tipi caratterre: singoli caratteri, stringhe anche di lunghezza variabile
- tipi numerici: interi, decimali
- tipi temporali: date, ore, intervalli di tempo
- booleani
- BLOB,CLOB (Binary/Character Large Object): per grandi immagini e testi

Un tipo particolare di dato è rappresentato dal valore nullo `NULL`. Molti DBMS supportano la possibilità di assegnare a qualsiasi *data-type* il valore `NULL`. Esso rappresenta un valore valido per ogni dominio in quanto non appartiene a un dominio preciso. La gestione dei valori `NULL` deve avvenire in modo dedicato e con cautela (per esempio confrontare `Matricola=NULL` risulta ambiguo).

## Il concetto di vincolo

All'interno di una relazione, come nella vita reale, possono essere indicati dei vincoli, delle regole (*constraints*), che devono essere rispettate da tutte le tuple che devono appartenere alla relazione. 

Quando si ha a che fare con un DBMS, si parla di **vincoli di integrità**, condizioni che devono essere verificate da **ogni** istanza della base di dati, per esempio dominio degli attributi.

Tra i vari vincoli di integrità abbiamo:
- **vincoli di integrità intra-relazionale** che agiscono all'interno di una singola relazione
- **vincoli di integrità di entità (chiave primaria)** che agiscono sull'istanza di una tabella garantendo che ogni tupla nel database sia univoca e identificabile
- **vincoli di integrità inter-relazionale (referenziale)** che agiscono sul legame tra relazioni differenti.

I **vincoli intra-relazionali** agiscono come regole di validazione interne a una singola tabella. Una tupla può essere inserita o mantenuta nella relazione solo se soddisfa tutti i vincoli imposti, garantendo così la correttezza atomica dei dati. Per esempio:
- l'età deve essere un intero (vincolo di dominio)
- il campo nome non può essere `NULL` (vincolo di colonna)
- il prezzo di vendita dev'essere maggiore del prezzo di acquisto (vincolo di tupla)

I **vincoli di chiave primaria** agiscono sull'intera tupla con una visione globale della relazione. Più precisamente consentono di specificare uno o più attributi come **chiave primaria** ovvero uno o più campi che identificano in modo univoco il record. Quindi per sua natura una chiave primaria non può assumere valori `NULL` e non ammette valori duplicati (per l'intera chiave) all'interno della stessa relazione. Per esempio:
- uno studente è identificato dalla matricola
- un paziente è identificato dal codice fiscale
- la frequentazione di un corso è identificata dalla matricola dello studente e dall'identificativo del corso. 

I **vincoli di integrità referenziale** agiscono sul legame tra relazioni, garantendo che io in una tabella possa inserire il valore di una chiave di un'altra tabella per evitare di inserire tutti i dati della tupla. Per esempio date le tabelle **Corsi** e **Docenti** io posso inserire nella relazione **Corsi** la matricola del docente, in modo da associare a ciascun corso l'identificativo del docente che lo sostiene. Per tanto realizziamo una correlazione logica tra le relazioni e il vincolo stabilisce che non può esistere una chiave, detta **chiave esterna**, che non esiste nella relazione in cui è chiave primaria (non posso aggiungere un corso sostenuto da un docente che non esiste nella tabella Docenti).


## DDL
Il DDL permette di definire e modificare lo schema delle relazioni, viste e definire vincoli di integrità. Per le prossime istruzioni useremo una notazione generica che consente di definire in modo generico la sintassi dei comandi SQL. Nel dettaglio useremo questa notazione:
- caratteri maiuscoli per le parole chiave del linguaggio
- `<>` indichiamo i nomi di variabili
- `[]` indichiamo i componenti opzionali
- `*` per indicare 0 o più occorrenze
- `|` per indicare delle opzioni (si intende scegliere un elemento piuttosto che un altro)

Il DDL utilizza le seguenti *keywords*:
- per la creazione **CREATE**
- per la modifica **ALTER**
- per la cancellazione **DROP**
- per l'interrogazione **SHOW**

Per la creazione di una relazione si usa la seguente istruzione:
```sql
CREATE TABLE <nome relazione>
(<specifica colonna> [,<specifica colonna>]*);
```
dove:
- `<nome relazione>` è il nome della relazione che viene creata
- `<specifica colonna>` è una specifica di colonna il cui formato è
  - ```sql 
    <nome colonna> <dominio> [DEFAULT <valore_default>]
    ```
dove:
  - `<nome colonna>` è il nome della colonna (necessariamente divero dal nome delle altre colonne della relazione)
  - `<dominio>` è il dominio della colonna, uno dei *data-type* SQL
  - `<valore_default>` è un valore del dominio, assunto dalle tuple se nessun valore è specificato per la colonna

Esempio:
```sql
CREATE TABLE Video(
colloc DECIMAL(4),
titolo VARCHAR(30),
regista VARCHAR(20),
tipo CHAR DEFAULT 'd');
```

Vediamo ora la definizione dei vincoli di integrità sulla tabella Video. In prima battuta vediamo la definizione di vincoli di colonna, per esempio il titolo e il regista non possono assumere valori di tipo `NULL`. In questo caso definiamo il campo come
```sql
CREATE TABLE Video(
colloc DECIMAL(4),
titolo VARCHAR(30) NOT NULL,
regista VARCHAR(20) NOT NULL,
tipo CHAR DEFAULT 'd');
```

Un altro tipo di vincolo di integrià è il vincolo di chiave, che possono essere:
- `UNIQUE` chiave univoca, quindi simile alla chiave primaria, ma può assumere valori nulli
- `PRIMARY KEY` chiave univoca che non può assumere valori nulli.
In una relazione possiamo specificare più chiavi `UNIQUE` ma una sola `PRIMARY KEY`.

La definizione di una chiave avviene direttamente sull'attributo con la seguente sintassi: 
```sql
<nome colonna> <dominio> [PRIMARY | [UNIQUE] KEY]
```
oppure dopo l'ultimo attributo, solitamente nel caso in cui abbiamo una chiave primaria composta poichè con la definizione *inline* è possibile definirla solo su un campo.

```sql

/*Esempio inline */
CREATE TABLE Video(
colloc DECIMAL(4) PRIMARY KEY,
titolo VARCHAR(30) NOT NULL,
regista VARCHAR(20) NOT NULL,
tipo CHAR DEFAULT 'd');

/*Esempio in coda */
CREATE TABLE Video(
colloc DECIMAL(4),
titolo VARCHAR(30),
regista VARCHAR(20),
tipo CHAR DEFAULT 'd',
PRIMARY KEY(titolo,regista));

/*Esempio in coda con UNIQUE*/
CREATE TABLE Noleggio
(
    colloc DECIMAL(4),
    dataNol DATE DEFAULT CURRENT_DATE,
    codCli DECIMAL(4) NOT NULL,
    dataRest DATE,
    PRIMARY KEY (colloc,dataNol)
    UNIQUE (colloc,dataRest)

);
```

Un altro tipo di vincolo è il vincolo di integrità referenziale, dove andiamo a definire una chiave esterna (**FOREIGN KEY**) sulla tabella riferita e che assume i valori della chiave primaria della tabella referente. Molto importante è che la chiave esterna abbia lo stesso dominio della chiave primaria a cui fa riferimento (non posso definire una chiave esterna stringa in riferimento a una chiave primaria numerica).

La sintassi di definizione della chiave esterna è la seguente (inserita come ultima riga in `CREATE TABLE`):
```sql
FOREIGN KEY (<lista nomi colonne>) REFERENCES <nome relazione>
    [ON DELETE {NO ACTION | CASCADE | SET NULL | SET DEFAULT}]
    [ON UPDATE {NO ACTION | CASCADE | SET NULL | SET DEFAULT}]
```

In caso di una chiave esterna composta da un solo campo è possibile usare la notazione *inline*:
```sql
<nome attributo> <dominio> REFERENCES <nome relazione>
```

Per esempio:
```sql
CREATE TABLE Film(
titolo VARCHAR(30),
regista VARCHAR(20),
anno DECIMAL(4) NOT NULL,
genere CHAR(15) NOT NULL,
valutaz NUMERIC(3,2),
PRIMARY KEY(titolo,regista));

CREATE TABLE Video(
    colloc DECIMAL(4) PRIMARY KEY
    titolo VARCHAR(30) NOT NULL,
    regista VARCHAR(30) NOT NULL,
    tipo CHAR NOT NULL DEFAULT 'd',
    FOREIGN KEY (titolo,regista) REFERENCES Film
);
```

![alt text](image-1.png)

Esempio *inline*:
```sql
CREATE TABLE Cliente(
    codCli DECIMAL(4) PRIAMRY KEY,
    ...
)

CREATE TABLE Noleggio(
    colloc DECIMAL(4) REFERENCES Video,
    dataNol DATE DEFAULT CURRENT_DATE,
    codCli DECIMAL(4) NOT NULL REFERENCES Cliente,
    ...
)
```

Nella clausola di **FOREIGN KEY** possiamo specificare dei comportamenti in caso di aggiornamento/cancellazione della chiave primaria che è usata come chiave esterna in una relazione:
- `ON UPDATE` permette di specificare le azioni da eseguire nel caso di modifica del valore di chiave di una tupla riferita tramite chiave esterna
- `ON DELETE` permette di specificare le azioni da eseguire nel caso di cancellazione di tuple nella tabella riferita tramite chiave esterna.
In entrambi i casi abbiamo 4 opzioni disponibili:
- `NO ACTION`: la cancellazione/modifica di una tupla dalla tabella riferita è eseguita solo se non esiste alcuna tupla nella tabella referente che fa riferimento alla tupla da cancellare
- `CASCADE`:  la cancellazione/modifica di una tupla dalla tabella riferita implica la cancellazione/modifica di tutte le tuple della tabella referente che fanno riferimento alla tupla da cancellare/modificare
- `SET NULL`: la cancellazione/modifica di una tupla dalla tabella riferita implica che in tutte le tuple della tabella referente che fanno riferimento alla tupla da cancellare/modificare, la chiave esterna viene posta a valore `NULL` (se ammesso)
- `SET DEFAULT`: la cancellazione/modifica di una tupla dalla tabella riferita implica che in tutte le tuple della tabella referente che fanno riferimento alla tupla da cancellare/modificare, la chiave esterna viene posta uguale al valore did efault specificato nel comando di CREATE TABLE.

In entrambi i casi, se omessa la modalitàm, di default si imposta `NO ACTION` per entrambi i casi. Non esiste una combinazione corretta o errata, dipende sempre dai vari casi di utilizzo ma la più utilizzata è `ON UPDATE CASCADE ON DELETE NO ACTION`.\

Infine l'ultimo vincolo di integrità definibile sul costrutto `CREATE TABLE` è il vincolo di tipo `CHECK` che consente di specificare condizioni di validità su una colonna o relazione.

Vincoli **CHECK su colonna** sono definiti come:
```sql
<nome attributo> <dominio> CHECK (query)
```
Ad esempio:
```sql
CREATE TABLE Film(..., 
    valutaz DECIMAL (3,2) CHECK (valutaz BETWEEN 0.00 AND 5.00),
    ...
);
```
Vincoli **CHECK su tabella** sono definiti alla fine dell'istruzione `CREATE TABLE`:
```sql
CREATE TABLE Film(
    ...,
    CHECK(dataRest >= dataNol)
);
```

Il vincolo CHECK deve sempre restituire `True/False` e viene invocato in caso di inserimento/aggiornamento e nel caso positivo, il record viene aggiunto/aggiornato, nel caso negativo non viene inserito/aggiornato.



## Operatori insiemistici
`UNION`,`INTERSECT` e `EXCEPT`.

Importante sottolineare che gli operatori insiemistici operano sullo stesso schema, quindi se una query restituisce una coppia di varchar anche l'altra parte della union deve dare coppie di varchar.
Inoltre non possiamo fare affidamento sull'ordine. Non abbiamo garanzia che avremo in output tutta la query uno e poi tutta la query due.

Altra nota è la clausola `ALL`, che non rimuove i duplicati e non fà ordinamento (se mettiamo `UNION` e basta, toglie i duplicati e fa ordinamento).

Per visualizzare le performance di una query possiamo andare a vedere l'*execution-plan*.

Il costrutto `VALUES` ci permette di generare tabelle temporanee che non ha nome ma può essere referenziata nella query.

```sql
select *
from (values('Erba','Lorenzo'), ('Liguori','Nicolas')) as tab_prova (cognome,nome)
union
select *
from (values('Martini','Laura'), ('Santi','Simone')) /* ometto alias, comanda la master che è la tabella di sx */
```

Con la keyword `explain` in testa a una query, posso vedere l'execution-plan di quella query, da leggere dal basso verso l'alto:

![alt text](image.png)

Nel caso di union, un duplicato è un record che compare sia in un insieme che nell'altro mentre nel caso di intersect significa che ho due o più record uguali in entrambi gli insiemi.

Nel caso di except, il duplicato é quando è la tabella di sinistra (la master) che ha due o più record uguali. Quindi se applico all li mantiene, se tolgo all toglie tutti i duplicati.

Esempio di duplicati con `UNION`:
```sql
select *
from (values('Lorenzo','Erba'), ('Nicolas','Liguori')) as tab_prova (cognome,nome)
union
select *
from (values('Lorenzo','Erba'), ('Santi','Simone')) as tab_prova_2 
```

Esempio di duplicati con `INTERSECT`:
```sql
select *
from (values('Lorenzo','Erba'),('Lorenzo','Erba'), ('Nicolas','Liguori')) as tab_prova (cognome,nome)
intersect
select *
from (values('Erba','Lorenzo'), ('Santi','Simone'),('Lorenzo','Erba')) as tab_prova_2 
```

Esempio di duplicati con `EXCEPT`:
```sql
select *
from (values('Lorenzo','Erba'),('Lorenzo','Erba'), ('Nicolas','Liguori')) as tab_prova (cognome,nome)
except
select *
from (values('Erba','Lorenzo'), ('Santi','Simone')) as tab_prova_2 
```

Il vantaggio degli insiemistici è che per esempio per le quadrature (controllare che una tabella copia ha tutti i record copiati correttamente) si usa molto `EXCEPT` e mi tolgo le seccature di gestione dei *null values*. Infatti è proprio l'operatore insiemistico che si gestisce la gestione dei null. Di fatti in una `JOIN` o `WHERE` l'espressione `NULL= NULL` restituisce `UNKNOWN` poichè non `NULL` non è confrontabile, per tanto dobbiamo usare il costrutto `IS [NOT] NULL`. Con gli operatori insiemistici invece è automatico il riconoscimento dei *null-values* (se eseguiamo una differenza insiemistica e `NULL` è presente in entrambi gli insiemi, l'operatore lo riconosce ed esegue la differenza).

Abbiamo poi alcuni *work-around* per gestire le incongruenze tra i datatype delle due tabelle quando operiamo con operatori insiemistici. Un esempio è attraverso i casting:

```sql
/* work around con casting */
select cognome,cast(nome as varchar)
from (values('Lorenzo',1), ('Nicolas',2)) as tab_prova (cognome,nome)
union
select *
from (values('Erba','Lorenzo'), ('Santi','Simone')) as tab_prova_2 /* ometto nome colonne, comanda la master che è la tabella di sx */
```


Infine vediamo l'ordine di esecuzione degli operatori insiemistici:
- intersect ha la stessa precedenza della moltiplicazione
- union/except ha la stessa precedenza della somma

*Best-practice* è esplicitare con le parentesi l'ordine di esecuzione.
Di seguito vengono riportati esempi di esecuzione di operatori insiemistici:
```sql
/*A partire dalle tabelle 'familiari' e 'iscritti', estrarre i nomi di tutti i familiari e di tutti gli iscritti. */

select f.nome
from familiari f 
union
select i.nome 
from iscritti i 

/* equivalente con join */

select coalesce(f.nome,i.nome)
from familiari f full join iscritti i on f.nome = i.nome 

/*A partire dalle tabelle 'persone' e 'auto', estrarre il numero di patente di quelle persone che non hanno alcuna auto. */

select p.patente 
from persone p
except
select a.proprietario 
from auto a

/* equivalente con join */
select distinct p.patente 
from persone p left join auto a on a.proprietario = p.patente 
where a.proprietario is null


/*A partire dalle tabelle 'persone' e 'auto', estrarre il numero di patente di quelle persone che hanno almeno un'auto. */
select p.patente 
from persone p
intersect
select a.proprietario 
from auto a

/* equivalente con join */
select distinct p.patente 
from persone p inner join auto a on p.patente = a.proprietario 

/*A partire dalla tabelle 'employees' e 'jobs', estrarre le coppie job_id, salario di quei dipendenti il cui salario è il minimo per quel job_id. */

select e.job_id , e.salary 
from employees e 
intersect
select j.job_id, j.min_salary 
from jobs j

/* equivalente con join */

select distinct(e.job_id) , e.salary
from employees e join jobs j on e.job_id = j.job_id and e.salary = j.min_salary 

/*5. A partire dalle tabelle 'employees' e 'departments' estrarre tutti i dipendenti a capo di un dipartimento 
 * ed il capo dell'azienda (quel dipendente con manager_id NULL). Mostrare in output la coppia employee_id, 
 * ruolo (dove ruolo è una stringa del tipo 'Capo dipartimento <nome_dipartimento>'/'CEO' ) 
 */

select d.manager_id, 'Capo dipartimento ' || d.department_name as ruolo
from departments d 
where d.manager_id is not null
union
select e.employee_id, 'CEO' as ruolo
from employees e 
where e.manager_id is null


/* equivalente con join */

select e.employee_id, case
	when t.roles = 'is_ceo' then 'CEO'
	when t.roles = 'not_ceo' then 'Capo dipartimento ' || d.department_name
end as ruolo
from employees e left join departments d on e.employee_id = d.manager_id
cross join (values ('is_ceo'),('not_ceo')) as t(roles)
where (d.manager_id is not null and t.roles = 'not_ceo') or (e.manager_id is null and t.roles='is_ceo')

/*6. A partire dalla tabella 'employees' creare una tabella copia chiamata 'employees_copy'.  

Sulla tabella 'employees_copy' effettuare le seguenti operazioni: 

a) eliminare i record dei dipendenti con i 5 employee_id più grandi 

b) aggiornare i record dei dipendenti con i 5 employee_id più piccoli sommando un anno al valore dell'attributo hire_date 

c) inserire 2 record prendendo i valori degli attributi dai 2 record con gli employee_id più piccoli della tabella e sommando 200 al loro employee_id. 

Scrivere a questo punto una query che ci permetta, per i soli record valorizzati in modo differente in 'employees' ed 'employees_copy' o non presenti in una delle due tabelle, di vedere quali siano i valori assunti nelle due tabelle, aggiungendo nel risultato della query un campo informativo valorizzato con: 

- 'old' per il record proveniente dalla tabella 'employees' (se presente) 

- 'new' per il record proveniente dalla tabella 'employees_copy' (se presente)  */

drop table employees_copy

create table employees_copy as select * from employees

/*a) eliminare i record dei dipendenti con i 5 employee_id più grandi */
delete from employees_copy where employees_copy.employee_id in (
select e.employee_id from employees e order by e.employee_id desc limit 5)

/* b) aggiornare i record dei dipendenti con i 5 employee_id più piccoli sommando un anno al valore dell'attributo hire_date  */
update employees_copy ec set hire_date = (hire_date + interval '1 year') where ec.employee_id in (
select e.employee_id from employees e order by e.employee_id limit 5)

/*c) inserire 2 record prendendo i valori degli attributi dai 2 record con gli employee_id più piccoli della tabella e sommando 200 al loro employee_id.  */

insert into employees_copy 
select ec2.employee_id + 200, 
ec2.first_name, 
ec2.last_name, 
ec2.email, 
ec2.phone_number, 
ec2.hire_date, 
ec2.job_id, 
ec2.salary, 
ec2.commission_pct, 
ec2.manager_id, 
ec2.department_id 
from employees ec2
order by ec2.employee_id
limit 2

/*Scrivere a questo punto una query che ci permetta, per i soli record valorizzati in modo differente in 'employees' ed 'employees_copy' o non presenti in una delle due tabelle, di vedere quali siano i valori assunti nelle due tabelle, aggiungendo nel risultato della query un campo informativo valorizzato con: 

- 'old' per il record proveniente dalla tabella 'employees' (se presente) 

- 'new' per il record proveniente dalla tabella 'employees_copy' (se presente)  */

(select ec.*, 'new' as informazione
from employees_copy ec left join employees e on ec.employee_id = e.employee_id 
where e.employee_id is null or ec.* is distinct from e.*) /* restituisce i record presenti in employees_copy ma non in employees */
union
(select e.*, 'old' as informazione
from employees e left join employees_copy ec on e.employee_id = ec.employee_id 
where ec.employee_id is null or e.* is distinct from ec.*) /* restituisce i record presenti in employees ma non in employees_copy  */
order by employee_id

/*equivalente*/

select e.*, 'old' as informazione
from employees e 
except 
select ec.*, 'old' as informazione
from employees_copy ec 
union
select ec.*, 'new' as informazione
from employees_copy ec
except 
select e.*, 'new' as informazione
from employees e

/*equivalente e ottimizzata con with*/

with old as (
    select *
    from employees e 
    except 
    select *
    from employees_copy ec 
), new as (
    select *
    from employees_copy ec 
    except 
    select *
    from employees e 
)

select *, 'old' as informazione
from old
union
select *, 'new' as informazione
from new
```
## Sub-query

Le sub-query o sotto-interrogazioni rappresentano una tecnica ad elevate prestazioni e flessibilità in quanto consentono di specificare all'interno di una query (detta *query esterna*) una seconda query chiamata *query-interna*. Le sub-query si suddividono principalmente in due macro tipologie in tre sotto-categorie:
- sub-query scalare, si tratta di una sotto-interrogazione che restituisce un solo valore (e.g. `SELECT max(eta) FROM persona`)
- sub-query colonna, si tratta di una sotto-interrogazione che restituisce una colonna (e.g. `SELECT nome FROM persona`)
- sub-query tabella, si tratta di una sotto-interrogazione che restituisce una tabella con più di un attributo (e.g. `SELECT nome,cognome FROM persona`)

Vediamo ora le sub-query **annidate**. Le sotto-interrogazioni annidate si chiamano così in quanto vengono definite come interrogazioni interne ad un'altra interrogazione e sono caratterizzate dal fatto che vengono eseguite una sola volta prima della valutazione della query più esterna. Il vantaggio delle query annidate è che possiamo scomporre il problema iniziale con approccio *divide-et-impera*.

Di seguito possiamo notare un esempio di query annidata scalare che restituisce il film con la valutazione superiore alla media:
```sql
SELECT titolo
FROM film 
WHERE valutazione > (SELECT AVG(valutaz)
FROM film)
```

È facilmente intuibile che quando utilizziamo sotto-interrogazioni scalari gli operatori più utilizzata sono quelli di confronto come `<,>,<>,>=, <=, ='`, questo perchè si prestano bene a eseguire confronti tra singoli record, ma nulla vieta di utilizzare altri operatori. **N.B Una query annidata è scalare se operiamo con funzioni di raggruppamento o chiave primaria nella SELECT**.

Se si vuole utilizzare una sotto-interrogazione che restituisce più valori, è necessario specificare come i valori restituiti devono essere usati nella clausola `WHERE` della query esterna. Per tanto esistono alcuni operatori di confronto molto utili come `IN, ANY, ALL, EXISTS`. Per prima cosa vediamo un esempio di query annidata colonna dove vogliamo titolo e anno dei film più vecchi di tutti i film di Quentin Tarantino. Questa interogazione potremmo formularla con `MIN` prelevando il film più vecchio di Tarantino e confrontando la data con quella degli altri film ma in alcuni casi le funzioni di aggregazione non funzionano su alcuni data type (per esempio `AVG` su un tipo `Date`). Inoltre usando le funzioni di aggregazione si introducono alcuni problemi.Per esempio se la sub-query è vuota, `MIN` restituisce `NULL` e il risultato finale sarebbe `NULL`. 

Introduciamo in ordine di semplicità questi operatori partendo da `IN`. Questo operatore ha lo stesso funzionamento dell'operatore $\in$ in matematica, ovvero l'appartenenza insiemistica. La forma di utilizzo dell'operatore `IN` segue sempre questa forma: `value IN (value1,value2, ...)`. È immediato notare che `IN`:
- restituisce true se `value` è uguale a un qualsiasi valore specificato nella lista di destra
- restituisce false se `value` non è presente nell'insieme di destra (risulta diverso da tutti).

Questo operatore è la versione sintetica della concatezione di OR logici fra di loro `value= value1 OR value= value2 ...`.

Importante notare che la lista di valori può essere *hard-coded*, quindi specificati a mano, oppure provenire da una subquery, ma in entrambi i casi dev'essere mantenuta coerenza tra il valore a sinistra di `IN` e quelli a destra (come per gli operatori insiemistici). Per esempio:
- possiamo confrontare varchar con varchar (e.g. `(nome) IN ( ('Lorenzo') )`), invece `'Lorenzo' IN (1,2,3)` produce errore
- possiamo confrontare solo valori di pari cardinalità (e.g. `(nome,cognome) IN ( ('Lorenzo','Erba') )`), invece `(nome,cognome) IN ( ('Lorenzo') )` produce errore
- il confronto avviene in modo ordinato, la prima colonna di sinistra con la prima colonna di destra e così via.

Esempio di utilizzo è un'interrogazione dove vogliamo il peso delle persone nate in provincia di Milano o di Como:
```sql
SELECT peso
FROM persona
WHERE provincia_nascita IN ('Milano','Como')
```

Nel caso di subquery un esempio è restituire il nome e cognome di coloro che hanno noleggiato film a partire dal 2025:
```sqL
SELECT nome,cognome
FROM cliente
WHERE cod_cliente IN (SELECT cod_cliente
FROM noleggio
WHERE data_noleggio >= '2025-01-01')
```

Analogamente a `IN` esiste l'operatore `NOT IN` che inverte il risultato ottenuto dall'applicazione di `IN`.

Introduciamo ora l'operatore `ALL`, il quale viene sempre utilizzato nella forma `expression operator ALL (subquery)`, per esempio `anno < ALL (subquery)`. L'operatore `ALL` dev'essere sempre preceduto da un operatore di confronto (`<,>,<>,>=, <=, ='`) e seguito da una subquery racchiusa tra parentesi tonde. Il funzionamento è semplice:
- `ALL` restituisce true se l'espressione di confronto è vera per ogni valore restituito dalla subquery
- `ALL` restituisce false se esiste almeno un record della sottoquery che non soddisfa la condizione (basta un solo record che non soddisfa la condizione).

Se la sotto-interrogazione è vuota (nessun risultato restituito), `ALL` restituisce true.

In questo caso, per ogni film valutato nella query esterna, controllo che l'anno di produzione sia minore (antecedente) a **tutti** i film di Tarantino. Perciò se esiste un film di Tarantino che è più vecchio del film considerato esternamente, quest'ultimo non verrà prelevato. Se invece nella tabella `film` non sono presenti film di Quentin Tarantino, otterremo in output l'intera tabella film.
```sql 
SELECT titolo,anno
FROM film
WHERE anno < ALL (SELECT anno
                    FROM film
                    WHERE regista = 'Quentin Tarantino')
```

Possiamo inoltre notare che `<> ALL` è un predicato che coincide con `NOT IN`.


Vediamo invece l'operatore `ANY`. Questo predicato viene utilizzato nella forma `expression operator ANY (subquery)`, per esempio `stipendio > ANY (subquery)`. L'operatore `ANY` dev'essere sempre preceduto da un operatore di confronto (`<,>,<>,>=, <=, ='`) e seguito da una subquery racchiusa tra parentesi tonde. Il funzionamento è semplice, `ANY` restituisce:
- true se l'espressione di confronto è vera per almeno un valore restituito dalla subquery
- false se l'espressione di confronto è falsa per tutti i valori restituiti dalla subquery.

Se la sotto-interrogazione è vuota (nessun risultato restituito), `ALL` restituisce false.

In questo caso, per ogni persona valutata nella query esterna, controllo che la provincia di residenza sia **una qualsiasi** provincia della Lombardia. Perciò se quella persona risiede in una provincia della Lombardia, quel record viene prelevato altrimenti se la sua provincia di residenza non compare tra quelle lombarde, il record non viene preso.
```sql
SELECT nome,cognome
FROM persona
WHERE provincia_residenza = ANY(SELECT provincia
                                FROM tabella_provincia
                                WHERE regione = 'Lombardia'
)
```
Possiamo inoltre notare che `= ANY` è un predicato che coincide con `IN`.

Altro tipo di interrogazione è restituire lo stipendio dei dipendenti del reparto Marketing che guadagnano più di almeno uno dei colleghi del reparto Amministrazione.
```sql
SELECT d.sitpendio
FROM dipendenti d
WHERE d.reparto = 'Marketing' AND d.stipendio > ANY (SELECT d2.stipendio
                                                        FROM dipendenti d2
                                                        WHERE d.reparto = 'Amministrazione')
```

Infine vediamo l'operatore `EXISTS`. Questo predicato viene utilizzato nella forma `EXISTS (subquery)`. Come gli operatori visti precedentemente, `EXISTS` restituisce un booleano che può essere:
- true se la subquery restituisce almeno un risultato
- false se la subquery non restituisce risultati.

Importante notare che se la subquery restituisce `NULL` allora `EXISTS` restituisce true. Inoltre se per ottenere true basta un record nella subquery non conviene usare `SELECT *` poichè di quei campi non ce ne facciamo nulla, basta che ci sia un risultato. Una soluzione ottimale è usare i *literal values*, ovvero valori direttamente specificati nella clausola `SELECT` (per esempio `SELECT 1 FROM film` restituisce una tabella di una colonna dove avremo tante righe con il valore 1 quante quelle presenti in film).

Analogamente a `IN` l'operatore `EXISTS` ammette lo speculare `NOT EXISTS`.

Esempio di interrogazione può essere di volere il codice fiscale di ciascuna persona (per attivare promozioni) solo se il cinema ha film di produzione italiana in sala:
```sql
SELECT cf
FROM clienti
WHERE EXISTS (SELECT 1
                FROM film
                WHERE produzione='Italia')
```

Sebbene l'operatore `EXISTS` possa essere usato in query annidate, spesso viene utilizzato in **subquery correlate**.

A differenza delle subquery annidate, che vengono eseguite una sola volta prima della valutazione della query esterna e il cui risultato viene salvato in memoria e utilizzato per i confronti, le subquery correlate prevedono che per ogni tupla (record) valutato nella query esterna viene calcolata la query interna, prelevato il valore e usato per il confronto. Successiavamente per il prossimo record si ripete l'operazione. Questo suggerisce che le query correlate sono molto più costose in termini di prestazioni e che le possiamo identificare immediatamente se nella query interna compare un riferimento alla tabella della query esterna. Vediamo qualche esempio:

Restituire titolo, reigsta e anno dei film la cui valutazione è superiore alla media delle valutazioni dei film dello stesso regista:
```SQL
SELECT f.titolo, f.regista, f.anno
FROM film f
WHERE valutazione > (SELECT AVG(f2.valutazione)
                        FROM film f2
                        WHERE f2.regista = f.regista
)
```
Notiamo come sia necessario rinominare con *alias* entrambe le tabelle altrimenti non sapremmo a quale attributo di quale tabella fare riferimento. L'ordine di esecuzione prevede di caricare la tabella film e per ogni record guardare il campo valutazione e assicurarsi che sia maggiore della media delle valutazioni di quei film per cui il regista è lo stesso del film considerato fuori.

Con gli operatori introdotti precedentemente abbiamo:

Restituire il nome e cognome dei clienti che hanno noleggiato film usciti nello stesso anno in cui sono nati:
```sql
SELECT c.nome, c.cognome
FROM clienti c
WHERE c.anno_nascita IN (
    SELECT f.anno 
    FROM noleggio n 
    JOIN film f ON n.film_id = f.film_id
    WHERE n.cod_cliente = c.cod_cliente  -- Correlazione
)
```

Trovare i film che hanno un prezzo di noleggio inferiore ad almeno uno dei noleggi effettuati dal cliente Rossi dello stesso genere.
```sql
SELECT f1.titolo, f1.anno
FROM film f1
WHERE f1.prezzo < ANY ( SELECT f2.prezzo
                        FROM noleggio n JOIN film f2 ON n.film_id = f2.film_id JOIN clienti c on n.cod_cliente = c.cod_cliente
                        WHERE c.cognome = 'Rossi' 
                        AND f2.genere = f1.genere -- Correlazione

)
```

Restituire i film che costano più di tutti i film prodotti nello stesso anno:
```sql
SELECT f1.titolo, f1.budget, f1.anno
FROM film f1
WHERE f1.budget >= ALL (
    SELECT f2.budget FROM film f2 WHERE f2.anno = f1.anno
)
```
Importante notare che qua usiamo `>= ALL` in quanto se due film hanno lo stesso budget questa query li restituisce entrambi. Se avessimo inserito `> ALL` invece avremmo dovuto in primis escludere lo stesso film, altrimenti non sarebbe mai possiible essere maggiori di sè stessi, e inoltre a parità di budget non avremmo nessuno dei due film. Una via più leggibile è attraverso `MAX` evitando oltretutto l'autoconfronto.
```sql
--equilvanete
SELECT f1.titolo, f1.budget, f1.anno
FROM film f1
WHERE f1.budget = (
    SELECT MAX(f2.budget)
    FROM film f2
    WHERE f2.anno = f1.anno -- Correlazione: per lo stesso anno
)
```

Trovare il nome dei dipendenti dell'ufficio Marketing senza omonimo (stesso nome):
```sql
SELECT d.nome
FROM dipendenti d
WHERE d.ufficio = 'Marketing' AND NOT EXISTS(SELECT 1
                                            FROM dipendenti d2
                                            WHERE d2.ufficio = d.ufficio AND d2.nome = d.nome
                                            AND d2.matricola <> d.matricola) -- importante escludere se stesso altrimenti lui è uguale a lui)
```

Una piccola nota dev'essere precisata per quanto riguarda i *row constructor* o costruttori di tupla che abbiamo visto nel caso di `IN`. Essi permettono di realizzare una tupla "temporanea" a partire da:
- valori *hard-coded* come `('Lorenzo','Erba')` che crea una tupla di due attributi con valore fisso
- valori calcolati dal DBMS come `(nome,cognome)` che crea una tupla di due attributi con valore che dipende dagli attributi del record valutato

Oltre a essere utilizzati con operatori `IN, NOT IN` possono essere usati con operatori di confronto come `<,>,<>,>=, <=, =`, sia singolarmente che abbinati a `ALL,ANY`. È importante però notare che gli operatori di confronto si comportano in modo differente a seconda del tipo di operatore:
- operatori come `<>, =` restituiscono true se la riga è esattamente diversa,uguale rispetto all'altra, quindi si controllano **TUTTI** i campi
- operatori restanti come `<,>,>=,<=` prevedono un confronto gerarchico a partire da sinistra verso destra. Se ipotizziamo di usare `>` come operatore, il confronto cessa di essere eseguito quando un campo è maggiore del rispettivo dall'altro lato. `(1,2) > (0,4)` restituisce true, perchè `1>2` e non viene valutato `2>4`. Il confronto scorre a destra se il precedente fallisce, per esempio `(1,3) > (3,1)` restituisce true ma perchè `1>3` restituisce false e quindi si passa a valutare `3>1` che restituisce true.

Analogamente ad altri confronti, il confronto tra un `NULL` e un valore preciso o due `NULL` restituisce un `NULL`. Per ovviare a questo problema è possibile utilizzare i costrutti `IS DISTINCT FROM` e `IS NOT DISTINCT FROM` che operano rispettivamente come `<>` e `=` ma gestendo i *null-values* garantendo risultati solo come true/false e assenza di *null-result*

Le stesse regole valgono per quando combiniamo operatori di confronto con operatori `ALL` e `ANY`, che a differenza degli operatori di confronto che richiedono una subquery scalare, richiedono una subquery colonna o tabella. 