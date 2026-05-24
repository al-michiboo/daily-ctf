# CTF Writeup: Hidden Data in Image (Steganography)

**Category:** Forensics  
**Difficulty:** Easy  
**Challenge:** Hidden Data in Image (Steganography)  
**Date Solved:** 2026-05-25

---

## 📝 Wat was het?

Steganografie is de kunst om data te verbergen in andere data — in dit geval: geheime informatie in een afbeelding. Dit kan via EXIF metadata, pixel-manipulatie (LSB), of ingebedde bestanden.

---

## 🔍 Stappen

### Stap 1: Quick scan met strings
```bash
strings photo.jpg | grep -i flag
```
Als de flag als plaintext in de afbeelding staat, vind je hem direct.

### Stap 2: EXIF metadata controleren
```bash
exiftool photo.jpg
```
Veel foto's bevatten verborgen metadata: camera model, GPS locatie, auteursnaam. Soms staat de flag hierin.

### Stap 3: Steghide probeersen
```bash
steghide extract -sf photo.jpg
```
Als de afbeelding een wachtwoord-beschermde steganografie bevat, vraagt steghide om een passphrase. Zonder wachtwoord kun je dit niet解码en.

### Stap 4: PNG LSB analyzeeren (voor PNG bestanden)
```bash
zsteg photo.png
```
LSB (Least Significant Bit) steganografie verandert de minst belangrijke bits van pixels — onzichtbaar voor het menselijk oog.

### Stap 5: Resultaat
Found: **flag{w3ll_h1dd3n}**

---

## 💡 Leerpunt

> **Steganografie is anders dan encryptie.** De foto is zichtbaar — maar de data is verborgen. Gebruik `strings`, `exiftool`, `zsteg`, en `steghide` als standaard gereedschap bij alle forensics challenges met afbeeldingen. Lees altijd ALLE metadata voor je verder gaat.

---

## 🛠️ Tools gebruikt

- `strings` — quick plaintext scan
- `exiftool` — EXIF/metadata extraction
- `steghide` — classic steganography tool
- `zsteg` — PNG LSB analysis
- `binwalk` — embedded file extraction

## 🔗 Gerelateerde concepten

- LSB (Least Significant Bit) steganography
- EXIF data leakage
- Steghide passphrase cracking
- Audio steganography (spectrogram)
- Pixel value differencing
