# 🐝 Dokumentace: Včelařství Hymon - Objednávkový systém

**Vytvořeno:** 5. února 2026  
**Projekt:** Webová stránka s objednávkovým formulářem pro Jiřího Hymona

---

## 📋 Co tento systém dělá

- **Webová stránka** s prezentací produktů (med, včelí královny)
- **Objednávkový formulář** - zákazníci vyplní kolik čeho chtějí
- **Automatický zápis do Google Sheets** - všechny objednávky se zapíší do tabulky
- **Automatický email** - při každé objednávce přijde email na `jiri.hymon@gmail.com`

---

## 🌐 Důležité odkazy

| Co | URL |
|---|---|
| **Živý web** | https://jurkis-dev.github.io/bee-project/ |
| **GitHub Repository** | https://github.com/Jurkis-DEV/bee-project |
| **Google Apps Script** | https://script.google.com/u/0/home/projects/1RmXPQ_rjsZxmU4HiPv4Hp6IiSxJYWN7Kc8ByquncGFWfs7aorbE_F6QC/edit |
| **Script URL (webová aplikace)** | https://script.google.com/macros/s/AKfycbwnWm3_aDReTGiJMVRasASioI-NVHUbm9WXvaOZ7irIc2DifOikXPCCPM6fKd5B9w/exec |

---

## 🔧 ČÁST 1: Google Sheets - Tabulka pro objednávky

### Kde: [Google Sheets](https://sheets.google.com)

### Co jsme nastavili:

1. **Vytvořena nová tabulka** (název: dle tvého výběru, např. "Objednávky - Včelařství")

2. **Hlavičky v první řádce:**
   ```
   A1: Datum a čas
   B1: Jméno
   C1: Telefon
   D1: Email
   E1: Počet medu
   F1: Počet královen
   G1: Poznámka
   ```

### ⚠️ Důležité:
- **MUSÍ být přesně tyto hlavičky** - jinak skript nebude fungovat
- Pokud přejmenujete tabulku nebo změníte hlavičky, objednávky se přestanou zapisovat

---

## 🤖 ČÁST 2: Google Apps Script - Zpracování objednávek

### Kde: Rozšíření → Apps Script (v Google Sheets)

### Kód který tam je:

```javascript
function doPost(e) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    const data = JSON.parse(e.postData.contents);
    
    // Přidá řádek s daty
    sheet.appendRow([
      data.timestamp,
      data.name,
      data.phone,
      data.email,
      data.honey,
      data.queens,
      data.note
    ]);
    
    // Pošle email s upozorněním
    const emailBody = `
Nová objednávka!

Jméno: ${data.name}
Telefon: ${data.phone}
Email: ${data.email}
Počet medu: ${data.honey} ks
Počet královen: ${data.queens} ks
Poznámka: ${data.note}
    `;
    
    // DŮLEŽITÉ: Změň email na správný!
    MailApp.sendEmail('jiri.hymon@gmail.com', 'Nová objednávka z webu', emailBody);
    
    return ContentService.createTextOutput(JSON.stringify({result: 'success'}))
      .setMimeType(ContentService.MimeType.JSON);
      
  } catch(error) {
    return ContentService.createTextOutput(JSON.stringify({result: 'error', error: error.toString()}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

### Nastavení nasazení:

1. **Implementovat** → **Nová implementace** → **Webová aplikace**
2. **Nastavení:**
   - **Popis:** "Objednávkový formulář"
   - **Spustit jako:** "Já" (jiri.hymon@gmail.com)
   - **Kdo má přístup:** **"Kdokoli"** ⬅️ VELMI DŮLEŽITÉ!
3. **Oprávnění:** Musí mít povolené:
   - ✅ Přístup k Google Sheets
   - ✅ Posílání emailů

### 🔑 Script URL (Webová aplikace):
```
https://script.google.com/macros/s/AKfycbwnWm3_aDReTGiJMVRasASioI-NVHUbm9WXvaOZ7irIc2DifOikXPCCPM6fKd5B9w/exec
```

**⚠️ Tato URL musí končit na `/exec`!**

---

## 💻 ČÁST 3: Webová stránka (HTML)

### Kde: GitHub - https://github.com/Jurkis-DEV/bee-project

### Soubor: `index.html`

### Důležitá část - propojení s Google Script:

V souboru `index.html` (řádek cca 163) je:
```javascript
const SCRIPT_URL = 'https://script.google.com/macros/s/AKfycbwnWm3_aDReTGiJMVRasASioI-NVHUbm9WXvaOZ7irIc2DifOikXPCCPM6fKd5B9w/exec';
```

**⚠️ Pokud znovu nasadíš Google Apps Script a dostaneš novou URL, musíš ji změnit i tady!**

---

## 🚀 ČÁST 4: GitHub Pages - Hostování webu

### Kde: GitHub → Settings → Pages

### Nastavení:

- **Source (Branch):** `main`
- **Folder:** `/ (root)`

### Živá adresa webu:
```
https://jurkis-dev.github.io/bee-project/
```

**ℹ️ Po každé změně v `index.html` trvá 1-2 minuty, než se web aktualizuje.**

---

## 🔍 ŘEŠENÍ PROBLÉMŮ

### ❌ Problém: "Objednávka se nezapisuje do tabulky"

**Možné příčiny:**

1. **Špatné hlavičky v Google Sheets**
   - Zkontroluj, že hlavičky v řádku 1 jsou přesně: `Datum a čas`, `Jméno`, `Telefon`, atd.
   
2. **Špatná URL v HTML souboru**
   - Zkontroluj, že v `index.html` je správná Script URL
   - URL musí končit na `/exec`
   
3. **Špatná oprávnění Google Apps Script**
   - Jdi do Apps Script → Implementovat → Spravovat implementace
   - Zkontroluj: "Kdo má přístup: **Kdokoli**"
   - Pokud je tam "Pouze já", změň to!

---

### ❌ Problém: "Chyba 403 v konzoli"

**Řešení:**
1. Jdi do Google Apps Script
2. **Implementovat** → **Spravovat implementace**
3. Klikni na **tužku** (upravit) u aktivní implementace
4. Změň **"Kdo má přístup"** na **"Kdokoli"**
5. Klikni **"Nasadit"**
6. Potvrď oprávnění (Google Sheets + Send email)

---

### ❌ Problém: "Email nepřichází"

**Řešení:**
1. Zkontroluj email v Google Apps Script (řádek 30)
2. Zkontroluj SPAM složku
3. Ověř, že při nasazení jsi povolil oprávnění "Send email as you"

---

### ❌ Problém: "Zelená hláška se zobrazí, ale nic se nezapíše"

**Řešení:**
1. Otevři web, stiskni **F12**
2. Klikni na záložku **"Console"**
3. Odešli formulář a zkontroluj chyby (červený text)
4. Pošli mi screenshot konzole

---

## 🔄 JAK AKTUALIZOVAT WEB

### Postup:

1. **Stáhni aktuální HTML:**
   - Jdi na GitHub: https://github.com/Jurkis-DEV/bee-project
   - Klikni na `index.html`
   - Klikni na tlačítko "Raw"
   - Pravé tlačítko → Uložit jako → `index.html`

2. **Udělej změny** v souboru v textovém editoru

3. **Nahraj zpět na GitHub:**
   - GitHub → Bee-Project
   - "Add file" → "Upload files"
   - Přetáhni změněný `index.html`
   - Napiš popis změn (např. "Změna textu")
   - "Commit changes"

4. **Počkej 1-2 minuty** než se web aktualizuje

---

## 📝 KONTAKTNÍ ÚDAJE V SYSTÉMU

### Email pro objednávky:
```
jiri.hymon@gmail.com
```
**Změnit:** Google Apps Script → řádek 30

### Email v patičce webu:
```
info@vcelarstvi.cz
```
**Změnit:** `index.html` → vyhledej "info@vcelarstvi.cz"

---

## 🎨 ČASTÉ ZMĚNY

### Změnit název webu:
- Soubor: `index.html`
- Najdi: `<h1>🐝 Včelařství Hymon</h1>`
- Změň na: `<h1>🐝 Tvůj nový název</h1>`

### Změnit barvy:
- Soubor: `index.html`
- V sekci `<style>` najdi:
  - `background: #d63031` - hlavní červená barva
  - `background: linear-gradient(135deg, #ffeaa7 0%, #fdcb6e 100%)` - žlutý gradient pozadí

### Přidat fotku:
```html
<img src="URL_FOTKY" alt="Popis" style="width: 100%; border-radius: 10px;">
```

---

## 📊 JAK ZKONTROLOVAT, ŽE VŠE FUNGUJE

### Testovací postup:

1. **Otevři web:** https://jurkis-dev.github.io/bee-project/
2. **Vyplň testovací objednávku:**
   - Počet medu: 2
   - Počet královen: 1  
   - Jméno: Test Test
   - Telefon: 123456789
3. **Odešli formulář**
4. **Zkontroluj:**
   - ✅ Zelená hláška "Objednávka byla úspěšně odeslána"
   - ✅ Nový řádek v Google Sheets tabulce
   - ✅ Email s objednávkou přišel na jiri.hymon@gmail.com

---

## 🆘 POTŘEBUJEŠ POMOCT?

### Informace které potřebuji:

1. **Screenshot chyby** (pokud se zobrazuje)
2. **Screenshot konzole:** 
   - Na webu stiskni F12
   - Záložka "Console"
   - Odešli formulář
   - Screenshot červených chyb
3. **Co jsi změnil/a** před tím, než to přestalo fungovat
4. **Odkaz na web** a **Google Sheets tabulku**

---

## 📌 ZÁLOHA

### Pro jistotu si zkopíruj:

✅ **Script URL:** https://script.google.com/macros/s/AKfycbwnWm3_aDReTGiJMVRasASioI-NVHUbm9WXvaOZ7irIc2DifOikXPCCPM6fKd5B9w/exec  
✅ **GitHub repo:** https://github.com/Jurkis-DEV/bee-project  
✅ **Živý web:** https://jurkis-dev.github.io/bee-project/  
✅ **Email:** jiri.hymon@gmail.com

---

**Vytvořeno s pomocí Claude AI - Včelařství Hymon 🐝🍯**