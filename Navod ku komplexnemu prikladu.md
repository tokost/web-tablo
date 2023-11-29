[Komplexny priklad s CMS](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)

I. INŠTALÁCIA PREVZATEJ ŠABLÓNY DO NÁŠHO PROSTREDIA
1./ vytvoríme virtuálne prostre

2./ najprv prejdeme do virtualneho prostredia príkazom:
~~~
$ . myvenv/scripts/activate
~~~
3./ nainštalujeme si djangocms
~~~
$ pip install https://github.com/nephila/djangocms-installer/archive/master.zip
~~~
2./ Potom si vytvoríme pod projektom CMS-PROJECT novú aplikáciu ktorú nazvenme myproject a tento krát ju vytvoríme príkazom:
~~~
$ djangocms myproject
~~~
**Poznámka:** predtým sme použili príkaz $ djangocms -s -f -p . myproject a vidíme rozdiel v štruktúre adresárov

3./ skontrolujeme či nová aplikácia beží pomocou príkazu:
~~~
$ python manage.py runserver
~~~
a ev. ak vyhodí nejaké chyby, tak ako prvé aktualizujeme verziu Djanga napr. na 3.2 príkazom:
~~~
$ pip install Django==3.2
~~~
o tom či sa daná verzia nainštalovala sa presvedčíme príkazom:
~~~
$ python -m django --version
~~~
4./ Pokiaľ by bol problém s prihlásením pomocou admin/admin, tak si vytvoríme superusera príkazom:
~~~
$ python manage.py createsuperuser
~~~
5./ Keď si vytvoríme domácu stránku tak tu už máme predinštalované tri šablóny:

![sablony 01](file:///D:/VYUKA/spracovanie_a_vizualizacia_dat/01_Django_CMS/Django-CMS-batch-UPG-navod/obrazky/sablony01.png)

a našou snahou bude sem pridať **zákaznícké šablóny**
Za tým úcelom si z nejakého zdoja, ktorým bude v našom prípade stránka "Boostrapmade" na adrese https://bootstrapmade.com/bizland-bootstrap-business-template/ stiahneme free verziu.
![sablony 02](file:///D:/VYUKA/spracovanie_a_vizualizacia_dat/01_Django_CMS/Django-CMS-batch-UPG-navod/obrazky/sablony02.png)

6./ Po stiahnutí nového adresára *BizLand* si ho rozbalíme a celý nakopírujeme do adresára static na úrovni *cms-project/myproject/static*.

7./ Z tohoto adresára následne nakopírujeme súbor **index.html** do adresára templates *cms-project/myproject/myproject/templates* a pozrieme sa na neho vo VS-Code. Táto šablóna predstavuje novú domácu stránku ktorá bola doteraz ťahaná zo súboru base.html. A sem by sme ju teda chceli preniesť !

8./ Ako prvé z base.html nakopírujeme:
~~~
{% load cms_tags menu_tags sekizai_tags %}
~~~
a prenesieme to na začiatok súboru index.html. Hneď za ním vložíme tento nový riadok, ktorý hovorí že statické súbory ako sú napr. obrázky budú ťahané z adresára *static*:
~~~
{% load static %}
~~~
9./ Všetky riadky čo majú v index.html adresár *assets* doplníme zloženými zátvorkami percentami a adresárom static a zvyšok dáme do jednoduchých uvodzoviek napr. takto v 16-om riadku
~~~
<link href="{% static 'assets/img/favicon.png' %}" rel="icon">
~~~

10./ Na 32 pozíciu v index.html vložíme z base.html riadok
~~~
{% render_block "css" %}
~~~
a na 1028 pozíciu v index.html vložíme z base.html riadok
~~~
{% render_block "js" %}
~~~
11./ V ďalšom nás čaká spustenie nového menu ktoré ktoré začneme znovu v base.html nakopírovaním riadku
~~~
{% cms_toolbar %}
~~~
a jeho prenesením na pozíciu 44 do index.html
12./ Keďže aj settings.py musí vedieť o našich nových šablónach musíme do čsti CMS_TEMPLATES na koniec pridať túto informáciu
~~~
('index.html', 'MyTemplate')
~~~
Doposial sme robili základné úkony ktoré súviseli s tým aby ste stiahnutú *BlizList* šablónu umiestnili do nášho prostredia. Teraz nastáva druhá fáza kedy kedy budeme prevzatú šablónu upravovať našim podmienkám  a predstavám.

II. ÚPRAVA PREVZATEJ ŠABLÓNY PODĽA NAŠICH POŽIADAVOK\
1./ Zmeníme názov stránky tak že zo súboru fullwidth.html vyberieme blok atribútov stránky
~~~
{% page_attribute "page_title" %}
~~~
ktorý vložíme do súboru index.html miesto <title>BizLand Bootstrap Template - Index</title> a pred neho dáme nový názov našej stránky. Nech je to *IT4 SŠŠ Trenčín -* a keď stránku refrešneme, tak sa nám hore v záložke objaví nový názov domovskej stránky.

2./ V ďalšom kroku zmeníme v súbore index.html na 64 riadku názov loga našej stránky z BizLannd na PoeticMe a pridáme atributy domácej stránky čím dostaneme takýto výraz:
~~~
 <h1 class="logo"><a href="index.html">IT4 SŠŠ Trenčín - {% page_attribute "page_title" %} <span>.</span></a></h1>
~~~
3./ Zmeníme privítanie ktoré sa nachádza na 104 riadku pri *Welcome to* a v nasledovnom riadku na text ktorý nám vyhovuje

4./ V tomto kroku ideme urobiť zmeny v hornom riadku s menu. V base.html nakoírujeme a trochu upravíme začiatok, toto:
~~~
    <ul>
        {% show_menu 0 100 100 100 %}
    </ul>
~~~
Potom to vložíme do súboru index.html od 69 riadku a riadok s 
~~~
<nav id="navbar" class="navbar">
~~~
ktorý sa nachádza pred vlozeným blokom upravíme takto:
~~~
<nav class="nav-menu d-none d-lg-block">
~~~
ostatné riadky až po </div> vymažeme čím nám z pôvodného menu zostane iba *Home page*. Ak sa pozriete na vymazané riadky ktoré reprezentujú odkazy z menu, nič nebráni tomu aby niektoré prvky ponechali a ošetrili príslušné odkazy.

5./ Aby sme na našu stránku umiestinili *Content* modul je potrebné aby sme zo súboru base.html nakopírovali blok:
~~~
    {% block content %}
       {% placeholder "content" %}
    {% endblock content %}
~~~
a umiestnili ho do súboru index.html pod riadok *</section><!-- End Hero -->* Tým sa nám rezime štruktúri daný modul zobrazí. Tento blok tiež môžeme použiť miesto na modro vyfarbovacieho modulu ktorý začína v súbore index.html na 103 riadku. Umiestnime ho tam miesto textu začínajúceho s *Voluptatum ...* a zopakujeme to aj v prípade ostatných troch fixných textov. Aby sme tieto rámco rozlíšili nakoľko môžu mať rôzne obsahy *Content* pridáme im číselné označenie od 1 do 4. 
6./ V režime štruktúry nám vznikli 4 nové rámce do ktorých môžeme vložiť plugin-y. Tak teda vložme do *Content* Google Map.

III. PRIDÁVANIE NAŠICH PLUGIN-ov

1./ Pridáme si na našu stránku mapu z maps.google.com aby sme ukázali kde sa naša škola nachádza

2./ Najprv si však musím ezistiť GPS súradnice miesta ktoré chceme zobraziť. To si môžeme zistiť na stránke *https://www.maps.ie/create-google-map/* resp. *https://www.maps.ie/coordinates.html*

Ak si vyberieme SŠŠ na Kožšníckej dostanme nasledovný script ktorý použijeme v našej aplikácii:
<div style="width: 100%"><iframe width="100%" height="600" frameborder="0" scrolling="no" marginheight="0" marginwidth="0" src="https://maps.google.com/maps?width=100%25&amp;height=600&amp;hl=en&amp;q=Ko%C5%BEu%C5%A1n%C3%ADcka%202,%20911%2005%20Tren%C4%8D%C3%ADn,%20Slovensko+(Stredn%C3%A1%20%C5%A1portov%C3%A1%20%C5%A1kola)&amp;t=&amp;z=14&amp;ie=UTF8&amp;iwloc=B&amp;output=embed"><a href="https://www.maps.ie/population/">Population mapping</a></iframe></div>

V triede použije me pre súštanie aplikácie príkaz
~~~
$ python manage.py runserver 8016
~~~
kde číslo 8016 vyjadruje port cez ktorý SW aplikácia komunikuje s HW aokolím. V terminálovej učebni č. 706 na SŠŠ posledné dvojčíslie označuje číslo terminálovej stanice (16-Murškovičová ...)

3./ Výmena textov v súbore index.html a pri každej zmene kontrola na spustenej stránke

4./ Ikony z Bootstrapu nájdeme [**tu**](https://icons.getbootstrap.com/?q=communication)

5./ Konkretizovanie odkazu na socialne siete (napr. facebook). class="Facebook" je vytvorenei triedy ktorej css .facebook sa opakovane použije všade tam kde bude v volané
~~~
<div class="facebook"> ... </div>
~~~
6./ Pri instagrame musíme ísť cez 3 bodky a Share to ...
![instagram link](instagram_link.png)


IV. NASTAL ČAS ABY SME NAŠU APLIKÁCIU DALI DO PRODUKCIE

... čo znamená že chceme aby jej aktuálny stav videli aj tí ktorí sa priamo nezúčastňujú na jej tvorbe a ktorí budú iba jej návštevníkmi. Vyžaduje si to nájisť web server u nejakého providera alebo využiť web server ktorý nám to umožní bez úhrady.

Takúto ponuku poskytuje [pythonanywhere.com](http://www.pythonanywhere.com). Podrobnejší popis ako to urobiť nájdeme v súbore [08_Pouzitie_PythonAnywhere.md](08_Pouzitie_PythonAnywhere.md)

1./ Ako prvé keď chces využiť služby *Pythonanywhere*, musíš sa na ich stránke zaregistrovať. Tento krok teraz nie je potrebné urobiť, lebo pracovné verzie nášho etabla budem na Pythoneanywhere ukladať ja a ja som tu už zaregistrovaný.

2./ URL t.j. adresa našej stránky pre webový prehliadač bude v tvare itaci.pythonanywhwre.com a je priradená uživateľskému menu t.j. v tomto prípade menu *itaci* ktoré som zadal pri registrácii.

3./ Aby sm emohli posielať údaje a kód z našej webovej aplikácie etablo na pythonanzwhere musíme pre tento účel vytvoriť tzv. aplikačné programové rozhranie **API**. To stačí vytvoriť iba raz a to takto:
    a.) vpravo hore stlačíme vo+lbu **Account**
    b.) na lište vyberieme **API Token**
    c.) stlačíme tlačítko **Create a new API token**
    d.) výberom voľby **Dashboard** sa dostaneš opäť na hlavnú stránkz pythonanywhere
    e.) kliknutím na tlačítko $ Bash ktoré sa nachádza pod textom *New console:* s adostaneš do terminálového okna pythonanywhere
    f.) nainštalujeme konfiguračný nástroj  pre nakonfigurovanie pythonanywhere pomocou príkazu *pip3.8 install --user pythonanywhere*
    g.) spustíme konfiguračný nástroj príkazom:
    *pa_autoconfigure_django.py --python=3.8 https://github.com/tokost/web-tablo-IT4SSS-TN.git*
    h.) ak sa neobjavia žiadne chyby ktoré by bolo treba riešiť webovú aplikáciu spustíš prikazom
    **aitaci.pythonanywhere.com**
    
4./ našu webovú aplikáciu spustíš prikazom
    [aitaci.pythonanywhere.com](aitaci.pythonanywhere.com)
5./ Pokiaľ máte na GitHub-e odkiaľ pythonanywhere ťahá vašu aplikáciu tak je potrebné pri konfigurácii to určiťa príkaz potom nadobudne formu:
~~~
$ pa_autoconfigure_django.py --python=3.9 https://github.com/tokost/web-tablo-IT4SSS-TN.git --branch=main --nuke
~~~


Cel

[UŽITOČNÉ PRÍKAZY Git-u](https://www.freecodecamp.org/news/10-important-git-commands-that-every-developer-should-know/)
git fetch - stiahne históriu
git log - zobrazí log file
git branch - zobrazí existujúce vetvy (branche) a tam kde je hviezdička, tak na tej sa nachádzaš (možno s -v)
git remote - zobrazí existujúce aliasy na GitHube
git merge - spojí vyriešenú úlohu z vetvy do main
git checkout - prechod z jednej vetvy na druhu
git branch -m master  main - premenovanie master na main
git checkout -f main - vratenie sa k main alebo na inu vetvu
git checkout -b <new_branch_name> - vytvorenei novej vetvy z aktívnej napr. main
$ git push --set-upstream origin develop -  (https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/git-push-new-branch-remote-github-gitlab-upstream-example)
