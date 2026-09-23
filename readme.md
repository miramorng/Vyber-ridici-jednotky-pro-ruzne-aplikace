# Výběr řídící jednotky pro různé aplikace

$${\color{#FFA500}E9 \space \color{#4682B4}A1 }$$

## Cíle

- **Orientovat se** v základních typech a architekturách řídicích jednotek (MCU, MPU, embedded systémy, PLC, iPC, programovatelná relé).
- **Rozlišovat klíčové technické parametry** (výpočetní výkon vs. spotřeba, typy a velikosti pamětí RAM/Flash/EEPROM, determinismus a reakční doba v reálném čase).
- **Zhodnotit provozní odolnost a robustnost** hardwaru (krytí IP, teplotní rozsah, vibrace, rušení EMC, srovnání spotřební vs. průmyslové techniky).
- **Navrhnout a technicko-ekonomicky obhájit** optimální řídicí jednotku pro konkrétní praktickou aplikaci podle I/O bilance, rozhraní a prostředí.

## Ověření cílů

Výběr řídící jednotky pro různé aplikace:

1. Příklady řídících jednotek
2. Jejich základní vlastnosti z hlediska výpočetního výkonu a velikosti paměťového prostoru
3. A z hlediska odolnosti
4. Příklady použití v praxi (kde se používají MCU, a kde ř. j. s MPU)

---

## Úlohy

### 1. Základní pojmy a architektury řídicích jednotek

*Časová dotace: 10–15 minut | Úvodní úloha*

Doplňte do níže uvedené tabulky význam zkratek, základní princip a typický příklad reálného nasazení nebo zástupce:

| Zkratka / Pojem | Co zkratka znamená (česky/anglicky) | Základní charakteristika (architektura, kde běží program) | Typický zástupce | Příklad nasazení |
| :--- | :--- | :--- | :--- | :--- |
| **MCU** | Microcontroller Unit (Mikrořadič) | Integrovaný čip (CPU + RAM + Flash na jednom křemíku), deterministický běh bez OS / RTOS | např. ESP32, PIC16LF1xxx, RP2040 | Senzory, domácí spotřebiče, hračky |
| **MPU** | Microprocessor Unit (Mikroprocesor) | Samostatný procesor vyžadující externí RAM a úložiště, často běží plnohodnotný OS (Linux) | ARM Cortex-A (např. Raspberry Pi), Intel Core | Tablety, pokročilé brány (IoT gateways), počítače |
| **Embedded** | Vestavěný systém | Účelově zaměřený počítačový systém kombinující hardware (MCU/MPU) a software pro specifickou úlohu | Embedded PLC, embedded PC | Bílá technika, bankomaty, plynové kotle... |
| **PLC** | Programmable Logic Controller (Programovatelný logický automat) | Průmyslový automat pro cyklické řízení procesů, vysoká odolnost, modulární/kompaktní | SIMATIC S7-1200/1500 | Průmyslové linky, výrobní stroje, robotika |
| **iPC** | Industrial PC (Průmyslový počítač) | Počítač v průmyslovém provedení, vysoká výkonnost, x86/ARM architektura, běží průmyslový OS | Siemens SIMATIC IPC, Beckhoff | Vizualizace (HMI/SCADA), náročný sběr dat |
| **Programovatelné relé** | Smart relay / Programovatelné relé | Zjednodušené malé PLC pro méně náročné úlohy (nahrazuje časovače a relé) | např. Siemens LOGO!, Eaton easyE4 | Ovládání osvětlení, menší vzduchotechnika, brány |

> **Vysvětlení pojmů a odborné zdroje:**
> - **SoC (System on Chip):** Čip integrující CPU, GPU, paměť i bezdrátové moduly (např. Wi-Fi/BT) na jediném substrátu (např. v telefonech, ESP32).
> - **DSP (Digital Signal Processor):** Specializovaný procesor s architekturou optimalizovanou pro bleskové matematické operace (filtrace zvuku, FFT, řízení motorů).
> - **FPGA (Field-Programmable Gate Array):** Programovatelné hradlové pole umožňující vytvořit libovolný digitální obvod přímo na hardwarové úrovni s nulovou programovou latencí.

**Bonusová otázka k úloze 1:**

Proč se u bezpečnostních aplikací v letectví nebo jaderné energetice stále upřednostňují jednoduché mikrořadiče nebo FPGA před moderními vícejádrovými procesory s gigabajty RAM?

*Odpověď:* **Důvodem je determinismus, předvídatelnost a certifikovatelnost. Vícejádrové procesory s velkou RAM a složitými mezipaměťmi využívají predikci skoků, sdílené sběrnice a dynamické plánování úloh. To způsobuje, že doba vykonání instrukcí není zcela konstantní, což je pro bezpečnostně kritické systémy nepřípustné. Jednoduché MCU nebo FPGA umožňují exaktně dokázat a verifikovat každý takt procesoru a stav hardwaru.**

---

### 2. Parametry, paměti a provozní odolnost (IP krytí)

*Časová dotace: max. 15 minut | Mírně náročnější úloha propojující parametry a praxi*

1. **Typy pamětí v řídicích jednotkách:**
   - Doplňte porovnání pamětí z hlediska stálosti dat a rychlosti:
     - **RAM:**
       - Je volatilní (energeticky závislá)? **ANO**
       - Rychlost zápisu: **EXTRÉMNĚ VYSOKÁ**
       - K čemu se využívá v PLC/MCU: **Ukládání proměnných, stavů a dat aktuálního běhu programu (pracovní paměť)**
     - **Flash (ROM):**
       - Je volatilní? **NE**
       - K čemu se využívá v PLC/MCU: **Ukládání samotného uživatelského programu, firmwaru a trvalých dat.**
     - **EEPROM / NVRAM:**
       - Je volatilní? **NE**
       - K čemu se využívá v PLC/MCU: **Ukládání konfiguračních parametrů, nastavení a dat, která je nutné uchovat i po vypnutí napájení (remanentní data).**

   - *Otázka z praxe:* Kam se v průmyslovém PLC ukládají aktuální provozní proměnné (např. čítače vyrobených kusů nebo motohodiny), aby se při nečekaném výpadku napájení neztratily (tzv. remanentní / retain data)?
     - Odpověď: **Do remanentní paměti (např. EEPROM, FRAM, nebo do RAM zálohované superkondenzátorem či baterií).**

2. **Reálný čas a determinismus (Hard vs. Soft Real-Time):**
   - Proč pro reakci na nouzové zastavení lisu (požadavek reakce do 5 ms) použijeme PLC či mikrokontrolér s RTOS, a nikoliv běžné Raspberry Pi s operačním systémem Raspberry Pi OS (standardní Linux)?
     - Odpověď: **Běžný operační systém (jako je Raspberry Pi OS) je preemptivní multi-taskingový systém, který rozděluje výkon mezi mnoho procesů na pozadí (správa paměti, síťový provoz). To může způsobit neočekávanou prodlevu (latenci) v řádu desítek milisekund. Pro bezpečnostní funkce, jako je nouzové zastavení lisu (požadavek do 5 ms), je nutný deterministický systém (Hard Real-Time), který zaručuje přesný časový limit odezvy bez výkyvů.**

3. **Odolnost vůči vlivům prostředí a dešifrování kódu IP:**
   - Dešifrujte kód **IP68**:
     - První číslice (6): **Úplná ochrana před vniknutím prachu (prachotěsné).**
     - Druhá číslice (8): **Ochrana proti nepřetržitému ponoření do vody za podmínek určených výrobcem.**
   - Jaké minimální krytí IP musí mít rozváděč umístěný ve venkovním nekrytém prostředí, kde na něj přímo dopadá déšť a fouká polétavý prach?
     - Označte správnou volbu: `[ ] IP20` | `[ ] IP44` | **[X] IP65** | `[ ] IP00`
     - Zdůvodnění: **Stupeň IP65 zaručuje úplnou ochranu proti prachu (6) a ochranu proti tryskající vodě ze všech směrů (5), což spolehlivě odolá dešti a polétavému prachu ve venkovním prostředí.**

4. **Konstrukční rozdíly kancelářského PC vs. průmyslového iPC:**
   - Vyberte a doplňte hlavní odlišnosti:
     - *Chlazení:*
       - Kancelářské PC: **Aktivní chlazení pomocí ventilátorů**
       - vs. iPC: **Pasivní chlazení s masivním žebrováním, hermeticky uzavřené proti prachu.**
     - *Napájecí napětí a filtrace:*
       - Kancelářské PC: **Standardní síťové napětí 230 V AC přes běžný zdroj**
       - vs. iPC: **Průmyslový standard 24 V DC s robustní filtrací proti elektromagnetickému rušení (EMI) a výkyvům napětí.**
     - *Odolnost proti otřesům a vibracím:*
       - Kancelářské PC: **Nízká, kvůli HDD**
       - vs. iPC: **Vysoká mechanická odolnost, kvůli SSD**
     - *Způsob montáže:*
       - Kancelářské PC: **na stůl/pod stůl**
       - vs. iPC: **Na DIN lištu, do panelu**

> **Vysvětlení pojmů a odborné zdroje:**
> - **Determinismus (Real-Time):** Vlastnost systému, která zaručuje, že odezva na vstupní událost proběhne vždy v přesně definovaném a předvídatelném čase (deadline). V *Hard Real-Time* systémech znamená nedodržení časového limitu fatální havárii celého procesu.
> - **Krytí IP (Ingress Protection):** Mezinárodní standard dle normy **ČSN EN 60529** určující stupeň ochrany krytem před vniknutím pevných cizích těles včetně prachu (1. číslice 0–6) a vniknutím vody (2. číslice 0–9K).
> - **Remanentní paměť (Retain):** Paměťový prostor v PLC, jehož obsah zůstává zachován i po přerušení napájecího napětí (využívá zálohovací baterii, superkondenzátor nebo zápis do FRAM/MRAM/EEPROM).

**Bonusová otázka k úloze 2:**

Co označuje doplňkové písmeno **K** v kódu krytí **IP69K** a v jakém průmyslovém odvětví je toto krytí bezpodmínečně vyžadováno?

*Odpověď:* **Označuje specifickou ochranu proti vysokotlakému čištění horkou vodou nebo olejem (čištění paroměrnou tryskou pod vysokým tlakem). Toto krytí je bezpodmínečně vyžadováno v potravinářském průmyslu, farmacii a při výrobě nápojů.**

---

### 3. Rozhodovací matice platforem (MCU vs. PLC vs. iPC)

*Časová dotace: 20–25 minut | Klasifikovaná inženýrská úloha na známky*

Jste v pozici nezávislého konzultanta automatizace. Tři různí zákazníci požadují navrhnout optimální kategorii řízení.

#### Popis zadaných aplikací:

1. **Vzorová aplikace 0 – Automatická vjezdová závora na parkoviště:** Jednoduchý jednoúčelový systém s indukční detekční smyčkou vozidla, bezpečnostní optozávorou, koncovými spínači polohy ramene, motorem závory (vpřed/vzad) a výstražným semaforem (červená/zelená).
2. **Aplikace A – Chytrý pokojový termostat (IoT):** Bateriově napájený přístroj měřící teplotu a vlhkost v místnosti, zobrazující údaje na e-ink displeji a odesílající data přes protokol ZigBee/Wi-Fi do domácí brány. Plánovaná sériová výroba: 10 000 kusů ročně.
3. **Aplikace B – Automatická balicí linka:** Průmyslová linka ve výrobní hale. Obsahuje 28 optických snímačů, 14 pneumatických válců, 3 dopravníkové pásy s asynchronními motory a bezpečnostní světelnou závoru. Vyžaduje se nepřetržitý provoz 24/7 a snadná údržba podnikovým elektrikářem.
4. **Aplikace C – Kontrolní stanice optické jakosti svarů:** Pracoviště se 2 vysokorychlostními průmyslovými GigE kamerami snímajícími svary na karoserii automobilu. Snímky v rozlišení 4K jsou analyzovány neuronovou sítí v reálném čase, vady jsou označeny a ukládány do podnikové relační databáze (SQL / MES).

#### Rozhodovací matice:

| Kritérium hodnocení | Vzorová aplikace 0 (Vjezdová závora - VZOR) | Aplikace A (Pokojový termostat) | Aplikace B (Balicí linka) | Aplikace C (Kamerová kontrola svarů) |
| :--- | :--- | :--- | :--- | :--- |
| **Doporučená platforma** *(MCU / PLC / iPC)* | Programovatelné relé / kompaktní PLC (např. Siemens LOGO!, Eaton easyE4) | **MCU / Embedded SoC** (např. nRF52, ESP32 nebo obdobný úsporný mikrokontrolér) | **Kompaktní / modulární PLC** (např. Siemens S7-1200 / S7-1500, Schneider Electric Modicon) | **Průmyslové PC (iPC)** (např. Advantech, Beckhoff s vysokým výpočetním výkonem a GPU/NPU) |
| **Pořizovací cena HW na 1 kus** *(nízká < 500 Kč / střední 5–30 tis. Kč / vysoká > 50 tis. Kč)* | Střední (cca 3 500 – 6 000 Kč) | **Nízká** (vzhledem k masové sériové výrobě 10 000 ks/rok v řádu stovek Kč za kus) | **Střední až vysoká** (dle rozsahu I/O modulů, v řádu desítek tisíc Kč) | **Vysoká** (> 50 tis. Kč vzhledem k výkonnému HW, licencím a průmyslovým kamerám) |
| **Primární programovací jazyk** *(C/C++/MicroPython vs. IEC 61131-3 ST/LAD vs. Python/C#/C++ pod OS)* | FBD / LAD (grafické funkční bloky nebo liniové schéma dle IEC 61131-3) | **C / C++ / MicroPython / Rust** (eventuálně IoT frameworky výrobců) | **IEC 61131-3 (LAD – kontaktní schéma)** | **Python / C++ / C#** (běžně pod OS Linux/Windows s využitím knihoven pro AI a OpenCV) |
| **Klíčový technický argument pro volbu** | Montáž přímo na DIN lištu v rozváděči, integrovaný displej pro nastavení časovačů přímo na místě, robustní reléové výstupy pro motor a semafor, napájení 24 V DC / 230 V AC bez nutnosti vývoje vlastního plošného spoje. | **Ultra nízká spotřeba pro bateriový provoz** (Deep Sleep režimy), bezdrátová konektivita (ZigBee/Wi-Fi), e-ink displej a nízká cena při vysokém sériovém násobení nákladů. | **Vysoká spolehlivost v průmyslu (24/7),** snadná diagnostika LED diodami, modulární rozšiřitelnost I/O pro snímače/pneu, servis běžným elektrikářem v jazyku LAD. | **Obrovský výpočetní výkon** pro zpracování 4K obrazu a běh neuronové sítě v reálném čase, integrace GigE Vision kamer a propojení s podnikovou SQL/MES databází. |
| **Hlavní riziko při volbě špatné platformy** *(proč by neuspěly ostatní dvě varianty)* | MCU: Nutnost vývoje vlastní desky, nízká odolnost vůči venkovnímu rušení a obtížný servis údržbou.<br><br>iPC: Zbytečně extrémní cena (> 30 tis. Kč), dlouhý start po výpadku napájení a vysoká spotřeba. | **PLC / iPC:** Neschopnost napájení z baterie (vysoký odběr), absence bezdrátových modulů, obrovské rozměry a zcela zruinující kusová cena (> 5 000 Kč) pro masovou sérii 10k ks. | **MCU:** Chybí průmyslové krytí, robustní svorkovnice, normované napájení 24V a servisovatelná dokumentace pro údržbu.<br><br>**iPC:** Zbytečně složitý operační systém, riziko zamrznutí OS, komplikovanější přímá obsluha binárních snímačů bez průmyslových I/O karet. | **PLC / MCU:** Absolutně nedostatečný výpočetní výkon pro AI/neuronové sítě, nemožnost zpracování 4K video-streamů ze 2 GigE kamer v reálném čase, chybějící podpora standardních databázových konektorů a složitá správa podnikového softwaru. |

| Kritérium hodnocení                                                                                   | **Vzorová aplikace 0 (Vjezdová závora - VZOR)**                                                                                                                                                                           | Aplikace A (Pokojový termostat) | Aplikace B (Balicí linka) | Aplikace C (Kamerová kontrola svarů) |
| :---------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :------------------------------ | :------------------------ | :----------------------------------- |
| **Doporučená platforma** *(MCU / PLC / iPC)*                                                          | **Programovatelné relé / kompaktní PLC** *(např. Siemens LOGO!, Eaton easyE4)*                                                                                                                                            | `...`                           | `...`                     | `...`                                |
| **Pořizovací cena HW na 1 kus** *(nízká < 500 Kč / střední 5–30 tis. Kč / vysoká > 50 tis. Kč)*       | **Střední** *(cca 3 500 – 6 000 Kč)*                                                                                                                                                                                      | `...`                           | `...`                     | `...`                                |
| **Primární programovací jazyk** *(C/C++/MicroPython vs. IEC 61131-3 ST/LAD vs. Python/C#/C++ pod OS)* | **FBD / LAD** *(grafické funkční bloky nebo liniové schéma dle IEC 61131-3)*                                                                                                                                              | `...`                           | `...`                     | `...`                                |
| **Klíčový technický argument pro volbu** *(např. spotřeba, determinismus, grafický výkon)*            | Montáž přímo na DIN lištu v rozváděči, integrovaný displej pro nastavení časovačů přímo na místě, robustní reléové výstupy pro motor a semafor, napájení 24 V DC / 230 V AC bez nutnosti vývoje vlastního plošného spoje. | `...`                           | `...`                     | `...`                                |
| **Hlavní riziko při volbě špatné platformy** *(proč by neuspěly ostatní dvě varianty)*                | **MCU:** Nutnost vývoje vlastní desky, nízká odolnost vůči venkovnímu rušení a obtížný servis údržbou.<br>**iPC:** Zbytečně extrémní cena (> 30 tis. Kč), dlouhý start po výpadku napájení a vysoká spotřeba.             | `...`                           | `...`                     | `...`                                |

> **Kritéria hodnocení úlohy 3 (bodování a známka):**
> - :star: **Správnost technického přiřazení platforem (30 %):** Stoprocentně logické a obhajitelné přiřazení všech 3 technologií.
> - :star: **Inženýrská a ekonomická argumentace (40 %):** Zohlednění ekonomiky sériovosti (kusová vs. masová výroba), spotřeby energie, náročnosti vývoje a schopností servisního personálu.
> - :star: **Analýza rizik nevhodné platformy (30 %):** Věcné zdůvodnění, proč je v daném případě jiná platforma neefektivní, příliš drahá nebo neschopná úlohu odbavit.

> :key: **Vysvětlení pojmů a odborné zdroje:**
> - **Norma ČSN EN 61131-3:** Mezinárodní standard pro programovací jazyky PLC automatů. Definuje dva textové jazyky (ST – strukturovaný text, IL – seznam instrukcí) a tři grafické jazyky (LD – příčkový diagram / kontaktní schéma, FBD – funkční blokové schéma, SFC – sekvenční funkční schéma).
> 	ČESKÝ NORMALIZAČNÍ INSTITUT. *ČSN EN 61131-3 ed. 3 (18 0080) Programovatelné řídicí jednotky - Část 3: Programovací jazyky*. Praha: Úřad pro technickou normalizaci, metrologii a státní zkušebnictví, 2014. Třídící znak 180080.
> - **GigE Vision:** Komunikační standard rozhraní pro průmyslové kamery využívající gigabitový Ethernet, umožňující přenos nekomprimovaného videa vysokou rychlostí na velké vzdálenosti.

<details>
<summary> :bulb: Tip pro Aplikaci A vs. B vs. C: </summary>
<p>U aplikace A rozhoduje kusová cena a odběr proudu z baterie (PLC ani iPC z baterie nerozběhnete). U aplikace B potřebujete vyměnitelný modul na DIN lištu s diagnostickými LED, který přeprogramuje běžný údržbář v jazyce LAD. U aplikace C potřebujete obrovský výpočetní výkon pro AI a ovladače pro průmyslové kamery, což MCU ani běžné PLC nezvládne.</p>
</details>

:star2: **Bonusová otázka k úloze 3:**
Co je to tzv. **SoftPLC** a jak umožňuje průmyslovému PC (iPC) kombinovat výhody operačního systému Windows/Linux a deterministického řízení reálného času v jediném fyzickém počítači?

*Vaše odpověď:*
`...`

---

### 4. Návrh a konfigurace řídicí jednotky pro čerpací stanici

*Časová dotace: 25–30 minut | :star: Klasifikovaná inženýrská úloha na známky*

Jste v roli projektanta automatizace. Zákazník poptává zhotovení řízení pro obecní přečerpávací stanici odpadních vod.

#### Zadání technologického procesu a periferií:
- **Snímače a vstupy:**
  - 3× plovákový hladinový spínač (havarijní spodní hladina proti chodu nasucho, zapínací hladina, havarijní přepad) – bezpotenciálový kontakt spínající 24 V DC.
  - 1× hydrostatická ponorná sonda výšky hladiny v jímce – výstupní signál 4–20 mA.
  - 1× termistorové ochranné relé přehřátí motoru čerpadla – poruchový kontakt 24 V DC.
- **Akční členy a výstupy:**
  - 2× stykač pro spouštění motorů hlavního a záložního čerpadla – spínání cívky stykače 230 V AC / 0,5 A.
  - 1× opticko-akustický výstražný maják – napájení 24 V DC / 0,3 A.
  - 1× řízení otáček frekvenčního měniče hlavního čerpadla – analogový signál 0–10 V.
- **Komunikace a přenos dat:**
  - Odesílání údajů o hladině a poruchách na dispečink vodáren (Ethernet / Modbus TCP nebo GSM/LTE modul).
- **Provozní podmínky:**
  - Venkovní nekrytý terén, rozváděč vystavený dešti, prachu a teplotám v rozmezí **-20 °C až +45 °C**.

#### Váš úkol:

1. **Sestavte tabulku I/O bilance** a spočtěte celkový počet signálů. Připočtěte rezervu min. 20 % pro budoucí rozšíření:

| Typ signálu | Požadavek aplikace (kusy) | Popis signálů v aplikaci | Počet po započtení rezervy (+20 %) |
| :--- | :--- | :--- | :--- |
| **Digitální vstup (DI)** | `...` | `...` | `...` |
| **Digitální výstup (DO) – reléový** | `...` | `...` | `...` |
| **Digitální výstup (DO) – tranzistorový** | `...` | `...` | `...` |
| **Analogový vstup (AI)** | `...` | `...` | `...` |
| **Analogový výstup (AO)** | `...` | `...` | `...` |

2. **Výběr konkrétního hardwaru z katalogu výrobce:**
   - Navrhněte konkrétní přístroj z praxe (např. *Siemens LOGO! 24RCE + rozšiřující moduly*, *Siemens S7-1200 CPU 1212C/1214C DC/DC/RLY*, *Schneider Modicon M221*, *Eaton easyE4-UC-12RC1*, *WAGO 750*, případně průmyslový IoT kontrolér typu *UniPi Neuron*).
   - Uveďte:
     - Výrobce a přesný model CPU: `...`
     - Objednací kód (Part Number / Order Code): `...`
     - Rozšiřující moduly (pokud jsou nutné pro AI 4–20 mA nebo AO 0–10 V): `...`
     - Napájecí napětí zvolené jednotky: `...`
     - Jak je vyřešeno odesílání dat na dispečink: `...`
     - Odkaz na technický list (datasheet): `...`
     - Odkazy na další použité zdroje: `...`

3. **Technické ověření z datasheetu:**
   - Zvládá zvolená jednotka garantovaný provoz při -20 °C? Doložte údaj z datasheetu: `...`
   - Jakým způsobem spínáte cívku stykače 230 V AC (reléový výstup jednotky přímo, nebo přes pomocné mezilehlé relé)? Zdůvodněte: `...`

4. **Krytí rozváděče:**
   - Jaké minimální krytí **IP skříně** zvolíte? Jak v rozváděči zajistíte provoz v mrazech -20 °C a v letních vedrech?
     - Zvolené krytí rozváděče: `...`
     - Teplotní management skříně: `...`

> **Kritéria hodnocení úlohy 4 (bodování a známka):**
> - :star: **Správnost I/O bilance a dimenzování (30 %):** Správný součet všech signálů, korektní rozlišení reléových vs. tranzistorových výstupů a správné započtení rezervy min. 20 %.
> - :star: **Reálnost výběru a kompatibilita HW (40 %):** Zvolený přístroj skutečně existuje na trhu, konfigurace plně pokrývá všechny vstupy/výstupy (včetně analogů 4–20 mA a 0–10 V) a komunikaci.
> - :star: **Posouzení provozních podmínek a instalace (30 %):** Správná volba krytí rozváděče (min. IP65), vyřešení vytápění/ventilace pro mráz a spolehlivé galvanické oddělení výkonových akčních členů.

> :key: **Vysvětlení pojmů a odborné zdroje:**
> - **Proudová smyčka 4–20 mA:** Průmyslový standard pro přenos analogových signálů ze senzorů. Výhodou oproti napěťovému signálu 0–10 V je vysoká odolnost proti elektromagnetickému rušení, nezávislost na odporu dlouhého vedení a detekce přetržení vodiče (pokud je proud roven 0 mA, jde o poruchu vedení – tzv. živá nula / live zero).
> 	Proudová smyčka. *Wikipedie: Otevřená encyklopedie* [online]. San Francisco (CA): Wikimedia Foundation, 2023, 2023-04-18 [cit. 2026-09-17]. Dostupné z: https://cs.wikipedia.org/wiki/Proudov%C3%A1_smy%C4%8Dka
> - **Galvanické oddělení:** Elektrické oddělení dvou elektrických obvodů (např. pomocí optočlenů nebo relé), které zabraňuje přenosu rušení, rozdílům zemních potenciálů a chrání citlivé vstupy řídicí jednotky před zničením přepětím.
> - **Bezpotenciálový kontakt** (označovaný také jako **dry contact**) je elektrický kontakt, který sám o sobě nemá žádné vlastní napětí ani neposkytuje žádný proud. Funguje čistě jako mechanický nebo elektronický spínač (jako klasický vypínač na zdi), který pouze spojí nebo rozpojí dva vodiče v externím obvodu.

<details>
<summary> :bulb: Tip pro výběr modulů: </summary>
<p>Pozor na analogové vstupy: Základní kompaktní jednotky (např. LOGO! nebo S7-1200) mívají integrované analogové vstupy pouze pro napětí 0–10 V. Vstupní signál 4–20 mA ze sondy vyžaduje buď speciální rozšiřující modul pro proudové signály, nebo zařazení přesného paralelního odporu 500 Ω (převod 4–20 mA na 2–10 V).</p>
</details>

:star2: **Bonusová otázka k úloze 4:**
Proč se u čerpadel v čistírnách odpadních vod a jímkách striktně upřednostňuje měření hladiny pomocí proudového signálu 4–20 mA před napěťovým signálem 0–10 V a proč se do jímky nepoužívá ultrazvukový senzor, pokud v ní vzniká hustá pěna?

*Vaše odpověď:*
`...`

---

### 5. Technický audit a oponentura nevhodného návrhu

*Časová dotace: 20–25 minut | :star: Klasifikovaná inženýrská úloha na známky*

Jako vedoucí inženýr jste převzal projekt po nezkušeném brigádníkovi, který navrhl řízení automatizovaného tvářecího a lisovacího stroje v prašné kovářské dílně následovně:
- **Řídicí deska:** Běžná vývojová deska **Arduino Uno (Rev3)** s mikrokontrolérem ATmega328P.
- **Pouzdro a umístění:** Plastová krabička vytištěná na 3D tiskárně z materiálu **PLA**, přišroubovaná přímo na těleso vibrujícího hydraulického lisu.
- **Napájení:** 5V USB nabíječka na mobilní telefon zapojená do prodlužovacího kabelu 230 V.
- **Spínání zátěže:** 4kanálový hobby reléový modul z čínského e-shopu propojený s Arduinem tenkými nepájenými vodiči (DuPont propojky). Modul přímo spíná 400V ventily hydrauliky.
- **Bezpečnost (Safety):** Nouzové stop tlačítko (E-Stop) je zapojeno přímo do digitálního pinu D2 Arduina jako softwarové přerušení (interrupt), které v kódu nastaví výstupy na `LOW`.

#### Váš úkol:

1. **Zpracujte písemný audit rizik (minimálně 4 fatální technická selhání):**
   Vyplňte protokol o zjištěných vadách a popište konkrétní fyzikální mechanismus, jak daná chyba způsobí havárii stroje či ohrožení lidského života:

| Oblast auditu | Zjištěná vada v amatérském návrhu | Fyzikální mechanismus selhání (proč to selže) | Následek pro stroj nebo obsluhu |
| :--- | :--- | :--- | :--- |
| **Elektromagnetická kompatibilita (EMC)** | `...` | Napěťové špičky z indukční zátěže hydraulických ventilů způsobí restart MCU... | `...` |
| **Mechanická a teplotní odolnost** | PLA plast a montáž na těleso lisu | `...` | `...` |
| **Konektivita a propojení vodičů** | DuPont propojovací kabely bez aretace | `...` | `...` |
| **Funkční bezpečnost (Safety)** | Nouzový stop řešený softwarově v čipu | `...` | `...` |

2. **Návrh profesionálního nápravného řešení:**
   - Navrhněte, jakými certifikovanými průmyslovými komponenty tento celek nahradíte při zachování minimálního rozpočtu:
     - *Náhrada řídicí jednotky:* `...` *(např. certifikované průmyslové programovatelné relé s montáží na DIN lištu a krytím)*
     - *Náhrada napájecího zdroje:* `...` *(např. stabilizovaný průmyslový zdroj 24 V DC na DIN lištu s ochranou proti přepětí)*
     - *Způsob zapojení bezpečnostního okruhu (Safety):* Jak musí být podle norem zapojeno tlačítko Emergency Stop (E-Stop)? Smí být spoléháno pouze na software mikrokontroléru? Zdůvodněte: `...`

> **Kritéria hodnocení úlohy 5 (bodování a známka):**
> - :star: **Odborná úroveň identifikace závad (35 %):** Přesná technická terminologie (např. elektromagnetická indukce, absence odrušovacích varistorů, skelný přechod PLA plastu při 60 °C, studené spoje a vyklepání konektorů vibracemi).
> - :star: **Pochopení norem funkční bezpečnosti Safety (35 %):** Znalost základního principu bezpečnosti strojních zařízení – nouzové zastavení musí být řešeno hardwarově přes certifikované bezpečnostní relé s nuceně vedenými kontakty, nikoliv pouhým softwarovým vstupem MCU.
> - :star: **Kvalita a realizovatelnost nápravného řešení (30 %):** Návrh odpovídá robustní průmyslové praxi s montáží do oceloplechového rozváděče na DIN lištu.

> :key: **Vysvětlení pojmů a odborné zdroje:**
> - **Funkční bezpečnost (Safety) vs. Kybernetická bezpečnost (Security):** *Safety* (dle ČSN EN ISO 13849-1) zajišťuje, že strojní zařízení nezpůsobí úraz člověku ani při vnitřní poruše řídicího systému (využívá redundantní obvody, bezpečnostní relé, optické závory, kategorii spolehlivosti PL a až PL e / SIL 3). *Security* řeší ochranu dat a systému před úmyslným napadením zvenčí (hackeři, malware).
> - **EMC (Elektromagnetická kompatibilita):** Schopnost zařízení spolehlivě pracovat v prostředí s elektromagnetickým rušením (odolnost / imunita) a současně nezpůsobovat nepřípustné rušení jiným zařízením (emise).
> 	Elektromagnetická kompatibilita. *Wikipedie: Otevřená encyklopedie* [online]. San Francisco (CA): Wikimedia Foundation, 2023, 2023-11-20 [cit. 2026-09-17]. Dostupné z: https://cs.wikipedia.org/wiki/Elektromagnetick%C3%A1_kompatibilita

<details>
<summary> :bulb: Tip k bezpečnostnímu okruhu (Safety): </summary>
<p>Základní pravidlo bezpečnosti: <strong>Software může selhat, zacyklit se nebo zamrznout.</strong> Bezpečnostní okruh nouzového zastavení (červený hřib) musí být vždy dvoukanálový, zapojený do hardwarového bezpečnostního relé (např. Pilz, Schneider Preventa, Siemens SIRIUS), které odpojí silové napájení stykačů ventilů přímo na hardwarové úrovni nezávisle na procesoru!</p>
</details>

:star2: **Bonusová otázka k úloze 5:**
Proč hobby reléové moduly s optočleny určené pro Arduino v průmyslovém rozváděči často shoří nebo způsobí trvalé sepnutí zátěže (tzv. přivaření kontaktů), i když jmenovitý proud relé je 10 A a cívka stykače odebírá jen 0,5 A?

*Vaše odpověď:*
`...`

---

### 6. Rozšiřující inženýrská výzva: TCO a životní cyklus v automatizaci

*Časová dotace: 15–20 minut | :star2: Bonusová výzva pro pokročilé studenty*

V průmyslové automatizaci nákupní cena řídicí jednotky (CAPEX) často tvoří méně než 15 % celkových nákladů na životní cyklus zařízení (OPEX / TCO).

Představte si, že management firmy rozhoduje mezi dvěma variantami řízení pro sérii 50 kusů výrobních linek s plánovanou životností 15 let:
- **Varianta 1 (Nízkonákladová na pořízení):** Využití levných embedded mikrokontrolérových desek s vlastním zákaznickým návrhem plošného spoje (cena HW: 2 500 Kč / kus, vývoj firmwaru v C/C++ od externího programátora bez dokumentace).
- **Varianta 2 (Průmyslový standard):** Využití modulárního PLC renomovaného výrobce (Siemens / Rockwell / Schneider) s cenou 22 000 Kč / kus, programováno v normovaném jazyce LAD/ST dle IEC 61131-3.

#### Váš úkol:
1. Srovnejte obě varianty v níže uvedené tabulce a uveďte předpokládaná skrytá rizika a náklady v horizontu 10–15 let:

| Aspekt životního cyklu | Varianta 1 (Custom Embedded MCU) | Varianta 2 (Průmyslové PLC) |
| :--- | :--- | :--- |
| **Dostupnost náhradních dílů za 10 let** | `...` | `...` |
| **Servisovatelnost podnikovým elektrikářem** | `...` | `...` |
| **Doba odstávky linky při poruše CPU** | `...` | `...` |
| **Cena vývojových nástrojů a licencí IDE** | `...` | `...` |
| **Závěrečné doporučení (kterou variantu vybrat a proč)** | `...` | `...` |

> :key: **Vysvětlení pojmů a odborné zdroje:**
> - **CAPEX (Capital Expenditure)**: Zjednodušeně jde o jednorázové kapitálové výdaje na pořízení samotného zařízení (hardware, licence).
> - **OPEX (Operating Expense)**: Zjednodušeně jde o průběžné provozní náklady nutné k udržení zařízení v chodu (energie, servis, podpora).
> - **TCO (Total Cost of Ownership):** Finanční odhad celkových přímých i nepřímých nákladů spojených s pořízením, provozem, servisem, údržbou a likvidací produktu po celou dobu jeho životnosti. Zjednodušeně je to součet CAPEX + OPEX za celou dobu životnosti zařízení. 
> 	Total cost of ownership. *Wikipedia: The Free Encyclopedia* [online]. St. Petersburg (Florida): Wikimedia Foundation, 2024, 2024-08-14 [cit. 2026-09-17]. Dostupné z: https://en.wikipedia.org/wiki/Total_cost_of_ownership
> - **Vendor Lock-in:** Stav závislosti zákazníka na konkrétním dodavateli produktů nebo služeb, kdy je přechod k jiné platformě spojen s neúměrně vysokými finančními i časovými náklady.

<details>
<summary> :bulb: Tip k úvaze o TCO: </summary>
<p>Když za 7 let odejde custom deska z Varianty 1 a původní vývojář již ve firmě nepracuje a čip se nevyrábí, musí firma vyvinout celou řídicí elektroniku znovu od nuly. Hodina odstávky automobilové linky přitom stojí desítky až stovky tisíc korun.</p>
</details>

:star2: **Bonusová otázka k úloze 6:**
Co znamená pojem **MTBF (Mean Time Between Failures)** v datasheetech průmyslových řídicích jednotek a jaký vliv má okolní teplota v rozváděči na tuto hodnotu (tzv. Arrheniovo pravidlo)?

*Vaše odpověď:*
`...`
