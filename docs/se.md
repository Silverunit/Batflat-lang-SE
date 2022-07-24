Allmänt
=======

Batflat är ett Polskt innehållshanteringssystem som är enkelt, lätt och snabbt. Det släpptes i maj 2016. Den kostnadsfria versionen av applikationen delas under en [licens](/license) som kräver att information om utvecklarna och tillbakalänkar lämnas. Batflat fungerar bra när du skapar små webbplatser som företagssidor, bloggar eller privata hemsidor. Med den här dokumentationen lär du dig att installera, konfigurera och skapa dina egna moduler och teman.

Dokumentationen är uppdelad i flera avsnitt. Den första är för allmänna instruktioner, den andra är för webbdesigners, och de två senare är för webbutvecklare.


Systemkrav
------------

Systemkraven för Batflat är låga, så varje modern server borde räcka.

+ Apache 2.2+ med `mod_rewrite`
+ PHP version 5.6+
+ tillgång till SQLite

PHP-konfiguration måste ha följande tillägg:

+ dom
+ gd
+ mbstring
+ pdo
+ zip
+ cURL


Installation
------------

Ladda först ner den senate versionen av [Batflat](http://feed.sruu.pl/batflat/download/latest).

Packa upp alla filer från det komprimerade paketet och överför dem sedan till den lokala katalogen eller fjärrservern. När det gäller en fjärrserver, anslut till den via a (S)FTP client, som exempelvis det fria [FileZilla](https://filezilla-project.org). Vanligvis laddas filerna upp till mappen `www`, `htdocs` eller `public_html`.

**Varning!** Se till att `.htaccess` filen finns på servern. Utan det fungerar inte Batflat CMS.

Vissa servrar kan kräva ytterligare behörigheter, `chmod 777` för följande kataloger och filer:

+ tmp/
+ uploads/
+ admin/tmp/
+ inc/data/
+ inc/data/database.sdb

Öppna din webbläsare och navigera till adressen där Batflats filer finns. Du bör se en standardmall med exempelinnehåll.

För att komma åt administrationspanelen, lägg till `/admin/` i slutet på din URL. **Standard login och password är *"admin"*.** Det bör ändras direkt efter inloggning av säkerhetsskäl. Vi rekommenderar också att byta namn på katalogen med administrationspanelen. *(Du måste sedan ändra konstantvärdet i definieringsfilen)*.


Konfiguration
-------------

Batflat-CMS kan konfigureras genom att redigera inställningarna i administrationspanelen och genom definitionsfilen. Vi rekommenderar dock inte att du ändrar konfigurationen i filen om du är en oerfaren person.

### Administrations panel
För att ändra den grundläggande konfigurationen på adminpanelen väljer du `Settings` fliken. Du kan ange ett sidnamn, beskrivning och nyckelord i metataggarna, såväl som någon annanstans i standardmallen, till exempel i sidhuvudet. Du kan också ändra startsidan, standardspråk *(separat för webbplatsen och administrationspanelen)*, definiera sidfotens innehåll och välja redigerare *(HTML eller WYSIWYG)* som kommer att finnas tillgängliga när du redigerar undersidor och blogginlägg.

Du kommer att ändra konfigurationen för de återstående modulerna i flikarna som motsvarar deras namn.

### Definieringsfilen
Mer avancerade saker kan du ändra i `inc/core/defines.php` filen, som innehåller definitioner av konstantvariabler. 

+ `ADMIN` — katalognamnet som innehåller administrationspanelen
+ `THEMES` — sökväg till katalogen som innehåller teman
+ `MODULES` — sökväg till katalogen som innehåller modulerna
+ `UPLOADS` — sökväg till katalogen som innehåller de överförda filerna
+ `FILE_LOCK` — låsa möjligheten att redigera filer via administrationspanelen
+ `BASIC_MODULES` — lista över grundläggande moduler som inte kan tas bort
+ `HTML_BEAUTY` — fin HTML-formatering efter parsning
+ `DEV_MODE` — utvecklarläge, där PHP-fel och anteckningar visas


Uppdatering
------

Om du vill hålla dig uppdaterad med alla de senaste nyheterna, bug fixes och säkerhetsproblem, du bör regelbundet leta efter Batflat-uppdateringar. Du kan göra detta i `Settings -> Updates` fliken. Systemet kommer att leta efter en ny version av skriptet och automatiskt ladda ner ett nytt paket från vår server och uppdatera kärnfilerna och modulerna.

Vid komplikationer kan du använda manuellt läge. För att göra detta, ladda ner den senaste versionen av Batflat, ladda upp den till den huvudsakliga programkatalogen,  och lägg sedan till `&manual` parameter till slutet av uppdateringens URL. CMS ska upptäcka ett zip-paket och när du klickar på uppdateringsknappen, processen för att extrahera och skriva över filerna kommer att utföras.

Före varje uppdatering skapar Batflat en säkerhetskopia. Du hittar den i skriptkatalogen, i `backup/` foldern. Om en uppdatering misslyckats kan du återställa den när som helst.


Teman
======

Struktur
---------

Strukturen för teman i Batflat är mycket enkel. Skapa bara en ny mapp i `themes/` katalogen och lägg till följande filer:

+ `index.html` — standardmall för undersidor
+ `manifest.json` — temainformation
+ `preview.png` — skärmdump som visar temat *(valfritt)*

Varje undersida kan använda en annan mall, så förutom den nämnda filen kan du också skapa en annan, exempelvis `xyz.html`. Mallval är tillgängligt i adminpanelen när du skapar en sida. Det finns inga regler om CSS- och JS-filer. Det finns full frihet.

I temamappen kan du också skapa dina egna modulvyer. För att göra detta måste du skapa en katalog `modules/module_name` och `*.html` filer med namn som motsvarar namnen på de ursprungliga vyerna. Till exempel bör vyn över kontaktformuläret finnas i följande sökväg: `themes/theme_name/modules/contact/form.html`. Batflat upptäcker automatiskt en ny vy och använder den istället för standardvyn för modulen.


Temataggar
-------------

CMS använder ett enkelt mallsystem som innehåller följande taggar:

### Variabler
```php
{$foo}        // enkel variabel
{$foo|e}      // HTML undgå för variabel
{$foo|cut:10} // innehållet i variabeln skärs till 10 tecken
{$foo.bar}    // en array
```
Tillgång till elementen i en array görs av ett prick-tecken.

### Förhållande
```php
{if: $foo > 5}
    lorem
{elseif: $foo == 5}
    ipsum
{else}
    dolor
{/if}
```

### Loopar
```html
<ul>
{loop: $foo}
    <li>{$key}, {$value}, {$counter}</li>
{/loop}

{loop: $foo as $bar}
    <li>{$key}, {$bar}, {$counter}</li>
{/loop}

{loop: $foo as $bar => $baz}
    <li>{$bar}, {$baz}, {$counter}</li>
{/loop}
</ul>
```
Loop-taggen har 3 utbyggnadssteg. Den första är en array-variabel som mallsystemet kommer att dela upp i tre namngivna variabler `$key`,` $value` och `$counter`, vilket räknar successiva upprepningar från noll. Det andra steget låter dig ange namnet på den variabel som har värdet, och det tredje är också namnet på indexvariabeln.

### Inkludera temafiler
```html
<html>
    <body>
    {template: header.html}
    <main>
        <p>Lorem ipsum dolor sit amet.</p>
    </main>
    {template: footer.html}
    </body>
</html>
```

### PHP kod
```php
Today&grave;s date: {?= date('Y-m-d') ?}
```
Om du utelämnar tecknet `=`, kommer koden bara att köras och ingenting visas. Detta gör att du till exempel kan definiera nya variabler i en mall:
```php
{? $foo = 5 ?}
```

### Inaktivera parsing
```
{noparse}Use the {$ contact.form} tag to display contact form.{/noparse}
```
Eventuella taggar inom *noparse* uttrycket kommer att förbli oförändrat.

### Kommentarer
```
{* this is a comment *}
```
Kommentarer visas inte i källfilen efter att mallen har sammanställts.

### Språk
```
{lang: pl_polski}
    Witaj świecie!
{/lang}
{lang: en_english}
    Hello world!
{/lang}
```
Om du vill anpassa mallelementen för ett visst språk använder du taggarna ovan.


Systemvariabler
----------------
Batflat, samt dess moduler, har många variabler *(vanligen arrays)* som tjänar till att visa varje sidelement. Här är de viktigaste:

+ `{$settings.pole}` — ett arrayelement som innehåller värdet för det givna Batflat-inställningsfältet
+ `{$settings.moduł.pole}` — ett arrayelement som innehåller värdet på fältet för modulinställningar
+ `{$bat.path}` — lagrar sökvägen där systemet finns
+ `{$bat.lang}` — visar för närvarande använt språk
+ `{$bat.notify}` — den sista notifieringen
+ `{$bat.notify.text}` — notifieringens text
+ `{$bat.notify.type}` — meddelandetyp som motsvarar Bootstrap-klasserna *(danger, success)*
+ `{$bat.header}` — ytterligare meta tags, JS scripts och CSS style sheets laddade av moduler
+ `{$bat.footer}` — ytterligare JS scripts laddade av moduler
+ `{$bat.theme}` — visar sökvägen till det aktiva temat med värden
+ `{$bat.powered}` — visar *Powered by Batflat* med en länk till den officiella sajten
+ `{$navigation.xyz}` — visar en lista över `<li>` navigationselement
+ `{$page.title}` — visar namnet på undersidan
+ `{$page.content}` — visar innehållet på undersidan

Exempel
-------

### manifest.json

```
{
    "name": "Example",
    "version": "1.0",
    "author": "Bruce Wayne",
    "email": "contact@waynecorp.com",
    "thumb": "preview.png"
}
```

### index.html

```html
<!doctype html>

<html>
<head>
  <meta charset="utf-8">
  <title>{$page.title} - {$settings.title}</title>
  <meta name="description" content="{$settings.description}">
  <meta name="keywords" content="{$settings.keywords}">
  <link rel="stylesheet" href="{$bat.theme}/styles.css">
  {loop: $bat.header}{$value}{/loop}
</head>

<body>
    <nav>
        <ul>
            {$navigation.main}
        </ul>
    </nav>
    
    <main>
        <h1>{$page.title}</h1>
        {$page.content}
    </main>
    
    <footer>
        {$settings.footer} {$bat.powered}
    </footer>
   
    <script src="{$bat.theme}/scripts.js"></script>
    {loop: $bat.footer}{$value}{/loop}
</body>
</html>
```

Moduler
=======

Struktur
---------

Varje modul, likt teman, måste finnas i en separat mapp skapad i `inc/modules/` sökvägen. Observera att katalogen inte innehåller versaler och specialtecken, till exempel mellanslag.

När du skapar en modul måste du tänka på vilken typ av modul du vill använda. Ska den vara konfigurerad i adminpanelen eller ska den bara fungera på webbplatsen? På grund av denna uppdelning skiljer vi i Batflat på tre huvudmodulfiler:

+ `Info.php` — innehåller information om modulen, till exempel namn, beskrivning, författare eller ikon
+ `Admin.php` — innehållet i den här filen kommer att vara tillgängligt via adminpanelen
+ `Site.php` — innehållet i den här filen kommer att vara tillgängligt för besökare på denna webbplats

Den fjärde men valfria filen är `ReadMe.md` som bör innehålla ytterligare information för den framtida användaren i [Markdown](https://en.wikipedia.org/wiki/Markdown), exempelvis, hur används denna modul.

Om du planerar att skriva en modul som använder HTML, det skulle vara bra att se till att PHP-koden är separat från hypertextmarkeringsspråket. För att göra detta måste du skapa en katalog `views` inuti modulmappen. Inkludera alla visningsfiler i den.

Problemet med flerspråkighet i modulen är liknande. Skapa bara språkfiler med `ini` ändelse inuti `lang` katalgoen.

Strukturen för modulen ska se ut så här:
```
example/
|-- lang/
|    |-- admin/
|    |    |-- en_english.ini
|    |    |-- pl_polski.ini
|    |-- en_english.ini
|    |-- pl_polski.ini
|-- views/
|    |-- admin/
|    |    |-- bar.html
|    |-- foo.html
|-- Admin.php
|-- Info.php
|-- Site.php
+-- ReadMe.md
```

Skapa en module
-----------------

### Informationsfil

Den viktigaste filen för varje modul. Den innehåller grundläggande information och instruktioner under installation och avinstallation.

```php
<?php

    return [
        'name'          =>  'Example',
        'description'   =>  'Lorem ipsum....',
        'author'        =>  'Robin',
        'version'       =>  '1.0',
        'compatibility' =>  '1.3.*',                    // Kompatibilitet med Batflat-versionen
        'icon'          =>  'bolt',
        
        'pages'         =>  ['Example' => 'example'],   // Registrering som en sida (valfritt)

        'install'       =>  function() use($core)       // Installationskommandon
        {
            // lorem ipsum...
        },
        'uninstall'     =>  function() use($core)       // AVinstallationskommandon
        {
            // lorem ipsum...    
        }
    ];
```

En lista över ikoner som du kan använda i den här filen finns tillgänglig på [fontawesome.io](http://fontawesome.io/icons/). Var noga med att inte ange ikonnamnet med `fa-` prefixet.

Om du registrerar en modul som en sida kan du fritt använda routingen och välja den som en hemsida.


### Adminfilen

Innehållet i den här filen kommer att visas i adminpanelen.

```php
<?php
    namespace Inc\Modules\Example;

    use Inc\Core\AdminModule;

    class Admin extends AdminModule
    {
        public function init()
        {
            // Förfaranden som åberopas vid modulinitieringen
        }

        public function navigation()
        {
            return [
                'Foo'   => 'foo',
                'Bar'   => 'bar',
            ];
        }

        public function getFoo($parm)
        {
            return "Foo $parm!";
        }

        public function postBar()
        {
            return "Bar!";
        }
    }
```

I `navigation` funktionen, inkludera arrayen med modulens undersidor. Varje sida bör tilldelas en funktion *(utan prefix)*. Objekt i denna array visas i menyn för administrationspanelen.

Funktioner kan också acceptera argument som skickas genom en URL. Till exempel, efter att du angivit `/example/foo/abc` adress, `getFoo` funktionen kommer generera *"Foo abc!"*.

Som du kan se i listan ovan bör varje funktion som representerar undersidan för modulen ha ett prefix som anger typ av begäran. I de flesta fall kommer vi att använda `getFoo` terminologin och `postFoo` för att skicka information. Om funktionen stöder alla typer, bör den föregå med `any` prefix *(till exempel, `anyFoo`)*. Detta är viktigt eftersom sidor utan prefix inte kommer att hanteras. Stödda funktioner översätts med dynamisk routing enligt följande:

+ `getFoo()` — as `/example/foo` för en GET request
+ `getFoo($parm)` — as `/example/foo/abc` för en GET request
+ `postBar()` — as `example/bar` för POST requests *(form submission)*
+ `anyFoo()` — as `/example/foo` för each request type

### Sitefilen

Denna fil är ansvarig för den del som besökarna av webbplatsen ser. Om modulen är ganska stor är god praxis att registrera den som en sida och tillämpa routing.

```php
<?php

    namespace Inc\Modules\Example;
    
    use Inc\Core\SiteModule

    class Site extends SiteModule
    {
        public function init()
        {
            $this->_foo();
        }

        public function routes()
        {
            $this->route('example', 'mySite');
        }

        public function mySite()
        {
            $page = [
                'title' => 'Sample title..',
                'desc' => 'Site description',
                'content' => 'Lorem ipsum dolor...'
            ];

            $this->setTemplate('index.html');
            $this->tpl->set('page', $page);
        }

        private function _foo()
        {            
            $this->tpl->set('bar', 'Why So Serious?');
        }
    }
```

I exemplet ovan, har en ny `bar` mall variabel skapats som genom att anropa `_foo()` funktionen i modulinitieraren och kan användas i temafilerna som `{$bar}`. Dessutom har `routes()` funktionen skapat `/example` subrutinen som pekar på `mySite()` funktionsanropet. Om du går till `http://example.com/example`, you will call the `mySite()` funktionen.

### Språkfilen

Modulen kan innehålla språkvariabler som kan användas i klasser och vyer. Språkfilen har ett `.ini` tillägg och finns i` lang` katalogen för modulen.
Till exempel, om du vill lägga till en språkfil som innehåller Engelska uttryck för den administrativa delen av `Example` modulen. Bör du skapa en ny fil i `inc/modules/example/lang/admin/en_english.ini` sökvägen.

Innehållet ska likna följande lista:

```
full_name           = "Firstname and surname"
email               = "E-mail"
subject             = "Subject"
message             = "Message"
send                = "Send"
send_success        = "Mail successfully sent. I will contact you soon."
send_failure        = "Unable to send a message. Probably mail() function is disabled on the server."
wrong_email         = "Submited e-mail address is incorrect."
empty_inputs        = "Fill all required fields to send a message."
antiflood           = "You have to wait a while before you will send another message."
```

Använd `$this->lang('subject')` funktionen i modulklassen och `{$lang.example.subject}` i vyerna. För en klass kan vi utelämna den andra parametern för `lang` funktionen, vilket är modulens namn.


Routing
-------

Routing är processen för att behandla en mottagen förfrågningsadress och bestämma vad som ska köras eller visas. Det är tänkt att anropa lämplig metod/funktion baserat på URL. Du måste använda routing inom offentlig `routes()` funktion.

```php
void route(string $pattern, mixed $callback)
```

Den första parametern för `route` funktionen är ett återkommande uttryck. Vissa uttryck har redan definierats:

+ `:any` — any string
+ `:int` — integers
+ `:str` — string that is a slug

Den andra parametern är ett metodnamn eller en anonym funktion som skickar vidare valfritt antal argument definierade i ett vanligt uttryck.

#### Exempel
```php
public function routes()
{
    // URL: http://example.com/blog

    // - by calling the method inside the module:
    $this->route('blog', 'importAllPosts');

    // - by calling an anonymous function:
    $this->route('blog', function() {
        $this->importAllPosts();
    });

    // URL: http://example.com/blog/2
    $this->route('blog/(:int)', function($page) {
        $this->importAllPosts($page);
    });

    // URL: http://example.com/blog/post/lorem-ipsum
    $this->route('blog/post/(:str)', function($slug) {
        $this->importPost($slug);
    });

    // URL: http://example.com/blog/post/lorem-ipsum/4
    $this->route('blog/post/(:str)/(:int)', function($slug, $page) {
        $this->importPost($slug, $page);
    });
}
```


Funktioner
-------

Moduler har speciella funktioner som underlättar åtkomst till funktionerna inuti kärnan. Detta gör att du kan förkorta anropen till `$this->core->foo->bar`.

### db

```php
void db([string $table])
```

Tillåter dig att arbeta i en databas. Detaljer beskrivs i kärnsektionen.

#### Argument
+ `table` — Databasens tabell namn *(valfritt)*

#### Example
```php
$this->db('table')->where('age', 20)->delete();
```


### draw

```php
string draw(string $file [, array $variables])
```

Returnerar en sammanställd visningskod som tidigare har använt mallens systemtaggar. Detta låter dig också definiera variabler genom att ersätta `set()` funktionen.

#### Argument
+ `file` — filnamn med en vy inuti modulen eller sökvägen till en fil utanför den
+ `variables` — en array med variabla definitioner som kan användas som taggar *(valfritt)*

#### Exempel
```php
// Sammanställning av vyn i modulen
$this->draw('form.html', ['form' => $this->formFields]);

// Sammanställning av vyn utanför modulen
$this->draw('../path/to/view.html', ['foo' => 'bar']);
```


### lang

```php
string lang(string $key [, string $module])
```

Returnerar innehållet i språk array namnet från den aktuella modulen eller indikeras av det andra argumentet.

#### Argument
+ `key` — namnet på språket array key
+ `module` — namnet på modulen från vilken du vill välja key *(valfritt)*

#### Exempel
```php
// Reference to local translation
$this->lang('foo');                 // $this->core->lang['module-name']['foo'];

// Reference to general translation
$this->lang('cancel', 'general');   // $this->core->lang['general']['cancel'];

// Reference to the translation of "pages" module
$this->lang('slug', 'pages')        // $this->core->lang['pages']['slug'];
```


### notify

```php
void notify(string $type, string $text [, mixed $args [, mixed $... ]])
```

Detta låter dig anropa meddelandet till användaren.

#### Argument
+ `type` — typ av resultat: *success* or *failure*
+ `text` — innehållet i meddelandet
+ `args` — övriga argument *(valfritt)*

#### Exempel
```php
$foo = 'Bar';
$this->notify('success', 'This is %s!', $foo); // $this->core->setNotify('success', 'This is %s!', $foo);

```


### settings

```php
mixed settings(string $module [, string $field [, string $value]])
```

Läser eller skapar värde på modulinställningarna.

#### Argument
+ `module` — modulnamn och valfritt fält separerat
+ `field` — modulens fältnamn *(valfritt)*
+ `value` — värdet till vilket modulfältet kommer att ändras *(valfritt)*

#### Exempel
```php
// Select the "desc" field from the "blog" module
$this->settings('blog.desc');    // $this->core->getSettings('blog', 'desc');

// Select the "desc" field from the "blog" module
$this->settings('blog', 'desc'); // $this->core->getSettings('blog', 'desc');

// Set the content of the "desc" field from the "blog" module
$this->settings('blog', 'desc', 'Lorem ipsum...');
```

### setTemplate

```php
void setTemplate(string $file)
```

Tillåter dig att ändra mallfilen på framsidan. Den här funktion fungerar endast i `Site` klassen.

#### Argument
+ `file` — namnet på temats fil

#### Exempel
```php
$this->setTemplate('index.html'); // $this->core->template = 'index.html';
```


Kärnan
====

Detta är kärnan/motorn i Batflat CMS, den viktigaste delen som ansvarar för alla dess grundläggande uppgifter. Kärnan innehåller många definitioner av konstanter, funktioner och metoder som du kan använda när du skriver moduler.

Konstanter
---------

Alla definitioner av konstanter beskrivs i den första delen av denna dokumentation. För att använda dem i en PHP-fil anropa bara deras namn. Konstanter är särskilt användbara när man bygger URL:er och sökvägar till filer.

#### Exempel
```php
echo MODULES.'/contact/view/form.html';

```


Funktioner
---------

Batflat CMS har flera inbyggda hjälpfunktioner som underlättar skapandet av moduler.

### domain

```php
string domain([bool $with_protocol = true])
```

Returnerar domännamnet med https eller http.

#### Argument
+ `with_protocol` — den avgör om adressen ska returneras med eller utan protokoll

#### Återgivet värde
Sträng med domännamnet.

#### Exempel
```php
echo domain(false);
// Result: example.com
```


### checkEmptyFields

```php
bool checkEmptyFields(array $keys, array $array)
```

Kontrollerar om arrayen innehåller tomma fält. Det är användbart vid validering av formulär.

#### Argument
+ `keys` — lista över arrayobjekt som funktionen måste kontrollera
+ `array` — källarray

#### Återgivet värde
Returnerar `TRUE` när minst ett fält är tomt. `FALSE` när alla fält är korrekta.

#### Exempel
```php
if(checkEmptyFields(['name', 'phone', 'email'], $_POST) {
    echo 'Fill in all fields!';
}
```


### currentURL

```php
string currentURL([bool $query = false])
```

Återger nuvarande URL.

#### Argument
+ `query` — den avgör om adressen ska returneras med eller utan fråga

#### Exempel
```php
echo currentURL();
// Result: http://example.com/contact

echo currentURL(true);
// Result: http://example.com/contact?foo=bar
```


### createSlug

```php
string createSlug(string $text)
```
Översätter text i icke-språkliga tecken, streck till mellanslag och tar bort specialtecken. Används för att skapa snedstreck i URL:er och variabla namn i mallsystemet.

#### Argument
+ `text` — text att konvertera

#### Återgivet värde
Returnerar texten i slug-form.

#### Exempel
```php
echo createSlug('To be, or not to be, that is the question!');
// Result: to-be-or-not-to-be-that-is-the-question
```


### deleteDir

```php
bool deleteDir(string $path)
```

Städfunktion som tar bort katalogen och allt dess innehåll.

#### Argument
+ `path` — sökväg

#### Återgivet värde
Returnerar `TRUE` förlyckat eller `FALSE` för misslyckat.

#### Exempel
```php
deleteDir('foo/bar');
```


### getRedirectData
```php
mixed getRedirectData()
```

Återger data skickat till sessionen när du använder `redirect()`.

#### Återgivet värde
En array eller `null`.

#### Exempel
```php
$postData = getRedirectData();
```


### htmlspecialchars_array

```php
string htmlspecialchars_array(array $array)
```

Ersätter specialtecken från arrayer i HTML-strängar.

#### Argument
+ `array` — array som kommer att konverteras

#### Återgivet värde
Returnerar den konverterade texten.

#### Exempel
```php
$_POST = htmlspecialchars_array($_POST);
```


### isset_or

```php
mixed isset_or(mixed $var [, mixed $alternate = null ])
```

Ersätter en tom variabel med ett alternativt värde.

#### Argument
+ `var` — variabel
+ `alternate` — alternativt värde av variabeln *(valfritt)*

#### Återgivet värde
Returnerar ett alternativt värde.

#### Exempel
```php
$foo = isset_or($_GET['bar'], 'baz');
```


### parseURL
```php
mixed parseURL([ int $key = null ])
```

Tolkar skriptets nuvarande URL.

#### Argument
+ `key` — URL parameter number *(optional)*

#### Återgivet värde
En array eller dess individuella värde.

#### Exempel
```php
// URL: http://example.com/foo/bar/4

var_dump(parseURL())
// Result:
// array(3) {
//   [0] =>
//   string(3) "foo"
//   [1] =>
//   string(3) "bar"
//   [2] =>
//   int(4)
// }

echo parseURL(2);
// Result: "bar"
```


### redirect

```php
void redirect(string $url [, array $data = [] ])
```

Omdirigera till den angivna URL:en. Det låter dig spara data från en array till en session. Det är användbart att lagra och spara data från formulär.

#### Argument
+ `url` — adress att omdirigera
+ `data` — en array som kommer att skickas till sessionen *(valfritt)*

#### Exempel
```php
redirect('http://www.example.com/');

// Save the array to session:
redirect('http://www.example.com/', $_POST);
```


### url
```php
string url([ mixed $data = null ])
```

Skapar ett absolut URL. Administratörspanelen lägger automatiskt till ett symbol.

#### Argument
+ `data` — sträng eller array

#### Återgivet värde
Absolut URL.

#### Exempel
```php
echo url();
// Result: http://example.com

echo url('foo/bar')
// Result: http://example.com/foo/bar

echo url('admin/foo/bar');
// Result: http://example.com/admin/foo/bar?t=[token]

echo url(['admin', 'foo', 'bar']);
// Result: http://example.com/admin/foo/bar?t=[token]
```


Funktioner
-------

Det finns flera viktiga funktioner som påskyndar processen för att skapa ny systemfunktionalitet.

### addCSS

```php
void addCSS(string $path)
```

Importerar CSS-filen i tema-huvudet.

#### Argument
+ `path` — URL till filen

#### Exempel
```php
$this->core->addCSS('http://example.com/style.css');
// Result: <link rel="stylesheet" href="http://example.com/style.css" />
```


### addJS

```php
void addJS(string $path [, string $location = 'header'])
```

Omporterar JS-filen i header eller footer på temat.

#### Argument
+ `path` — URL till filen
+ `location` — *header* eller *footer* *(valfritt)*

#### Exempel
```php
$this->core->addJS('http://example.com/script.js');
// Result: <script src="http://example.com/script.js"></script>
```


### append

```php
void append(string $string, string $location)
```

Lägger till en sträng i header eller footer.

#### Argument
+ `string` — teckensträng
+ `location` — *header* eller *footer*

#### Exempel
```php
$this->core->append('<meta name="author" content="Bruce Wayne">', 'header');
```


### getModuleInfo

```php
array getModuleInfo(string $dir)
```

Returnerar modulinformation. Den här funktionen fungerar endast i `Admin` klassen.

#### Argument
+ `name` — modulens katalognamn

#### Återgivet värde
Array med information.

#### Exempel
```php
$foo = $this->core->getModuleInfo('contact');
```


### getSettings

```php
mixed getSettings([string $module = 'settings', string $field = null])
```

Hämtar värdet på modulinställningarna. Som standard är detta de huvudsakliga Batflat-inställningarna.

#### Argument
+ `module` — modulnamn *(valfritt)*
+ `field` — fält med definition av inställning *(valfritt)*

#### Återgivet värde
Array eller sträng.

#### Exempel
```php
echo $this->core->getSettings('blog', 'title');
```


### getUserInfo

```php
string getUserInfo(string $field [, int $id ])
```

Returnerar information om den inloggade användaren eller användaren med angivet ID. Den här funktionen fungerar endast i `Admin` klassen.

#### Argument
+ `field` — fältnamn i databasen
+ `id` — ID nummer *(valfritt)*

#### Återgivet värde
Strängen för det valda fältet.

#### Exempel
```php
// The currently logged in user
$foo = $this->core->getUserInfo('username');

// User with given ID
$foo = $this->core->getUserInfo('username', 1);
```


### setNotify

```php
void setNotify(string $type, string $text [, mixed $args [, mixed $... ]])
```

Genererar notifiering.

#### Argument
+ `type` — typ av notifiering: *success* eller *failure*
+ `text` — innehåll i notofieringen
+ `args` — övriga argument *(valfritt)*

#### Exempel
```php
$foo = 'Bar';
$this->core->setNotify('success', 'This is %s!', $foo);
// Result: "This is Bar!"
```


Databasen
--------

Databasen som används i Batflat CMS är SQLite version 3. För dess användning använder Batflat CMS en enkel klass som gör det enkelt att skapa frågor. Du behöver inte känna till SQL för att kunna använda den.

Dessutom rekommenderar vi [phpLiteAdmin](https://github.com/sruupl/batflat-pla).Ett verktyg för databashantering. Detta är ett PHP-skript med en fil som liknar *phpMyAdmin*, där du kan administrera Batflat tabeller. Detta gör att du kan bekanta dig med befintliga tabeller.
Databasfilen finns i `inc/data/database.sdb`.


### SELECT

Välj flera poster:

```php
// JSON
$rows = $this->core->db('table')->toJson();

// Array
$rows = $this->core->db('table')->select('foo')->select('bar')->toArray();

// Object
$rows = $this->core->db('table')->select(['foo', 'b' => 'bar'])->toObject();
```

Välj enbart en post:
```php
// JSON
$row = $this->core->db('table')->oneJson();

// Array
$row = $this->core->db('table')->select('foo')->select('bar')->oneArray();

// Object
$row = $this->core->db('table')->select(['foo', 'b' => 'bar'])->oneObject();
```


### WHERE

Välj en post med det angivna numret i `id` kolumen:

```php
$row = $this->core->db('table')->oneArray(1);
// or
$row = $this->core->db('table')->oneArray('id', 1);
// or
$row = $this->core->db('table')->where(1)->oneArray();
// or
$row = $this->core->db('table')->where('id', 1)->oneArray();
```

Avacerade förhållanden:
```php
// Fetch rows whose column value 'foo' is GREATER than 4
$rows = $this->core->db('table')->where('foo', '>', 4)->toArray();

// Fetch rows whose column value 'foo' is GREATER than 4 and LOWER than 8
$rows = $this->core->db('table')->where('foo', '>', 4)->where('foo', '<', 8)->toArray();
```

OR WHERE:
```php
// Fetch rows whose column value 'foo' is EQUAL 4 or 8
$rows = $this->core->db('table')->where('foo', '=', 4)->orWhere('foo', '=', 8)->toArray();
```

WHERE LIKE:
```php
// Fetch rows whose column 'foo' CONTAINS the string 'bar' OR 'bases'
$rows = $this->core->db('table')->like('foo', '%bar%')->orLike('foo', '%baz%')->toArray();
```

WHERE NOT LIKE:
```php
// Fetch rows whose column 'foo' DOES NOT CONTAIN the string 'bar' OR 'baz'
$rows = $this->core->db('table')->notLike('foo', '%bar%')->orNotLike('foo', '%baz%')->toArray();
```

WHERE IN:
```php
// Fetch rows whose column value 'foo' CONTAINS in array [1,2,3] OR [7,8,9]
$rows = $this->core->db('table')->in('foo', [1,2,3])->orIn('foo', [7,8,9])->toArray();
```

WHERE NOT IN:
```php
// Fetch rows whose column value 'foo' DOES NOT CONTAIN in array [1,2,3] OR [7,8,9]
$rows = $this->core->db('table')->notIn('foo', [1,2,3])->orNotIn('foo', [7,8,9])->toArray();
```

Gruppering av förhållanden:
```php
// Fetch rows those column value 'foo' is 1 or 2 AND status is 1
$rows = $this->core->db('table')->where(function($st) {
            $st->where('foo', 1)->orWhere('foo', 2);
        })->where('status', 1)->toArray();
```

Tillåtna jämförelseoperatörer: `=`, `>`, `<`, `>=`, `<=`, `<>`, `!=`. 


### JOIN

INNER JOIN:
```php
$rows = $this->core->db('table')->join('foo', 'foo.table_id = table.id')->toJson();
```

LEFT JOIN:
```php
$rows = $this->core->db('table')->leftJoin('foo', 'foo.table_id = table.id')->toJson();
```


### HAVING

```php
$rows = $this->core->db('table')->having('COUNT(*)', '>', 5)->toArray();
```

OR HAVING:
```php
$rows = $this->core->db('table')->orHaving('COUNT(*)', '>', 5)->toArray();
```


### INSERT

`save` funktionen kan lägga till en ny post i tabellen eller uppdatera en befintlig när den har ett villkor. När du lägger till en ny post kommer identitetsnumret att returneras.

```php
// Add a new record
$id = $this->core->db('table')->save(['name' => 'James Gordon', 'city' => 'Gotham']);
// Return value: ID number of new record

// Update an existing record
$this->core->db('table')->where('age', 50)->save(['name' => 'James Gordon', 'city' => 'Gotham']);
// Return value: TRUE on success or FALSE on failure
```


### UPDATE

Uppdatering av poster kommer att generera `TRUE` om de lyckas. Annars `FALSE`.

```php
// Changing one column
$this->core->db('table')->where('city', 'Gotham')->update('name', 'Joker');

// Changing multiple columns
$this->core->db('table')->where('city', 'Gotham')->update(['name' => 'Joker', 'type' => 'Villain']);
```


### SET

```php
$this->core->db('table')->where('age', 65)->set('age', 70)->set('name', 'Alfred Pennyworth')->update();
```


### DELETE

Framgångsrik radering av poster returnerar deras nummer.

```php
// Delete record with `id` equal to 1
$this->core->db('table')->delete(1);

// Deletion of record with condition
$this->core->db('table')->where('age', 20)->delete();
```


### ORDER BY

Stigande ordning:
```php
$this->core->db('table')->asc('created_at')->toJson();
```

Fallande ordning:
```php
$this->core->db('table')->desc('created_at')->toJson();
```

Kombinera ordning:
```php
$this->core->db('table')->desc('created_at')->asc('id')->toJson();
```


### GROUP BY

```php
$this->core->db('table')->group('city')->toArray();
```


### OFFSET, LIMIT

```php
// Fetch 5 records starting at tenth
$this->core->db('table')->offset(10)->limit(5)->toJson();
```


### PDO

Inte alla frågor kan skapas med ovanstående funktioner *(exempelvis skapa eller tabort en tabell)*, så du kan också skriva frågor med [PDO](http://php.net/manual/en/book.pdo.php):

```php
$this->core->db()->pdo()->exec("DROP TABLE `example`");
``` 


Mallsystem
---------------

Att använda mallsystemet är enkelt och baseras främst på två funktioner. En tillåter tilldelning av variabler, medan den andra returnerar den sammanställda koden. I exceptionella situationer är andra funktioner användbara.

### set

```php
void set(string $name, mixed $value)
```

Tilldelar ett värde eller en funktion till en variabel som kan användas i vyer.

#### Argument
+ `name` — variabelns namn
+ `value` — variabelns värde eller anonym funktion

#### Exempel
```php
// Assignment of the array
$foo = ['bar', 'baz', 'qux'];
$this->tpl->set('foo', $foo);

// Assign an anonymous function
$this->tpl->set('bar', function() {
   return ['baz' => 'qux']; 
})
```


### draw

```php
string draw(string $file)
```

Returnerar en sammanställd visningskod som tidigare har använt mallens systemtaggar.

#### Argument
+ `file` — filens sökväg

#### Återgivet värde
En sträng, dvs en sammanställd vy.

#### Exempel
```php
$this->tpl->draw(MODULES.'/galleries/view/admin/manage.html');
```


### noParse

```php
string noParse(string $text)
```

Skyddar mot att sammanställa mallens systemtaggar.

#### Argument
+ `text` — sträng som ska lämnas oförändrad

#### Exempel
```php
$this->tpl->noParse('Place this tag in website template: {$contact.form}');
```


### noParse_array

```php
array noParse_array(array $array)
```

Skyddar mot att sammanställa mallens systemtaggar i en array.

#### Argument
+ `array` — array som ska lämnas oförändrad

#### Exempel
```php
$this->tpl->noParse_array(['{$no}', '{$changes}']);
```

Språk
---------

Alla språkfiler finns i `lang` kataloger i respektive modul och i katalogen `inc/lang`.
I den sista katalogen finns mappar som motsvarar språknamnen i följande format: `en_english`. Den första delen är förkortningen för språket och den andra är det fulla namnet.
I katalogen finns filen `general.ini`, som innehåller allmänna språkvariabler för systemet.
Efter att ha skapat en ny språkmapp, Batflat CMS upptäcker automatiskt det tillagda språket och gör det möjligt att välja det i adminpanelen. Observera att proceduren för att skapa ett nytt språk ska upprepas för varje modul.
