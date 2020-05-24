---
title: "Gears generator #1. Teorie"
date: 2020-05-15T19:55:41+03:00
lastmod: 2020-05-15T19:55:41+03:00
draft: true

categories: ["Proiecte"]
---

## Ce reprezintă transmisiuni prin angrenare

În comparaţie cu alte tipuri de transmisii mecanice, angrenajele prezintă o anumită serie de avantaje esenţiale ca[^organe-de-masini]:

* raport de transmitere constant;
* durabilitate şi siguranţă în exploatare (funcţionare);
* dimensiuni şi gabarit redus;
* posibilitatea de a transmite puteri într-un domeniu larg de viteze şi rapoarte de transmitere;
* randament ridicat (ajungând la 0.995);
* direcţia axelor conducătoare şi conduse poate fi oarecare;

Acestor avantaje li se opun şi unele dezavantaje ca[^organe-de-masini]:

* necesitatea unei precizii înalte de execuţie şi montaj;
* funcţionarea cu zgomot la viteze ridicate;
* imposibilitatea realizării oricărui raport de transmitere.

## Clasificarea angrenajelor cu axe fixe

Există o mulțime de clasificări ale angrenajelor, însă pentru mine am evidențiat 3 criterii de clasificare, care vor fi relatate în continuare.

{{< figure src="/images/2020/05/gears-generator-1-teorie/Angrenaje.svg" title="Clasificarea angrenajelor." >}}

În funcție de aceste clasificări distingem numeroase tipuri de angrenaje[^kggear]:

FOTO

Cred eu că lista acesta nu este exhaustivă și că mai există și alte tipuri patentate, însă ca studiu eu voi lua **angrenajul cilindric cu dinții drepți** (spur gear).

Acest tip de angrenaj, din cercetările efectuate, este cel mai simplu pentru familiarizarea în domeniul **transmisiilor mecanice prin angrenaje**.

## Analiza profilului dintelui

La prima vedere, profilul dintelui în cazul angrenajului cilindric cu dinții drepți îmi părea unul obișnuit. Vedeam un arc de cerc și atât.

Realitatea este că cele mai răspândite roți dințate au profilul dinților evolventic.

{{< image src="https://i2.wp.com/upload.wikimedia.org/wikipedia/commons/c/c2/Involute_wheel.gif" alt="Roți dințate evolventice." caption="Roți dințate evolventice. Credits: [en.wikipedia.org](https://en.wikipedia.org/wiki/Involute_gear#/media/File:Involute_wheel.gif)">}}

Profilul evolventic este aşadar cel mai răspândit tip de profil datorită următoarelor avantaje[^organe-de-masini]:

* angrenajul nu este sensibil la abaterile distanţei dintre axe, deoarece, profilurile dinţilor conjugaţi, fiind evolventice, rămân în contact pe o linie de angrenare, iar raportul de transmitere nu-şi schimbă valoarea;
* roţile dinţate se pot prelucra cu o sculă simplă, având profil nerectiliniu;
* angrenajele se controlează uşor;
* dantura poate fi uşor modificată, în funcţie de cerinţele unei funcţionări optime.

În continuare, voi încerca să explic ce reprezintă o evolventă a unui cerc.

## Evolventa unui cerc

Din motivul că nu am studii necesare în acest domeniu, numele de **evolventă** nu îmi sugera nimic.

Căutând în diverse surse, am înțeles cum utilizând memoria asociativă va fi posibil de asimilat ce reprezintă evolventa unui cerc.

Imaginați-vă că cercul este un mosor cu ață și ținând ața întinsă permanent, începeți a depana ața de pe mosor. Anume aceasta și va fi evolventa unui cerc! 💡

{{< youtube l4PlT3QIfS0 >}}

OK, înțelesesem din start ce reprezinta aceasta, însă cum se construiește aceasta sub aspect matematic sau mai bine spus geometric nu aveam idee. Și aici au început căutările mele prin universul informațional al Internetului. 🔎

Din toată teoria amplă am constatat că evolventa poate fi reprezentată cu ajutorul ecuațiilor parametrice în sistemul de coordonate carteziene:

$$ x = r_b (\cos\psi + \psi\sin\psi) $$
$$ y = r_b (\sin\psi - \psi\cos\psi) $$

În cazul dat, $r_b$ este raza cercului și $\psi$ este unghiul.

[^organe-de-masini]: Ștefănescu I, Spânu C. *Organe de Mașini*. Vol 2. Editura Europlus, Galați, 2009.
[^kggear]: Technical Data. KG Stock Gears. [](https://www.kggear.co.jp/en/wp-content/themes/bizvektor-global-edition/pdf/TechnicalData_KGSTOCKGEARS.pdf). Accesat la 01.05.2020.
