# Java Propedeutico
All'interno di questo blocco di appunti sono presenti delle note prese dal corso di Java. 
## Piccolo disclaimer
Poichè conosco già Java, mi limiterò a inserire solo appunti di istruzioni che per me sono nuove o che ho usato poche volte e quindi per cui vale la pena tenere traccia. Eventuali parti mancanti o non trattate nelle note possono essere integrate da siti come W3school o documentazioni semi-ufficiali/ufficiali.

## Java Basics

### Intro Java

For each loop:
```java
    System.out.println("===== for enhanced =====");
    // array: collezione omogenea di dati vicini in memoria
    int[] arr = {3, 53, 64, 253};
    for (int value : arr) {
        System.out.println(value);
    }
```

Nuovo switch case:
```java
    // switch nuovo
    int other = 5;
    switch (other) {
    case 3 -> {}
    case 5 -> System.out.println("sei nel caso 5");
    case 6 -> System.out.println("oh no sono sul 6");
    default -> System.out.println("def");
    }
```

Se omettiamo il modificatore di accesso (`public,private, protected`), Java si comporta come package-private ovvero assegna di default `private` come modificatore di accesso.

A partire da Java 17 è possibile definire i Text Blocks per semplificare la scrittura su più righe.

```java
String messaggio = """
    Ciao!
    Questa è una stringa
    su più righe in Java 17.
    """;
```
Alcuni vantaggi sono per esempio definire blocchi di testo JSON o query SQL dove i doppi apici possono essere inseriti senza il carattere di escape `\`:
```java
String json = """
    {
      "utente": "Mario Rossi",
      "eta": 30,
      "email": "mario@example.com"
    }
    """;

String query = """
    SELECT id, nome, cognome
    FROM utenti
    WHERE stato = 'attivo'
    ORDER BY nome ASC;
    """;
```

Alcuni metodi della classe String:
- `valueOf`:ritorna una stringa a partire da qualcosa che non è una stringa, un carattere ad esempio
- `charAt`: ritorna il carattere in posizione i-esima nella stringa
- `codePointAt`: ritorna il codice ASCII corrispondente al carattere in posizione i-esima.
- `trim`: ritorna la stringa senza caratteri non stampabili all'inizio e alla fine
                 ovvero pulisce la stringa da spazi, a capi, tab all'inizio e alla fine
- `matches`: ritorna true se la stringa rispetta la regex, false altrimenti

A partire da Java 10 è possibile usare la keyword `var` per permettere l'inferenza del tipo locale. In pratica stiamo comunicando al compilatore di assegnare alla variabile un tipo in base al valore associato a destra dell'uguaglianza senza che venga specificato da parte nostra.

```java
var numero = 10; // il compilatore capisce che è un int
var nome = "Bob"; // il compilatore capisce che è una String
var check = List.of(1,2,3); // il compilatore capisce che è una List
```

Le limitazioni nell'uso di `var`:
- **Utilizzo solo nelle variabili locali**. Si può usarlo solo dentro i metodi ma non puoi usarlo per attributi di classe, parametri di metodi o tipi di ritorno.
- **Inizializzazione immediata**: Devi assegnare un valore sulla stessa riga (es. `var x`; non compila perché Java non sa cos'è `x`).
- **Niente null**: Non puoi scrivere `var x = null`; perché il tipo rimarrebbe ambiguo.
- **Tipizzazione statica**: una volta che il compilatore ha deciso che `var x = 10`; è un int, non potrai mai assegnargli una stringa più avanti nel codice.

Importante notare che, poichè il tipo della variabile viene risolto in compilazione, la variabile può chiamare metodi/attributi della classe in quanto il tipo viene riconosciuto subito.

### Wrapper

In Java oltre ai tipi primitivi abbiamo le classi wrapper, delle classi che offrono metodi e la possibilità di inizializzare il valore a `null`.

Esempi di classi wrapper sono `Integer,Float...`. Quando Java da un tipo primitivo crea un tipo wrapper, esegue il *boxing*:
```java
Integer integerwrapper = 2;
```

Viceversa, quando si passa da un tipo wrapper a un primitivo si esegue l'*unboxing*:
```java
System.out.println(n2 + 43);
```

Importante notare che Java, eseguendo l'unboxing automatico, da errore se proviamo a sommare un intero un valore null:
```java
// Da errore
Integer x = null;
System.out.println(x + 3);
```

### Interfacce e classi astratte

Una classe definita come `abstract` permette di definire una classe non istanziabile, solitamente con un comportamento base, e che deve avere almeno un metodo astratto ma può avere anche metodi definiti. Inoltre una classe astratta può possedere delle variabili d'istanza. Cosa importante, si può estendere una sola classe astratta tramite `extends`. Se omesso i metodi sono `private`.

Un'interfaccia invece rappresenta una sorta di classe, non istanzaibile, con solo metodi non definiti (metodi definiti possono essere solo `default`,`static` e `private`). Supporta solo variabili d'istanza `final` (costanti) e se ne possono implementare molte tramite `implements`. Se omesso i metodi sono `public abstract`.

Come detto le interfacce possono avere metodi definiti, ma possono essere di tipo `default, private o static`:
```java
/*
     Posso avere dei metodi di default che hanno un'implementazione di base
     L'implementazione è comune a tutti e se si vuole personalizzare si può overridare.
     Esattamente come abbiamo visto per le classi normali.
    */
    default boolean scontato() {
        return false;
    }
``` 
### Strutture dati
Vediamo una carrellata veloce di strutture dati.

Le liste o `List` sono collezioni di dati omogenei di dimensione variabile dove in generale si possono aggiungere e rimuovere elementi per indice. Essendo `List` un interfaccia essa può essere poi istanziata con tipo dinamico a seconda dell'esigenza, solitamente:
- `ArrayList`: array che si finge come una lista. All'esterno sono array di dimensione variabile mentre internamente sono implementati come array di dimensione fissa (se si vuole aggiungere un nuovo elemento, si crea un array più grande e si copiano i precedenti valori dal vecchio array)
- `LinkedList`: realizza una lista concatenata dove si ha riferiento al primo e/o ultimo elemento e ogni elemento nella lista ha un riferimento al valore successivo. Importante notare che `LinkedList` implementa più interfacce, per tale motivo se vogliamo sfruttare i metodi di puntamento al primo e ultimo elemento, anche il tipo statico dev'essere definito come `LinkedList`.

```java
// Dichiarazione di una LinkedList con tipo statico List 
List<String> linkedListComeList = new LinkedList<>();
        linkedListComeList.add("primo");
        linkedListComeList.add("secondo");
        secondoElemento = linkedListComeList.get(1);

// Dichiarazione di una LinkedList con tipo statico LinkedList
LinkedList<String> linkedListComeLinkedList = new LinkedList<>();
        linkedListComeLinkedList.addFirst("primo");
        linkedListComeLinkedList.addLast("ultimo");
        linkedListComeLinkedList.addLast("ultimo davvero");
        linkedListComeLinkedList.add("ultimo davvero davvero");

// Dichiarazione di un ArrayList
List<String> arrayList = new ArrayList<>();
        arrayList.add("primo");
        arrayList.add("secondo");
        arrayList.toArray();
```

Altri tipi di collezione sono:
- Queue: realizza una coda con accesso FIFO. Trattandosi di un'interfaccia può essere implementata come ArrayList tramite `ArrayDeque` oppure tramite lista concatenata come `LinkedList`.

```java
Queue<String> codaComeArrayList = new ArrayDeque<>();
codaComeArrayList.add("primo elemento");
codaComeArrayList.offer("elemento incodato");
codaComeArrayList.offer("altro elemento incodato");
String elementoScodato = codaComeArrayList.poll(); // poll prende e rimuove l'elemento dalla coda
elementoScodato = codaComeArrayList.peek(); // peek prende l'elemento senza rimuovere
codaComeArrayList.remove(); // remove prende e rimuove l'elemento dalla coda


Queue<String> codaComeLinkedList = new LinkedList<>(); //uguale ma cambia il funzionamento interno
codaComeLinkedList.add("primo elemento");
codaComeLinkedList.offer("elemento incodato");
codaComeLinkedList.offer("altro elemento incodato");
elementoScodato = codaComeLinkedList.poll(); // poll prende e rimuove l'elemento dalla coda
elementoScodato = codaComeLinkedList.peek(); // peek prende l'elemento senza rimuovere
codaComeLinkedList.remove(); // remove prende e rimuove l'elemento dalla coda
```
- Stack: rappresenta una pila di elementi con politica di accesso LIFO.
```java
Stack<String> stack = new Stack<>();
stack.addElement("primissimo"); //addElement e push sono uguali
stack.push("primo");
stack.push("secondo");
String elementoSpilato = stack.pop();// prende e rimuove l'elemento dalla pila
elementoSpilato = stack.peek();// peek prende l'elemento senza rimuovere
```
- Map: rappresenta una collezione di coppie di elementi chiave-valore, come un dizionario si accede ad ogni valore in base alla chiave. Essendo un'interfaccia può essere implementata come (le più comuni):
  - HashMap: è l'implementazione più performante, basata su una tabella hash. Non garantisce alcun ordine per le chiavi e permette l'inserimento di una chiave `null`.
  - TreeMap: è un'implementazione basata su una struttura ad albero (Red-Black tree). Mantiene le chiavi sempre ordinate secondo il loro ordine naturale (es. alfabetico o numerico) o un comparatore personalizzato e non permette chiavi `null`.

Le Map in Java sono collezioni di tipo `<key,valore>`:
```java
Map<String, String> mappaConChiaveStringaEValoreStringa = new HashMap<>();
        mappaConChiaveStringaEValoreStringa.put("chiave", "valore");
        mappaConChiaveStringaEValoreStringa.put("altraChiave", "altroValore");
        String altroValore = mappaConChiaveStringaEValoreStringa.get("altraChiave");

```
Esempio di utilizzo delle mappe:

```java
/*
        Possiamo usare le mappe come modo per contare o raggruppare elementi
        Il costo principale viene dal fatto che dobbiamo costruire tutta la mappa prima di interrogarla
        ma poi il costo per ogni interrogazione è costante
     */
    public static Map<Integer, Integer> count(int[] nums) {
        Map<Integer, Integer> counter = new HashMap<>();

        for (int n : nums) {
            int prev = counter.getOrDefault(n, 0);
            counter.put(n, prev + 1);
        }

        return counter;
    }
/*
        Le mappe sono comode da usare come cache.
        Nella funzione fib ci sono alcuni calcoli che si ripetono
        e più n sarà grande più questi calcoli si ripeteranno.
        Possiamo sacrificare un po' di memoria per accorciare il tempo di esecuzione:
        usiamo una mappa per tener traccia dei numeri n che abbiamo già calcolato così ci risparmiamo
        di ricalcolarlo altre volte
     */
    public static long fib(long n, Map<Long, Long> cache) {
        if (n == 0 || n == 1) return n;

        if (cache.containsKey(n)) {
            return cache.get(n);
        }
        long res = fib(n - 1, cache) + fib(n - 2, cache);
        cache.put(n, res);

        return res;
    }
```

- Set: si tratta di una collezione di dati omogenei che non ammette duplicati. Può essere implementata come le `Map` con `HashSet,TreeSet`. Nel caso per esempio di `HashSet` invece di usare una tupla chiave-valore ha solo un valore.
```java
Set<String> set = new HashSet<>();
        set.add("Ciao");
        set.add("Ciao");
        set.add("NonCiao");
        int numElementi = set.size();// l' elemento duplicato verra eliminato, la size sarà 2
```

- PriorityQueue: si tratta di una collezione di dati che viene gestita con una politica di accesso prioritaria.
```java
PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
        pq.add(4);
        pq.add(2);
        pq.add(8);
        pq.add(18);
        pq.add(1);
// Gli elementi verranno accodati nel seguente ordine: 18,8,4,2,1
```

### Programmazione funzionale

Da Java 8 è stato introdotto il paradigma funzionale all'interno di Java. Esso permette infatti di specificare e utilizzare funzioni, oggetti che sono puri, non producono effetti collaterali. Una funzione, se chiamata cento volte con lo stesso input produrrà sempre lo stesso output.

In Java la classe `Optional` rappresenta un wrapper per funzioni che operano su un solo elemento. Il vantaggio di lavorare con questa classe wrapper è che possiamo agire con valori nulli senza avere problemi, basta sfruttare i metodi offerti dalla classe.
```java
//Creo un opzionale vuoto
Optional<Integer> optI = Optional.empty()

//Creo un opzionale a partire da un valore che so non essere nullo
Optional<Integer> optI = Optional.of(4)

//Creo un opzionae a partire da un valore che potrebbe essere nullo
Optional<Integer> optI = Optional.ofNullable(4)

optI.isPresent(); // mi dice se l'elemento che contiene non è nullo
optI.isEmpty(); // mi dice se l'elemento che contiene è nullo
optI.get(); // prende l'elemento contenuto nell'opzionale (attenzione se l'opzionale è vuoto questo lancerà eccezione!)
```

Esempio di applicazione di un `Optional` è quando si ha un oggetto annidato, in diversi livelli da esplorare, per esempio
```java
public class A{
    B b;
}

public class B{
    C c;
}

public class C{
    int val;
}
```
Ora in un contesto senza programmazione funzionale, dovremmo agire in questo modo per ottenere il valore intero a partire dall'oggetto di classe `A`:
```java
if (a != null && a.b != null && a.b.c != null) {
    System.out.println(a.b.c.val);
}
```
Questo metodo, per quanto legittimo, risulta essere dispendioso e poco chiaro, in quanto dovremmo realizzare una serie di if annidati per verificare che ogni oggetto non è nullo.

Di conseguenza `Optional` fornisce un modo chiaro ed elegante di eseguire gli stessi controlli:
```java
A aInput = new A();
int x = Optional.ofNullable(aInput) // a partire da un valore che non son sicuro esserci
                // Ogni volta che uso map implicitamente vengono wrappati tutti i valori in degli opzionali
                .map(a -> a.b) // passo da a a a.b
                .map(b -> b.c) // passo da b a b.c
                .map(c -> c.val) // passo da c a c.val
                // A questo punto ho un opzionale che se è pieno contiene val altrimenti sarà vuoto
                .orElse(0);

```
In questo modo stiamo "comunicando" a Java:
- crea un oggetto `Optional` attorno all'oggetto `aInput` 
  - se `aInput` esiste, la scatola è piena.
  - se aInput è `null`, la scatola è vuota.
- applica il metodo `map` all'oggetto `Opitonal` applicando la *Lambda Expression* `a -> a.b`. Questa espressione rappresenta di fatto una funzione, che prende in input un oggetto chiamato `a` e restituisce in output `a.b`. Quindi `map` prende in input l'oggetto `aInput` e se:
  - se non è `null`, modifica l'oggetto Optional in modo che contenga `aInput.b`. Se `aInput.b` è `null` il nuovo Optional sarà vuoto altrimenti contiene il valore restituito.
  - se è `null` Java salta questo passaggio e restituisce un Optional vuoto. 
- applica un nuovo `map` al nuovo Optional utilizzando la Lambda Expression `b -> b.c`. Se l'opzionale non risulta vuoto, applica il map e se `b.c` non è `null` si ottiene un nuovo opzionale.
- applica un nuovo `map` al nuovo Optional, utilizzando la Lambda Expression `c -> c.val`. 
- applica al nuovo Optional il metodo `.orElse` il quale prevede di assegnare il valore specificato (in questo caso 0) se l'Opzionale è vuoto, altrimenti mantiene il suo valore attuale.

Un altro esempio più descrittivo riguarda la necessità di reperire l'indirizzo di un cliente. Se il cliente per qualche motivo non ha specificato l'indirizzo avremo alla fine un Opzionale vuoto, ma grazie al metodo `.orElse` possiamo bypassare questo problema e assegnare noi un valore a un Opzionale vuoto.
```java

Optional.ofNullable(ordine)  // ordine è di classe 'Ordine'
    .map(o -> o.cliente)            // cliente è di classe 'Cliente'
    .map(c -> c.indirizzo)          // indirizzo è di classe 'Indirizzo'
    .map(i -> i.citta)              // citta è una String
    .orElse("Città sconosciuta");
```

Ora passiamo a vedere la classe `Stream`. In Java la classe `Stream` rappresenta un wrapper per funzioni che operano su più elementi.

Per esempio questo codice, a partire da uno stream di interi, applica la funzione `toDouble`, poi la funzione `toSquare` e infine la funzione `isBelow70`, la quale restituisce `true` se l'intero è minore di 70 (nel caso degli Stream si applica la filter a ogni elemento, se la condizione viene rispettata l'elemento rimane altrimenti viene scartato):
```java
List<Integer> transformed = Stream.of(7, 2, 4, 21)
                .map(Main::toDouble)
                .map(Main::toSquare)
                .filter(Main::isBelow70)
                // qui ricolleziono gli elementi dello stream in una lista
                .toList();
```

Un esempio simile al precedente che permette di prendere il primo elemento dello stream:
```java
int firstTransformed = Stream.of(7, 2, 4, 21)
                .map(Main::toDouble)
                .map(Main::toSquare)
                .filter(Main::isBelow70)
                // qui prendo il primo elemento, visto che può non esserci viene wrappato in un opzionale
                .findFirst() // resttiuisce un Optional<Integer>
                .orElse(-1);
```

Un'altra funzione utile è `reduce`, che prende in input lo stream e lo compatta in unico elemento a seconda della funzione specificata.
```java
List<Integer> lista = List.of(1,2,3,4);
//1 rappresenta l'elemento neutro dell'operazione moltiplicazione
//(acc,x) -> acc * x rappresenta la funzione che prendéin input due elementi e restituisce il prodotto, dove acc è l'accumulatore dei prodotti parziali mentre x è l'elemento i-esimo della lista
int resultCompressed = lista.stream()
        .reduce(1,(acc,x)-> acc * x);

```

Possiamo realizzare anche degli stream a partire da delle stringhe. In questo caso andiamo a usare il metodo `lines` che restituisce uno stream di stringhe separando le righe (spezza la stringa dove incontra un `\n`). Successivamente applichiamo la funzione `map(String::trim)` che modifica lo stream, eliminando caratteri di spazio e tabulazioni. Poi applichiamo `mapToInt` a cui passiamo una funzione che prenda l'oggetto e restituisca un int (nel nostro caso la built-in `Integer.parseInt`). Infine applichiamo la funzione `sum`, la quale applica la reduce vista prima con una funzione somma.

```java 
String l = "1\n2\n3\n";

int sumOfStr = l.lines()
        .map(String::trim)
        // map to int mi genera un IntStream, uno stream specializzato per gli interi
        .mapToInt(Integer::parseInt)
        // l'intStream ha un metodo per sommare gli elementi
        .sum();
```

Se vogliamo generare uno stream di valori compresi in un determinato range, possiamo utilizzare la funzione `range` della classe `IntStream`. Successivamente applichiamo la funzione `average` che restituisce un `OptionalDouble` in quanto lo stream potrebbe essere vuoto.
```java
double avg = IntStream.range(0, 11).average().orElse(0.0);
```

Analogamente possiamo calcolarci la media a partire da un array:
```java
int[] arr = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
double avg2 = Arrays.stream(arr).average().orElse(0.0);
```

Di seguito possiamo osservare un'applicazione degli stream:
```java
public static void main(String[] args) {
        System.out.println(areAnagram(List.of("ciao", "caio", "coia", "fahskdf")));
    }

private static boolean areAnagram(List<String> s) {
    return s
            .stream()
            .map(AnagrammiFunzionali::anagram)
            .collect(Collectors.groupingBy(Function.identity(), Collectors.toSet()))
            .size() == 1;
}

private static Map<Integer, Long> anagram(String input) {
    return input
            .chars()
            .boxed()
            .collect(Collectors.groupingBy(
                    Function.identity(),
                    Collectors.counting())
            );
}
```

In questo blocco di codice possiamo osservare che a partire da una lista di stringhe, generiamo uno stream al quale applichiamo un mapping basato sulla funzione `AnagrammiFunzionali::anagram`. 

Questa funzione prende una stringa in input e genera in output una mappa dove si memorizza come chiave la codifica ASCII di ciascuna lettera e come valore il conteggio delle occorrenze. Nel dettaglio a partire dalla stringa in input applichiamo `chars` che restituisce un `IntStream` coi valori ASCII di ciascuna lettera. Successivamente chiamiamo `boxed` che wrappa lo stream di int in `Stream<Integer>`. Infine applichiamo la funzione `collect` sullo stream di interi appena ottenuto, dove andiamo a fare raggruppamento `Collectors.groupingBy`, in cui specifichiamo di usare come chiave il valore stesso (`Function.identity`) e come valore associato il conteggio delle occorrenze (`Collectors.counting`).

Dopo aver applicato la map (che ricordiamo restituisce una mappa per ogni stringa dello stream, e quindi otteniamo uno `Stream<Map>`) applichiamo ancora una volta una collect per raggruppamento, dove usiamo come chiave il valore stesso e come valore un oggetto `Set` che si occupa di creare un set di elementi per cui se un elemento è già presente non verrà inserito due volte. Quindi se collect restituisce una Map dove la chiave è una mappa e il valore un set, otteniamo un `Map<Map,Set>`.

Infine restituiamo il risultato di `size` applicato a una mappa, che restituisce la dimensione della mappa stessa (intesa come, quante coppie chiave:valore ci sono).

Esempio di output applicato alle stringe indicate nel codice precedente:
```java
// CHIAVE 1: La mappa delle frequenze per "ciao" (e i suoi anagrammi)
    {99=1, 105=1, 97=1, 111=1} = [
        {99=1, 105=1, 97=1, 111=1}
    ],

    // CHIAVE 2: La mappa delle frequenze per "fahskdf"
    {102=2, 97=1, 104=1, 115=1, 107=1, 100=1} = [
        {102=2, 97=1, 104=1, 115=1, 107=1, 100=1}
    ]

```

Il seguente blocco di codice replica quanto visto ma con uno stream di interi:
```java
List<Integer> nums = List.of(123, 34, 45, 55, 55, 55, 34);
var s = nums
    .stream()
    .collect(Collectors.groupingBy(
            Function.identity(),
            Collectors.toList() // (toList per aggregare le liste)
    ))
    .values(); // stampa i valori associati a ogni chiave (ogni chiave ha una lista di valori associata)
```

Di seguito possiamo vedere come realizzare una generica funzione in Java tramite `Function`, una generica funzione che prende in input esattamente un valore e restituisce un solo output. Questa funzione viene definita e successivamente possiamo utilizzarla tramite il metodo `apply`. Il vantaggio di funzioni definite in primis e applicate solo all'occorrenza è proprio la lazy evaluation (una funzione viene definita ma eseguita solo all'occorrenza)
```java
Function<Persona, String> fnGetNome = x -> x.nome;
String tizio = fnGetNome.apply(persone.getLast())
```
Un esempio di vantaggio per la lazy evaluation è quando operiamo con gli stream. Fin quando non eseguiamo operazioni terminali (`forEach` oppure `findFirst`), la funzione viene letta ma non eseguita. Inoltre se applichiamo per esempio una `filter`, successivamente un `map` e poi un `findFirst`, l'ottimizzatore di Java applica queste operazioni direttamente sul primo elemento, e se rispetta le condizioni, ignora l'esecuzione delle trasformazioni sugli elementi successivi:
```java
List<String> nomi = List.of("Al", "Bob", "Anna", "Claudio");

String risultato = nomi.stream()
    .filter(s -> s.startsWith("A"))
    .map(s -> s.toUpperCase())
    .findFirst() // <-- Operazione terminale
    .orElse("");
```

Un altro esempio di funzione è `Supplier`, una function che ritorna un valore senza prenderne uno in ingresso.
```java
Supplier<String> getHello = () -> "Hello!";
getHello.get();
```
Abbiamo poi `Predicate`, una function che prende in input esattamente un parametro e restitusice sempre un solo valore booleano.
```java
Predicate<Persona> isCliente = p -> p.codiceCliente != null;
System.out.println(isCliente.test(persona1));
```

Abbiamo poi `Consumer`, una funzione che prende esattamente un solo input ma non restituisce nulla
```java
List<String> nomi = List.of("Marco", "Luca");

// Esempio di Consumer che stampa
Consumer<String> stampatore = s -> System.out.println("Ciao " + s);

nomi.forEach(stampatore); 
```

Un altra funzione è `Comparator`, che si occupa di ricevere in ingresso due input dello stesso tipo e restituire un `int` per indicare chi viene prima:
- intero positivo, il primo oggetto viene prima del secondo
- zero, il primo oggetto e il secondo sono uguali in ordinamento
- intero negativo, il primo oggetto viene dopo il secondo.

Possiamo definirlo in modo esplicito, indicando la regola di comparazione (in questo caso indichiamo che presi due oggetti persona, la comparazione dev'essere eseguita sulla differenza delle età):
```java
Comparator<Persona> orderByAge = (o1, o2) -> o2.eta - o1.eta;
```
oppure in modo implicito, sfruttando il metodo `comparing` 

```java
Comparator<Persona> orderByAge2 = Comparator.comparing(Persona::age);
//equivalente
Comparator<Persona> orderByAge2 = Comparator.comparingInt(o -> o.eta);
```
E possiamo usare il comparatore per ordinare un array di oggetti Persona:
```java
Persona[] personas = {persona1,persona2};
Arrays.sort(personas, orderByAge);
```

Importante indicare che su oggetti Stream possiamo invocare direttamente il metodo `sorted`, che però richiede che gli oggetti su cui si intende applicare l'ordinamento implementino l'interfaccia `Comparable` per cui bisogna necessariamente overridare il metodo `compareTo`.
```java
List<Persona> persone = List.of(persona1,persona2);
var orderPersone = persone.stream()
                .sorted()
                .toList();
System.out.println(orderPersone);
```

Oltre a `Comparator` e `Predicate` abbiamo anche alcuni metodi built-in degli stream, i metodi **XXXmatch**, i quali richiedono in input un predicato e restituisco un booleano:
- `allMatch`: restituisce true se tutti gli elementi dello stream rispettano il predicato
- `anyMatch`: restituisce true se almeno un elemento dello stream rispetta il predicato
- `noneMatch`: restituisce true se nessun elemento dello stream rispetta il predicato
Esempio:
```java
boolean almenoUnaPersonaHaIlCognomeTre = persone
                .stream()
                .noneMatch(x -> "tre".equalsIgnoreCase(x.cognome));
```

Nell'introduzione delle `Function`, abbiamo definito `fnGetNome`. Essa può essere utilizzata non solo tramite *apply* ma come funzione all'interno di alcuni metodi come il *groupingBy*:
```java
var byName = persone
                .stream()
                .collect(Collectors.groupingBy(
                        fnGetNome,
                        Collectors.toList()
                ));
```

### Eccezioni
In Java le eccezioni sono di due tipi:
- Checked Exception
- Unchecked Exception

Le eccezioni **Checked** sono eccezioni che vengono controllate in fase di compilazione, quindi se c'è qualcosa che potrebbe andare in errore il compilatore non fa eseguire nulla se prima lo sviluppatore non gestisce l'errore.  Le eccezioni checked sono quelle eccezioni che estendono `Exception` ma non `RunTimeException` (per esempio FileNotFoundException).
Per gestire l'errore possiamo:
1. Decidere di gestire noi l'eccezione con un blocco `try-catch-finally`
2. Demandare la gestione dell'eccezione al chiamante del metodo tramita le keyword `throws` nella firma del metodo.

Le eccezioni **Unchecked** sono eccezioni che non vengono controllate in fase di compilazione, quindi se c'è qualcosa che potrebbe andare in errore il compilatore fa eseguire ugualmente il codice. Se in fase di esecuzione si verifica qualche problema, viene lanciata l'eccezione e il programma termina. Le eccezioni unchecked sono quelle eccezioni che estendono `RunTimeException` (per esempio ArithmeticException).

Solitamente le eccezioni controlate vengono utilizzate per modellare anomalie legate a eventi esterni al programma mentre le non controllate per modellare anomalie legate a eventi interni. 

Un'eccezione inoltre può essere sollevata dallo sviluppatore tramite il costrutto `throw new <NomeException>`.

Esempio di checked exception:

```java
public class FileAssenteEccezione extends RuntimeException {
    private String nomeFile;

    public FileAssenteEccezione(String nomeFile, Throwable cause) {
        super(getMessage(nomeFile), cause);
        this.nomeFile = nomeFile;
    }

    private static String getMessage(String nomeFile) {
        return String.format("Oh no, non ho trovato il file: %s", nomeFile);
    }
}
String path = "src/main/java/jpc/h_eccezioni/file_da_trovar.txt";
try {
    String content = Files.readString(Paths.get(path));
    System.out.println(content);
} catch (IOException e) {
    //lancio manuale dell'eccezione
    throw new FileAssenteEccezione(path, e);
}
```

Esempio di unchecked exception:
```java
String a = "34d";
//il parsing può lanciare una NumberFormatException che però viene sollevata solo a run-time
int b = Integer.parseInt(a);
```

In Java é possibile inoltre utilizzare il costrutto `try-catch-finally` per modellare un comportamento comune nel ramo *finally*, sia che venga eseguito il *try* o il *catch*, per la gestione delle risorse. Per esempio in questo caso modelliamo nel *finally* la chiusura dello stream:
```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
try {
    String something = br.readLine();
} catch (IOException e) {
    throw new RuntimeException("oh no");
} finally {
    br.close();
}
```

Nelle versioni recenti di Java, la gestione di risorse può avvenire tramite il costrutto `try-catch with resources` che implicitamente implementa un `try-catch-finally`. In questo caso possiamo specificare la risorsa da chiudere prima del ramo *try* purhcè quest'ultima implementi l'interfaccia `Closable`.

```java
//br2 viene chiuso sia che si esegua il try o il catch
try (BufferedReader br2 = new BufferedReader(new InputStreamReader(System.in))) {
    String something = br2.readLine();
} 
catch (IOException e) {
    throw new RuntimeException("Something went wrong");
}
```

### Tipi generici
In Java è possibile scrivere metodi e classi usando tipi generici. Per esempio possiamo realizzare una classe che si occupa di fare dei conteggi, indipendentemente se l'oggetto di cui contare le occorrenze è un intero, una stringa o altro.
Per definire un metodo o classe con tipo generico possiamo utilizzare il *diamond-operator* dove specifichiamo un generico tipo `<T>`. Importante notare che se definiamo un metodo statico o un metodo in una classe non generica, dobbiamo obbligatoriamente specificare il tipo generico prima del tipo di valore restituito (questo serve a dire al compilatore che da li in poi, `T` non fa riferimento a una classe specifica ma ad un tipo generico). Se invece la classe è definita come `class MyClass<T>` allora non serve specificarlo.

Inoltre possiamo restringere il campo del tipo generico indicando `T extends`. Per esempio un tipo generico solo numerico viene espresso come `T extends Number`.

```java
//indichiamo un tipo generico T, accettiamo una lista di elementi di tipo T e restituiamo una Map<T,Long>
public static <T> Map<T, Long> counter(List<T> elements) {
        return elements.stream()
                .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
}

//oppure
public class ClassEsempio{
    public <T extends Number> Map<T, Long> counter(List<T> elements) {
        return elements.stream()
                .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
    }
}
```

Esempio di lista concatenata generica in Java:
```java
@Getter
@Setter
public class Node<T> {
    T val;
    Node<T> next;

    public Node(T val) {
        this.val = val;
    }
}

public class ConcList<T> {
    private Node<T> head;
    private int len;

    public T get(int i) {
        if (len <= i) throw new IndexOutOfBoundsException();
        Node<T> cur = head;
        int j = 0;
        while (i != j) {
            cur = cur.getNext();
            j++;
        }
        return cur.getVal();
    }

    public void add(T val) {
        final Node<T> newNode = new Node<>(val);
        len++;
        if (head == null) {
            head = newNode;
            return;
        }
        Node<T> cur = head;
        while (cur.getNext() != null) {
            cur = cur.getNext();
        }
        cur.setNext(newNode);
    }

    public int size() {
        return len;
    }
}
ConcList<Integer> cl = new ConcList<>();
cl.add(2);
cl.add(4);
cl.add(6);
cl.add(8);
cl.add(10);
cl.add(12);
cl.add(14);
cl.add(16);
cl.add(18);
cl.add(110);
IntStream.range(0, cl.size())
        .map(cl::get)
        .forEach(System.out::println);
```

### Enumerazioni
Una classe enumerativa è un tipo di classe utilizzata per definire un insieme fisso di costanti. Come le altre classi accetta attributi, costruttori sempre *private* e metodi.
Esempio di enumerazione semplice:
```java
public enum Gender {
    M,
    F,
    ND,
    NB
}
```
Esempio di enumerazione con costruttore, attributi e metodi:
```java
public enum SolarSystem {
    MERCURY(123),
    VENUS(345),
    EARTH(346),
    MARS(53453);

    private final long distance;


    SolarSystem(long distance){
        this.distance = distance;
    }

    public long getDistance(
        return this.distance;
    )

    public void greet() {
        System.out.printf("Hi from %s\n", this.name());
    }
}
```

### K record
In Java sono stati introdotti i tipi `record`, un tipo speciale di classe progettata per essere un semplice contenitore di dati immutabili. Il suo scopo principale è quello di eliminare il codice *boilerplate* scritto solitamente per le classi POJO ().
Di fatti quando si dichiara un record, il compilatore genera in automatico:
- campi privati e final per ogni componente
- costruttore che inizializza tutti i campi
- metodi accessor (es. `nome()`)
- metodi `equals` e `hashCode`
- metodo `toString`
Per esempio in questo blocco definiamo un record chiamato `Person` con due campi, `name` e `surname`, e un metodo custom:
```java
public record Person(String name, String surname) {
    public void greet() {
        System.out.printf("Hi i'm %s %s\n", this.name, this.surname);
    }
}
```

### Thread
In Java l'esecuzione parallela avviene tramite l'uso dei Thread. Possiamo definire una classe che rappresenta un oggetto da eseguire in un thread in due modi:
- classe che estende `Thread`, solitamente utilizato per definire un comportamento specifico e complesso del thread
- classe che implementa `Runnable`, solitamente utilizzato nel caso in cui non vogliamo definire in modo dettagliato il comportamento del thread ma vogliamo solo fornire qualcosa da "eseguire" al thread.
Entrambi devono fornire una propria implementazione del metodo `run`, che rappresenta il motore di esecuzione del thread.
Nel primo caso estendendo direttamente `Thread` per far partire l'esecuzione basta invocare il metodo `start` della classe Thread.

Nel secondo caso invece dobbiamo prima istanziare l'oggetto della classe che implementa `Runnable` e successivamente lo passiamo come parametro a un oggetto di classe `Thread` prima di far partire l'esecuzione.

Esempio con classe che estende `Thread`:
```java
public class CounterThread extends Thread {
    @Override
    public void run() {
        IntStream.range(0, 100).forEach(this::msg);
    }

    private void msg(int i) {
        System.out.printf("Thread %s dice %d\n", Thread.currentThread(), i);
    }
}

public static void main(String[] args){
    var listOfThread = List.of(new CounterThread(),new CounterThread());

    for(CounterThread obj : listOfThread){
        obj.start();
    }
}

Esempio con classe che implementa `Runnable`:
```java
public class Counter implements Runnable {
    @Override
    public void run() {
        IntStream.range(0, 100).forEach(this::msg);
    }

    private void msg(int i) {
        System.out.printf("Thread %s dice %d\n", Thread.currentThread(), i);
    }
}

for (int i = 0; i < 10; i++) {
            var ct = new Thread(new Counter());
            ct.start();
            }
```

Il risultato in entrambi i casi è caotico e imprevedibile, poichè nell'esecuzione concorrente non sappiamo quale thread parte prima e quale dopo e soprattutto come si alternano.

Un altro aspetto importante quando si lavora con i thread sono i metodi `synchronized`. Di fatti un problema comune quando si programmano thread è la *race-condition* su risorse comune. Se ipotizziamo che una serie di thread sono programmati per versare/prelevare contanti da un salvadanaio, quello che potrebbe avvenire è che mentre un thread preleva, uno versa del denaro, e siccome le operazioni di sottrazione e somma non sono implementate in modo atomico nel computer, potremmo prelevare del denaro ancor prima che il versamente sia stato effettivamente concluso. 

Tramite la keyword `synchronized` su un metodo, andiamo a comunicare che qualsiasi thread esegua quel metodo, egli acquisice un *lock* su di esso e fin tanto che tale metodo non termina, il lock non verrà rilasciato. In questo modo garantiamo mutua esclusione sull'accesso e utilizzo delle risorse condivise.

In questo blocco di codice utilizziamo la classe `ExecutorService` che permette di creare un pool fisso di n thread, e successivamente per un ciclo di 1000 iterazioni gli n thread si danno il cambio chiamando il metodo `inserireMonetina`:
```java
@Getter
public class Salvadanaio {
    private long soldini;

    public synchronized void inserireMonetina() {
        soldini++;
    }
}

var salvadanaio = new Salvadanaio();
        try (ExecutorService es = Executors.newFixedThreadPool(3)) {
            IntStream.range(0, 1000)
                    .forEach(_ -> es.submit(salvadanaio::inserireMonetina));
        }
```

Il codice appena visto crea i cosidetti **Platform Threads** (Thread del sistema operativo). Si tratta di thread pesanti utilizzati solitamente per task *CPU intensive*. A partire da Java 21 sono stati introdotti i **Virtual Threads** (Thread virtuali), solitamente utilizzati per operazioni *IO-intensive*.

Oltre all'interfaccia `Runnable`, una classe può anche implementare l'interfaccia `Callable`. Questa interfaccia essenzialmente svolge le stesse attività di Runnable ma con due comportamenti aggiuntivi:
1. può restituire un risultato 
2. può lanciare eccezioni.
Di fatti `Runnable` è come un operaio a cui dici "Vai e fai questo lavoro". Una volta finito, l'operaio torna da te ma non ti consegna nulla (`il metodo è public void run()`). `Callable`è come un consulente a cui dici "Fai questa ricerca e portami il rapporto". Quando ha finito, ti restituisce un valore (il metodo è `public V call()`, dove `V` è il tipo di dato che ti aspetti).

Per esempio in questo blocco di codice viene definito il record `Namer` che overrida il metodo `call` nel quale il thread viene addormentato per una certa quantità di tempo e successivamente restituisce il nome.
```java
private record Namer(String name, long millis) implements Callable<String> {
        @Override
        public String call() {
            try {
                Thread.sleep(millis);
                return name;
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
    }
```
Il record `Namer` può essere utilizzato tramite thread come nell'esempio precedente ma tramite il metodo `invokeAll`. Esso a differenza di `submit` che lancia e dimentica, `invokeAll` invia tutti i Namer al pool, ogni thread riceve un proprio namer (non si hanno thread che lavora contemporaneamente sullo stesso Namer) e l'esecuzione si blocca finchè l'ultimo dei tre thread non ha finito (`invokeAny` invece fa partire tutti e 3 ma l'esecuzione termina non appena il più veloce completa il task). Quando hanno tutti finito si ottiene una lista di oggetti `Future` che rappresentano dei "ticket" per ritirare il risultato finale. Nel nostro caso, prendiamo il terzo ticket e preleviamo il risultato prodotto dal Namer:
```java
try (ExecutorService es = Executors.newFixedThreadPool(3)) {
    var callables = List.of(
            new Namer("Paolo", 10000),
            new Namer("Patrizia", 10000),
            new Namer("Possagno", 5000)
    );
    //var res = es.invokeAny(callables);
    var res = es.invokeAll(callables);
    System.out.println(res.get(2).get());
} catch (InterruptedException | ExecutionException e) {
    throw new RuntimeException(e);
}
```

Di seguito possiamo vedere un esempio più moderno di esecuzione di una `Callable`. A differenza di `ExecutorService` che richiede una gestione manuale del pool di thread, il metodo `supllyAsync` prende il compito (la lambda) e lo lancia immediatamente in background usando di default il `ForkJoinPool.commonPool()`, un pool di thread condiviso gestito automaticamente da Java (senza che tu debba creare un `newFixedThreadPool`).

Invece di restituire un semplice `Future` (che è "passivo"), restituisce un `CompletableFuture` (che è "attivo").
Quindi si tratta di un metodo non bloccante, in quanto il tuo thread principale (main) continua a correre alla riga successiva senza aspettare i 2 secondi di `Thread.sleep` ma promettendo che dopo lo sleep, restituirà una stringa.

```java
CompletableFuture<String> strSupplier = CompletableFuture.supplyAsync(() -> {
            try {
                Thread.sleep(2000);
                return "Gabriele";
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        });
```

Infine possiamo comunicare che al termine dell'esecuzione dei thread precedentemente invocati vogliamo due risultati:
- `res` che rappresenta il risultato di tipo `String` in questo caso
- `ex` che rappresenta l'eventuale eccezione lanciata se qualcosa è andato storto (se tutto è ok `ex=null`)
```java
strSupplier.whenComplete((res, ex) -> {
            if (ex == null) {
                System.out.println(res);
            } else {
                System.out.println("Oh no, something went wrong!");
            }
        });
```

## Design pattern
I design pattern in informatica rappresentano una collezione di soluzioni e stili adottabili per risolvere problemi ricorrenti e fornire una versione "standardizzata" ed efficiente del design di un componente (indipendentemente che sia una classe, un'architettura di rete o altri elementi in ambito IT).

Prima di introdurre i vari *design pattern* più utilizzati, è importante sottolineare che indipendentemente dalla loro applicazione è consigliato adottare uno stile di programmazione che segua il principio **SOLID**:
- **Single Responsibility**. Una classe deve avere un solo compito. Se una classe gestisce sia la gestione dei pagamenti che la comunicazione col database non va bene, meglio dividerla in due.
- **Open-Closed**. Le classi devono essere aperte alle estensioni (nuove funzionalità) ma chiuse alle modifiche. Si ottiene usando interfacce e polimorfismo invece di modificare il codice esistente.
- **Liskov Substitution**. Una sottoclasse deve poter sostituire la sua classe base senza rompere il programma. Se `Quadrato` estende `Rettangolo` ma ne altera il comportamento logico, stai violando questo principio.
- **Interface Segregation**. Un'interfaccia non dovrebbe costringere una classe a implementare metodi che non le servono. Meglio avere tante interfacce piccole che una interfaccia grande che contiene tutti i metodi.

## Design pattern
I design pattern in informatica rappresentano una collezione di soluzioni e stili adottabili per risolvere problemi ricorrenti e fornire una versione "standardizzata" ed efficiente del design di un componente (indipendentemente che sia una classe, un'architettura di rete o altri elementi in ambito IT).

Prima di introdurre i vari *design pattern* più utilizzati, è importante sottolineare che indipendentemente dalla loro applicazione è consigliato adottare uno stile di programmazione che segua il principio **SOLID**:
- **Single Responsibility**. Una classe deve avere un solo compito. Se una classe gestisce sia la gestione dei pagamenti che la comunicazione col database non va bene, meglio dividerla in due.
- **Open-Closed**. Le classi devono essere aperte alle estensioni (nuove funzionalità) ma chiuse alle modifiche. Si ottiene usando interfacce e polimorfismo invece di modificare il codice esistente.
- **Liskov Substitution**. Una sottoclasse deve poter sostituire la sua classe base senza rompere il programma. Se `Quadrato` estende `Rettangolo` ma ne altera il comportamento logico, stai violando questo principio.
- **Interface Segregation**. Un'interfaccia non dovrebbe costringere una classe a implementare metodi che non le servono. Meglio avere tante interfacce piccole che una interfaccia grande che contiene tutti i metodi.
- **Dependency Inversion**. Bisogna dipendere dalle astrazioni (interfacce), non dalle implementazioni concrete. Questo rende il codice facilmente testabile e modulare.