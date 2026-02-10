# Big Data Analytics

## Lezione 9/2/26 - Ripasso introduttivo

La resilienza degli RDD si può spiegare introducendo due famiglie di operazioni in Spark:
1. trasformazioni
2. azioni

Una trasformazione in Spark è un'operazione che a partire da un RDD crea un altro RDD. Si parla di operazioni immutabili, quindi una trasformazione non causa modifiche alla struttura originale (ad esempio il metodo .select() fa a tutti gli effetti una trasformazione). Le trasformazioni in Spark sono sempre indicizzate, ovvero a ogni RDD è associato un lineage (la sequenza di operazioni che portano alla formazione di quel RDD).

La proprietà fondamentale delle trasformazioni in Spark è che sono **lazy-evaluated** ovvero Spark di base esegue le trasformazioni solo quando servono (in altre parole solo quando vengono chieste esplicitamente, attraverso le azioni).

Le **azioni** sono tutte le operazioni che permettono di ottenere un risultato, per esempio la funzione .count().

Quindi avere un lineage associato a ogni trasformazione garantisce resilienza (in caso di errori posso ripercorrere all'indietro la costruzione del dato per garantire **fault-tolerance**). 


Abbiamo due tipi di trasformazioni:
- **narrow**: input e output sono nella stessa partizione, quindi no data movement (per esempio map e filter)
- **wide**: è necessario pescare input da altre partizioni e quindi i data necessariamente vengono mischiati prima del processing (per esempio groupBy, join e distinct). L'operazione di trasferimento dati tra partizioni prende il nome di *shuffle* ed è un'operazione pesante perchè genera traffico sul cluster di nodi.
---
Vediamo ora come viene organizzata un'applicazione Spark proprio sul piano di esecuzione.

Un'applicazione viene inizialmente divisa in una serie di job, una serie di operazioni generate in risposta a una determinata azione.
I job creano a loro volta una serie di operazioni chiamate Stage, che sono operazioni eseguibili senza shuffle. Se eseguo una nuova trasformazione wide, genero pertanto un nuovo stage. A loro volta infine gli stage sono composti da una serie di task che sono le unità elementari da eseguire sugli executor del cluster.S
