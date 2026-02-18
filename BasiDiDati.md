# Basi di dati 
All'interno di questo file markdown sono presenti appunti ed esercitazioni riguardanti il corso di Basi di Dati.

## Piccolo disclaimer
Poichè posseggo una discreta conoscenza dei database relazionali e linguaggi di query, all'interno del file sono presenti solo informazioni che per me risultano nuove o poco chiare, per cui conviene tenerne traccia negli appunti.

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

Nel caso di union, un duplicato è un record che compare sia a destra che a sinistra, mentre nel caso di intersect significa che ho due o più record uguali sia a destra che a sinistra.

Nel caso di except, il duplicato é quando la tabella di sinistra (la master) che ha due o più record uguali. Quindi se applico all li mantiene, se tolgo all toglie tutti i duplicati.

Il vantaggio degli insiemistici è che per esempio per le quadrature si usa molto `EXCEPT` e mi tolgo le seccature di gestione dei null values. Infatti è proprio l'operatore insiemistico che si gestisce la gestione dei null.

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