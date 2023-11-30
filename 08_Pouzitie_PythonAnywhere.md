>## Uloženie nášho blogu na PythonAnywhere

Uloženie nášho blogu na PythonAnywhere sa skladá z niekoľkých častí. Tou prvou je vytvorenie účtu na PythonAnywhere, ktoré si urobíme nasledovne.

### Vytvorenie PythonAnywhere účtu

PythonAnywhere je služba, ktorá ti umožní nechať bežať svoj kód v Pythone na serveroch v "cloude". Použijeme ju na uverejnenie našej stránky na internete.

Náš blog uverejníme na PythonAnywhere. Vytvor si "Beginner" účet na PythonAnywhere (verzia zdarma stačí, nepotrebuješ kreditnú kartu). Toto je stránka PythonAnywhere, kde sa vytvára účet, s tlačidlom pre vytvorenie "Beginner" účtu zdarma [www.pythonanywhere.com](http://www.pythonanywhere.com)

**Poznámka** Pri výbere používateľského mena mysli na to, že URL tvojho blogu bude v tvare **tvojeuzivatelskemeno.pythonanywhere.com** (napr. PriezviskoM.pythonanywhwre.com), takže si vyber prezývku alebo názov, o čom tvoj blog je. Taktiež si určite zapamätaj svoje heslo (pridaj si ho do svojho správcu hesiel, ak nejaký používaš).

### Vytvorenie PythonAnywhere API tokenu

So skratkou API sa budeme pomerne často stretávať takže je na mieste aby sme si o nej niečo povedali. [**API**](https://www.turing.com/kb/7-examples-of-APIs) je **A**pplication **P**rogramming **I**nterface a  jednoducho je to nejaký softvér, ktorý posiela informácie tam a späť medzi webovou stránkou alebo aplikáciou a používateľom.

V našom prípade API na PythonEnywhere musíme spraviť len raz. Keď si vytvoríš PythonAnywhere účet, hodí ťa to na tvoju nástenku. Nájdi odkaz na svoj účet (Account) vpravo hore:

![](./obrazky/eshop06.png)

následne vyber záložku "API token" a stlač tlačítko "Create new API token".

Choď naspäť do hlavného PythonAnywhere dashboardu kliknutím na logo a vyber možnosť začať "Bash" konzolu -- to je PythonAnywhere verzia príkazového riadku, presne ako na tvojom počítači v terminály.

![](./obrazky/eshop07.png)

**Poznámka** PythonAnywhere je založený na Linuxe (jeden z ďalších dôvodov prečo používať v terminály Git Bash), takže ak si vo Windowse, konzola bude vyzerať trochu inak ako v tvojom počítači. 

Nasadenie webovej aplikácie na PythonAnywhere zahŕňa stiahnutie kódu z GitHubu a nakonfigurovanie PythonAnywhere, aby ho rozpoznal a začal ho poskytovať ako webovú aplikáciu. Sú manuálne spôsoby, ako to urobiť, ale PythonAnywhere poskytuje pomocný nástroj, ktorý všetko urobí za teba. Najprv ho nainštalujme cez túto konzolu do tvojho účtu:
~~~
$ pip3.8 install --user pythonanywhere
~~~
Malo by to zobraziť niečo ako **Collecting pythonanywhere** a nakoniec skončiť riadkom, ktorý hovorí **Successfully installed (...) pythonanywhere- (...)**.

Teraz spustíme pomocníka, ktorý automaticky nakonfiguruje našu aplikáciu z GitHubu. Zadaj nasledujúci príkaz do konzoly na PythonAnywhere (nezabudni použiť užívateľské meno z GitHubu namiesto <your-github-username>, aby sa URL zhodovala s klonovacou URL z GitHubu):
~~~
$ pa_autoconfigure_django.py --python=3.8 https://github.com/<your-github-username>/my-first-blog.git
~~~

Keď budeš sledovať, ako to beží, uvidíš, čo to robí:
* Sťahuje tvoj kód z GitHubu
* Vytvára virtualenv na PythonAnywhere, presne ako si ho ty vytvoril na tvojom počítači
* Aktualizuje tvoj súbor settings so zopár nastaveniami špecifickými pre nasadenie
* Pripravuje databázu na PythonAnywhere pomocou príkazu manage.py migrate
* Nastavuje tvoje statické súbory (o tých sa dozvieme viac neskôr)
* A nastavuje PythonAnywhere, aby sprístupnil tvoju stránku cez svoju API

Na PythonAnywhere sú všetky tieto kroky zautomatizované, ale sú to presne **tie isté kroky, aké by si musel spraviť u hocijakého iného poskytovateľa serverov**.

Pokiaľ by si chcel prejisť všetky kroky prípravy PythonAnywhere manuálne, postup nájdeš na tomto videu https://www.youtube.com/watch?v=Y4c4ickks2A 

Často po spustení git pull na našej bash konzole PythonAnywhere, dostaneme túto správu s chybou:
~~~
(tokost.pythonanywhere.com) 19:19 ~/tokost.pythonanywhere.com (main)$ git pull
Updating 5607fb5..6341aea
error: Your local changes to the following files would be overwritten by merge:
        db.sqlite3
Please commit your changes or stash them before you merge.
Aborting
~~~
Keď následne na tej istej konzole spustíme príkaz **git diff** ktorý nám ukaźe odlišnosti medzi GitHub-om a tokost.PythonAnywhere dostaneme nasledovný výpis:
~~~
(tokost.pythonanywhere.com) 19:54 ~/tokost.pythonanywhere.com (main)$ git diff
diff --git a/db.sqlite3 b/db.sqlite3
index 99df703..878e90f 100644
Binary files a/db.sqlite3 and b/db.sqlite3 differ
diff --git a/mysite/settings.py b/mysite/settings.py
index 87bf63b..3aa8e99 100644
--- a/mysite/settings.py
+++ b/mysite/settings.py
@@ -125,3 +125,6 @@ STATIC_ROOT = BASE_DIR / 'static'
 # https://docs.djangoproject.com/en/3.2/ref/settings/#default-auto-field
 
 DEFAULT_AUTO_FIELD = 'django.db.models.BigAutoField'
+
+MEDIA_URL = '/media/'
+MEDIA_ROOT = Path(BASE_DIR / 'media')
\ No newline at end of file
~~~
Git Nám takto hovorí, že sme v settings.py do PythonAnywhere pridali nejaké veci. To znamená, že **ak stiahneme zmeny z githubu, prepíšu sa**. Takže máte dve možnosti:

1./ Ak je kód, ktorý máme **na githube je to čo chceme**, a chceme **zahodiť zmeny na PythonAnywhere**, môžeme gitu na PythonAnywhere povedať, aby to urobil pomocou tohto príkazu:
~~~
$ git reset --hard
~~~
To nás zanechá v stave, keď tieto zmeny z PythonAnywhere už nebudú, takže pomocou **git pull** na PythonAnywhere môžeme stiahnuť zmeny z githubu ktoré budú totožné a bez chýb. 

2./ Ak je ale **požadovaný kód kombináciou zmien**, ktoré ste vykonali v PythonAnywhere, so zmenami, ktoré sú na githube, môžete zmeny potvrdiť v PythonAnywhere a potom stiahnuť zmeny z githubu. Príkaz na vykonanie zmien by bol niečo ako
~~~
$ git commit -am"Updated MEDIA_URL, MEDIA_ROOT and STATIC_ROOT"
~~~
kde -am je kombináciou príkazu git add . a -m pre správu z commitu ktorá sa nachádza medzi uvodzovkami. A teraz zase môžeme urobiť **git pull**. Pretože v tomto prípade budeme spájať zmeny z dvoch rôznych vetiev vášho kódu (tú na PythonAnywhere a tú na github), pravdepodobne vám ponúkne **editor vim** ktorý používa táto konzola a ktorý sa ohlási >. Tu sa od Vás očakáva komentár ohľadom zlúčenia ale ak nie ste s vim-om oboznámení je trochu ťažké ho použiť. Komentár, ktorý on sám navrhne, však bude v poriadku a tak všetko, čo musíte urobiť, je uložiť súbor a ukončiť vim. Na to stlačte niekoľkokrát kláves escape, potom napíšte „:wq“ a potom klávesu return na odoslanie k spracovaniu tohoto príkazu.

Ak všetko v 1./ alebo v 2./ urobíme, budeme musieť **presunúť zmeny z PythonAnywhere na github** pomocou príkazu:
~~~
git push
~~~
...a na Váš vlastný stroj ich z GitHub-u do local repository dostanete pomocou príkazu:
~~~
git pull
~~~
![](./obrazky/eshop34.png)


**Dôležitá vec**, ktorú si treba teraz všimnúť, je, že tvoja databáza na PythonAnywhere je vlastne úplne iná a oddelená od databázy na tvojom PC - to znamená, že budeš mať iné príspevky a administrátorský účet. Preto musíme najprv inicializovať admin účet pomocou **createsuperuser**, presne ako sme to spravili na svojom vlastnom počítači. PythonAnywhere pre teba automaticky vytvoril virtualenv, takže ti stačí spustiť:
~~~
(PriezviskoM.pythonanywhere.com) $ python manage.py createsuperuser
~~~
Zadaj detaily pre admin užívateľa. Najlepšie je použiť tie isté, ako používaš na vlastnom počítači, a predísť tak prípadnému zmäteniu neskôr, ibaže by si chcel spraviť heslo na PythonAnywhere o niečo bezpečnejšie.

A teraz, ak chceš, sa môžeš pozrieť na svoj kód na PythonAnywhere pomocou príkazu ls:
~~~
(priezviskom.pythonanywhere.com) $ ls
blog  db.sqlite3  manage.py  mysite requirements.txt static
(tokost.pythonanywhere.com) $ ls blog/
__init__.py  __pycache__  admin.py  apps.py  migrations  models.py
tests.py  views.py
~~~
Môžeš tiež ísť do záložky "Files" a pohybovať sa pomocou zabudovaného PythonAnywhere prehliadača súborov. (Na stránke Console sa vieš dostať ku zvyšku stránok na PythonAnywhere pomocou tlačítka v menu v pravom hornom okraji. Keď už si na jednej z týchto stránok, linky na ostatné nájdeš hore.)

## Si online!
a tvoja stránka by teraz mala byť online na internete! Preklikaj sa cez stránku "Web" na PythonAnywhere, kým sa dostaneš k odkazu na ňu. Môžeš ho poslať, komu len chceš.

**Poznámka** Toto je začiatočnícky tutoriál a pre nasadenie stránky sme si niektoré veci zjednodušili, čo nie je ideálne z pohľadu bezpečnosti. Ak sa rozhodneš na tomto projekte ďalej stavať, mal by si si prejsť [Django deployment checklist](https://docs.djangoproject.com/en/3.2/howto/deployment/checklist/) pre zopár tipov, ako svoju stránku urobiť bezpečnejšiu.

### Odstraňovanie chýb - typy pre debugovanie kódu

Ak uvídíš chybu počas behu skriptu **pa_autoconfigure_django.py**, toto sú niektoré z častých dôvodov:

* Zabudol si si vytvoriť PythonAnywhere API token.
* Urobil si chybu vo svojej githubovej URL.
* Ak sa ti zobrazí chybové hlásenie "Could not find your settings.py", pravdepodobne je to spôsobené tým, že sa ti nepodarilo pridať všetky súbory do Gitu a/alebo sa ich nepodarilo úspešne pridať na GitHub. Znovu sa pozri na odstavec o Gite vyššie
* Ak si už mal účet na PythonAnywhere a mal si problém s collectstatic, pravdepodobne máš účet so staršou verziou SQLite (napr. 3.8.2). V takom prípade si vytvor nový účet a skús príkazy v sekcii PythonAnywhere vyššie.
* Ak pri pokuse navštíviť svoju stránku uvidíš chybu, prvým miestom, kde hľadať problém, je **error log**. Odkaz naň nájdeš na PythonAnywhere v [záložke Web](https://www.pythonanywhere.com/web_app_setup/). Pozri, či tam nie sú nejaké chybové hlášky - tie najnovšie sú naspodku.

Môžeš skúsiť aj [všeobecné debugovacie tipy na help stránke PythonAnywhere](http://help.pythonanywhere.com/pages/DebuggingImportError).

### Pozri si svoju stránku !

Hlavná stránka tvojej aplikácie by ťa mala vítať nápisom "It worked!", tak ako na tvojom počítači. Skús pridať /admin/ na koniec adresy URL, a budeš presmerovaný na stránky administrácie. Prihlás sa svojím menom a heslom a uvidíš, že môžeš na server pridať nové príspevky. Nezabudni, že príspevky z tvojej lokálnej testovacej databázy sme neposlali na tvoj online blog.

Keď vytvoríš niekoľko príspevkov, môžeš sa vrátiť do tvojho lokálneho prostredia (nie PythonAnywhere). Odteraz by si na zmenách mal pracovať vo svojom lokálnom prostredí. To je **štandardný pracovný postup** pri vývoji webových aplikácií - urobíš zmeny lokálne, dáš tieto zmeny na GitHub a stiahneš zmeny na svoj webový server. Vďaka tomu môžeš pracovať a experimentovať bez toho, aby si niečo pokazila na svojej online stránke. 

TAKTO SI PRIPRAVENÝ používať webový server pre tvoju webovú aplikáciu ktorú budeš napr. v rámci odbornej maturitnej práci vytvárať.

Nasadenie serveru je jedna z najzradnejších častí vývoja web stránok a často zaberie ľuďom aj niekoľko dní, kým to celé spojazdnia. Ale ty už máš svoju stránku online teraz, na skutočnom internete!

Pre použužívanie bezplatnej verzie pythonanywhare.com je vyčlenený pamäťový priestor 512MB. Naša aplikácia v pôvodnej verzii zaberá priestor 469 MB a naráža na kapacitný problém. Kontrolu zaberaného priestoru na pythonanywhare.com zistíme príkazom:
~~~
du -sh /home/tokost
~~~
Riešenie tohoto problému spočíva v nasledovnom:
    a.) vyčistiť nepotrebné súbory alebo priečinky, aby ste uvoľnili miesto
    b.) Odstráňte dočasné súbory, staré protokoly alebo nepoužívané prostriedky, aby ste znížili využitie disku
    c.) vyhnete zbytočným balíkom alebo obmedzíte rozsah nainštalovaných balíkov. Skontrolujte requirements.txtsúbor a odstráňte všetky balíky, ktoré nie sú pre váš projekt kľúčové
    d.) ako dočasné riešenie môžete skúsiť nainštalovať menej balíkov naraz alebo použiť menšie verzie požadovaných balíkov
    e.) ak problém pretrváva aj napriek týmto krokom, zvážte oslovenie podpory PythonAnywhere pre ďalšiu pomoc týkajúcu sa chyby prekročenia diskovej kvóty. Môžu vám poskytnúť konkrétne pokyny alebo upraviť limity vášho účtu na vyriešenie problému.
    
Príkaz na to aby sme zistili zoznam nainštalovaných balíkov v našom virtuálnom prostredí použijeme vo VS-Code príkaz:
~~~
pip freeze > installed_packages.txt
~~~
Potom porovnáme obsah tohoto súboru s requirements.txt. Iná, lepšia metóda je založená na *pipdeptree* ktorý nainštalujeme vo VS-Code príkazom :
~~~
pip install pipdeptree
~~~
Pomocou neho vygenerujeme strom závislostí pomocou príkazu :
~~~
$ python -m pipdeptree
~~~
resp.
~~~
python pipdeptree --warn silence --freeze | grep -v "@" > installed_dependencies.txt
~~~
ktorý porovnáme s obsahom requirements.txt