# ⚡ Riferimento Rapido - D&D 5e

Questa guida contiene le tabelle e formule più utilizzate durante una sessione di gioco.

## 🎲 Meccaniche Base

### Tiro Standard
```
1d20 + Modificatore + (Bonus Competenza se competente)
```

**Successo**: Risultato ≥ Classe Difficoltà (CD)

### Modificatori dalle Caratteristiche
```
(Punteggio - 10) / 2 (arrotonda per difetto)
```

| Punteggio | Modificatore | Punteggio | Modificatore |
|-----------|--------------|-----------|--------------|
| 1 | -5 | 12-13 | +1 |
| 2-3 | -4 | 14-15 | +2 |
| 4-5 | -3 | 16-17 | +3 |
| 6-7 | -2 | 18-19 | +4 |
| 8-9 | -1 | 20-21 | +5 |
| 10-11 | +0 | 22-23 | +6 |

### Bonus di Competenza per Livello

| Livello | Bonus | Livello | Bonus |
|---------|-------|---------|-------|
| 1-4 | +2 | 13-16 | +5 |
| 5-8 | +3 | 17-20 | +6 |
| 9-12 | +4 | | |

---

## ⚔️ Combattimento

### Sequenza del Turno
1. **Iniziativa**: 1d20 + modificatore DES (tira 1 volta all'inizio)
2. **Nel tuo turno**: Movimento + Azione + (eventuale Azione Bonus)
3. **Reazione**: 1 per round (nel turno di chiunque)

### Azioni in Combattimento

| Azione | Descrizione |
|--------|-------------|
| **Attacco** | Attacca con arma o incantesimo |
| **Lanciare Incantesimo** | Lancia un incantesimo (tempo 1 azione) |
| **Scatto** | Muovi il doppio della velocità |
| **Disimpegno** | Movimento senza attacchi di opportunità |
| **Schivare** | Attacchi contro di te con svantaggio |
| **Aiutare** | Dai vantaggio a un alleato |
| **Nascondersi** | Prova DES (Furtività) vs Percezione passiva |
| **Preparare** | Prepara azione con trigger |
| **Cercare** | Prova SAG (Percezione) o INT (Indagare) |
| **Usare Oggetto** | Interagisci con un oggetto |

### Attacco con Arma

**Tiro per Colpire**:
```
1d20 + Modificatore caratteristica + Bonus competenza
```

**Per armi in mischia**: Usa FOR (o DES per armi accurate)
**Per armi a distanza**: Usa DES

**Se ≥ CA del bersaglio → Colpo!**

**Danni**:
```
Dado danno arma + Modificatore caratteristica
```

### Colpo Critico
**Naturale 20 sul d20**: 
- Colpisci automaticamente
- Tira tutti i dadi danno due volte
- Aggiungi modificatore una volta sola

**Esempio**: Spada lunga (1d8+3)
- Critico: 2d8 + 3

### Copertura

| Tipo | Bonus CA | Bonus TS DES |
|------|----------|--------------|
| Mezza copertura | +2 | +2 |
| Tre quarti | +5 | +5 |
| Copertura totale | Non colpibile | Non colpibile |

### Condizioni Comuni

| Condizione | Effetto Principale |
|------------|-------------------|
| **Accecato** | Svantaggio attacchi, vantaggio vs te |
| **Affascinato** | Non può attaccare il charmer, svantaggio abilità |
| **Afferrato** | Velocità 0, svantaggio attacchi a distanza |
| **Invisibile** | Vantaggio attacchi, svantaggio vs te |
| **Privo di sensi** | Incapacitato, attacchi auto-critici se vicini |
| **Prono** | Svantaggio attacchi, vantaggio vs te in mischia |
| **Spaventato** | Svantaggio se vedi la fonte |
| **Stordito** | Non può muoversi/agire/reagire, auto fallisci TS FOR/DES |

---

## 🎯 Difficoltà (CD)

| CD | Difficoltà | Esempio |
|----|------------|---------|
| 5 | Molto facile | Saltare un ruscello stretto |
| 10 | Facile | Scalare una parete con appigli |
| 15 | Media | Scassinare una serratura standard |
| 20 | Difficile | Scalare muro liscio con attrezzi |
| 25 | Molto difficile | Nuotare in una cascata |
| 30 | Quasi impossibile | Convincere il drago a darti il tesoro |

---

## ⚡ Vantaggio e Svantaggio

### Vantaggio (tira 2d20, prendi il più alto)
- Aiuto di un alleato
- Attaccare nemico prono (in mischia)
- Attaccare nemico che non ti vede
- Abilità razziali specifiche

### Svantaggio (tira 2d20, prendi il più basso)
- Attaccare nemico che non vedi
- Attaccare a distanza nemico vicino
- Essere proni e attaccare
- Attaccare oltre gittata lunga

**NOTA**: Vantaggio e svantaggio si annullano sempre (anche multipli)

---

## 💊 Guarigione e Danni

### Punti Ferita a 0
1. Cadi privo di sensi
2. Ogni turno: Tiro salvezza morte (CD 10)
   - ≥10: Successo (3 successi = stabilizzato)
   - <10: Fallimento (3 fallimenti = morte)
   - 1 naturale: 2 fallimenti
   - 20 naturale: Recuperi 1 PF

### Morte Istantanea
Se i danni in eccesso a 0 PF ≥ PF massimi → **Morte istantanea**

**Esempio**: 10 PF max, 5 PF attuali, subisci 20 danni
- Scendi a 0, eccesso 15
- 15 ≥ 10 → Morte istantanea

### Riposi

**Riposo Breve** (1+ ore):
- Spendi Dadi Vita per recuperare PF
- Tira dado + mod COS per dado speso
- Recuperi alcune abilità di classe

**Riposo Lungo** (8 ore):
- Recuperi tutti i PF
- Recuperi metà dei Dadi Vita (minimo 1)
- Recuperi tutte le abilità di classe
- 1 riposo lungo per 24 ore

---

## 📏 Distanze e Movimento

### Conversione
- **1 quadretto** = 1,5 metri = 5 piedi
- **Velocità media** = 9 metri = 30 piedi = 6 quadretti

### Gittate Armi Comuni

| Arma | Gittata Normale | Gittata Lunga |
|------|-----------------|---------------|
| Arco corto | 24m | 96m |
| Arco lungo | 45m | 180m |
| Balestra leggera | 24m | 96m |
| Balestra pesante | 30m | 120m |
| Pugnale | 6m | 18m |
| Giavellotto | 9m | 36m |

**Oltre gittata normale**: Svantaggio all'attacco
**Oltre gittata lunga**: Non puoi attaccare

### Movimento in Combattimento

| Terreno | Costo |
|---------|-------|
| Normale | 1,5m per 1,5m |
| Difficile | 3m per 1,5m |
| Nuotare/Scalare | 3m per 1,5m (se no velocità specifica) |
| Prono → In piedi | Metà velocità |
| Montatura | Usa velocità della montatura |

---

## 🔮 Magia

### Lanciare un Incantesimo

**CD Tiro Salvezza**:
```
8 + Bonus competenza + Modificatore caratteristica da incantatore
```

**Bonus Attacco Incantesimo**:
```
Bonus competenza + Modificatore caratteristica da incantatore
```

### Caratteristiche da Incantatore

| Classe | Caratteristica |
|--------|----------------|
| Bardo, Paladino, Stregone, Warlock | Carisma |
| Chierico, Druido, Ranger | Saggezza |
| Mago | Intelligenza |

### Slot Incantesimo per Livello (Classi Complete)

| Livello PG | 1° | 2° | 3° | 4° | 5° | 6° | 7° | 8° | 9° |
|------------|----|----|----|----|----|----|----|----|---|
| 1 | 2 | - | - | - | - | - | - | - | - |
| 2 | 3 | - | - | - | - | - | - | - | - |
| 3 | 4 | 2 | - | - | - | - | - | - | - |
| 4 | 4 | 3 | - | - | - | - | - | - | - |
| 5 | 4 | 3 | 2 | - | - | - | - | - | - |
| 6 | 4 | 3 | 3 | - | - | - | - | - | - |
| 7 | 4 | 3 | 3 | 1 | - | - | - | - | - |
| 8 | 4 | 3 | 3 | 2 | - | - | - | - | - |
| 9 | 4 | 3 | 3 | 3 | 1 | - | - | - | - |
| 10 | 4 | 3 | 3 | 3 | 2 | - | - | - | - |

### Concentrazione
- Molti incantesimi richiedono concentrazione
- Solo 1 incantesimo di concentrazione alla volta
- Se prendi danno: TS COS (CD 10 o metà danno, il più alto)
- Se fallisci: Incantesimo termina

### Incantesimi Rituali
- Tempo +10 minuti
- Non consuma slot incantesimo
- Devi avere l'incantesimo preparato (o nel grimorio per Maghi)

---

## 💰 Equipaggiamento

### Prezzi Comuni

| Oggetto | Costo |
|---------|-------|
| Pozione Guarigione (2d4+2) | 50 mo |
| Antidoto | 50 mo |
| Torcia | 1 mr |
| Corda (15m) | 1 mo |
| Razioni (1 giorno) | 5 ma |
| Otre | 2 ma |
| Sacco a pelo | 1 mo |
| Kit Guaritore | 5 mo |
| Attrezzi Scasso | 25 mo |

### Valuta

| Moneta | Valore |
|--------|--------|
| 1 mo (oro) | = 10 ma (argento) |
| 1 ma (argento) | = 10 mr (rame) |
| 1 mo | = 100 mr |

**Conversione**: 1 mo = 10 ma = 100 mr

### Armature Comuni

| Armatura | CA | Tipo | Costo |
|----------|----|----- |-------|
| Imbottita | 11 + DES | Leggera | 5 mo |
| Cuoio | 11 + DES | Leggera | 10 mo |
| Corazza di pelle | 12 + DES (max 2) | Media | 10 mo |
| Cotta di maglia | 13 + DES (max 2) | Media | 50 mo |
| Corazza di scaglie | 14 + DES (max 2) | Media | 50 mo |
| Piastre | 14 + DES (max 2) | Media | 400 mo |
| Cotta di maglia | 16 | Pesante | 75 mo |
| Corazza di piastre | 18 | Pesante | 1.500 mo |
| Scudo | +2 CA | - | 10 mo |

### Armi Comuni

| Arma | Danno | Proprietà | Costo |
|------|-------|-----------|-------|
| Bastone | 1d6 cont. | Versatile (1d8) | 2 ma |
| Pugnale | 1d4 perf. | Accurata, leggera, lancio 6/18m | 2 mo |
| Randello | 1d4 cont. | Leggera | 1 ma |
| Spada corta | 1d6 perf. | Accurata, leggera | 10 mo |
| Spada lunga | 1d8 tagl. | Versatile (1d10) | 15 mo |
| Ascia grande | 1d12 tagl. | Pesante, due mani | 30 mo |
| Arco corto | 1d6 perf. | Due mani, munizioni 24/96m | 25 mo |
| Arco lungo | 1d8 perf. | Pesante, due mani, mun. 45/180m | 50 mo |

---

## 📊 Tabelle Esperienza

### PE per Livello

| Livello | PE Totali | Bonus Comp. |
|---------|-----------|-------------|
| 1 | 0 | +2 |
| 2 | 300 | +2 |
| 3 | 900 | +2 |
| 4 | 2.700 | +2 |
| 5 | 6.500 | +3 |
| 6 | 14.000 | +3 |
| 7 | 23.000 | +3 |
| 8 | 34.000 | +3 |
| 9 | 48.000 | +4 |
| 10 | 64.000 | +4 |

### PE per Grado Sfida (CR)

| CR | PE | Difficoltà vs Livello 1 |
|----|-----|------------------------|
| 0 | 10 | Banale |
| 1/8 | 25 | Facile |
| 1/4 | 50 | Facile |
| 1/2 | 100 | Media |
| 1 | 200 | Difficile |
| 2 | 450 | Pericoloso |
| 3 | 700 | Mortale |
| 4 | 1.100 | - |
| 5 | 1.800 | - |

---

## 🎭 Condizioni di Gioco

### Illuminazione

| Tipo | Effetto |
|------|---------|
| **Luce intensa** | Visione normale |
| **Luce fioca** | Lievemente oscurato (svantaggio Percezione) |
| **Oscurità** | Pesantemente oscurato (effettivamente cieco) |

### Visione

| Tipo | Descrizione |
|------|-------------|
| **Visione normale** | Dipende dall'illuminazione |
| **Scurovisione** | In oscurità vedi come luce fioca (b/n) |
| **Visione cieca** | Percepisci senza vedere (raggi, ultrasuoni) |
| **Percezione delle vibrazioni** | Rilevi movimento a terra entro raggio |

---

## ⏱️ Tempo

| Unità | Durata |
|-------|--------|
| Round | 6 secondi |
| Minuto | 10 round |
| Turno | 10 minuti (esplorazione) |
| Ora corta | 6 turni = 1 ora |
| Riposo breve | 1+ ore |
| Riposo lungo | 8 ore |
| Giorno | 24 ore |

---

## 💡 Consigli Rapidi

### Per Giocatori
1. **Conosci le tue abilità**: Leggi la tua scheda prima della sessione
2. **Preparati**: Decidi cosa fare prima del tuo turno
3. **Comunica**: Descrivi cosa vuoi fare, il DM ti dirà come
4. **Prendi note**: Nomi, luoghi, missioni importanti
5. **Lavora in squadra**: D&D è cooperativo

### Per DM
1. **Regola d'oro**: Divertimento > Regole perfette
2. **CD al volo**: 10 facile, 15 medio, 20 difficile
3. **Sì, e...**: Accetta idee creative dei giocatori
4. **Mantieni il ritmo**: Non fermarti troppo su singole regole
5. **Bilancia**: Alterna combattimento, esplorazione, roleplay

---

## 🔍 Cerca Veloce

**Quanto sono i danni di un incantesimo?** → Vedi descrizione incantesimo
**Quanti slot ho?** → Vedi tabella slot per livello classe
**Come funziona concentrazione?** → Vedi sezione Magia
**Quali azioni posso fare?** → Vedi Azioni in Combattimento
**A che CD tirare?** → Vedi tabella Difficoltà (CD)
**Come calcolo attacco?** → 1d20 + mod. caratteristica + bonus comp.

---

*Ricorda: Quando hai dubbi, chiedi al DM. Le regole servono a rendere il gioco divertente, non a rallentarlo!* 🎲
