# 💧 ESPHome Trojni Merilnik Vode (ESP32-S3-POE-ETH)

🌐 Jeziki: [🇬🇧 English](README.md) | [🇸🇮 Slovenščina](README.sl.md)

## 📘 Pregled
Ta projekt vsebuje popolno **ESPHome konfiguracijo** za spremljanje **treh ločenih vodomerov** z uporabo **ESP32-S3-POE-ETH** ploščice z integriranim **Power-over-Ethernet (PoE)** in **Ethernet (W5500)** modulom. 

Popolnoma je združljiv s **Home Assistant** sistemom, lahko pa ga spremljaš tudi neposredno prek vgrajenega **spletnega strežnika** z uporabo IP-naslova naprave.

<p>
  <img src="images/Homeassistant.png" width="400" style="margin-right: 20px;">
  <img src="images/Watermeters.JPG" width="400">
</p>

---

## ⚡ Glavne značilnosti
- Podpira **tri neodvisne vodomere**
- Namenjeno za **PoE napajano ESP32-S3-POE-ETH** ploščo (brez Wi-Fi)
- Deluje z **reed stikali** ali **NPN NO induktivnimi senzorji**
- Spremlja **dnevno, tedensko, mesečno in letno** porabo
- Vgrajeni **samodejni reseti** opolnoči, ob ponedeljkih, prvi dan v mesecu in prvi dan v letu
- Ročni vnos začetnih vrednosti števcev
- Vključen **web_server** za samostojno spremljanje
- **Restart stikalo** dostopno prek Home Assistanta
- Stabilno in zanesljivo — preizkušeno več mesecev v realnem okolju

---

## 🧠 Strojna oprema

### 🪛 Krmilnik
- **ESP32-S3-POE-ETH** (z W5500 Ethernet + PoE modulom)  
  🔗 https://www.aliexpress.com/item/1005007945002026.html

---

### 🌐 Prednosti uporabe PoE

Uporaba Power-over-Ethernet (PoE) je zelo zanesljiv in praktičen način napajanja ESP32-S3-POE-ETH modulov:

✔ Stabilno napajanje na dolge razdalje — PoE deluje do 100 m brez opaznih izgub ali motenj

✔ En kabel za vse — napajanje in podatki prek istega Ethernet kabla

✔ Večja zanesljivost v primerjavi z Wi-Fi, kar je idealno za merjenje porabe

✔ Ni potrebe po lokalnih napajalnikih, vtičnicah ali USB kablih na mestu montaže

Če nimaš PoE stikala, lahko PoE vseeno uporabljaš s PoE injektorjem, ki v Ethernet kabel doda napajanje in ohranja enostavno ter robustno povezavo.

---

### 🔌 Shema ožičenja

Spodaj je celotna shema ožičenja, uporabljena v tem projektu:

![ESP32-S3 Wiring Diagram](images/ESP32-S3-ETH-details.png)

*Pogled podrobnosti ESP32-S3 povezav, senzorjev, napajanja in Ethernet vmesnika.*

---

## 💧 Vodomeri in senzorji

### **S1 – Maddalena DS TRP**
- Uporablja **REED SWITCH TRP PULSER**

> **Opomba za uporabnike v Sloveniji:**  
> Ker sem iz Slovenije, sem Maddalena RSTP stikalo pridobil preko slovenskega distributerja za SENSUS – podjetja **ARSS d.o.o.**  
>  
> Podjetje se ukvarja z iskanjem rešitev na področju merjenja in digitalizacije porabe vode.  
> Osebje je bilo zelo prijazno, hitro in odzivno – stikalo sem prejel praktično že naslednji dan.  
>  
> Če se soočate z izzivi pri spremljanju porabe vode, jih lahko kontaktirate – morda so podobno težavo že rešili.   
> Na voljo imajo tudi široko ponudbo senzorjev za različne tipe vodomerov.  
>  
> 🔗 Spletna stran: https://arss.si/  
> 📧 Kontakt: info@arss.si


<p align="left">
  <img src="images/Reed-switch-pulse-emitter.png" width="350">
</p>

- Vključuje strojni **debounce**:
  - 100 nF keramični kondenzator (104)
  - 10 kΩ pull-up upor

---

### **S2 in S3 – Sensus 420**
- Uporabljata **induktivna NPN NO senzorja (LJ18A3-8-Z/BX-5V)**  
  🔗 https://www.aliexpress.com/item/1005004867517992.html
- Napajana pri **5 V**
- Povezana neposredno z internimi pull-up upori, omogočenimi v programski opremi:

```yaml
mode: INPUT_PULLUP
```

*Testirano več mesecev — **brez lažnih impulzov ali motenj**.*

---

## ⚙️ Podrobnosti programske opreme

### 🔌 Komunikacija
- Ethernet (W5500)
- PoE napajanje
- Ni potrebe po Wi-Fi nastavitvah

---

### 📊 Podatkovne točke za vsak vodomer

Vsak vodomer (`S1`, `S2`, `S3`) zagotavlja naslednje senzorje:

| Entiteta              | Opis                                    |
| --------------------- | ---------------------------------------- |
| **Skupaj**            | Življenjska skupna poraba (litri)        |
| **Dnevno**            | Reset vsak dan opolnoči                  |
| **Tedensko**          | Reset vsak ponedeljek opolnoči           |
| **Mesečno**           | Reset prvi dan vsakega meseca            |
| **Letno**             | Reset 1. januarja                         |
| **Trenutna poraba**   | Sledi kratkoročnim vodnim dogodkom       |
| **Zadnja poraba**     | Zadnji dogodek pred resetom              |

---

## 🔁 Samodejni reseti

| Perioda | Čas sprožitve        | Dejanja                 |
| ------- | --------------------- | ------------------------ |
| Dnevno  | Vsak dan opolnoči     | Reset dnevnih števcev    |
| Tedensko| Ponedeljek opolnoči   | Reset tedenskih števcev  |
| Mesečno | 1. v mesecu opolnoči  | Reset mesečnih števcev   |
| Letno   | 1. januarja           | Reset letnih števcev     |

---

## 🧮 Ročna inicializacija

Če moraš ob prvi namestitvi ali ob menjavi naprave vnesti trenutne vrednosti vodomerov, lahko zdaj to narediš enostavno preko Načina za urejanje (Edit Mode), brez spreminjanja YAML kode.

🟦 Kako deluje

Vklopi Edit Mode
Preklopi stikalo »Enable Edit Mode« na ON.
S tem omogočiš ročni vnos vrednosti.

Vnesi začetne vrednosti
Nato vneseš vrednosti za:

Set Total S1

Set Total S2

Set Total S3

Vpiši skupno porabo v litrih, ki jo trenutno kažejo tvoji dejanski vodomeri.

Potrdi nove vrednosti
Ko so vse vrednosti vnesene, preklopi stikalo »Restart«.
Modul se bo ponovno zagnal in naložil nove začetne vrednosti kot osnovo za vse števce.

📝 Opombe

Po ponovnem zagonu se Edit Mode samodejno izklopi, da prepreči nenamerne spremembe.

Če želiš vrednosti kasneje ponovno popraviti, postopek samo ponoviš.

---

## 🌐 Spletni vmesnik

Konfiguracija vključuje vgrajeni **spletni strežnik**.  
Ko je naprava povezana v omrežje, v brskalnik vpiši:

```
http://<ip_naprave>
```

Prikazale se bodo trenutne meritve za vse tri vodomere.

---

## 🔄 Restart tipka

**Programski restart** je na voljo prek Home Assistanta:

```yaml
button:
  - platform: restart
    name: "${friendly_name} Restart"
```
---


## 🛠 Možnosti namestitve

Na voljo sta dva načina namestitve programske opreme na ESP32-S3-POE-ETH krmilnik:

### 1️⃣ Namestitev z uporabo ESPHome Builderja (priporočeno za uporabnike Home Assistanta)
Če uporabljaš Home Assistant in imaš nameščen ESPHome, preprosto naložiš priloženo YAML konfiguracijo v ESPHome in preko ESPHome vmesnika skompiliraš ter naložiš firmware na krmilnik.


### 2️⃣ Namestitev s predpripravljeno .BIN datoteko (za uporabnike brez Home Assistanta)
Za uporabnike, ki **ne** uporabljajo Home Assistanta, je na voljo že pripravljena **`.bin` datoteka.** .

Na napravo lahko naložiš neposredno prek uradnega ESPHome spletnega orodja:

https://web.esphome.io/

Preprosto priključi ESP32-S3-POE-ETH preko USB-ja, odpri spletno stran, klikni **Install**, izberi priloženo `.bin` datoteko – in namestitev je končana.

Po namestitvi lahko porabo vseh treh vodomerov spremljaš prek vgrajenega **spletnega vmesnika**, , brez Home Assistanta.

---

## MQTT podpora (neobvezno)

Projekt podpira MQTT za integracijo z drugimi sistemi (Node-RED, Grafana, ioBroker …).

MQTT ni potreben za uporabo z Home Assistantom (ESPHome API).

👉 Podrobna navodila so v:
[MQTT_README_SI.md](MQTT/MQTT_README_SI.md)

---

## 🧰 Odvisnosti
* **ESPHome** ≥ 2024.6.0  
* **Home Assistant** (neobvezen, vendar priporočljiv)  
* Podpora za Ethernet mora biti omogočena v tvoji ESPHome namestitvi  

---

## ⚖️ Licenca
MIT License

---

## 🚀 Prihodnje izboljšave
* Izračun pretoka v realnem času  
* Prilagoditev za različne ESP32 plošče  
* Opcijski OLED zaslon za lokalni prikaz  

---

## 🙌 Zahvale
Razvijal in testiral **Mitja Modic**  
Navdihnjen z realnimi potrebami za zanesljivo merjenje porabe vode preko Ethernet (PoE) povezav.

---

⭐ *Če ti je projekt koristen, mi na GitHubu podari zvezdico!* ⭐

---

### ☕ Če vam je projekt koristen in bi radi podprli moje delo, me lahko povabite na kavo

[![Ko-Fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/mitjamodic)