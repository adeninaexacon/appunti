# Test Automation

## Introduzione a Selenium WebDriver
Selenium WebDriver permette di automatizzare browser reali (Chrome, Firefox, Edge).  
Consente di:
- aprire pagine web
- individuare elementi HTML
- interagire con essi (click, testo, selezioni)
- automatizzare test end-to-end

Metodo principale per individuare un elemento:

```java
WebElement element = driver.findElement(By.<strategia>());
```

Per ottenere più elementi:

```java
List<WebElement> elements = driver.findElements(By.<strategia>());
```

---

## Strategie di selezione degli elementi (By)

### ID
```java
driver.findElement(By.id("username"));
```

### Name
```java
driver.findElement(By.name("email"));
```

### Class Name
```java
driver.findElement(By.className("btn-primary"));
```

### Tag Name
```java
driver.findElement(By.tagName("h1"));
```

### Link Text / Partial Link Text
```java
driver.findElement(By.linkText("Accedi"));
driver.findElement(By.partialLinkText("Acc"));
```

### CSS Selector (molto versatile)

#### Selezione per attributo
```css
[attributo="valore"]
```

Esempi:
```java
driver.findElement(By.cssSelector("[name='prova']"));
driver.findElement(By.cssSelector("input[type='password']"));
driver.findElement(By.cssSelector("button[data-test='submit']"));
```

#### Gerarchia padre > figlio
```css
div.card > h2.title
```

### XPath
```java
driver.findElement(By.xpath("//*[@name='prova']"));
driver.findElement(By.xpath("//button[text()='Invia']"));
driver.findElement(By.xpath("//div[@class='card']//h2"));
```
L'Xpath si può copiare direttamente dalla console del browser
questo è il modo meno efficente per cercare un elemento
by id > css select > .. > by xpath

csselcet
start-with(@class, "navigation") è un modo per cercare utilizzando gli xpath

---

## Interazioni con gli elementi

### Inserimento testo e comandi da tastiera
```java
element.sendKeys("test");
element.sendKeys(Keys.ENTER);
```

### Click
```java
element.click();
```

### Lettura testo
```java
String txt = element.getText();
```

---

## Introduzione a TestNG

TestNG gestisce:
- ciclo di vita dei test
- setup e teardown
- asserzioni
- suite e classi di test

### Annotazioni principali

| Annotazione | Significato |
|------------|-------------|
| `@BeforeMethod` | Eseguito prima di ogni test |
| `@AfterMethod` | Eseguito dopo ogni test |
| `@Test` | Identifica un metodo di test |
| `@BeforeClass` | Eseguito una volta prima dei test della classe |
| `@AfterClass` | Eseguito una volta dopo i test della classe |

---

## Asserzioni con TestNG

### assertTrue
```java
Assert.assertTrue(condizione);
```

### assertEquals
```java
Assert.assertEquals(valoreAtteso, valoreReale);
```

---

## Flusso tipico di un test automatizzato

1. Setup del browser – `@BeforeMethod`  
2. Esecuzione del test – `@Test`  
   - individuazione elementi (`findElement`)  
   - interazioni (`click`, `sendKeys`, ecc.)  
   - verifiche (`assert`)  
3. Teardown – `@AfterMethod`

---

## Gestione dei menu dropdown (`<select>`)

Gli elementi `<select>` richiedono una classe dedicata:

```java
Select titleSelect = new Select(driver.findElement(By.id("user_title")));
```

Metodi disponibili:
- `selectByIndex(int index)`
- `selectByValue(String value)` (preferibile)
- `selectByVisibleText(String text)`
- `selectByContainsVisibleText(...)`

### Perché preferire selectByValue?
Il testo visibile può cambiare in siti multilingua.  
Il valore dell’attributo `value` è solitamente stabile e indipendente dalla lingua.

---

## Implicit Wait

L’implicit wait definisce quanto tempo il driver attende
prima di sollevare un’eccezione se un elemento non è immediatamente disponibile. Valore consigliato 4/5 secondi.

Serve per attendere il corretto caricamento della pagina.
```java
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
```

Caratteristiche:

- vale per tutte le findElement

- riduce errori dovuti a caricamenti lenti

- non è selettivo

## Gestione degli iframe

Un iframe è una pagina web annidata all’interno di un’altra pagina.

Per interagire con elementi all’interno di un iframe è necessario cambiare contesto.
```java
driver.switchTo().frame("frameId");
```

Per tornare alla pagina principale:
```java
driver.switchTo().defaultContent();
```

## Nota su .isDisplayed()

Si tratta di un metodo che restituisce un booleano:
- `true` se l'elemento è presente e visibile nella pagina in cui si trova il driver.
- `false` se l'elemento è **presente** ma non è visibile.

Il problema è che questo metodo ci dice solo che un elemento è visibile non se è presente.

Noi usiamo spesso questo metodo se vogliamo controllare (con un assert) che un'azione del nostro test sia stata effettivamente compiuta. 

Quindi cerchiamo un elemento tramite la `.findElement(...)` e poi guardiamo se è visibile nella pagina.

Il problema è che se l'elemento non è presente il metodo non ritorna `false` ma fallisce lanciando un Exception (`NoSuchElementException`). 

Come fare?

Si utilizza un try-catch statement:

```java
try {
   return driver.findElement(...).isDisplayed();
} catch (NoSuchElementException e) {
   return false;
}
```
Questo check ritorna esattamente quello che volevamo controllare.

---

## Page Object Model

Cos'è il POM?

Si tratta di un pattern per aumentare la leggibilità, la riproducibilità e la pulizia del codice utilizzato per fare test su siti web.

Bisogna creare una classe per ogni pagina che visiterò durante il test.

Queste classi vanno inserite in un package generalmente chiamato `pages`.

Spesso è utile creare una classe `BasePage.java`: questo oggetto rappresenta una pagina astratta (not in a java way) da cui le altre pagine ereditano metodi comuni (ad esempio un metodo per effettuare una ricerca con una barra di ricerca presente in tutte le pagine del sito). 

creo un pacchetto chiamato pages 

creo un oggetto per ogni pagina e all'interno definisco
un metodo per ogni azione che devo svolgere su quella pagina

è importante passare il driver all'oggetto pagina

nella classe in cui abbiamo facciamo i test e prepariamo l'ambiente
dobbiamo instanziare le pagine che utilizzeremo

questo pattern permette di avere dei test più leggibili e permette anche una migliore flessibilità nelle operzione che vogliamo fare in seguito 

importante quando voglio vedere se un elemento è ancora presente nella pagine se utilizzo findElement e  l'elemento non c'è più (ad esempio il box dei coockies) fallisce lanciando un eccezione. 

posso racchiuderlo all'interno di un try catch statement

try {
   driver.findElement(...)
   return true;
} catch (NoSuchElementExeption e) {
   return false;
}

questo per verificare se l'elemento è presente 

se voglio verificare la visibilità allora faccio questo 

try {
   return driver.findElement(...).isDisplayed();
} catch (NoSuchElementExeption e) {
   return false;
}

esiste anche un secondo modo

sfrutto una feature del findElements
cerca tutti gli elementi con un certo id (id o name o blblbl)
in questo caso il metodo ritorna una lista vuota

quindi se controllo con il metodo isEmpty ottengo true se non è presente in pagina e false se c'è a quel punto posso controllare se è displyato 

i metodi si equivalgono a livello di tempo di esecuzione e a livello
di complessità



BasePage o TemplatePage 

pagina senza corrispettivi nel sito da cui le altre pagine ereditno operzioni comuni 

creo il driver come un attributo protetto (solo le classi che lo ereditano possono utilizzarlo)

---

page factory 

utilizza delle annotazioni di selenium 

bisogna dichiarare l'elemento con il nome che si vuole
```java
@FindBy(id = "iddell'elemento")
private WebElement elementodelweb;

public BasePage(WebDriver driver) {
   this.driver = driver;
   PageFactory.initElements(driver, this);
}
```