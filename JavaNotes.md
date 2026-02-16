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