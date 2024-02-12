# clean-code-javascript

## Inhoudsopgave

1. [Inleiding](#inleiding)
2. [Variabelen](#variabelen)
3. [Functies](#functies)
4. [Objecten en Datastructuren](#objecten-en-datastructuren)
5. [Klassen](#klassen)
6. [SOLID](#solid)
7. [Testen](#testen)
8. [Gelijktijdigheid](#gelijktijdigheid)
9. [Foutafhandeling](#foutafhandeling)
10. [Opmaak](#opmaak)
11. [Commentaar](#commentaar)

## Inleiding

![[Humoristische afbeelding vergelijkt 'goede code' en 'slechte code', gemeten door het aantal keer dat 'WTF' wordt gezegd tijdens een code review](#)
](https://www.osnews.com/images/comics/wtfm.jpg)

Softwareontwikkelingsprincipes geïnspireerd door Robert C. Martin's boek [_Clean Code_](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882), echter toegespitst op JavaScript. Dit document dient niet zozeer als een stijlgids, maar meer als een handleiding voor het schrijven van heldere, herbruikbare en eenvoudig aan te passen JavaScript-code.

Het is niet nodig om elk principe rigide te volgen; niet iedereen zal het overal mee eens zijn. Beschouw deze als handvatten, gebaseerd op jarenlange ervaring van de _Clean Code_-auteurs.

Onze expertise in softwareontwikkeling is pas een halve eeuw oud en we blijven constant leren. Misschien dat we, als onze discipline even oud is als de bouwkunst, striktere regels hebben. Tot die tijd kunnen deze richtlijnen je helpen de kwaliteit van je JavaScript-code te peilen.

Onthoud echter dat je niet direct een betere ontwikkelaar wordt, door enkel deze principes te kennen. En zelfs na jaren ervaring kun je nog steeds fouten maken. Elk codefragment begint als een ruwe schets, die vorm krijgt en verfijnd wordt, vooral na feedback van collega's. Wees dus niet te hard voor jezelf als je code nog niet perfect is; het is de code die verbeterd kan worden, niet jij.

## **Variabelen**

### Gebruik betekenisvolle en uitspreekbare variabelennamen

**Onjuist:**

```javascript
const yyyymmdstr = moment().format("YYYY/MM/DD");
```

**Juist:**

```javascript
const currentDate = moment().format("YYYY/MM/DD");
```

**[⬆ naar boven](#inhoudsopgave)**

### Gebruik dezelfde woordenschat voor hetzelfde type variabele

**Onjuist:**

```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Juist:**

```javascript
getUserDetails();
getUserData();
getUserRecord();
```

**[⬆ naar boven](#inhoudsopgave)**

### Gebruik namen die makkelijk te vinden zijn

We zullen meer code lezen dan we ooit zullen schrijven. Het is belangrijk dat de code die we schrijven zowel leesbaar als makkelijk te vinden is. Door het _niet_ geven van name die betekenisvol zijn voor het begrijpen van ons programma, benadelen we onze lezers. Zorg ervoor dat je namen gebruikt die makkelijk te vinden zijn. Hulpmiddelen 
zoals [buddy.js](https://github.com/danielstjules/buddy.js) en
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md) kunnen helpen bij het identificeren van niet-benoemde constanten.

**Onjuist:**

```javascript
// Wat betekent 86400000 eigenlijk?
setTimeout(blastOff, 86400000);
```

**Juist:**

```javascript
// Declareer ze als benoemde constanten, in hoofdletters, om duidelijk te maken dat dit onveranderlijke waarden zijn
const MILLISECONDS_PER_DAY = 60 * 60 * 24 * 1000; //86400000;

setTimeout(blastOff, MILLISECONDS_PER_DAY);
```

**[⬆ naar boven](#inhoudsopgave)**

### Gebruik beschrijvende variabelen

**Onjuist:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(
  address.match(cityZipCodeRegex)[1],
  address.match(cityZipCodeRegex)[2]
);
```

**Juist:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
// Gebruik destructuring met een placeholder (_) voor ongebruikte waarden, om duidelijk de stad en postcode uit het adres te halen
const [_, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```

**[⬆ naar boven](#inhoudsopgave)**

### Vermijd mentale sprongen

Expliciet is beter dan impliciet.

**Onjuist:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(l => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Wacht, waar stond `l` ook alweer voor?
  dispatch(l);
});
```

**Juist:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(location => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Nu is het duidelijk dat `location` de huidige locatie in de iteratie vertegenwoordigt.
  dispatch(location);
});
```

**[⬆ naar boven](#inhoudsopgave)**

### Voeg geen onnodige context toe

Als de naam van je klasse/object je al iets vertelt, herhaal dat dan niet in je variabelenaam.

**Onjuist:**

```javascript
const Car = {
  carMake: "Honda",
  carModel: "Accord",
  carColor: "Blue"
};

function paintCar(car, color) {
  car.carColor = color;
}
```

**Juist:**

```javascript
const Car = {
  make: "Honda",
  model: "Accord",
  color: "Blue"
};

function paintCar(car, color) {
  car.color = color;
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Gebruik standaardparameters in plaats van fallbacks

Standaardparameters zorgen voor schonere code dan fallback-waarden. Let erop dat je bij gebruik hiervan alleen `undefined` argumenten vervangt door standaardwaarden. Andere "falsy" waarden zoals `''`, `""`, `false`, `null`, `0`, en
`NaN`, krijgen geen standaardwaarde.

**Onjuist:**

```javascript
function createMicrobrewery(name) {
  const breweryName = name || "Hipster Brew Co.";
  // ...
}
```

**Juist:**

```javascript
function createMicrobrewery(name = "Hipster Brew Co.") {
  // ...
}
```

**[⬆ naar boven](#inhoudsopgave)**

## **Functies**

### Functieparameters (idealiter 2 of minder)

Het beperken van het aantal parameters in een functie is cruciaal omdat het de functie makkelijker testbaar maakt. Meer dan drie parameters zorgen ervoor dat het aantal combinaties van invoerwaarden snel toeneemt, wat het testen, vanwege de vele combinaties, complexer maakt.

Het ideaal is om één of twee parameters te hebben, en het gebruik van drie parameters moet worden vermeden indien mogelijk. Meer dan dat zou samengevoegd moeten worden. Vaak betekent het hebben van meer dan twee parameters dat je functie te veel probeert te doen. In gevallen waar dit niet zo is, kan meestal een hoger niveau object als parameter dienen.

Aangezien JavaScript je toelaat om op de vlucht objecten aan te maken, zonder veel class boilerplate, kun je een object gebruiken als je merkt dat je veel parameters nodig hebt.

Om het duidelijk te maken welke eigenschappen de functie verwacht, kun je de ES2015/ES6 destructureringsyntax gebruiken. Dit heeft een aantal voordelen:

1. Wanneer iemand naar de functiedefinitie kijkt, is direct duidelijk welke
   eigenschappen worden gebruikt.
2. Het kan gebruikt worden om genoemde parameters te simuleren.
3. Destructurering kloont ook de gespecificeerde primitieve waarden van het object dat
   als argument wordt doorgegeven. Dit kan helpen bijwerkingen te voorkomen. Let op: objecten en arrays die uit het argumentobject worden gedestructureerd, worden NIET gekloond.
4. Linters kunnen je waarschuwen voor ongebruikte eigenschappen, wat onmogelijk zou zijn
   zonder destructurering.

**Onjuist:**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

createMenu("Foo", "Bar", "Baz", true);

```

**Juist:**

```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true
});
```

**[⬆ naar boven](#inhoudsopgave)**

### Functies moeten één ding doen

Dit is verreweg de belangrijkste regel in software-engineering. Wanneer functies meer dan één ding doen, zijn ze lastiger te combineren, te testen en te doorgronden. Als je een functie kunt isoleren tot slechts één actie, kan deze gemakkelijk worden gerefactord en zal je code veel leesbaarder zijn. Als er ook maar 1 ding is dat je wilt leren uit dit document, kies dit punt, dan loop je al voor op veel ontwikkelaars.

**Onjuist:**

```javascript
function emailClients(clients) {
  clients.forEach(client => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Juist:**

```javascript
function emailActiveClients(clients) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Functienamen moeten zeggen wat ze doen

**Onjuist:**

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// Het is lastig om uit de functienaam af te leiden wat er wordt toegevoegd
addToDate(date, 1);
```

**Juist:**

```javascript
function addMonthToDate(date, month) {
  // ...
}

const date = new Date();
addMonthToDate(date, 1);
```

**[⬆ naar boven](#inhoudsopgave)**

### Functies moeten slechts één abstractieniveau hebben

Wanneer je meer dan één abstractieniveau in je functie hebt, doet de functie vaak te veel. Functies opsplitsen leidt tot herbruikbaarheid en eenvoudiger testen.

**Onjuist:**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach(token => {
    // lex...
  });

  ast.forEach(node => {
    // parse...
  });
}
```

**Juist:**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);
  syntaxTree.forEach(node => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens) {
  const syntaxTree = [];
  tokens.forEach(token => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Verwijder dubbele code

Doe je uiterste best om dubbele code te vermijden. Dubbele code is slecht omdat het
betekent dat er meer dan één plek is om iets te wijzigen als je de logica moet veranderen.

Stel je voor dat je een restaurant runt en je houdt je voorraad bij: al je
tomaten, uien, knoflook, kruiden, enz. Als je meerdere lijsten hebt waarop
je dit bijhoudt, dan moeten al die lijsten bijgewerkt worden wanneer je een gerecht serveert met tomaten erin. Als je slechts één lijst hebt, is er maar één plek
om bij te werken!

Het komt vaak voor dat je in je code dezelfde dingen herhaalt omdat je te maken hebt met taken die net een beetje van elkaar verschillen. Ze lijken op elkaar, maar net die kleine verschillen zorgen ervoor dat je aparte functies moet maken die in essentie veel overlappen. Het slim weghalen van deze dubbele code betekent dat je een slimme manier vindt om met één enkele functie, module of klasse deze variaties te beheren.

Het is echt belangrijk om dit goed te doen en het is slim om de SOLID-principes te volgen die we bij het onderdeel over _klassen_ beschrijven. Niet goed doordachte oplossingen kunnen soms meer kwaad dan goed doen! Maar als je een sterke, algemene oplossing kunt bedenken, ga er dan voor. Het laatste wat je wilt, is dat je steeds op meerdere plekken aanpassingen moet doen wanneer je eigenlijk maar één dingetje wilde veranderen.

**Onjuist:**

```javascript
function showDeveloperList(developers) {
  developers.forEach(developer => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(manager => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Juist:**

```javascript
function showEmployeeList(employees) {
  employees.forEach(employee => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience
    };

    switch (employee.type) {
      case "manager":
        data.portfolio = employee.getMBAProjects();
        break;
      case "developer":
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Set default objects with Object.assign

**Onjuist:**

```javascript
const menuConfig = {
  title: null,
  body: "Bar",
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || "Foo";
  config.body = config.body || "Bar";
  config.buttonText = config.buttonText || "Baz";
  config.cancellable =
    config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Juist:**

```javascript
const menuConfig = {
  title: "Order",
  // User did not include 'body' key
  buttonText: "Send",
  cancellable: true
};

function createMenu(config) {
  let finalConfig = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true
    },
    config
  );
  return finalConfig
  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```

**Juist (BONUS ES6):**

```javascript
const menuConfig = {
  title: "Order",
  // User did not include 'body' key
  buttonText: "Send",
  cancellable: true
};

function createMenu(config) {
  const defaultConfig = {
    title: "Foo",
    body: "Bar",
    buttonText: "Baz",
    cancellable: true
  };
  let finalConfig = {...defaultConfig, ...config};
  return finalConfig;
  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```

**[⬆ naar boven](#inhoudsopgave)**

### Vermijd het gebruik van vlaggen in functies

Het gebruik van (booleaanse) vlaggen als parameters voor functies geeft aan dat de functie verschillende gedragingen kan hebben, afhankelijk van de vlag. Het is beter om functies specifieker te maken, zodat ze één duidelijke taak uitvoeren zonder afhankelijk te zijn van vlaggen voor verschillend gedrag. Dit maakt de code eenvoudiger te begrijpen en te onderhouden.

**Onjuist:**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Juist:**

```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Vermijd Bijwerkingen (deel 1)

Een functie veroorzaakt een bijwerking als deze iets anders doet dan een waarde aannemen en een andere waarde of waarden retourneren. Bijwerkingen kunnen bijvoorbeeld het schrijven naar een bestand zijn, het wijzigen van een globale variabele of per ongeluk al je geld overmaken naar een vreemde.

Natuurlijk zijn bijwerkingen soms noodzakelijk in een programma. Bijvoorbeeld, je moet mogelijk gegevens naar een bestand schrijven. Wat je wilt doen, is centraliseren waar je dit doet. Het is geen goed idee om meerdere functies en klassen te hebben die naar een bepaald bestand schrijven. Het is beter om slechts één service te hebben die dit doet. Slechts één.

HHet is essentieel om veelgemaakte fouten te voorkomen, zoals het delen van gegevens tussen objecten zonder duidelijke organisatie, het gebruiken van gegevenstypen die door iedereen kunnen worden gewijzigd en het verspreiden van bijwerkingen overal in je code. Als je deze fouten kunt vermijden, zul je jezelf gelukkiger voelen dan de meeste andere programmeurs.

**Onjuist:**

```javascript
// Globale variabele die wordt aangeroepen door de volgende functie.
// Als er nog een functie was die deze naam gebruikte, zou het nu een array zijn en dat zou problemen veroorzaken.
let name = "Ryan McDermott";

function splitIntoFirstAndLastName() {
  name = name.split(" ");
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Juist:**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(" ");
}

const name = "Ryan McDermott";
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

**[⬆ naar boven](#inhoudsopgave)**

### Vermijd Bijwerkingen (deel 2)

In JavaScript zijn sommige waarden vast (immutable) en andere kunnen veranderen (mutable). Objecten en arrays behoren tot de veranderlijke waarden, dus we moeten voorzichtig zijn wanneer we ze als parameters aan een functie doorgeven. Een JavaScript-functie kan de eigenschappen van een object wijzigen of de inhoud van een array aanpassen, wat gemakkelijk tot problemen elders in de code kan leiden.

Stel je voor dat er een functie is die een array als parameter accepteert om een winkelwagentje voor te stellen. Als deze functie wijzigingen aanbrengt in die winkelwagen-array, zoals het toevoegen van een item, zullen andere functies die dezelfde cart-array gebruiken, worden beïnvloed door deze wijzigingen. Dat kan handig zijn, maar het kan ook problemen veroorzaken. Laten we een situatie bedenken waarin dat mis kan gaan:

Een gebruiker klikt op de knop "Kopen" en roept een koop-functie op die een netwerkverzoek start en de winkelwagen-array naar de server stuurt. Vanwege een slechte netwerkverbinding moet de koop-functie het verzoek herhaaldelijk proberen. Wat als de gebruiker in de tussentijd per ongeluk op de knop "Toevoegen aan winkelwagen" klikt voor een item dat ze eigenlijk niet willen voordat het netwerkverzoek begint? Als dat gebeurt en het netwerkverzoek begint, zal de koop-functie het per ongeluk toegevoegde item verzenden omdat de winkelwagen-array is gewijzigd.

Een goede oplossing zou zijn dat de `addItemToCart`-functie altijd een kopie van de `cart` maakt, deze bewerkt en de kopie retourneert. Hierdoor worden functies die nog steeds de oude winkelwagen gebruiken, niet beïnvloed door de wijzigingen.

Er zijn twee belangrijke punten om op te merken bij deze aanpak:

1. Er kunnen gevallen zijn waarin je daadwerkelijk de invoer van het object wilt
   wijzigen, maar wanneer je deze programmeerpraktijk volgt, zul je merken dat die gevallen vrij zeldzaam zijn. De meeste dingen kunnen worden herschreven om geen onverwachte gevolgen te hebben!

2. Het klonen van grote objecten of arrays kan prestatieproblemen veroorzaken. Gelukkig is dit in de praktijk meestal
   geen groot probleem omdat er uitstekende [bibliotheken](https://facebook.github.io/immutable-js/) zijn die deze aanpak minder geheugenintensief maken dan wanneer je handmatig objecten en arrays zou klonen.

**Onjuist:**

```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Juist:**

```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

**[⬆ naar boven](#inhoudsopgave)**

### Schrijf niet naar de globale naamruimte

Het verontreinigen van de globale naamruimte is geen goede gewoonte in JavaScript, omdat je kunt botsen met een andere bibliotheek en de gebruiker van jouw API zou hier niets van weten totdat ze een uitzondering in productie krijgen.
Stel dat je JavaScript's native `Array` methode wilt uitbreiden met een `diff` methode die het verschil tussen twee arrays kan tonen. Je zou je nieuwe functie kunnen toevoegen aan `Array.prototype`, maar dit kan conflicteren met een andere bibliotheek die hetzelfde probeert te doen. Wat als die andere bibliotheek gewoon `diff` zou gebruiken om het verschil tussen het eerste en laatste element van een array te vinden? Het is veel beter om eenvoudigweg ES2015/ES6 klassen te gebruiken en de Array naamruimte niet te veranderen.

**Onjuist:**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Juist:**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Functioneel programmeren boven imperatief programmeren

JavaScript is geen functionele taal zoals Scala of Rust, maar het heeft wel een functionele inslag. Functionele talen hebben vaak een helderder en gemakkelijker te begrijpen code, wat het testen vereenvoudigt. Kies voor deze programmeerstijl wanneer mogelijk.

**Onjuist:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Juist:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

**[⬆ naar boven](#inhoudsopgave)**

### Voeg voorwaarden samen

**Onjuist:**

```javascript
if (fsm.state === "fetching" && isEmpty(listNode)) {
  // ...
}
```

**Juist:**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === "fetching" && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Voorkom negatieve voorwaarden

Het gebruiken van negatieve voorwaarden in de namen van functies kan het wat lastiger maken om je code te begrijpen. Wanneer je zoiets tegenkomt, moet je eigenlijk twee keer nadenken (een soort van dubbele ontkenning) en dat maakt het lezen van de code gewoon moeilijker. Het is een stuk fijner als de namen van functies en voorwaarden op een duidelijke, positieve manier worden opgesteld.

**Onjuist:**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Juist:**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Vermijd voorwaarden

Dit klinkt misschien als een onmogelijke opgave. De meeste mensen denken bij het horen hiervan meteen: "Hoe kan ik iets doen zonder een `if`?" Het antwoord is dat je in veel gevallen polymorfisme kunt gebruiken om dezelfde taak uit te voeren. Polymorfisme stelt ons in staat om objecten van verschillende klassen te behandelen via een gemeenschappelijke interface, waardoor de noodzaak voor expliciete conditionele logica wordt verminderd. De volgende vraag is meestal: "Waarom zou ik dat willen doen?" Het antwoord heeft te maken met een eerder principe dat we hebben beschreven: een functie moet slechts één ding doen. Wanneer je klassen en functies hebt met `if`-verklaringen, geef je aan je gebruiker aan dat je functie meer dan één ding probeert te doen. Onthoud, focus gewoon op één ding.

**Onjuist:**

```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Juist:**

```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Vermijd typecontroles (deel 1)

JavaScript heeft ongecontroleerde datatypen, wat betekent dat je functies elk type argument kunnen accepteren. Soms word je verleid om typecontroles in je functies te doen vanwege deze vrijheid. Er zijn echter veel manieren om dit te vermijden. Het eerste waar je aan moet denken is het gebruik van consistente API's.

**Onjuist:**

```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location("texas"));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location("texas"));
  }
}
```

**Juist:**

```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location("texas"));
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Vermijd typecontroles (deel 2)

Als je werkt met eenvoudige primitieve waarden zoals strings en integers, en je kunt geen polymorfisme gebruiken maar toch de behoefte voelt om typecontroles uit te voeren, overweeg dan om TypeScript te gebruiken. TypeScript biedt de mogelijkheid om expliciete typen te definiëren, wat helpt bij het vangen van typegerelateerde fouten tijdens de compile-time, in plaats van runtime. Het probleem met handmatig typecontroles uitvoeren in normaal JavaScript is dat dit zoveel extra woorden vereist dat de nep-"type-veiligheid" die je krijgt, niet opweegt tegen de verloren leesbaarheid. Houd je JavaScript schoon, schrijf goede tests en voer goede code-reviews uit. Of doe dit allemaal, maar dan met TypeScript!

**Onjuist:**

```javascript
function combine(val1, val2) {
  if (
    (typeof val1 === "number" && typeof val2 === "number") ||
    (typeof val1 === "string" && typeof val2 === "string")
  ) {
    return val1 + val2;
  }

  throw new Error("Must be of type String or Number");
}
```

**Juist:**

```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Vermijd Overmatige Optimalisatie

Het is verleidelijk om je code tot in het kleinste detail te optimaliseren, maar in moderne browsers worden veel optimalisaties automatisch uitgevoerd. Het advies om overmatige optimalisatie te vermijden is gebaseerd op het principe van premature optimalisatie, waar Donald Knuth over zei: "Premature optimization is the root of all evil." Dit idee benadrukt dat het te vroeg optimaliseren van code, voordat duidelijk is waar de werkelijke prestatieknelpunten liggen, niet alleen tijdverspilling kan zijn, maar ook de code complexer en moeilijker te onderhouden kan maken. [Er zijn goede bronnen](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers) beschikbaar, die je kunnen vertellen waar je nog moet optimaliseren. Richt je daar eerst op.

**Onjuist:**

```javascript
// Oude browsers kunnen elke iteratie met een niet-gecachte `list.length` kostbaar maken
// vanwege de herberekening van `list.length`. In moderne browsers is dit geoptimaliseerd.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Juist:**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Verwijder dode code

Dode code is net zo slecht als dubbele code. Er is geen reden om het in
je codebase te houden. Als het niet wordt aangeroepen, verwijder het dan! Mocht je het nog nodig hebben, dan staat het veilig in je versiegeschiedenis opgeslagen.

**Onjuist:**

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**Juist:**

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**[⬆ naar boven](#inhoudsopgave)**

## **Objecten en Datastructuren**

### Gebruik getters en setters

Het gebruik van getters (ophalen) en setters (instellen) om toegang te krijgen tot gegevens in objecten kan beter zijn dan simpelweg te zoeken naar een eigenschap in een object. Mocht je je afvragen waarom, dan is hier een ongeorganiseerde lijst met redenen waarom:

- Wanneer je meer wilt doen dan alleen een eigenschap van een object ophalen, hoef je niet elke toegangsmethode in je codebase op te zoeken en te wijzigen.
- Het toevoegen van validatie is eenvoudig bij het instellen (_setten_) van een waarde.
- Het sluit de interne representatie af.
- Eenvoudig om logging en foutafhandeling toe te voegen bij het ophalen en instellen.
- Je kunt de eigenschappen van je object uitgesteld laden, bijvoorbeeld door deze van een server te halen. (Lazy loading)

**Onjuist:**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Juist:**

```javascript
function makeBankAccount() {
  // dit is privé
  let balance = 0;

  // een "getter", publiekelijk toegankelijk via het geretourneerde object hieronder
  function getBalance() {
    return balance;
  }

  // een "setter", publiekelijk toegankelijk via het geretourneerde object hieronder
  function setBalance(amount) {
    // ... valideer voordat het saldo wordt bijgewerkt
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

**[⬆ naar boven](#inhoudsopgave)**

### Geef objecten privéleden

In JavaScript worden zowel eigenschappen als methoden als "leden" van een object beschouwd. Dit kan worden bereikt via closures (voor ES5 en lager).

**Onjuist:**

```javascript
const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Juist:**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    }
  };
}

const employee = makeEmployee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```

Hoewel de techniek van het gebruik van closures voor het creëren van privéleden in objecten zeer nuttig is, zijn er met de introductie van ES6 (ECMAScript 2015) en latere versies meer formele middelen beschikbaar gekomen voor het definiëren van privé-eigenschappen en -methoden in klassen, zoals de # syntax voor privévelden.

Hier is hoe je het zou kunnen doen met ES6+ klassen en privévelden:

```javascript
class Employee {
  #name;

  constructor(name) {
    this.#name = name;
  }

  getName() {
    return this.#name;
  }
}

const employee = new Employee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
// Directe toegang tot #name buiten de klasse is niet mogelijk, dus geen delete operatie mogelijk
```

Deze moderne aanpak biedt een duidelijke en gestandaardiseerde manier om privéleden in klassen te definiëren, terwijl de voordelen van closures behouden blijven, maar met een meer leesbare en onderhoudbare syntax. Het is echter belangrijk op te merken dat deze functie mogelijk niet in alle JavaScript-omgevingen wordt ondersteund zonder transpilatie (bijvoorbeeld via Babel), afhankelijk van het doelplatform van je project.

**[⬆ naar boven](#inhoudsopgave)**

## **Klassen**

### Geef de voorkeur aan ES2015/ES6-klassen boven ES5 functies

Het is veel makkelijker om leesbare klassen te maken met ES2015/ES6. Als je overerving nodig hebt (en wees je ervan bewust dat je dit misschien niet nodig hebt), geef dan de voorkeur aan ES2015/ES6-klassen. Geef echter de voorkeur aan kleine functies boven klassen totdat je merkt dat je grotere en complexere objecten nodig hebt.

**Onjuist:**

```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error("Instantiate Human with `new`");
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Juist:**

```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() {
    /* ... */
  }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() {
    /* ... */
  }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() {
    /* ... */
  }
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Gebruik methodekoppelingen

Deze techniek, in het engels _Method Chaining_, is erg handig in JavaScript en je ziet het in veel bibliotheken zoals jQuery en Lodash. Het maakt je code expressiever en minder omslachtig. Daarom is het aangeraden, om methodekoppelingen te gebruiken en zie hoe duidelijk je code wordt. In je klassefuncties, retourneer eenvoudigweg `this` aan het einde van elke functie, en je kunt vervolgens meer klassemethoden eraan koppelen.

**Onjuist:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red");
car.setColor("pink");
car.save();
```

**Juist:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // Geef this terug om te kunnen koppelen
    return this;
  }

  setModel(model) {
    this.model = model;
    // Geef this terug om te kunnen koppelen
    return this;
  }

  setColor(color) {
    this.color = color;
    // Geef this terug om te kunnen koppelen
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // Geef this terug om te kunnen koppelen
    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

**[⬆ naar boven](#inhoudsopgave)**

### Kies compositie boven overerving

Je hebt misschien wel eens gehoord van het principe "Liever compositie dan overerving". Dit betekent dat je, als je kunt kiezen, beter compositie kunt gebruiken dan overerving. Hoewel overerving handig kan zijn, biedt compositie vaak een flexibelere en onderhoudsvriendelijkere oplossing. Dit staat beschreven in [_Design Patterns_](https://en.wikipedia.org/wiki/Design_Patterns) door de 'Gang of Four'.

Je vraagt je misschien af: "Wanneer zou ik dan overerving moeten gebruiken?" Nou, het hangt af van het probleem dat je probeert op te lossen. Over het algemeen is overerving handig wanneer je een nieuwe klasse wilt maken die alle eigenschappen en methoden van een bestaande klasse erft. Bijvoorbeeld in de volgende gevallen:

1. De erfenis vertegenwoordigt een ‘is-een’-relatie en niet een ‘heeft-een’-relatie
   relatie. (Bijvoorbeeld: Mens->Dier vs. Gebruiker->Gebruikersdetails).
2. Je kunt code uit de basisklassen hergebruiken. (Bijvoorbeeld: mensen kunnen bewegen zoals alle dieren).
3. Je wilt globale wijzigingen aanbrengen in afgeleide klassen door een basisklasse te wijzigen. (Bijvoorbeeld: verander het calorieverbruik van alle dieren wanneer ze bewegen).

**Onjuist:**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Onjuist want Employees "hebben" tax data. EmployeeTaxData is geen type van Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Juist:**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```

**[⬆ naar boven](#inhoudsopgave)**

## **SOLID**

### Het principe van één verantwoordelijkheid (SRP)

Het principe van één verantwoordelijkheid is het Engels bekend als Single Responsibility Principle (SRP). In "Clean Code" staat: "Een klasse mag nooit meer dan één reden hebben om te veranderen". Het is verleidelijk om een klasse te vullen met veel verschillende taken, net zoals wanneer je maar één koffer mag meenemen op reis. Maar als je dit doet, wordt je klasse onsamenhangend en zal het vele redenen hebben om te veranderen. Het is belangrijk om het aantal keren dat je een klasse moet aanpassen te minimaliseren. Dit is van belang omdat als een klasse te veel verschillende taken heeft en je er één aanpast, het lastig kan zijn om te begrijpen hoe dat andere delen van je code beïnvloedt.

**Onjuist:**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Juist:**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Het Open/Gesloten Principe (OCP)

Het Open/Gesloten Principe (OCP) is een slimme manier om te zorgen dat je software kan groeien en veranderen zonder dat je elke keer alles opnieuw moet bouwen of aanpassen. Stel je voor dat je een LEGO-bouwwerk maakt. In plaats van dat je elke keer het hele bouwwerk uit elkaar haalt om een nieuw stukje toe te voegen, ontwerp je het zó dat je gemakkelijk nieuwe blokjes kunt toevoegen zonder de rest te verstoren. Zo werkt het Open/Gesloten Principe ook voor het ontwerpen van software. Je maakt je code op een manier dat je nieuwe dingen kunt toevoegen (dat is het "open" deel) zonder de bestaande code te hoeven veranderen (dat is het "gesloten" deel). Dit maakt je software flexibel en toekomstbestendig, waardoor je makkelijker kan inspelen op nieuwe eisen of technologieën zonder steeds vanaf nul te beginnen.

**Onjuist:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === "ajaxAdapter") {
      return makeAjaxCall(url).then(response => {
        // transform response and return
      });
    } else if (this.adapter.name === "nodeAdapter") {
      return makeHttpCall(url).then(response => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Juist:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then(response => {
      // transform response and return
    });
  }
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Het Principe van Liskov Substitutie (LSP)

Dit principe houdt in dat als de basisklasse T gebruikt wordt in een programma, het programma ook correct dient te werken met alle specifieke implementaties van T, zonder wijzigingen in het programma te hoeven maken. Dit klinkt misschien ingewikkeld, maar eigenlijk is het vrij simpel. Het komt erop neer dat als je een ouderklasse en een kindklasse hebt, je ze onderling moet kunnen uitwisselen zonder dat het programma stuk gaat. Laten we eens kijken naar het klassieke voorbeeld van Vierkant-Rechthoek om het wat duidelijker te maken. Wiskundig gezien is een vierkant een speciaal geval van een rechthoek, maar als je ze gebruikt in je code, moeten ze zich op dezelfde manier gedragen.

**Onjuist:**

```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach(rectangle => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // FOUT: Geeft 25 terug voor Vierkant. Zou 20 moeten zijn.
    rechthoek.render(oppervlakte);
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Juist:**

```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach(shape => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ naar boven](#inhoudsopgave)**

### Interface Segregation Principle (ISP)

Het Interface Segregation Principle (ISP) benadrukt het belang van het scheiden van interfaces, zodat clients (gebruikers van de interface) niet afhankelijk worden gemaakt van interfaces die ze niet gebruiken. Dit principe draagt bij aan de flexibiliteit en onderhoudbaarheid van de code door te voorkomen dat onnodige afhankelijkheden de ontwikkeling vertragen of complexer maken.

In JavaScript zijn er geen strikte interfaces, maar het idee blijft belangrijk. ISP zegt eigenlijk: "Clients moeten niet afhankelijk zijn van interfaces die ze niet gebruiken." In JavaScript worden interfaces vaak impliciet gebruikt vanwege _duck-typing_.

Een goed voorbeeld hiervan is bij klassen die grote instellingenobjecten vereisen. Het is handig om clients niet te dwingen om enorme hoeveelheden opties op te geven, omdat ze die meestal toch niet allemaal nodig hebben. Optionele instellingen voorkomen een overvolle interface.

**Onjuist:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.settings.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  animationModule() {} // Meestal hebben we geen animatie nodig bij het doorlopen.
  // ...
});
```

**Juist:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  options: {
    animationModule() {}
  }
});
```

**[⬆ naar boven](#inhoudsopgave)**

### Het Principe van Dependency Inversion (DIP)

Dit principe draait om twee belangrijke zaken:

1. Hoog-niveau modules moeten niet afhankelijk zijn van laag-niveau modules. Beide moeten afhankelijk zijn van abstracties.
2. Abstracties moeten niet afhankelijk zijn van details. Details zouden afhankelijk moeten zijn van abstracties.

Dit kan in het begin lastig zijn om te begrijpen, maar als je bekend bent met AngularJS, heb je waarschijnlijk al een implementatie van dit principe gezien in de vorm van Dependency Injection (DI). Hoewel ze geen identieke concepten zijn, zorgt DIP ervoor dat modules op hoog niveau geen details hoeven te kennen van hun modules op laag niveau en deze hoeven op te zetten. Dit kan worden bereikt via DI. Een groot voordeel hiervan is dat het de koppeling tussen modules vermindert. Koppeling is een slecht ontwikkelingspatroon omdat het je code moeilijk maakt om te veranderen.

Omdat JavaScript geen interfaces heeft, zijn de abstracties waarop wordt vertrouwd impliciete contracten. Met andere woorden, het gaat om de methoden en eigenschappen die een object/klasse aanbiedt aan een ander object/klasse. In het onderstaande voorbeeld is het impliciete contract dat elk Request-module voor een `InventoryTracker` een `requestItems`-methode zal hebben.

**Onjuist:**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // FOUT: We zijn afhankelijk geworden van een specifieke implementatie van het verzoek.
    // Het zou beter zijn geweest als requestItems afhankelijk was van een verzoeksmethode genaamd request.
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();
```

**Juist:**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  requestItem(item) {
    // ...
  }
}

// Door onze afhankelijkheden extern te construeren en te injecteren, kunnen we onze
// requestmodule eenvoudig vervangen door een mooie nieuwe die WebSockets gebruikt.
const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();
```

**[⬆ naar boven](#inhoudsopgave)**

## **Testen**

Testen is cruciaal voor het uitrollen van kwaliteitscode. Zonder tests loop je het risico om telkens wanneer je code uitrolt onbedoelde fouten te introduceren. Het bepalen van de juiste hoeveelheid tests is een teambeslissing, maar streven naar 100% dekkingsgraad (alle statements en takken) zorgt voor een hoge mate van vertrouwen en gemoedsrust onder ontwikkelaars. Dit betekent dat naast het gebruik van een goed testframework, ook een [dekkingshulpmiddel](https://gotwarlost.github.io/istanbul/) van essentieel belang is.

Er zijn geen goed excuus om geen testen te schrijven. Er zijn [veel goede JavaScript test frameworks](https://jstherightway.org/#testing-tools) beschikbaar, dus kies er een die jouw team of jij zelf het prettigst vindt. Zodra je een geschikt framework hebt gevonden, is het belangrijk om altijd tests te schrijven voor nieuwe functies/modules die worden toegevoegd. Of je nu de voorkeur geeft aan Test Driven Development (TDD) of niet, het doel is om ervoor te zorgen dat je de gestelde dekkingsgraad bereikt voordat je een nieuwe functie lanceert of een bestaande wijzigt.

### Eén concept per test

**Onjuist:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**Juist:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles 30-day months", () => {
    const date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ naar boven](#inhoudsopgave)**

## **Gelijktijdigheid**

### Gebruik Promises, geen callbacks

Het gebruik van Promises in plaats van callbacks is een significante verbetering in het beheren van asynchrone operaties in JavaScript. Promises bieden een meer leesbare en gestructureerde benadering voor asynchrone flow controle, waardoor de zogenaamde _callback hell_ wordt voorkomen. Dit maakt je code niet alleen schoner, maar verbetert ook de foutafhandeling door het gebruik van `.then()` voor succesvolle operaties en `.catch()` voor fouten.

**Onjuist:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);
```

**Juist:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**[⬆ naar boven](#inhoudsopgave)**

### Async/Await is nog beter dan Promises

Promises zijn al een stuk schoner dan callbacks, maar met de introductie van ES2017/ES8 zijn async en await geïntroduceerd, wat een nog leesbaardere oplossing biedt. Je hebt alleen een functie nodig die is gemarkeerd met het `async`-woord, en dan kun je je logica schrijven zonder een `then`-keten van functies. Gebruik dit als je in je codebase al gebruik kunt maken van de functies van ES2017/ES8!

**Onjuist:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**Juist:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle()
```

**[⬆ naar boven](#inhoudsopgave)**

## **Foutafhandeling**

Fouten zijn goed! Wanneer een fout optreedt, biedt dit een kans om te leren,
te diagnosticeren, en uiteindelijk je code robuuster te maken. Wanneer een fout
wordt gegenereerd, biedt dit essentiële feedback. Het onderbreken van de
uitvoering en het genereren van een _stack trace_ zijn mechanismen die bedoeld
zijn om ontwikkelaars te helpen de oorzaak van het probleem te identificeren.
Door aandacht te besteden aan deze signalen, kun je de onderliggende oorzaken
van problemen in je code adresseren.

### Negeer opgevangen fouten niet

Het negeren van opgevangen fouten, of dit nu bewust of onbewust gebeurt, is een gemiste kans voor verbetering en kan leiden tot onvoorspelbare gedragingen in je applicatie. Door fouten naar de console te loggen (`console.log`) en er vervolgens niets mee te doen, mis je de kans om je applicatie te verbeteren en de gebruikerservaring te optimaliseren. Het is cruciaal om een strategie te hebben voor het geval er fouten optreden, zodat je gepast kunt reageren.

**Onjuist:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Juist:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // Een optie (luider dan console.log):
  console.error(error);
  // Een andere optie:
  notifyUserOfError(error);
  // Nog een optie:
  reportErrorToService(error);
  // OF doe alle drie!
}
```

### Negeer mislukte promises niet

Promises zijn ontworpen om het werken met asynchrone operaties te vereenvoudigen, maar ze introduceren ook nieuwe uitdagingen, vooral rond foutafhandeling. Wanneer een Promise wordt afgewezen, betekent dit dat de verwachte asynchrone operatie is mislukt. Het correct afhandelen van deze afwijzingen is cruciaal voor het behouden van de stabiliteit van je applicatie en het bieden van een goede gebruikerservaring.

**Onjuist:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    console.log(error);
  });
```

**Juist:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    // Een optie (luider dan console.log):
    console.error(error);
    // Een andere optie:
    notifyUserOfError(error);
    // Nog een optie:
    reportErrorToService(error);
    // OF doe alle drie!
  });
```

**[⬆ naar boven](#inhoudsopgave)**

## **Opmaak**

Net als veel andere regels hier, is er geen harde regel voor de opmaak van je code die je moet volgen. Het belangrijkste is: ga niet discussiëren over opmaak. Er zijn veel [tools](https://standardjs.com/rules.html die dit automatiseren voor je. Kies er een! Het is zonde van tijd en geld voor je team of andere ontwikkelaars om te discussiëren over opmaak.

Voor zaken die niet automatisch worden opgemaakt (inspringing, tabs vs. spaties, enkele vs. dubbele aanhalingstekens, enzovoort), volgen hier wat richtlijnen.

### Gebruik hoofdletters consistent

Hoofdletters zijn in een ongetypeerde taal als JavaScript van cruciaal belang om intentie en soort variabele aan te duiden. Het consistent gebruik van hoofdletters voor constanten, camelCase voor functies en variabelen, en PascalCase voor klassenamen helpt bij het snel identificeren van de soorten en doeleinden van verschillende elementen in je code Deze regels zijn een kwestie van smaak, dus je team kan kiezen wat ze willen. Het belangrijkste is dat je consistent bent.

**Onjuist:**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Juist:**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ naar boven](#inhoudsopgave)**

### Houd functies en aanroepen dicht bij elkaar

Door functies en hun aanroepen dicht bij elkaar te houden, maak je het voor anderen (en voor jezelf in de toekomst) veel makkelijker om te begrijpen hoe de verschillende delen van je applicatie met elkaar samenwerken. Dit is vooral nuttig in grotere projecten, waar het navigeren door de code complex kan worden. Mensen hebben de neiging om code van boven naar beneden te lezen, zoals een krant. Daarom moet je ervoor zorgen dat je code op die manier leesbaar is.

**Onjuist:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**Juist:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ naar boven](#inhoudsopgave)**

## **Commentaar**

### Alleen commentaar toevoegen bij complexe bedrijfslogica

Commentaar is als een verontschuldiging, geen vereiste. Goede code documenteert zichzelf _meestal_.

**Onjuist:**

```javascript
function hashIt(data) {
  // De hash
  let hash = 0;

  // Lengte van de string
  const length = data.length;

  // Loop door ieder karakter in de data
  for (let i = 0; i < length; i++) {
    // Zoek de karakter code.
    const char = data.charCodeAt(i);
    // Maak de hash
    hash = (hash << 5) - hash + char;
    // Converteer naar een 32-bit integer
    hash &= hash;
  }
}
```

**Juist:**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Converteer naar een 32-bit integer om overloop te voorkomen
    hash &= hash;
  }
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Laat geen uitgecommentarieerde code achter in je codebase

Versiebeheer bestaat niet voor niets. Laat oude code in je geschiedenis staan.

**Onjuist:**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Juist:**

```javascript
doStuff();
```

**[⬆ naar boven](#inhoudsopgave)**

### Vermijd dagboekachtige opmerkingen

Onthoud, gebruik versiebeheer! Er is geen noodzaak voor dode code, uitgecommentarieerde code, en vooral dagboekachtige opmerkingen. Schrijf duidelijke en beschrijvende commitberichten die de aard van de wijziging en de reden erachter uitleggen. Dit is waardevolle documentatie voor het team en voor toekomstige jij. Gebruik `git log` om de geschiedenis te bekijken!

**Onjuist:**

```javascript
/**
* 2016-12-20: Monaden verwijderd, begreep ze niet (RM)
* 2016-10-01: Verbeterd met speciale monaden (JP)
* 2016-02-03: Typecontroles verwijderd (LI)
* 2015-03-14: Combinatiefunctie toegevoegd met typecontroles (JR) 
*/
function combine(a, b) {
  return a + b;
}
```

**Juist:**

```javascript
function combine(a, b) {
  return a + b;
}
```

**[⬆ naar boven](#inhoudsopgave)**

### Vermijd positionele markers

Positionele markers zijn de horizontale regels of groepen van commentaartekens die bedoeld zijn om verschillende secties van de code visueel te scheiden. Hoewel ze in theorie kunnen helpen bij het organiseren van de code, leiden ze vaak tot meer visuele ruis en kunnen ze de aandacht afleiden van de eigenlijke code. Laat de functie- en variabelennamen, samen met de juiste inspringing en opmaak, de visuele structuur van je code bepalen.

**Onjuist:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Juist:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};
```

**[⬆ naar boven](#inhoudsopgave)**
