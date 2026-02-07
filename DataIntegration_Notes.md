# Data Integration

## Esercizi Riassuntivi

### Ese 1

**Creare nel repository Talend i metadati relativi al file Employees.txt.**

Per poter creare dei metadati per prima cosa andiamo nella sezione Repository -> Metradata -> File Delimited. Successivamente creaiamo un nuovo file delimited, fornendo un nome significativo, un path al file, formato Unix.

Alla schermata successiva specifichiamo:
- enconding UTF-8
- field separator (nel nostro caso "," ma dipende dal tipo di file .csv)
- row separator "Standard End Of Line"
- row to skip spuntiamo header con valore 1 (significa che ignoriamo l'header che si trova in riga 1)

Importante ora é il setting dell'escape char. Siccome siamo in un contesto Java con file CSV dobbiamo selezionare opzione per CSV file, inserendo:

- Escape char "\ "" (usiamo il \" come in Java per gli escape character)
- Text enclosure "\ "" (nel nostro caso ogni stringa nel file è racchiusa tra doppi apici. In questo modo ci assicuriamo che Talend legga il testo escludendo gli apici che lo racchiudono)

Clicchiamo su refresh preview per un cross-check, e se otteniamo un risultato simile a come lo otterremo su Excel, è tutto corretto.


---

### Ese 2

**Utilizzando i metadati definiti nell'esercizio 1, leggere il file Employees.txt e stamparne a video il contenuto.**

Per l'esercizio 2, dobbiamo realizzare per prima cosa un Job, cliccando con tasto destro su Job Designs e creando un nuovo Job (assegnandogli un nome significativo).

Successivamente possiamo inserire nel job due elementi, recuperabili in due modi:
1. Trascinandoli dalla palette di lato (dopo averli cercati dalla barra di ricerca)
2. Cliccare sulla scacchiera centrale e digitare direttamente il nome del componente.

I componenti necessari sono:

- tFileInputDelimited che rappresenta il nostro file csv da leggere in input
- tLogRow che fornisce la possibilità di stampare a video un log (report)

Questi due componenti però devono dialogare, di conseguenza necessitano di essere collegati. In Talend si hanno due tipi di collegamenti:
- row, che consente di trasferire un flusso dati (Data flow)
- trigger, che consente di trasferire un flusso di controllo (Control flow)

Nel nostro caso, possiamo collegare tFileInputDelimited al tLogRow semplicemente tirando la riga arancione da sx a dx. In questo caso inseriamo una row Main che trasferisce il contenuto dal componente a monte al componente a valle, in un unico flusso.

Ora passiamo a configurare il tFileInputDelimited.

Poichè esso rappresenta il file di input, andiamo a fare un dobbio click sul componente e selezioniamo la schermata "Component". Da qui:

- "Schema" = Repository
- selezioniamo il primo ... per selezionare lo schema creato nell'esercizio due (andiamo in file delimited >  "nome del file" > metadata). Se viene chiesto di propagare le modifiche, diciamo di sì, in questo modo verranno applicate anche al tLogRow. Per verificare, clicchiamo Edit Schema, se tutto coincide facciamo cancel
- File name/Stream = path/to/file.csv
- Spuntiamo CSV options, inserendo escape char e text enclosure come indicato nell'ese 1, e header = 1. Infine spuntiamo "Die on error" e togliamo la spunta da "Skip empty rows".

Rimamnendo sempre sulla scheda Component, selezioniamo "Advanced Settings" e modifichiamo l'enconding in UTF-8.

Ora passiamo a configurare il componente TLogRow. 

Sempre dalla scheda Component clicchiamo "Edit schema" e possiamo notare come a sx abbiamo lo schema di ingresso (tutte le colonne) mentre a dx abbiamo lo schema di output. Possiamo selezionare, a nostro piacimento per esempio, di voler in output solo uno o più campi, ma per il momento li lasciamo tutti e clicchiamo cancel.

Nella scheda Component, mettiamo il radio button su Mode = Table, in questo modo la stampa avrà un aspetto tipo tabella.

Terminato tutto possiamo andare nella scheda Run e cliccare "Run" per eseguire il job.


### Ese 3



