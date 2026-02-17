# Java Propedeutico
All'interno di questo blocco di appunti sono presenti delle note prese dal corso di Java. 
## Piccolo disclaimer
Poichè conosco già Java, mi limiterò a inserire solo appunti di istruzioni che per me sono nuove o che ho usato poche volte e quindi per cui vale la pena tenere traccia. Eventuali parti mancanti o non trattate nelle note possono essere integrate da siti come W3school o documentazioni semi-ufficiali/ufficiali.

## Java Basics

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

Le Map in Java sono collezioni di tipo `<key,valore>`
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
```

```java
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