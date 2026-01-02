# 🚀 Come Creare la Repository su GitHub

## Metodo 1: Via Browser (Consigliato per principianti)

### Step 1: Crea la Repository
1. Vai su **https://github.com/new**
2. Compila i campi:
   - **Repository name**: `sotterranei-e-draghi`
   - **Description**: `Guida completa a Dungeons & Dragons 5e (2024) in italiano`
   - Visibilità: **Public** ✅
   - ❌ **NON** selezionare "Add a README file"
   - ❌ **NON** selezionare ".gitignore"
   - License: **MIT License** ✅
3. Clicca **"Create repository"**

### Step 2: Scarica i File del Progetto
Scarica questi file che ti ho preparato:
- `README.md` (descrizione progetto)
- `guida_dnd_neofiti.md` (guida principale)
- `LICENSE` (licenza MIT)
- `.gitignore` (file da ignorare)

### Step 3: Carica i File
1. Nella pagina della repository appena creata
2. Clicca **"uploading an existing file"**
3. Trascina i file scaricati
4. Scrivi commit message: `🎲 Initial commit: Guida D&D 5e in italiano`
5. Clicca **"Commit changes"**

✅ **Fatto!** La tua repository è online!

---

## Metodo 2: Via Command Line (Per utenti avanzati)

### Prerequisiti
- Git installato sul tuo computer
- Account GitHub configurato

### Step 1: Crea la Repository su GitHub
1. Vai su **https://github.com/new**
2. Nome: `sotterranei-e-draghi`
3. **NON** inizializzare con README
4. Crea repository

### Step 2: Comandi nel Terminal

```bash
# Naviga nella cartella dove hai scaricato i file
cd /path/to/cartella-progetto

# Inizializza Git
git init

# Aggiungi tutti i file
git add .

# Primo commit
git commit -m "🎲 Initial commit: Guida D&D 5e in italiano"

# Collega alla repository GitHub (SOSTITUISCI 'TUO_USERNAME')
git branch -M main
git remote add origin https://github.com/TUO_USERNAME/sotterranei-e-draghi.git

# Push su GitHub
git push -u origin main
```

### Step 3: Verifica
Vai su `https://github.com/TUO_USERNAME/sotterranei-e-draghi` e verifica che tutti i file siano stati caricati.

---

## 🌐 Bonus: Attiva GitHub Pages

Per pubblicare la guida come sito web:

1. Vai su **Settings** della repository
2. Nel menu laterale clicca **Pages**
3. In "Build and deployment":
   - Source: **Deploy from a branch**
   - Branch: **main** e cartella **/ (root)**
4. Clicca **Save**

Dopo qualche minuto, la tua guida sarà disponibile su:
**`https://TUO_USERNAME.github.io/sotterranei-e-draghi/`**

---

## 📱 Crea un Sito Web Vero e Proprio

Vuoi trasformare la guida in un sito web interattivo?

### Opzione A: GitHub Pages con Jekyll (semplice)
```bash
# Aggiungi un file _config.yml
echo "theme: jekyll-theme-cayman" > _config.yml
git add _config.yml
git commit -m "Aggiungi tema Jekyll"
git push
```

### Opzione B: React App (avanzato)
Se vuoi creare un'app React professionale, posso aiutarti a:
- Impostare un progetto Create React App o Next.js
- Creare componenti interattivi (generatore personaggi, calcolatore danni, ecc.)
- Aggiungere routing e navigazione
- Stilizzare con Tailwind CSS

---

## ❓ Serve Aiuto?

Se hai problemi con uno qualsiasi di questi passaggi, fammi sapere e ti guido più nel dettaglio!

## 🎯 Prossimi Passi Consigliati

1. ✅ Crea la repository
2. ✅ Carica i file
3. 🌐 Attiva GitHub Pages
4. 📝 Aggiungi un CONTRIBUTING.md per spiegare come contribuire
5. 🏷️ Crea il primo "release" (v1.0.0)
6. 🎨 Personalizza il README con screenshot
7. 📱 Sviluppa features interattive (opzionale)
