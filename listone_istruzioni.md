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

---



# 🔍 Espressioni XPath

# Cheat Sheet Selettori XPath

Guida rapida alla sintassi XPath per l'automazione con Selenium.

| Sintassi / Esempio | Descrizione |
| :--- | :--- |
| `/` | **Root**: Parte dall'inizio assoluto del documento (nodo radice) oppure tra i figli del nodo. |
| `//` | **Ricerca Relativa**: Cerca l'elemento in tutta la pagina, a qualsiasi livello di profondità. Nel caso di un nodo interno, cerca non solo tra i figli ma a qualsiasi livello di profondità. |
| `//div` | Seleziona tutti gli elementi `div` presenti nel documento. |
| `//div[@id='tabs-1']` | Seleziona i `div` che hanno esattamente l'attributo ID uguale a "tabs-1". |
| `//*[@id='tabs-1']` | Seleziona **qualsiasi** tag (`*`) che abbia l'ID "tabs-1". |
| `//*[@class='btn' and @method='post']` | **AND**: L'elemento deve soddisfare entrambi i requisiti contemporaneamente. |
| `//*[@class='btn' or @method='post']` | **OR**: L'elemento viene selezionato se possiede almeno uno dei due attributi. |
| `//*[text()]='Settings'` | Seleziona elementi in cui il testo è `'Settings'`. |
| `//*[starts-with(@class, 'nav')]` | Seleziona elementi la cui classe **inizia** con "nav" (utile per ID/classi dinamiche). |
| `//*[starts-with(text(), 'UserID : ')]` | Seleziona qualsiasi elemento (`*`) in tutta la pagina (`//`) il cui testo visibile inizia esattamente con la stringa `'UserID : '`|
| `//*[contains(@class, 'ui-widget')]` | **Contiene**: Seleziona elementi la cui classe include la stringa specifica (ottimo per classi multiple). |
| `//div[@id='tabs']/*[3]` | Seleziona il **terzo figlio** (di qualsiasi tipo) del div con ID "tabs". |



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
Il metodo `.switchTo()` inoltre supporta alcuni metodi utili per *switchare* immediatamente ad alcuni componenti standard, come una *alert window* in JavaScript, tramite il comando:
```java
Alert logoutAlert = driver.switchTo().alert();

// conferma sull'alert
logoutAlert.accept();
```


In Selenium ogni oggetto restituo dal metodo ```.findElement()``` risulta di tipo WebElement. Nel caso ci sia un elemento HTML ```<select>```, possiamo ottenere l'oggetto associato, che viene identificato dalla classe ```Select```:
```java
Select titleSelect = new Select(driver.findElement(By.id("user_title")));
```
e possiamo selezionare la voce da scegliere specificando la posizione nella lista oppure tramite valore:
```java
titleSelect.selectByIndex(1);
titleSelect.selectByVisibleText("Doctor");
titleSelect.selectByContainsVisibleText("Docto");
```

Su un oggetto di classe ```WebElement``` oltre ai metodi ```.getText()```, ```.click()```, ```.sendKeys()``` possiamo anche invocare metodi che controllano lo stato dell'oggetto nel DOM, come il metodo ```.isDisplayed()``` che verifica se l'oggetto risulta visibile nel DOM:
```java
Assert.assertFalse(driver.findElement(By.id("calculatedpremium")).isDisplayed());
```
Oltre alle espressioni tramite CSS Selector, possiamo esprimere anche delle espressioni XPath per identificare il percorso dell'oggetto da cercare. Per esempio la seguente istruzione ```/html/body/div[3]/div/div[1]/h2```:
- cerca a partire dal nodo radice ```<html>```
- entra nel ```<body>```
- cerca il terzo ```<div>```
- entra nel ```<div>``` figlio di quest'ultimo
- entra nel primo ```<div>``` figlio
- individua l'elemento ```<h2>```

Di seguito possiamo vedere una sua applicazione:
```java
WebElement title = driver.findElement(By.xpath("/html/body/div[3]/div/div[1]/h2"));
```

Mettendo a confronto i due tipi di selettori, possiamo notare come il CSS Selector sia più intuitivo di XPath.
```java
//CSS Selector: [href="#menu"]
//XPath: //*[@href='#menu']

---

//CSS Selector: #header [href="#menu"]
//XPath: //*[@id='header']/*[@href='#menu']
```
Di seguito vengono riportati un esempio di selezioni tramite XPath con la relativa descrizione:

| Codice XPath | Descrizione |
| :--- | :--- |
| `//span[@id='header']/nav/*[@href='#menu']` | Cerca uno `span` con ID header, entra nel figlio diretto `nav` e seleziona **qualsiasi figlio diretto** che abbia l'attributo href='#menu'. |
| `//*[@id='header']/nav/*[@href='#menu']` | Più flessibile: cerca **qualsiasi tag** con ID header, entra nel figlio diretto `nav` e cerca il figlio con l'href specificato. |
| `//*[@id='header']/*/*[@href='#menu']` | Molto strutturale: partendo dall'ID header, scende esattamente di **due livelli di figli diretti** (qualsiasi essi siano) per trovare l'elemento target. |
| `//*[@id='header']//*[@href='#menu']` | **Il più robusto**: cerca l'elemento con l'href specificato **ovunque** (a qualsiasi profondità) all'interno dell'elemento con ID header. |


In alcune pagine web come Amazon.it o altri marketplace, si possono avere risultati che sono liste di elementi (i prodotti in vendita), quindi è necessario ottenere una lista di elementi, solitamente identificati tutti da un id comune o una classe comune:
```java
List<WebElement> lista_oggetti = driver.findElements(By.cssSelector("[data-component-type='s-search-result'] [data-component-type='s-product-image'] > a"));
```

Un esempio di applicazione avviene per il **web scraping multi-pagina**. In questo caso realizziamo:
- un metodo `getProductLinks()` che si occupa di collezionare tutti i link di ogni prodotto
- un metodo `goToNextPage()` che si occupa di passare alla pagina dei prodotti successiva
- un metodo `getProductLinksToPage(int finalPageNumber)` che si occupa dchiamare ciclicamente il metodo `getProductLinks` su ogni pagina (sfogliate tramite `goToNextPage`) fino a che non si raggiunge l'ultima pagina `finalPageNumber`.

```java
    public List<String> getProductLinks() {
        List<String> productLinks = new ArrayList<>();
        driver.findElements(By.cssSelector("[data-component-type='s-search-result'] [data-component-type='s-product-image'] > a")).forEach(
                anchor -> productLinks.add(anchor.getAttribute("href"))
        );
        return productLinks;
    }

    public List<String> getProductLinksToPage(int finalPageNumber) {
        List<String> productLinks = new ArrayList<>();
        for (int i = 1; i <= finalPageNumber; i++) {
            productLinks.addAll(getProductLinks());
            if (i != finalPageNumber)
                goToNextPage();
        }
        return productLinks;
    }

    public void goToNextPage() {
        driver.findElement(By.className("s-pagination-next")).click();
    }
```

In Selenium esiste il **Page Object Model (POM)** è un **Design Pattern** (un modello di progettazione) che serve a rendere il codice dei test più ordinato, leggibile e facile da mantenere.
Di fatti Selenium ci permette di specificare in una classe degli attributi di tipo `WebElement` su cui viene automaticamente iniettato l'oggetto specifico del DOM tramite annotazioni come `@FindBy(id = "esempio_id")`.
Successivamente queste annotazioni vengo iniettate automaticamente tramite la chiamata `PageFactory.initElements(WebDriver,Object)`:
```java
//Si comporta come una .findElement
@FindBy(id = "cookies_panel_id")
    private WebElement cookiesPanel;

//Si comporta come una .findElements dato che lo abbiamo dichiarato come una List<WebElement>
@FindBy(css = "[id^='item']")
    private List<WebElement> products;

public BasePage(WebDriver driver) {
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }
```
In questo modo possiamo agire direttamente a livello di istanza del componente, per esempio:
```java
public boolean isCookiesPanelVisible() {
        try {
            return cookiesPanel.isDisplayed();
        } catch (NoSuchElementException e) {
            return false;
        }
    }
```

Dopo aver parlato delle I*mplicit Wait* introduciamo le **Explicit Wait**. 

L'Explicit Wait è un'attesa "intelligente" che mette in pausa l'esecuzione del codice finché non si verifica una **condizione specifica** su un determinato elemento.
Viene dichiarata attraverso un costruttore della classe `WebDriverWait` che richiede in input il WebDriver e la durata della wait:
```java
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(4));
```
Succesivamente possiamo indicare, per ogni utilizzo specifico, il tipo di condizione e il tipo di oggetto su cui applicare la wait:

```java
@FindBy(css = "[data-testid='modal-accept-button']")
    private WebElement acceptCookiesButton;

@FindBy(css = "[data-testid='cookie-modal-content']")
    private WebElement cookiesModal;
public void acceptCookies() {
        wait.until(ExpectedConditions.elementToBeClickable(acceptCookiesButton));
        acceptCookiesButton.click();
    }

    public boolean isAlertModalInvisible() {
        try {
            wait.until((ExpectedConditions.invisibilityOf(cookiesModal)));
            return true;
        } catch (TimeoutException e) {
            return false;
        }
    }
````

Se vogliamo testare condizioni di visibilità, non visibilità o cambio di stato possiamo usare:
```java
        // Invisibility
        wait.until((ExpectedConditions.invisibilityOf(cookiesModal)));

        // Visibility
        wait.until((ExpectedConditions.visibilityOf(cookiesModal)));

        // Appears (Invisible -> Visible)
        wait.until((ExpectedConditions.invisibilityOf(cookiesModal)));
        wait.until((ExpectedConditions.visibilityOf(cookiesModal)));

        // Disappears (Visible -> Invisible)
        wait.until((ExpectedConditions.visibilityOf(cookiesModal)));
        wait.until((ExpectedConditions.invisibilityOf(cookiesModal)));
```
**Importante notare** che se creiamo una classe con annotazioni `@FindBy` possiamo usare metodi come `visibilityOf` ma se la classe presenta attributi identificati tramite
```java
private final By cookiesModal = By.cssSelector("[data-testid='cookie-modal-content']");
```
dobbiamo usare metodi come `visibilityOfElementLocated`. Questo avviene principalmente perchè nel primo caso PageFactory ha già creato un "contenitore" per l'elemento. Le ExpectedConditions che lavorano su un oggetto già esistente si aspettano che tu glielo passi direttamente, mentre nel secondo caso Selenium deve prima andare nel DOM, cercarlo e poi verificare la condizione.

Vediamo ora come trattare nel dettaglio i componenti chiamati *iFrame*. Un iFrame incorpora un documento HTML separato nella pagina corrente. Poiché ha un proprio DOM, Selenium non può interagire direttamente con i suoi elementi. È necessario spostare esplicitamente il focus di WebDriver sull'iframe prima di interagire, e tornare al documento principale al termine dell'operazione. In HTML gli iframe sono identificati dal tag specifico `<iframe>`.

Lato istruzioni in Selenium utilizziamo il comando `driver.switchTo().frame()` dove andiamo a specificare di cambiare contesto e passare dal contesto di default (attuale) all'interno di uno specifico iframe. Il metodo `frame` richiede un parametro che permetta di identificare l'iframe in cui posizionari. Tale parametro può essere:
- un'intero che rappresenta l'indice (posizione) dell'iframe nella pagina (il primo coincide con indice 0)
- una stringa che rappresenta il valore assunto dal tag `<id>` o `<name> ` dell'iframe. In caso di molteplici iframe restituiti viene selezionato il primo
- un WebElement che rappresenta l'oggetto iframe recuperato tramite `driver.findElement()`

Una volta selezionato un iframe possiamo navigare al suo interno e procedere a cercare gli elementi contenuti (elementi figlio) dell'iframe. Per esempio
```java
@Test
public void test_iframe() {
    driver.switchTo().frame(driver.findElement(By.id("iframe1")));
    String paragraph = driver.findElement(By.id("iframe-paragraph")).getText();
    assertEquals(paragraph, "This is the page number 1.");
}
```

Dopo esserci posizionati all'interno di un iframe non possiamo cercare elementi presenti in un altro DOM, per esempio nella pagina principale. Per ritornare al contesto precedente (di default) utilizziamo l'istruzione `driver.switchTo().defaultContent()` che consente di ripristinare la nostra posizione nel DOM princiapele.
```java
@Test
public void test_default_content() {
    driver.switchTo().frame("iframe1");
    String iFrameTitle = driver.findElement(By.id("title")).getText();
    assertEquals(iFrameTitle, "iFrame Page 1");
    driver.switchTo().defaultContent();
    String mainPageTitle = driver.findElement(By.id("title")).getText();
    assertEquals(mainPageTitle, "Main Page");
}
```

Inoltre un iframe può essere presente all'interno di un altro iframe. In questo caso utiliziamo il comando `switchTo().frame()` in cascata per raggiungere l'iframe desiderato.
```java
@Test
public void test13() {
    driver.switchTo().frame("iframe1");
    String iFrame1Title = driver.findElement(By.id("title")).getText();
    assertEquals(iFrame1Title, "iFrame Page 1");
    driver.switchTo().frame("iframe2");
    String iFrame2Title = driver.findElement(By.id("title")).getText();
    assertEquals(iFrame2Title, "iFrame Page 2");
    driver.switchTo().frame("iframe3");
    String iFrame3Title = driver.findElement(By.id("title")).getText();
    assertEquals(iFrame3Title, "iFrame Page 3");

}
```
