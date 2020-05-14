---
title: "QGIS Plugin: Implementarea serviciului Map.md API"
date: 2019-08-04T12:09:51+00:00
draft: true

featuredImage: "/images/2019/08/qgis-plugin-implementarea-serviciului-map-md-api/maxresdefault.jpg"
categories: ["Proiecte"]
tags: ["pyqgis", "pyqt5", "python", "qgis"]

---

## Introducere

În acest articol se demonstrează construirea unui Plugin pentru aplicația [QGIS 3][1], ce folosește serviciile Map.md API. Având o experiență modestă în programare, în acest articol pot fi întâlnite „biciclete inventate“ 😄. Voi fi recunoscător dacă veți oferi sfaturi cum ar fi posibil să îmbunătățesc codul scris.

Într-o bună zi, navigând pe rețeaua LinkedIn, am dat de [postarea][26] lui Roman Știrbu, CEO al companiei Simpals. În această postare, dumnealui a menționat că [a fost lansat][2] serviciul API al sitului Map.md pentru companii care au nevoie de integrare cu CRM sau alte soluții IT.

Pentru mine a fost o noutate extraordinară. Operând adesea cu date geospațiale, aveam nevoie de un serviciu similar pentru [geocodificarea][3] adreselor. Sigur că la momentul actual existau serviciile [Google Geocoding API][4] și [OpenStreetMap Nominatim][5], însă nu eram satisfăcut de rezultatele obținute ale acestor servicii.

Aflând de acest serviciu, imediat mi-a venit o idee de a-l implementa în aplicația QGIS, prin construirea unui [Plugin][6] 💡. La elaborarea acestuia, m-am inspirat din extensia deja existentă [MMQGIS][7]. Printre multiplele funcționalități ce le are, acesta dispune și de geocodificarea adreselor prin intermediul serviciilor menționate anterior.

{{< youtube _BRBshZOqd0 >}}

## Crearea șablonului extensiei QGIS (QGIS Plugin) {#crearea-sablonului-extensiei-qgis-qgis-plugin}

Pentru a crea șablonul extensiei QGIS, este nevoie de aplicația propriu-zisă [instalată][8] în calculator și de extensiile [Plugin Builder 3][9], [debugvs][10] și [Plugin Reloader][11].

### Generarea șablonului cu ajutorul extensiei Plugin Builder 3

La pornirea acestei aplicații navigăm meniul `Plugins ► Plugin Builder ► Plugin Builder`.

![Interfața aplicației QGIS 3.](/images/2019/08/qgis-plugin-implementarea-serviciului-map-md-api/1-min.png)

După afișarea dialogului, completăm forma cu [detaliile][12] referitoare la extensie.

![Dialogul extensiei QGIS Plugin Builder.](/images/2019/08/qgis-plugin-implementarea-serviciului-map-md-api/2-min.png)

Apăsând butonul next, introducem informația detailată privind extensia QGIS, șablonul căreia urmează a fi creat.

![Forma unde se introduce informația detailată privind extensia QGIS.](/images/2019/08/qgis-plugin-implementarea-serviciului-map-md-api/4.png)

În următoarea formă, alegem șablonul `Tool button with dialog`, meniul `Plugins` și numele elementului meniului, spre exemplu, `MapMD`.

![Forma unde se introduce informația privind tipul șablonului.](/images/2019/08/qgis-plugin-implementarea-serviciului-map-md-api/5-min.png)

Sărind la următorul pas, selectăm toate opțiunile, pentru a avea o funcționalitate mai vastă a extensiei.

![Forma unde se alege opțiunile extensiei.](/images/2019/08/qgis-plugin-implementarea-serviciului-map-md-api/6.png)

La următorul pas, suplinim forma privind publicarea extensiei. Pentru aceasta, eu am creat prealabil un [repository][13] pe Github și am introdus datele indicate în imaginea de mai jos. Plus la aceasta, am marcat extensia dată ca experimentală, deoarece aceasta se va afla la starea de development și va fi instabilă la început.

![Forma unde se suplinesc câmpurile privind publicarea extensiei.](/images/2019/08/qgis-plugin-implementarea-serviciului-map-md-api/7.png)

La ultimul pas, alegem mapa unde va fi generat proiectul și apăsăm butonul `Generate`.

![Forma unde se alege mapa unde va fi generat proiectul extensiei.](/images/2019/08/qgis-plugin-implementarea-serviciului-map-md-api/8-min.png)

### Note suplimentare privind procesul de generare al șablonului extensiei

**Notă**: Pentru a putea face development și debugging este necesar ca proiectul cu extensie să fie localizat aici:

```batch
%APPDATA%\QGIS\QGIS3\profiles\default\python\plugins\map_md_geocoding
```

În caz că se afișează avertizare `The resource compiler pyrccc5 was not found in your path`, omitem și primim o instrucție cu pașii ulteriori pentru a avea posibilitatea dezvolta cu succes extensia.

## Configurarea mediului de dezvoltare al extensiei

Pentru dezvoltarea extensiei, este nevoie de configurat mediul de dezvoltare. În acest scop, folosesc sistemul de operare Windows 10 și editorul [Visual Studio Code][14].

### Pregătirea mediului de dezvoltare

În mapa cu extensia dată creăm un fișier cu denumirea `start_vscode.cmd` și scrim instrucțiunile de mai jos. De fiecare dată când va fi necesar de dezvoltat extensia, vom rula acest fișier. Acest fișier setează [variabilele de mediu][15] necesare pentru dezvoltarea și debugging-ul extensiei și, ulterior, lansează editorul VS Code.

```batch
@echo off
SET OSGEO4W_ROOT=C:\OSGeo4W64
call "%OSGEO4W_ROOT%"\bin\o4w_env.bat
call "%OSGEO4W_ROOT%"\bin\qt5_env.bat
call "%OSGEO4W_ROOT%"\bin\py3_env.bat

path %PATH%;%OSGEO4W_ROOT%\apps\qgis\bin
path %PATH%;%OSGEO4W_ROOT%\apps\Qt5\bin
path %PATH%;%OSGEO4W_ROOT%\apps\Python37\Scripts
path %PATH%;C:\Program Files\7-Zip
path %PATH%;C:\Program Files\Git\cmd

set PYTHONPATH=%PYTHONPATH%;%OSGEO4W_ROOT%\apps\qgis\python\
set PYTHONPATH=%PYTHONPATH%;%OSGEO4W_ROOT%\apps\qgis\python\qgis
set PYTHONPATH=%PYTHONPATH%;%OSGEO4W_ROOT%\apps\qgis\python\qgis\PyQt5
set PYTHONPATH=%PYTHONPATH%;%OSGEO4W_ROOT%\apps\qgis\python\qgis\core

set PYTHONHOME=%OSGEO4W_ROOT%\apps\Python37

start "VS Code with PyQGIS and OsGeo4W" /B "%LOCALAPPDATA%\Programs\Microsoft VS Code\Code.exe" .
```

În VS Code, creăm un nou fișier în mapa generată a extensiei cu denumirea `requirements.txt` și introducem în el pachetele necesare pentru instalare în mediul virtual:

```requirements.txt
autopep8
pb_tool
ptvsd
sphinx_rtd_theme
```

Ulterior, instalăm aceste pachete, efectuând următoarea instrucțiune:

```batch
pip install -r requirements.txt
```

Având pachetele necesare instalate, compilăm resursele aplicației prin efectuarea instrucțiunii de mai jos. Aceasta va genera fișierul `resources.py`.

```batch
pb_tool compile
```

### Verificarea funcționalității extensiei

Pentru a verifica dacă totul funcționează bine, lansăm proiectul prin executarea instrucțiunii de mai jos. Aceasta trebuie sa compileze proiectul și să-l copie în mapa cu extensiile QGIS.

```batch
pb_tool deploy
```

După, pornim aplicația QGIS și activăm extensia noastră prin navigarea meniului `Plugins ► Manage and Install Plugins... ► Installed ► MapMD`. Ulterior, pornim extensia creată prin apăsarea butonului corespunzător de pe bara de instrumente.

![Fereastra extensiei ce conține numai două butoane.](/images/2019/08/qgis-plugin-implementarea-serviciului-map-md-api/2019-06-30_8-33-08-min.png)

## Ajustarea interfeței extensiei

Pentru a adăuga elemente în interfața extensiei, este necesar de a rula aplicația Qt Designer. Aceasta o găsiți pe următoarea cale `C:\OSGeo4W64\bin\qgis-designer.bat`.

Apoi, deschideți cu ajutorul acestei aplicații fișierul `map_md_dialog_base.ui` ce se află în mapa cu proiectul extensiei, adăugați componentele necesare și salvați fișierul, ca urmare fereastra să arate în felul următor:

![Ajustarea interfeței extensiei.](/images/2019/08/qgis-plugin-implementarea-serviciului-map-md-api/2019-06-30_8-57-17-compressor.png)

## Crearea clasei destinată procesului de geocodificare

Pentru a implementa funcționalitățile extensiei, metodele ulterioare le-am inclus într-o clasă distinctă care va fi moștenită de la clasa [QgsTask][27]. Această clasă permite ca procesul de geocodificare să fie adăugat în managerul de sarcini al aplicației QGIS, cu alte cuvinte extensia nu va bloca interfața aplicației geospațiale și procesul de geocodificare va putea fi întrerupt la cererea utilizatorul, ca rezultat afișându-se numai adresele geocodificare până la această întrerupere.

În această clasă am importat bibliotecile necesare și am declarat variabilele cu care voi opera pe parcurs, denumirea celor private începându-se cu două simboluri _underscore_ („_”). Constructorul clasei conține argumente cu valori implicite care sunt opționale.

```python
import csv
import codecs
import sqlite3
import urllib.parse
import re
import requests
import shapely

from shapely.geometry import shape

# pylint: disable=import-error
from qgis.utils import iface
from qgis.core import (QgsDataSourceUri, Qgis,
                       QgsTask, QgsMessageLog)
# pylint: enable=import-error

# Clasa moștenită de la clasa QgsTask,
# ce permite adăugarea sarcinii de geocodificare în
# managerul de sarcini al aplicației Qgis.
class MapMdUtils(QgsTask):

    def __init__(self, input_filename, output_filename="",
                 notfound_filename="", api_key="",
                 street1_index=-1, street2_index=-1,
                 house_number_index=-1, locality_index=-1):
        # Se apelează constructorul clasei QgsTask,
        # unde se introduce denumirea sarcinii și abilitatea de a
        # întrerupe procesul de geocodificare.
        super().__init__("Geocodificarea adreselor", QgsTask.CanCancel)
        self.__api_key = api_key # Cheia API
        self.__street1_index = street1_index # Indicele coloanei Strada1
        self.__street2_index = street2_index # Indicele coloanei Strada2
        self.__house_number_index = house_number_index # Indicele coloanei numărul casei
        self.__locality_index = locality_index # Indicele coloanei Localitate
        self.__input_filename = input_filename # Calea spre fișierul de intrare
        self.__output_filename = output_filename # Calea spre fișierul de ieșire
        self.__notfound_filename = notfound_filename # Calea spre fișierul cu adrese neidentificate
        self.__table_name = "point_geometry" # Denumirea tabelului SpatiaLite (SQLite)
        self.__header = [self.__quote_identifier(
            item) for item in next(self.read_csv())] # Denumirile coloanelor
        self.__not_found_count = 0 # Cantitatea adreselor neidentificate
        self.exception = None # Excepția returnată în urma geocodificării
```

## Lucrul cu fișierul de intrare și cel ce conține adrese neidentificate

### Citirea fișierului de intrare

Pentru citirea fișierului de intrare, care trebuie să fie de tip CSV cu codificarea UTF-8, eu am creat o metodă care va citi rând cu rând acest fișier, iar în caz că va surveni o excepție, aceasta va fi afișată în bara de mesaje ale aplicației geospațiale QGIS.

```python
def read_csv(self):
    """ Read CSV file. """
    try:
        with open(self.__input_filename, 'r', encoding='utf-8') as csvfile:
            # Identify CSV dialect (delimiter)
            dialect = csv.Sniffer().sniff(csvfile.read(4096))
            csvfile.seek(0)
            reader = csv.reader(csvfile, dialect)

            for row in reader:
                yield row
    except IOError:
        iface.messageBar().pushCritical(
            "Input CSV File",
            "Failure opening " + self.__input_filename)
    except UnicodeDecodeError:
        iface.messageBar().pushCritical(
            "Input CSV File",
            "Bad CSV file - Unicode decode error")
    except csv.Error:
        iface.messageBar().pushCritical(
            "Input CSV File",
            "Bad CSV file - verify that your delimiters are consistent")
```

### Calcularea numărului de rânduri ale fișierului de intrare

Pentru ca în aplicația QGIS să fie afișat procentajul de îndeplinirii a sarcinii de geocodificare, eu am creat o metodă pentru calcularea numărului de rânduri ale fișierului de intrare de tip CSV.

```python
def __count_csv_lines(self):
    """ Count CSV lines. """
    try:
        with open(self.__input_filename, 'r', encoding='utf-8') as csvfile:
            return sum(1 for row in csvfile)
    except csv.Error:
        iface.messageBar().pushCritical(
            "Input CSV File",
            "Bad CSV file - verify that your delimiters are consistent")
```

### Scrierea rândurilor cu adrese neidentificate în fișier CSV

În caz că adresele indicate în fișierul de intrare nu au fost posibil de geocodificat, extensia dată copie întregul rând ce conține adresa neidentificată în fișierul de ieșire care, la fel, reprezintă un fișier CSV cu codificarea UTF-8 și incrementează numărul de adrese neidentificate care la final de process va fi afișat în aplicația QGIS.

```python
def __write_notfound_street_to_csv(self, row):
    """ Write not found street to CSV file.
    param line: Line to be written in CSV file.
    type line: str
    """
    with open(self.__notfound_filename, mode='a', newline='') as csvfile:
        csv_writter = csv.writer(
            csvfile, delimiter=',',
            quotechar='"', quoting=csv.QUOTE_MINIMAL)
        csv_writter.writerow(row)

    self.__not_found_count += 1 # Increment pentru cantitatea adreselor neidentificate
    # Se expediază către aplicația QGIS log că adresa nu a fost identificată.
    QgsMessageLog.logMessage(
        "Nu a fost geocodificat rândul CSV: %s" % ','.join(row),
        level=Qgis.Warning)
```

## Utilizarea serviciului API Geocodificare al site-ului Map.md

Pentru a utiliza serviciul API Geocodificare al site-ului Map.md API, este nevoie de a obține cheia API 🔑.

### Obținerea cheii API

Pentru a utiliza serviciul API Geocodificare, este necesar de obținut un cod unic de identificare. Pentru aceasta, este necesar să ne conectăm la sistemul Simpals-ID, utilizând login-ul sau parola contului pentru oricare dintre proiectele companiei.

Apoi, accesăm link-ul [map.md/ro/api][28], facem click pe butonul `Obțineți codul`, completăm formularul special și salvăm codul rezultat.

Informația detailată privind obținerea cheii API o puteți găsi în [acest articol][29].

### Implementarea metodelor destinate adresării către Map.md API

Pentru a geocodifica adresele conținute în fișierul de intrare, eu am realizat 3 metode care vor răspunde de:

* căutarea și obținerea identificatorului străzii;
* obținerea datelor geospațiale ale străzii; și
* obținerea datelor geospațiale ale străzii cu numărul casei indicat.

```python
def __map_md_search_street(self, row, street):
    """ Map.md search street method.
    param row: Row list.
    type row: list of str
    param street: Street.
    type street: str
    :return: False or JSON
    :rtype: dict of str
    """
    # Se obține denumirea localității din rândul CSV.
    locality = row[self.__locality_index]

    # Se face cerere către Map.md API, pentru a obține
    # identificatorul străzii.
    r = requests.get(
        "https://map.md/api/companies/webmap/search_street?" +
        "location=%s&q=%s" % (urllib.parse.quote(locality),
                                urllib.parse.quote(street)),
        auth=(self.__api_key, ""))

    # Dacă nu a fost găsită strada sau a fost returnată o eroare,
    # rândul se scrie în fișierul cu adrese neidentificate și
    # metoda returnează False.
    if not r or not r.json():
        self.__write_notfound_street_to_csv(row)
        return False
    return r.json()

def __map_md_get_street(self, street_id, row):
    """ Map.md get street method.
    param street_id: The Map.md street id.
    type street_id: int
    param row: Row list.
    type row: list of str
    :return: False or JSON
    :rtype: dict of str
    """
    # Se obține denumirea localității din rândul CSV.
    locality = row[self.__locality_index]
    # Se face cerere către Map.md API, pentru a obține datele
    # geospațiale ale străzii.
    r = requests.get(
        "https://map.md/api/companies/webmap/get_street?" +
        "id=%s&location=%s" % (urllib.parse.quote(street_id),
                                urllib.parse.quote(locality)),
        auth=(self.__api_key, ""))

    # Dacă nu a fost găsită strada sau a fost returnată o eroare,
    # rândul se scrie în fișierul cu adrese neidentificate și
    # metoda returnează False.
    if not r or not r.json():
        self.__write_notfound_street_to_csv(row)
        return False
    return r.json()

def __map_md_search_street_with_house_number(self, row, street_id,
                                                house_number):
    """ Map.md search street with house number method.
    param row: Row list.
    type row: list of str
    param house_number: House number.
    type house_number: str
    :return: False or JSON
    :rtype: dict of str
    """
    # Se face cerere către Map.md API, pentru a obține datele
    # geospațiale ale străzii și numărului casei.
    r = requests.get(
        "https://map.md/api/companies/webmap/get_street?" +
        "id=%s&number=%s" % (urllib.parse.quote(street_id),
                                urllib.parse.quote(house_number)),
        auth=(self.__api_key, ""))

    # Dacă nu a fost găsită strada și numărul casei sau a fost
    # returnată o eroare, rândul se scrie în fișierul cu adrese
    # neidentificate și metoda returnează False.
    if not r or not r.json():
        self.__write_notfound_street_to_csv(row)
        return False
    return r.json()
```

## Utilizarea bazei de date SpatiaLite pentru stocarea adreselor geocodificate

### De ce SpatiaLite

Extensia MMQGIS, care a servit drept sursă de inspirație, salvează adresele geocodificate într-un fișier (ba chiar în mai multe 😏) cu extensia [ShapeFile][16] (*_.shp_).

Această metodă de a salva adresele geocodificate nu este favorabilă deoarece acest tip de fișier are următoarele [limite][17] și dezavantaje:

* Lungimea denumirii coloanelor nu poate depăși 10 caractere;
* Suport slab a codificării Unicode;
* Pe lângă fișierul cu extensia_*.shp_, în aceeași mapă se mai stochează și [alte fișiere cu diverse extensii][18] (_*.dbf_, _*.prj_, _*.qpj_, _*.shx_, etc.).

Operând adesea cu date geospațiale, în ultimul timp le salvam, prin intermediul aplicației QGIS, în baze de date de tip SpatiaLite, care reprezintă nu altceva, decât baze de date de tip SQLite cu date geospațiale.

Aplicația QGIS permite de a manipula aceste baze de date, salvând o mulțime de straturi ale aplicației într-un singur fișier. Super! E ceea de ce am nevoie! 🤗

Problema consta în aceea, că cu baze de date de tip SQL reușisem să operez la un nivel superficial, însă nu aveam idee cum să proiectez baze de date de tip SpatiaLite.

Navigând pe Internet am găsit următoarea [serie de articole][19] care explică clar cum funcționează această bază de date. Minunat! E timpul pentru experimente 🧪💡.

### Structura bazei de date SpatiaLite

Baza de date de tip SpatiaLite trebuie să conțină un tabel destinat pentru salvarea datelor geospațiale 🌍.

Baza de date și tabelul vor fi create la începutul geocodificării adreselor, adăugându-se coloanele tabelului în strictă corespundere cu coloanele din fișierul de intrare de tip CSV. Ulterior, se mai adaugă o coloană ce conține date geospațiale, în cazul dat, de tip _Point_ cu geoproiecția [WGS84 (4326)][20] 🌏.

La final, este necesar de creat index pentru cheia primară și pentru coloana cu date geospațiale, pentru a îmbunătăți performanța bazei de date 📈.

### Proiectarea bazei de date SpatiaLite {#proiectarea-bazei-de-date-spatialite}

Modalitatea de inițiere a bazei de date, creării tabelului pentru adrese geocodificate și a index-urilor este indicată în metoda de mai jos.

```python
def __init_spatialite_db(self):
    """ Init SpatiaLite database."""
    with sqlite3.connect(self.__output_filename) as conn:
        conn.enable_load_extension(True)
        conn.load_extension("mod_spatialite")
        conn.execute("SELECT InitSpatialMetaData(1);")
        conn.execute("""CREATE TABLE IF NOT EXISTS %s (
                        PointId INTEGER NOT NULL
                        PRIMARY KEY AUTOINCREMENT);""" % self.__table_name)
        conn.execute("""CREATE UNIQUE INDEX IF NOT EXISTS
                        idx_%s_id ON
                        %s (PointId);""" % (self.__table_name,
                                            self.__table_name))

        cur = conn.cursor()
        cur.execute("PRAGMA table_info('%s');" % self.__table_name)
        db_columns = cur.fetchall()
        db_columns = (x[1] for x in db_columns)
        db_columns = [self.__quote_identifier(x) for x in db_columns]

        for column in self.__header:
            # Se adauga coloanele care nu au existat anterior
            if column not in db_columns:
                conn.execute("""ALTER TABLE %s
                                ADD COLUMN %s TEXT""" %
                                (self.__table_name, column))

        # Se adauga coloana ce contine date geospatiale
        # cu geoproiectia WSG 84 (4326)
        if "Geometry" not in db_columns:
            conn.execute(
                """SELECT AddGeometryColumn(
                    'point_geometry', 'Geometry',
                    4326, 'POINT', 'XY');""")

            # Se adauga index geospatial
            conn.execute(
                "SELECT CreateSpatialIndex('point_geometry', 'Geometry');")
```

În fragmentul de cod ce urmează, este realizată funcționalitatea de adăugare a rândului CSV, ce a fost geocodificat cu succes, în tabelul bazei de date SpatiaLite și adăugarea coordinatelor punctului unde este localizată adresa sau intersecția.

```python
def __add_row_to_db(self, row, geometry):
    """ Add CSV row to database.
        param csv_row: CSV row.
        type csv_row: list of str
        param geometry: Point geometry.
        type geometry: str
        """
    with sqlite3.connect(self.__output_filename) as conn:
        conn.enable_load_extension(True)
        conn.load_extension("mod_spatialite")
        cur = conn.cursor()

        # Insert it.
        sql = """INSERT INTO point_geometry
                    (%s, Geometry) VALUES
                    (%s, GeomFromText('%s', 4326));""" % \
        (
            ','.join(self.__header),
            ','.join([self.__quote_identifier(item)
                      for item in row]),
            geometry
        )
        cur.execute(sql)
```

## Implementarea procesului de geocodificare propriu-zis

Extensia pe care am creato va implementa 4 tipuri de geocodificări, după cum urmează:

* Geocodificarea străzii, numărului casei și localității;
* Geocodificarea străzii și localității, când numărul casei se conține în câmpul cu stradă;
* Geocodificarea intersecțiilor (strada1, strada2, localitate);
* Geocodificarea combinată (se alege una din cele menționate mai sus, în dependență de ce câmpuri în fișierul CSV sunt suplinite).

### Implementarea metodelor de geocodificare a rândurilor CSV

În următoarele fragmente de cod, eu am implementat 2 metode ce vor geocodifica strada și numărul casei și, respectiv, intersecția a două străzi. Aceste metode vor apela la metodele create anterior care comunică cu serviciul API Geocodificare al site-ului [Map.md][21].

```python
def __geocode_street_and_house_number(self, row, street, house_number):
    """ Geocode street1 and house number.
    param row: Row list.
    type row: list of str
    param street: Street.
    type street: str
    param house_number: House number.
    type house_number: str
    :return: Return bool.
    :rtype: bool
    """
    r = self.__map_md_search_street(row, street)
    if not r:
        return False

    # Se obtine lista cu numerele caselor adresei solicitate
    buildings = r[0]['buildings']

    # Daca numarul casei nu se gaseste in lista,
    # nu se indeplineste cel de-al doilea request
    if house_number not in buildings:
        self.__write_notfound_street_to_csv(row)
        return False

    r = self.__map_md_search_street_with_house_number(
        row, r[0]['id'], house_number)
    if not r:
        return False

    geometry = "POINT(%s %s)" % (r['point']['lon'],
                                    r['point']['lat'])
    self.__add_row_to_db(row, geometry)

def __geocode_street1_and_street2(self, row, street1, street2):
    """ Geocode street1 and street2.
    param row: Row list.
    type row: list of str
    param street: Street1.
    type street: str
    param street: Street2.
    type street: str
    :return: Return bool.
    :rtype: bool
    """
    # Se cauta strada1 pentru a obtine identificatorul ei
    r1 = self.__map_md_search_street(row, street1)

    # Se cauta strada2 pentru a obtine identificatorul ei
    r2 = self.__map_md_search_street(row, street2)

    # Verificare strada1 si strada2
    if not r1 or not r2:
        return False

    # Se obtine datele despre strada1 si strada2
    r1 = self.__map_md_get_street(r1[0]['id'], row)
    r2 = self.__map_md_get_street(r2[0]['id'], row)
    if not r1 or not r2:
        return False

    r1_geo_json = r1['geo_json']
    r2_geo_json = r2['geo_json']

    s1 = shape(r1_geo_json)
    s2 = shape(r2_geo_json)

    if not s1.intersects(s2):
        self.__write_notfound_street_to_csv(row)
        return False

    geometry = s1.intersection(s2)

    # In cazul ca se identifica MultiPoint,
    # se ia primul Point in consideratie
    if isinstance(geometry,
                    shapely.geometry.multipoint.MultiPoint):
        geometry = geometry[0]

    self.__add_row_to_db(row, geometry.wkt)
```

### Implementarea metodei de rulare a procesului de geocodificare

Pentru a rula procesul de geocodificare, instrucțiunile destinate acestui scop au fost plasate în metoda [run][22], din motiv că managerul de sarcini al aplicației QGIS apelează anume această metodă când se adaugă o sarcină nouă în acesta (metoda [addTask][23]).

```python
def run(self):
    """ Geocode addresses using Map.md API.
    return: Return bool type.
    type: bool
    """
    QgsMessageLog.logMessage("Început geocodificare.", level=Qgis.Info)

    # Se initializeaza baza de date SpatiaLite
    self.__init_spatialite_db()

    pattern = r"^((?:[a-z0-9ăîșțâ]+[\., ]+)+)(\d{1,3}(?:[\/ ]?\w{1,2})?)$"

    if self.__street1_index == -1 and self.__locality_index == -1:
        self.exception = Exception(
            "Indicele câmpurilor street1 și/sau locality sunt goale!")

    # Se omite primul rand, deoarece contine denumirile coloanelor
    iter_rows = iter(self.read_csv())
    next(iter_rows)

    for index, row in enumerate(iter_rows):
        self.setProgress(index*100/self.__count_csv_lines())
        # verifică isCanceled() pentru a gestiona anularea geocodificării
        if self.isCanceled():
            return False

        if not row[self.__street1_index] and \
                not row[self.__locality_index]:
            self.__write_notfound_street_to_csv(row)

        elif self.__street2_index > -1 and \
                row[self.__street2_index]:

            QgsMessageLog.\
                logMessage("Street1 + Street2 + Locality",
                            level=Qgis.Info)

            geocode_street1_and_street2 = \
                self.__geocode_street1_and_street2(
                    row,
                    row[self.__street1_index],
                    row[self.__street2_index])

            if not geocode_street1_and_street2:
                continue

        elif self.__house_number_index > -1 and \
                row[self.__house_number_index]:

            QgsMessageLog.\
                logMessage("Street1 + House number + Locality",
                            level=Qgis.Info)

            geocode_street_and_house_number = \
                self.__geocode_street_and_house_number(
                    row,
                    row[self.__street1_index],
                    row[self.__house_number_index])
            if not geocode_street_and_house_number:
                continue

        else:
            QgsMessageLog.logMessage("Street1 + Locality",
                                        level=Qgis.Info)
            match = re.search(pattern,
                                row[self.__street1_index],
                                re.IGNORECASE | re.UNICODE)
            if not match:
                self.__write_notfound_street_to_csv(row)
                continue

            street = match.group(1).replace(',', '').strip()
            house_number = match.group(2).strip()

            geocode_street_and_house_number = \
                self.__geocode_street_and_house_number(
                    row, street, house_number)
            if not geocode_street_and_house_number:
                continue

    return True
```

### Implementarea metodei de finisare a procesului de geocodificare

Clasa QgsTask mai implementează o metodă denumită [finished][24], care va fi apelată după finalizarea sarcinii (fie prin finalizare cu succes, fie prin anulare la cererea utilizatorului). Argumentul _result_ reflectă dacă sarcina a fost finalizată cu succes sau nu. Această metodă este întotdeauna apelată de la thread-ul principal, deci se permite de a adăuga stratul cu puncte geocodificate în aplicație.

```python
def finished(self, result):
    """
    This function is automatically called when the task has
    completed (successfully or not).
    You implement finished() to do whatever follow-up stuff
    should happen after the task is complete.
    finished is always called from the main thread, so it's safe
    to do GUI operations and raise Python exceptions here.
    result is the return value from self.run.
    """
    if result:
        # Se adauga stratul in QGIS
        self.__add_spatialite_layer_to_qgis()

        csv_row_count = self.__count_csv_lines()

        QgsMessageLog.logMessage(
            "Sfârșit geocodificare." +
            "Au fost geocodificate %i din %i adrese." %
            (csv_row_count-self.__not_found_count, csv_row_count),
            level=Qgis.Success)
    elif self.exception is None:
        # Se adauga stratul in QGIS
        self.__add_spatialite_layer_to_qgis()

        QgsMessageLog.logMessage(
            "Geocodificarea a fost anulată. " +
            "Se afișează rezultatele obținute până la moment.",
            level=Qgis.Warning)
    else:
        QgsMessageLog.logMessage(
            "Procesul de geocodificare a returnat o excepție:\n%s" %
            str(self.exception), level=Qgis.Critical)
        raise self.exception
```

## Implementarea logicii pentru interfața dialogul de geocodificare

Interfața dialogului extensiei creată anterior este lipsită de un oarecare funcțional, cu alte cuvinte nu implementează nici-o logică. E timpul să reparăm acest lucru 🤗.

### Importarea bibliotecilor necesare

În primul rând, ne asigurăm că am importat tot de ce vom avea nevoie pe parcurs:

```python
import os
from PyQt5 import uic
from PyQt5 import QtWidgets
from PyQt5.QtWidgets import QFileDialog, QDialogButtonBox
from .map_md_utils import MapMdUtils
```

### Asigurarea conexiunii evenimentelor cu metodele respective

Ulterior, la constructorul clasei MapMdDialog, implementăm conexiunile evenimentelor de modificare a textului, de apăsare click pe butoane cu metodele corespunzătoare.

```python
self.button_box.button(QDialogButtonBox.Ok).setEnabled(False)

# Conectăm evenimentele de modificare a textului cu metoda ce va
# activa butonul OK.
self.input_filename.textChanged.connect(self.is_ready_to_geocode)
self.output_spatialite_filename.textChanged.connect(self.is_ready_to_geocode)
self.output_notfound_filename.textChanged.connect(self.is_ready_to_geocode)
self.api_key.textChanged.connect(self.is_ready_to_geocode)
self.street_field1.currentTextChanged.connect(self.is_ready_to_geocode)
self.locality_field.currentTextChanged.connect(self.is_ready_to_geocode)

# Conectarea evenimentelor click pe butoane cu metodele ce vor
# afișa dialoguri de deschidere/salvare a fișierelor.
self.browse_infile.clicked.connect(self.browse_infile_dialog)
self.browse_spatialite.clicked.connect(self.browse_spatialite_file_dialog)
self.browse_notfound.clicked.connect(self.browse_notfound_file_dialog)
```

### Implementarea metodei de alegere a fișierului de intrare

În fragmentul de cod ce urmează, este implementat funcționalul de alegere a fișierului de intrare de tip CSV UTF-8. Pentru aceasta, după apăsarea click pe primul buton _Browse&#8230;_, se va afișa dialogul de deschidere a fișierului.

După alegerea fișierului de intrare, în câmpul fișierului de ieșire se va salva calea absolută a fișierului de intrare, aceeași denumire ca și fișierul de intrare + extensia _.db_, iar în câmpul fișierului cu adrese neidentificate – calea absolută a fișierului de intrare cu denumirea fișierului _notfound.csv_.

După aceasta, toate elementele combobox vor fi suplinite cu denumirile coloanelor fișierului de intrare, pentru a permite utilizatorului de a asocia câmpurile extensiei cu coloanele din fișierul de intrare.

```python
def browse_infile_dialog(self):
    """ Browse input CSV file dialog """
    input_file_name, _ = QFileDialog.getOpenFileName(
        None, "Address CSV Input File",
        self.input_filename.displayText(),
        "CSV File (*.csv *.txt)")
    if input_file_name and len(input_file_name) > 4:
        abspath = os.path.abspath(input_file_name)
        # Se seteaza calea fisierului de intrare
        self.input_filename.setText(abspath)
        # Se seteaza calea spre fisierul de iesire (SpatiaLite)
        # Se inlocuieste extensia '.csv' cu '.db'
        self.output_spatialite_filename.setText(
            os.path.join(
                os.path.dirname(abspath), os.path.splitext(
                    os.path.basename(abspath))[0] + '.db'))
        # Se seteaza calea spre fisierul CSV cu adrese neidentificate
        self.output_notfound_filename.setText(
            os.path.join(os.path.dirname(abspath), 'notfound.csv'))

        combolist = [self.street_field1, self.street_field2,
                        self.house_number_field, self.locality_field]
        for box in combolist:
            box.clear()
            box.addItem("(none)")
            box.setCurrentIndex(0)

        map_md_utils = MapMdUtils(
            self.input_filename.displayText())

        try:
            header = next(map_md_utils.read_csv())
            header = [field for field in header]
            if header is None:
                return

            for index in header:
                for box in combolist:
                    box.addItem(index)
        except StopIteration:
            pass
```

### Implementarea metodei de alegere a căilor spre fișierele de ieșire

Cum am menționat anterior, după alegerea fișierului de intrare, în mod implicit se modifică și calea spre fișierele de ieșire și a celui ce conține adrese neidentificate, însă aceste căi (precum și denumirea fișierelor) pot fi modificate la cererea utilizatorului, apăsând pe butoanele _Browse&#8230;_ în drept cu câmpurile menționate.

```python
def browse_spatialite_file_dialog(self):
    """ Browse SpatiaLite file dialog. """
    output_file_name, _ = QFileDialog.getSaveFileName(
        None, "Output SpatiaLite File",
        self.output_spatialite_filename.displayText(),
        "SpatiaLite File (*.db *.sqlite)")
    if output_file_name:
        self.output_spatialite_filename.setText(
            os.path.abspath(output_file_name))

def browse_notfound_file_dialog(self):
    """ Browse Not Found file dialog. """
    output_file_name, _ = QFileDialog.getSaveFileName(
        None, "Output Not Found File",
        self.output_notfound_filename.displayText(),
        "CSV File (*.csv *.txt)")
    if output_file_name:
        self.output_notfound_filename.setText(
            os.path.abspath(output_file_name))
```

### Verificarea disponibilității procedurii de geocodificare

* Câmpul cu calea spre fișierul de intrare să nu fie gol;
* Câmpul cu calea spre fișierul de ieșire să nu fie gol;
* Câmpul cu cheia API să nu fie gol;
* Componentul combobox cu denumirea _Street 1 field_ să nu fie gol;
* Componentul combobox cu denumirea _Locality field_ să nu fie gol.

După satisfacerea acestor condiții, butonul _OK_ al interfeței dialogului extensiei va fi pornit și va fi posibil de apăsat.

```python
def is_ready_to_geocode(self):
    """ Enable or disable OK button whether all
    required field are filled. """
    is_ready = self.input_filename.displayText() \
        and self.output_spatialite_filename.displayText() \
        and self.output_notfound_filename.displayText() \
        and self.api_key.displayText() \
        and self.street_field1.currentIndex() > 0 \
        and self.locality_field.currentIndex() > 0

    self.button_box.button(QDialogButtonBox.Ok).setEnabled(
        bool(is_ready))
```

## Modificarea fișierului principal al extensiei

În fișierul _map_md.py_ ne asigurăm că am importat următoarele biblioteci:

```python
import os.path
from PyQt5.QtCore import QSettings, QTranslator, qVersion, QCoreApplication
from PyQt5.QtGui import QIcon
from PyQt5.QtWidgets import QAction
from qgis.core import QgsApplication, QgsMessageLog
from .resources import *
from .map_md_dialog import MapMdDialog
from .map_md_utils import MapMdUtils
```

### Obținerea datelor din interfața dialogului extensiei și adăugarea procesului de geocodificare în managerul de sarcini

În constructorul clasei _MapMd_, atribuim o variabilă care va obține obiectul managerului de sarcini al aplicației QGIS.

```python
self.task_manager = QgsApplication.taskManager()
```

Ulterior, dacă a fost apăsat butonul _OK_ din interfața dialogului extensiei, atunci se obține toți parametrii setați în aceasta, se crează o nouă instanță a clasei _MapMdUtils_, care extinde clasa _QgsTask_ și o transmite metodei _addTask_ a obiectului _taskManager_.

```python
if result:
    # Init variables
    api_key = self.dlg.api_key.displayText()
    street1_index = self.dlg.street_field1.currentIndex() - 1
    street2_index = self.dlg.street_field2.currentIndex() - 1
    house_number_index = self.dlg.house_number_field.currentIndex() - 1
    locality_index = self.dlg.locality_field.currentIndex() - 1

    input_filename = self.dlg.input_filename.displayText()
    output_filename = self.dlg.output_spatialite_filename.displayText()
    notfound_filename = self.dlg.output_notfound_filename.displayText()

    map_md_utils = MapMdUtils(input_filename, output_filename,
                              notfound_filename, api_key,
                              street1_index, street2_index,
                              house_number_index, locality_index,
                             )

    # Geocoding CSV rows
    task_id = self.task_manager.addTask(map_md_utils)
    QgsMessageLog.logMessage("Atribuită sarcină nr. %s" % str(task_id))
```

## Încheire

La primele testări, această extensie funcționează bine. Extensia o puteți găsi pe [depozitul Plugin-urilor QGIS][25] sau pe [repository oficial Github][13]

 [1]: https://www.qgis.org/en/site/
 [2]: https://map.md/ru/blog/map-md-lanseaza-setul-de-servicii-api
 [3]: https://ro.wikipedia.org/wiki/Geocodificare
 [4]: https://developers.google.com/maps/documentation/geocoding/start
 [5]: https://nominatim.openstreetmap.org
 [6]: https://ro.wikipedia.org/wiki/Plugin
 [7]: https://plugins.qgis.org/plugins/mmqgis/
 [8]: https://www.giscourse.com/install-qgis-through-osgeo4w/
 [9]: http://plugins.qgis.org/plugins/pluginbuilder/
 [10]: https://plugins.qgis.org/plugins/debug_vs/
 [11]: https://plugins.qgis.org/plugins/plugin_reloader/
 [12]: http://geoapt.net/pluginbuilder/
 [13]: https://github.com/victor-pogor/map.md-geocoding
 [14]: https://code.visualstudio.com/
 [15]: https://www.digitalcitizen.ro/content/intrebari-simple-ce-sunt-variabilele-mediu-windows
 [16]: https://en.wikipedia.org/wiki/Shapefile
 [17]: https://en.wikipedia.org/wiki/Shapefile#Limitations
 [18]: http://desktop.arcgis.com/en/arcmap/latest/manage-data/shapefiles/geoprocessing-considerations-for-shapefile-output.htm#GUID-D880FCB5-066A-4639-BC18-160A8FEB36A3
 [19]: https://www.gaia-gis.it/gaia-sins/spatialite-cookbook/index.html#common
 [20]: https://en.wikipedia.org/wiki/World_Geodetic_System
 [21]: https://map.md/
 [22]: https://qgis.org/pyqgis/master/core/QgsTask.html#qgis.core.QgsTask.run
 [23]: https://qgis.org/pyqgis/master/core/QgsTaskManager.html#qgis.core.QgsTaskManager.addTask
 [24]: https://qgis.org/pyqgis/master/core/QgsTask.html#qgis.core.QgsTask.finished
 [25]: https://plugins.qgis.org/plugins/map_md/
 [26]: https://www.linkedin.com/feed/update/urn:li:activity:6536171635165192192
 [27]: https://qgis.org/pyqgis/3.0/core/Task/QgsTask.html
 [28]: https://map.md/ru/api
 [29]: https://map.md/ro/blog/map-md-lanseaza-setul-de-servicii-api
