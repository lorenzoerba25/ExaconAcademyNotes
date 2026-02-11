# Il Listone
In questo file sono elencati tutte le istruzioni e comandi principali per il corso di Test Automation.

# 🔍 Selettori CSS (CSS Selectors)

I selettori CSS sono utilizzati per identificare e selezionare elementi specifici all'interno di un documento HTML.

---

## Selettori Base

| Sintassi | Nome del Selettore | Descrizione | Esempio |
| :--- | :--- | :--- | :--- |
| **`tagName`** | **Tipo (Tag)** | Seleziona tutti gli elementi con quel nome di tag. | `div` $\rightarrow$ tutti gli elementi `<div>`. |
| **`.className`** | **Classe** | Seleziona tutti gli elementi che hanno la classe specificata. | `.test` $\rightarrow$ elementi con `class="test"`. |
| **`#id`** | **ID** | Seleziona l'unico elemento con l'ID specificato (gli ID dovrebbero essere unici). | `#main` $\rightarrow$ elemento con `id="main"`. |

---

## Selettori di Attributo

| Sintassi | Descrizione | Esempio |
| :--- | :--- | :--- |
| **`[attributo="valore"]`** | Seleziona elementi con l'attributo che ha un valore **esatto**. | `[aria-label="Search"]` |
| **`[attributo]`** | Seleziona elementi che **possiedono** l'attributo, indipendentemente dal valore. | `[aria-label]` |

***Nota:*** `[id="gb"]` è equivalente a `#gb`, e `[class="test"]` è equivalente a `.test`.

---

## Combinazione e Relazioni

### 1. Concatenazione (Stesso Elemento)

Si possono combinare selettori senza spazi per selezionare elementi che soddisfano **tutte** le condizioni.

* **Esempio:** `.button#cta[data-type="primary"]`
    * Seleziona l'elemento con classe `button`, ID `cta`, e attributo `data-type="primary"`.

### 2. Selettori di Discendenza (Relazioni tra Elementi)

| Sintassi | Tipo di Relazione | Descrizione | Esempio |
| :--- | :--- | :--- | :--- |
| **`selettore1 selettore2`** | **Discendente** | Cerca `selettore2` **all'interno** di `selettore1`, a qualsiasi livello (figli, nipoti, etc.). | `div a` |
| **`selettore1 > selettore2`** | **Figlio Diretto** | Cerca `selettore2` solo come **figlio immediato** di `selettore1`. | `ul > li` |

### 3. Negazione

* **Sintassi:** `selettore1:not(selettore2)`
* **Descrizione:** Seleziona tutti gli elementi di `selettore1` ed **esclude** quelli che corrispondono anche a `selettore2`.
* **Esempio:** `div:not(.header)`


### 🎯 CSS Selector: Filtri sugli Attributi
Quando devi gestire classi dinamiche o parziali (come `searchbox_searchButton__rbrIQ`), usa questi operatori tra parentesi quadre:

| Sintassi | Operatore | Descrizione | Esempio Pratico |
| :--- | :--- | :--- | :--- |
| `[attr*="valore"]` | **Contiene** | Trova l'elemento se l'attributo contiene la stringa in **qualsiasi posizione**. | `[class*="searchButton"]` |
| `[attr^="valore"]` | **Inizia con** | Trova l'elemento se l'attributo **inizia** con quella stringa. | `[class^="search-input"]` |
| `[attr$="valore"]` | **Finisce con** | Trova l'elemento se l'attributo **termina** con quella stringa. | `[class$="GGtY1"]` |
| `[attr="valore"]` | **Esatto** | L'attributo deve essere **identico** al 100% (non ammette altre classi). | `[type="submit"]` |

**Esempio per il tuo errore:**
Per il pulsante con classe complessa, il selettore più robusto è:
`driver.findElement(By.cssSelector("button[class*='searchbox_searchButton']"));`

---

## Selenium

```java
System.setProperty("webdriver.firefox.driver", Const.GECKODRIVER_PATH);
```

```java
FirefoxDriver driver = new FirefoxDriver();
driver.get("https://www.google.com/");
```

```java
WebElement button = driver.findElement(By.id("W0wltc"));
button.click();
```

```java
driver.findElement(By.tagName("textarea")).sendKeys("test");
driver.findElement(By.name("btnK")).click();
```

Questa istruzione serve a impostare alcune opzioni del browser come disabilitare l'avvio sandbox di Chrome
```java
ChromeOptions chromeOptions = new ChromeOptions();
chromeOptions.addArguments("--no-sandbox");
ChromeDriver chromeDriver = new ChromeDriver(chromeOptions);
```

### Selenium with TestNG

Una best-practice nell'utilizzo di TestNG consiste nel realizzare una suite di testing attraverso l'uso delle Java notations, organizzate in questo modo:

- un metodo ```setup()``` dove setuppiamo il driver, con java notation ```@BeforeMethod``` che specifica che verrà eseguito questo metodo ogni volta prima dei metodi annotati come ```@Test```
- un metodo ```tearDown()``` dove inseriamo la chiusura del browser, annotandolo con ```@AfterMethod``` per far si che venga eseguito sempre alla fine di ogni metodo annotato come ```@Test```
- un metodo ```test()``` dove scriviamo il test vero e proprio annotato tramite ```@Test```.

Inoltre possiamo utilizzare altri due tipi di annotazioni:
- ```@BeforeSuite``` che sostanzialmente annota un metodo che viene eseguito una sola volta prima di tutti i test
- ```@AfterSuite``` che sostanzialmente annota un metodo che viene eseguito una sola volta dopo l-esecuzione di tutti i test (indipendentemente dall'esito).

Esempio:
```java
private WebDriver driver;

    @BeforeSuite
    public void generalSetUp() {
        System.out.println("1");
    }

    @AfterSuite
    public void generalTearDown() {
        System.out.println("2");
    }

    @BeforeMethod
    public void setUp() {
        System.out.println("3");
        System.setProperty("webdriver.firefox.driver", Const.GECKODRIVER_PATH);
        driver = new FirefoxDriver();
        driver.get("https://duckduckgo.com/");
    }

    @AfterMethod
    public void tearDown() {
        System.out.println("4");
        driver.quit();
    }

    @Test
    public void test1() {
        System.out.println("5");
        driver.findElement(By.id("searchbox_input_FAKE")).sendKeys("Test");
    }

    @Test
    public void test2() {
        System.out.println("6");

    }

    @Test
    public void test3() {
        System.out.println("7");

    }
```

Per verificare che una determinata condizione si verifica oppure no, all'interno di un test possiamo indicare la clausola:

```java
Assert.assertTrue(firstResultTitle.toLowerCase().contains("test"));

//oppure 

Assert.assertFalse(firstResultTitle.toLowerCase().contains("test"));
```
Se vogliamo invece indurre un fallimento, possiamo usare l'istruzione:
```java
Assert.fail();
```

---

Questa istruzione prende un elemento tramite CSS selector esplicitando che vogliamo un attributo di classe "data-testid" con valore "result-title-a" di cui poi preleviamo il primo figlio (operatore >), da cui a sua volta estraiamo il testo.

```java
String firstResultTitle = driver.findElement(By.cssSelector("[data-testid='result-title-a'] > span")).getText();
```

In Selenium possiamo utilizzare le *implicit-wait*, un'impostazione globale che dice a Selenium di aspettare un certo lasso di tempo prima di lanciare un'eccezione se un elemento non è immediatamente disponibile nel DOM. Possiamo impostare una implicit wait con il comando

```java
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
```
In questo modo diciamo a Selenium di aspettare massimo 10 secondi prima di lanciare una ```NoSuchElementException```. Importante sottolineare che le implicit wait garantiscono il loro funzionamento solo per metodi come ```.findElement()``` e ```.findElements()```. 


Solitamente quando si vuole inserire del testo in una barra di ricerca e poi premere invio si eseguono questi due comandi in simbiosi:
```java
driver.findElement(By.id("searchbox")).sendKeys("Test");
driver.findElement(By.cssSelector("[class='buttonEnter']")).click();
```
Queste due istruzioni sono del tutto legittime e funzionanti ma possiamo evitare di cercare il bottone da cliccare per avviare la ricerca, simulando la digitazione del tasto *Enter* della tastiera dopo aver digitato il testo da cercare:
```java
driver.findElement(By.id("searchbox")).sendKeys("Test" + Keys.ENTER);
```

Selenium può vedere solo un documento alla volta. Se un pulsante (come "Accetta tutto") si trova dentro un *iframe*, Selenium non riuscirà mai a trovarlo o cliccarlo finché non "entri" esplicitamente in quel frame. Per questo motivo possiamo cercare l'iframe e successivamente spostare il *focus* di Selenium su quel frame, tramite il comando ```.switchTo().frame()```, accettare i cookies e tornare alla pagina iniziale tramite il comando ```switchTo().defaultContent()```:
```java
WebElement cookiesAlert = driver.findElement(By.cssSelector("[id^='sp_message_iframe_']"));
driver.switchTo().frame(cookiesAlert);
driver.findElement(By.cssSelector("button.accept-all")).click();
driver.switchTo().defaultContent();
```