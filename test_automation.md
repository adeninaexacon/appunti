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

### 2.1 ID
```java
driver.findElement(By.id("username"));
```

### 2.2 Name
```java
driver.findElement(By.name("email"));
```

### 2.3 Class Name
```java
driver.findElement(By.className("btn-primary"));
```

### 2.4 Tag Name
```java
driver.findElement(By.tagName("h1"));
```

### 2.5 Link Text / Partial Link Text
```java
driver.findElement(By.linkText("Accedi"));
driver.findElement(By.partialLinkText("Acc"));
```

### 2.6 CSS Selector (molto versatile)

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

### 2.7 XPath
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

## Gestione dei menu dropdown (<select>)

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
prima di sollevare un’eccezione se un elemento non è immediatamente disponibile.

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