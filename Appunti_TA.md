# Appunti

## Diario delle lezioni
* [Lezione del 6/2/26 - 252](#lezione-6226)


## Lezione 6/2/26

Un web driver è un'applicazione che permette di rendere eseguite operazioni normalmente eseguite da uno user generico tramite righe di codice.

Questo metodo permette di impostare una variabile d'ambiente a livello di sistema in cui si esprime quale Web Driver utilizzare. 
```java
System.setProperty("webdriver.firefox.driver", ConstGECKODRIVER_PATH);
```

Ogni classe driver specifica per un browser (per esempio FirefoxDriver) sono classi figlie di RemoteWebDriver. A sua volta RemoteWebDriver implementa un'interfaccia, chiamata WebDriver. In questo modo garantiamo che ogni Driver fornisca un'implementazione specifica per il relativo browser.

Per tale motivo posso dichiarare un oggetto di tipo statico WebDriver e istanziarlo dinamicamente a seconda del driver necessario
```java
WebDriver driverForChrome = new ChromeDriver();

WebDriver driverForFirefox = new FirefoxDriver();
```

---
Il Web Driver ci serve quindi per automatizzare le operazioni che normalmente facciamo manualmente sul browser attraverso righe di codice. 

Come prima cosa dobbiamo specificare su quale pagina dobbiamo aprire sul browser. Questo viene eseguito attraverso il metodo ```driver.get``` :

```java
driver.get("https://www.google.com/");
```
il quale prende come parametro una stringa contenente l'URL del sito web.


Successivamente attraverso il metodo ```findElement```:

```java
WebElement genericElement = driver.findElement(By.id("idOfButton"));
```

è un metodo che permette di ottenere un elemento identificato dall'attributo **id='idOfButton'**.

---

Il metodo 

driver.manage().timeout().implicitlyWait(Duration.ofSeconds(4));

consente di effettuare, per tutte le volte che chiamaiamo findElement, di provare a cercare quell'elemento, e se non lo trova, continua a cercarlo fino alla durata specificata nel timer (in questo caso 4 secondi). Se lo trova, non aspetta quella quantità di tempo, bensì termina. Se non lo trova e trascorre quella quantià di tempo, lancia un'eccezione.


---

Il metodo invece

driver.quit();

consente di chiudere la finestra del browser. Ovviamente se prima si verifica una qualsiasi eccezione prima del quit, il browser non verrà mai chiuso. Una possibile soluzione è usando un try-catch sull'eccezione ```NoSuchElementException```.


--- 

Tutte queste operazioni non vengono mai inserite nel main (non è una best practice). Una cosa migliore da fare è usare TestNG per automatizzare i test. L'automazione avviene tramite le java notation.

La best-practice da usare è quella di definire:

- un metodo ```setup()``` dove setuppiamo il driver, con java notation ```@BeforeMethod``` che specifica che verrà eseguito questo metodo ogni volta prima dei metodi annotati come ```@Test```
- un metodo ```tearDown()``` dove inseriamo la chiusura del browser, annotandolo con ```@AfterMethod``` per far si che venga eseguito sempre alla fine di ogni metodo annotato come ```@Test```
- un metodo ```test()``` dove scriviamo il test vero e proprio annotato tramite ```@Test```.

Il vantaggio è avere una struttura pulita, automatizzata ed efficiente, perchè posso scrivere più test, separati. 

Un ulteriore vantaggio è che possiamo produrre dei report che specificano quali test sono passati e quali sono falliti.

---

Quando vogliamo individuare un elemento nella pagina web ma che magari ha un attributo diverso dal solito id/class, possiamo usare i CSS Selector. Essi rappresentano delle expression per indicare in modo più a grana fine ogni elemento nella pagina web.

Per esempio ```driver.findElement(By.cssSelector("[attributo='valore'"]))```

---

Un'altro esempio di test che possiamo fare per esempio è attraverso l'uso degli assert. Per esempio:


```Assert.assertTrue(driver.finElementById(...).isDisplayed())```

controlla se l'elemento specificato viene mostrato a video, oppure

```Assert.assertEquals(String, driver.findElement(..).getText());```

per controllare se l'elemento contiene il testo desiderato.

