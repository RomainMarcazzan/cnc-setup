# 📦 BOM - Bill of Materials CNC Raw Inventive

Liste complète du matériel nécessaire pour la construction de la CNC bois 3 axes.

## 📐 Dimensions de la CNC

| Axe | Dimension | Note |
|-----|-----------|------|
| **X** | ~1220mm | Longueur standard |
| **Y** | ~1220mm | **Divisé par 2 vs doc originale** (2440mm → 1220mm) |
| **Z** | ~60-80mm | Course standard |

> Format final : **~1200×1200mm** (carré) au lieu de 1220×2440mm (original)

> **Légende** : ✅ Reçu | ⚠️ Reçu (écart de spec à vérifier) | ❌ Non commandé | ❓ Commande en attente de détails

---

## 🎛️ Électronique

| Fait | Qté | Article | Prix payé | N° Commande | Notes |
|------|-----|---------|-----------|-------------|-------|
| - [x] | 1x | **MKS DLC32 MAX** | €19.37 | #3070968990253723 | ✅ Reçu |
| - [x] | 4x | NEMA 23 2.2N 82mm (arbre **8mm**) | inclus kit | #3070968990313723 | ✅ Kit HLTNC 4 sets - Shaft 8mm confirmé |
| - [x] | 1x | Alimentation 350W 36V | inclus kit | #3070968990313723 | ✅ Inclus dans kit HLTNC |
| - [x] | 4x | Drivers TB6600 | €190.48 (kit total) | #3070968990313723 | ✅ Kit HLTNC : 4x NEMA23 + 4x TB6600 + PSU 36V + carte USB |
| - [ ] | 14x | Caps jumpers | - | ❌ Non commandé | Probablement inclus avec TB6600 - vérifier |

---

## 🔩 Plaques MDF

| Fait | Qté | Article | Prix payé | N° Commande | Notes |
|------|-----|---------|-----------|-------------|-------|
| - [ ] | 1 | MDF 12mm 1220x2440mm | - | ❌ Achat local | **Dimension CNC: 1220x1220mm** (Y divisé par 2 vs doc originale) |
| - [ ] | 1 | Papier pour templates/pochoirs | - | ❌ Achat local | Bureau/loisirs créatifs |

---

## 🏗️ Structure métallique

| Fait | Qté | Article | Prix payé | N° Commande | Notes |
|------|-----|---------|-----------|-------------|-------|
| - [ ] | ~8m | Cornière aluminium 20x20x3mm | - | ❌ Non commandé | **Dimension CNC 1220x1220mm** - ajusté pour format réduit |
| - [x] | 1x | Profilé aluminium 20x60mm - 300mm | €17.78 | #3070968990213723 | ✅ Axe Z |

---

## ⚙️ Transmission

**Configuration axes :**
- **X** : 1× courroie HTD 3M (fixée aux extrémités, chariot se déplace)
- **Y** : 2× courroies HTD 3M (double moteur, une de chaque côté)
- **Z** : Vis T8 lead 8mm (pas de courroie)

**📏 Calcul longueur courroie (courroie ouverte/fixée aux extrémités) :**

| Axe | Course | Longueur nécessaire |
|-----|--------|---------------------|
| **X** | 1220mm | ~1.35m (course + marge pour poulies moteur/roulements) |
| **Y** | 1220mm | ~2.7m (2 côtés × ~1.35m) |
| **TOTAL** | | **~4m** |

> ✅ **5m reçus > 4m nécessaires = SUFFISANT** (marge ~1m incluse)

| Fait | Qté | Article | Prix payé | N° Commande | Notes |
|------|-----|---------|-----------|-------------|-------|
| - [x] | 24x | Roulements à V-groove (Delrin/POM) | €17.84 | #3070968990293723 | ✅ 24 reçus, 20 nécessaires |
| - [x] | 20x | Roulements 608-ZZ (8x22x7mm) | €6.71 + €9.75 = €16.46 | #3071365197063723 + #3071365197083723 | ✅ 2 commandes = 20 pièces, 12 nécessaires |
| - [x] | 1x | Vis trapézoïdale T8 **300mm, pitch 2mm, lead 8mm** + écrou | €4.69 | #3070968990233723 | ✅ Lead 8mm → Steps/mm = 200 (à configurer GRBL $102) |
| - [x] | 1x | Écrou anti-backlash POM T8 **lead 8mm** | €4.12 | #3070968990193723 | ✅ Compatible vis 8mm lead |
| - [x] | 2x | Roulements de butée KFL08 8mm | €5.26 | #3070968990353723 | ✅ Support vis Z |
| - [x] | 1x | Couplage souple D25L35 **8x8mm** | €6.11 | #3070968990173723 | ✅ Compatible shaft moteur 8mm |
| - [x] | 5m | Courroie HTD 3M **PU renfort acier**, largeur 15mm | €12.60 | #3070968990273723 | ✅ **5m reçus — SUFFISANT** pour format 1220x1220mm (besoin ~4m) |
| - [x] | 5x | Poulies 15 dents HTD 3M, largeur 15mm **alésage 8mm** | €10.28 | #3070968990333723 | ✅ Compatible shaft moteur 8mm |

---

## 🔪 Outils de coupe

| Fait | Qté | Article | Prix payé | N° Commande | Notes |
|------|-----|---------|-----------|-------------|-------|
| - [x] | 1x | Défonceuse 800W 30,000 tr/min, pince 6.35mm, 220V EU | €63.00 | #3070655448253723 | ✅ |
| - [x] | 1x | Fraise ARDEN Ø3mm `1/4x3`, 2 cannelures, carbure, L=45mm | €5.29 | #3070655448333723 | ✅ **Reçue** - Tige 6.35mm |
| - [x] | 1x | Fraise ARDEN Ø6mm `1/4x6`, 2 cannelures, carbure, L=57mm | €5.29 | #3070655448333723 | ✅ **Reçue** - Tige 6.35mm |
| - [x] | 1x | Collier défonceuse Ø65mm | €20.44 | #3070655448313723 | ✅ Fixation axe Z |

---

## 🔒 Interrupteurs de fin de course (OBLIGATOIRE)

| Fait | Qté | Article | Prix payé | N° Commande | Notes |
|------|-----|---------|-----------|-------------|-------|
| - [ ] | 6x | Micro-switchs 3 broches (endstop) | - | ❌ Non commandé | **URGENT** — requis avant tests moteurs |
| - [ ] | 1x | Câble 22-24AWG multi-conducteur | - | ❌ Non commandé | |
| - [ ] | Option | Connecteurs JST 2.54mm | - | ❌ Non commandé | |

---

## 🔧 Quincaillerie détaillée

### Visserie M5 (Kit reçu complet)

| Fait | Qté | Article | Prix payé | N° Commande | Notes |
|------|-----|---------|-----------|-------------|-------|
| - [x] | 20x | M5 x 6mm | inclus kit | #3070655448383723 | ✅ Kit M5 reçu |
| - [x] | 20x | M5 x 8mm | inclus kit | #3070655448383723 | ✅ |
| - [x] | 20x | M5 x 10mm | inclus kit | #3070655448383723 | ✅ |
| - [x] | 20x | M5 x 12mm | inclus kit | #3070655448383723 | ✅ |
| - [x] | 10x | M5 x 16mm | inclus kit | #3070655448383723 | ✅ |
| - [x] | 10x | M5 x 20mm | inclus kit | #3070655448383723 | ✅ |
| - [x] | 10x | M5 x 25mm | inclus kit | #3070655448383723 | ✅ |
| - [x] | 10x | M5 x 30mm | inclus kit | #3070655448383723 | ✅ |
| - [x] | 120x | Écrous M5 | inclus kit | #3070655448383723 | ✅ |
| - [x] | 40x | Écrous en T M5 | €9.51 | #3071365197103723 | ✅ |
| - [x] | ~230x | **Kit rondelles complètes** | €14.39 | #3070655448363723 | ✅ M4×12(40) + M4×16(30) + M5×12(40) + M5×16(30) + M6×16(30) + M6×18(20) + M6×20(20) + M8×20(10) + M8×25(10) |

### Visserie M6 (Kit reçu complet)

| Fait | Qté | Article | Prix payé | N° Commande | Notes |
|------|-----|---------|-----------|-------------|-------|
| - [x] | 10x | M6 x 8mm | inclus kit | #3070655448383723 | ✅ Kit M6 reçu |
| - [x] | 10x | M6 x 10mm | inclus kit | #3070655448383723 | ✅ |
| - [x] | 10x | M6 x 12mm | inclus kit | #3070655448383723 | ✅ |
| - [x] | 10x | M6 x 14mm | inclus kit | #3070655448383723 | ✅ |
| - [x] | 10x | M6 x 20mm | inclus kit | #3070655448383723 | ✅ |
| - [x] | 10x | M6 x 25mm | inclus kit | #3070655448383723 | ✅ |
| - [x] | 10x | M6 x 30mm | inclus kit | #3070655448383723 | ✅ |
| - [x] | 70x | Écrous M6 | inclus kit | #3070655448383723 | ✅ |

### Visserie M8

| Fait | Qté | Article | Prix payé | N° Commande | Notes |
|------|-----|---------|-----------|-------------|-------|
| - [ ] | 8x | M8 x 40mm | - | ❌ Non commandé | 🟡 À acheter — pas dans le kit reçu |
| - [ ] | 8x | Écrous M8 | - | ❌ Non commandé | 🟡 À acheter — pas dans le kit reçu |
| - [x] | 10x | Rondelles M8 Ø25mm | inclus kit | #3070655448363723 | ✅ Rondelles uniquement reçues |
| - [x] | 10x | Rondelles M8 Ø20mm | inclus kit | #3070655448363723 | ✅ Rondelles uniquement reçues |

### Autre quincaillerie

| Fait | Qté | Article | Prix payé | N° Commande |
|------|-----|---------|-----------|-------------|
| - [ ] | 24x | Vis à bois 10mm (fixation cornières) | - | ❌ Non commandé |
| - [ ] | 18x | Entretoises 35mm (renforts optionnels latéraux) | - | ❌ Non commandé |
| - [ ] | 18x | Vis M5 x 65mm (pour renforts) | - | ❌ Non commandé |
| - [ ] | 18x | Écrous M5 (pour renforts) | - | ❌ Non commandé |

---

## 🧰 Accessoires

| Fait | Qté | Article | Prix payé | N° Commande | Notes |
|------|-----|---------|-----------|-------------|-------|
| - [ ] | 2m | Chaîne porte-câbles 15x30 | - | ❌ Non commandé | Protection câbles |
| - [x] | 10x | Connecteurs d'angle profilé 2028 | €9.16 | #3071365197043723 | ✅ Équivalent équerres alu |
| - [ ] | 6x | Plaques de fixation trouées 20x40 | - | ❌ Non commandé | Renforts |
| - [ ] | 1x | Silicone/colle | - | ❌ Achat local | Assemblage MDF |

---

## 🔌 Câblage

| Fait | Qté | Article | Prix payé | N° Commande | Notes |
|------|-----|---------|-----------|-------------|-------|
| - [ ] | 10-14m | Câble CAT6 ou 4 conducteurs 20 AWG | - | ❌ Non commandé | Pour moteurs |
| - [ ] | 1x | Câble USB-C vers USB-B | - | ❌ Non commandé | Pour Mac |

---

## 🛍️ Commandes supplémentaires (hors BOM original)

Ces articles ont été commandés mais n'étaient pas dans le BOM initial.

| Fait | Article | Prix payé | N° Commande | Notes |
|------|---------|-----------|-------------|-------|
| - [x] | Colonnes d'espacement 40mm NEMA23, 8 pièces | €6.55 | #3070655448293723 | Supports de montage moteurs |
| - [x] | Câbles Dupont M-M 40pcs 20cm | €3.85 | #3070655448273723 | Câblage / connexions |
| - [❓] | **Commande Big Big Store** | €51.32 | #3070655448383723 | ❓ **Détails manquants** — merci de préciser les articles |
| - [x] | **Commande YYS Tool Store** | €12.22 | #3070655448333723 | ✅ **Fraises ARDEN 6.35x3 et 6.35x6** — reçues |

---

## ⚠️ Points d'attention — Écarts de specs à vérifier

| Problème | Commandé | BOM requis | Action |
|----------|----------|------------|--------|
| **Vis Z** | 300mm | 350mm | Vérifier si 300mm suffit selon montage de l'axe Z sur la mécanique |
| **Courroie** | 5m | 7m | **Commander 2m supplémentaires** pour être à l'aise |

---

## 💰 Total AliExpress commandé

| N° Commande | Fournisseur | Article | Montant payé |
|-------------|-------------|---------|-------------|
| #3071365197043723 | CCHT Store | Connecteurs angle profilé 2028 (10pcs) | €9.16 |
| #3071365197063723 | Jarhead Official Store | Roulements 608ZZ (10pcs) | €6.71 |
| #3071365197083723 | Evie Store | Roulements 608ZZ (10pcs) | €9.75 |
| #3071365197103723 | DIY Tools Hardware Store | Écrous en T M5 (40pk) | €9.51 |
| #3070968990173723 | GTRIC Factory Store | Couplage souple D25L35 8x8mm | €6.11 |
| #3070968990353723 | Boutique Roulements pandas | KFL08 roulements billes 8mm (2pcs) | €5.26 |
| #3070968990193723 | Maecoom Store | Écrou anti-backlash POM T8 | €4.12 |
| #3070968990213723 | CCHT Store | Profilé alu 20x60mm 300mm | €17.78 |
| #3070968990233723 | Maccurat Store | Vis T8 300mm + écrou | €4.69 |
| #3070968990333723 | KTE Transmission | Poulies 15T HTD 3M (5pcs) bore 8mm | €10.28 |
| #3070968990253723 | singasong Official Store | MKS DLC32 MAX | €19.37 |
| #3070968990273723 | JHong Transmission Store | Courroie HTD 3M 5m largeur 15mm | €12.60 |
| #3070968990293723 | Victory 3D Store | Roulements V-groove POM (24pcs) | €17.84 |
| #3070968990313723 | HLTNC Official Store | Kit 4x NEMA23 + 4x TB6600 + PSU 36V | €190.48 |
| #3070655448253723 | JZLTOOL Store | Défonceuse 800W 220V EU | €63.00 |
| #3070655448273723 | Quality supplier Store | Câbles Dupont M-M 40pcs | €3.85 |
| #3070655448293723 | PUO Store | Colonnes espacement NEMA23 40mm (8pcs) | €6.55 |
| #3070655448363723 | Shop1105319999 | Rondelles acier 230pcs M4-M8 | €14.39 |
| #3070655448383723 | Big Big Store | **Kit visserie M5 (110pcs) + M6 (70pcs) + écrous** | €51.32 |
| #3070655448313723 | Shop1105344692 | Collier broche 65mm | €20.44 |
| #3070655448333723 | YYS Tool Store | Fraises ARDEN 6.35x3 + 6.35x6 | €12.22 |
| | | **TOTAL AliExpress** | **€515.43** |

---

## 💰 Taxes et frais de douane

| Colis | Montant | Transporteur | Note |
|-------|---------|--------------|------|
| Colis 1 | €25.00 | Chronopost | Taxe douane colis #1 |
| Colis 2 | €17.00 | Chronopost | Taxe douane colis #2 |
| Colis 3 | €20.00 | Chronopost | Taxe douane colis #3 |
| Colis 4 | €17.00 | Chronopost | Taxe douane colis #4 |
| Kit moteurs/drivers/alim | €41.64 | FedEx | Taxe douane kit HLTNC (#3070968990313723) |
| | **TOTAL taxes** | | **€120.64** |

---

## 💵 Récapitulatif total des coûts

| Catégorie | Montant |
|-----------|---------|
| Matériel AliExpress | €515.43 |
| Taxes douane (5 colis : 4×Chronopost + 1×FedEx) | €120.64 |
| **Total dépensé** | **€636.07** |
| Estimation achats restants | ~€75-100 |
| **Budget total projet** | **~€710-740** |

---

## 🛒 Encore à acheter

| Priorité | Article | Estimation |
|----------|---------|------------|
| 🔴 URGENT | 6x Interrupteurs fin de course (endstop) | ~10€ |
| 🟡 Important | Visserie M8 (boulons + écrous) | ~8€ |

| 🟢 Quand prêt | ~8m Cornière alu 20x20x3mm | ~20€ local |
| 🟢 Quand prêt | 1x Panneau MDF 12mm 1220x2440 | ~20€ local |
| 🟢 Quand prêt | Chaîne porte-câbles 15x30 (2m) | ~10€ |
| 🟢 Quand prêt | Câble moteur 10-14m | ~15€ |
