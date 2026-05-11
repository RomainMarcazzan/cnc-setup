# 🛠️ Setup CNC - Documentation

Configuration complète pour la CNC bois 3 axes basée sur le projet **Raw Inventive** avec contrôleur MKS DLC32 MAX.

---

## 📐 Base du projet

Cette CNC est basée sur le projet **Raw Inventive** de [rawcnc.com](https://rawcnc.com). La mécanique et la structure suivent ce design éprouvé, avec une mise à jour électronique moderne (MKS DLC32 MAX).

**Ressources officielles :**

- [Vidéo tutoriel](https://youtu.be/DFU-zd2rKJA) (électronique alternative avec Arduino Mega)
- [Site officiel RawCNC](https://rawcnc.com) - Plaques MDF et hardware kits disponibles

---

## 📦 Matériel

> **📋 Liste complète du matériel avec checkboxes → [BOM.md](BOM.md)**

### Contrôleur

- **Carte** : Makerbase MKS DLC32 MAX 1.0
- **Firmware** : **grblHAL v1.1f** (build 20260506) — compilé depuis [grblHAL Web Builder]()
  - **Y-ganged activé** → Dual Y géré par firmware (connecteurs Y = Y1, A = Y2)
  - Test confirmé : les deux moteurs Y bougent ensemble
- **Détails firmware** → [FIRMWARE.md](FIRMWARE.md)
- **Config EEPROM** → [EEPROM.md](EEPROM.md) (snapshot de la carte)
- **Connectivité** : USB + WiFi + SD card

### Électronique de puissance

- **Moteurs** : NEMA 23 57HS82 — 3.0A, 2.2 N·m, 82mm (x4) - 2x sur Y, 1x X, 1x Z
- **Drivers** : TB6600
- **Alimentation** : 350W 36V

### Broche et outils

- **Défonceuse** : 800W 30,000 tr/min, pince 1/4" (6.35mm), 220V EU
- **Fraises ARDEN** : Ø 3mm (gravure) et Ø 6mm (dégrossissage)

---

## 🔥 État Actuel & Prochaines Étapes

### ✅ Déjà fait (2026-05-11)

| Étape                      | Statut      | Détail                                                                 |
| -------------------------- | ----------- | ---------------------------------------------------------------------- |
| Conception Fusion 360      | ✅ Fait     | 3 opérations, G-code généré                                            |
| Alimentation carte         | ✅ Fait     | Chargeur 19.2V/2A - LED stable                                         |
| Flashage firmware          | ✅ Fait     | grblHAL v1.1f (build 20260506) avec **Y-ganged** activé               |
| Config GRBL EEPROM         | ✅ Fait     | `$100=71.11`, `$101=71.11`, `$102=400` (théorique) → voir [EEPROM.md](EEPROM.md) |
| TB6600 réglés              | ✅ Fait     | 1/16 microstep, 2.8A courant                                           |
| Test Dual Y                | ✅ Fait     | Les deux moteurs Y bougent ensemble (Y=Y1, A=Y2)                      |
| Connexion gSender          | ✅ Fait     | USB/WiFi OK                                                          |
| gSender                    | ✅ Installé | v1.6.0 Apple Silicon natif                                             |
| **Phase 1 Tests électriques** | ✅ **Fait** | Tous les moteurs testés : X, Y1, Y2, Z — sens OK, température OK     |

---

## 📋 Checklist Projet

Par ordre de réalisation (on peut faire des tests moteurs SANS endstops) :

### 🔹 Phase 1 — Tests électriques ✅ COMPLÉTÉ (2026-05-11)

**Peut être fait avant montage mécanique complet**

| Fait | Étape | Détail |
|------|-------|--------|
| ✅ | Tester moteur X | Jog `+10mm`, tourne dans le bon sens, position affichée correctement |
| ✅ | Tester moteur Y1 | Jog Y, Y1 tourne (sens antihoraire sur Y+) |
| ✅ | Tester moteur Y2 | Jog Y, Y2 tourne **en même temps** (Y-ganged confirmé) |
| ✅ | Tester sens des deux Y | Y1 antihoraire, Y2 horaire (sens opposés = normal pour portique) |
| ✅ | Tester moteur Z | Jog `+10mm`, tourne plus vite que X/Y (normal, lead 8mm vs 45mm) |
| ✅ | Vérifier température TB6600 | Tièdes, ventilateurs thermorégulés fonctionnent (s'allument/coupent) |

> **Note sur les endstops :** On peut faire TOUTE cette phase SANS endstops. Ils ne sont utiles que pour le homing (`$H`) et les soft limits.

---

### 🔹 Phase 2 — Montage mécanique

| Fait | Étape | Détail |
|------|-------|--------|
| ⬜ | Structure fer | Assembler les cornières 20x20 selon Raw Inventive |
| ⬜ | Fixer plaques MDF | Percer et fixer les plaques |
| ⬜ | Monter rails/roulements | V-groove et 608ZZ |
| ⬜ | Monter moteurs X/Y/Z | Fixer les NEMA 23 avec les espacements |
| ⬜ | Tendre courroies X/Y | HTD 3M - ni trop lâche, ni trop tendue |
| ⬜ | Monter vis Z | T8 lead 8mm + KFL08 + écrou anti-backlash |

---

### 🔹 Phase 3 — Calibration mécanique (mesure réelle)

| Fait | Étape | Détail |
|------|-------|--------|
| ⬜ | Calibrer X | Jog X+100mm, mesurer, ajuster `$100` |
| ⬜ | Calibrer Y | Jog Y+100mm, mesurer, ajuster `$101` |
| ⬜ | Calibrer Z | Jog Z+20mm, mesurer, ajuster `$102` |
| ⬜ | Ajuster vitesses | Augmenter `$110-$112` progressivement |
| ⬜ | Ajuster accélérations | Augmenter `$120-$122` si les moteurs ne perdent pas de pas |

---

### 🔹 Phase 4 — Endstops & Homing (avant usinage)

| Fait | Étape | Détail |
|------|-------|--------|
| ⬜ | Brancher Z- | Connecter sur `Z-` (droite carte) |
| ⬜ | Brancher X- | Connecter sur `X-` |
| ⬜ | Brancher Y- | Connecter sur `Y-` |
| ⬜ | Tester avec `?` | Vérifier que `Pn:` apparaît quand on appuie sur un switch |
| ⬜ | Activer homing | `$22=1` |
| ⬜ | Config direction | `$23` si besoin |
| ⬜ | Tester `$H` | Home Z d'abord, puis X+Y |

---

### 🔹 Phase 5 — Premier usinage

| Fait | Étape | Détail |
|------|-------|--------|
| ⬜ | Air cut | Lancer programme SANS pièce, SANS broche — vérifier le parcours |
| ⬜ | Test stylo | Fixer un stylo au lieu de la fraise, tracer sur papier |
| ⬜ | Chute de bois | Première vraie passe, profondeur réduite (1-2mm) |
| ⬜ | Pièce finale | Uniquement après tous les tests validés |

---

## 🚀 Démarrage Rapide

### Alimentation

**Chargeur Krisdonia 19.2V/2A (testé et validé)**

```
Chargeur mural → DC jack 5.5x2.1mm → MKS DLC32 MAX (12-24V)
```

⚠️ **L'USB seul ne suffit pas !** La carte a besoin de 12-24V sur VIN (max 10A selon doc officielle).  
⚠️ **L'USB ne fournit pas de 5V** — le 5V disponible sur la carte vient du régulateur interne (nécessite VIN branché).

### Connexion

**USB :** `/dev/tty.usbserial-XXXX` (115200 baud)  
**WiFi :** Se connecter à `MKS_DLC32_XXXX` → `http://192.168.4.1`

### Configuration GRBL actuelle

✅ **Config théorique appliquée** → voir [EEPROM.md](EEPROM.md) pour l'état réel.

```
$100=71.11  → X steps/mm (HTD 3M, poulie 15 dents, 1/16 microstep)
$101=71.11  → Y steps/mm (idem)
$102=400    → Z steps/mm (vis T8, lead 8mm/multifilets, 1/16 microstep)
$44=4       → Homing cycle 1 (Z d'abord)
$45=3       → Homing cycle 2 (X+Y ensemble)
$46=0       → Homing cycle 3 (aucun)
$32=0       → Mode CNC (pas laser)
```

⚠️ **Important :** Ce sont des valeurs **calculées théoriquement**. Tu devras **recalibrer** après montage mécanique avec une mesure réelle (ex: jog `X+100mm`, mesurer, ajuster `$100`).

> **💡 Sauvegarde :** Exporte ta config depuis gSender (Config → Export) pour backup JSON

---

## 💻 Logiciels

### 1. Fusion 360 (CAD/CAM)

Télécharger : [autodesk.com/products/fusion-360/personal](https://www.autodesk.com/products/fusion-360/personal)  
Version Personal Use (gratuite pour hobby, revenus < $1000/an)

### 2. gSender (Contrôle CNC) ⭐

Télécharger : [github.com/Sienci-Labs/gsender](https://github.com/Sienci-Labs/gsender)  
Version : **gSender-1.6.0-Mac-Silicon.dmg** (Apple Silicon natif)

**Avantages :**

- Visualisation 3D complète du parcours
- Éditeur G-code intégré
- Jogging précis avec raccourcis
- Gestion SD card (upload/run/delete)
- Connexion USB ou WiFi
- **Éditeur EEPROM intégré** (Config) pour modifier les réglages GRBL

**Workflow :**

1. Ouvrir fichier `.gcode` dans gSender → Visualiser 3D
2. Connecter à la CNC (USB ou WiFi)
3. Homing ($H)
4. Jogger → Positionner outil → Zero X/Y/Z
5. Lancer usinage

**Connexion validée :**

- **Port USB** : `/dev/tty.usbserial-130` (115200 baud)
- **WiFi AP** : `MKS_DLC61693` (IP: 192.168.4.1)
- **Firmware détecté** : grblHAL V1.0.10

---

## 🔄 Workflow Complet

```
┌─────────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────┐
│  MAC (Fusion)   │ ──► │   FICHIER    │ ──► │  gSender     │ ──► │   CNC    │
│ • CAD/CAM       │     │   .gcode     │     │ • Visualize  │     │ • Moteurs│
│ • Post-process  │     │              │     │ • Jog/Zero   │     │ • Broche │
└─────────────────┘     └──────────────┘     └──────────────┘     └──────────┘
         │                                           ▲
         └───────────────────────────────────────────┘
                         USB / WiFi
```

---

## 🎨 Fusion 360 - Post-Processeur

```
Manufacture → Post Process
├── Post : Generic GRBL
├── Output : Documents/Fusion 360/NC Programs/
└── Unit : mm (G21)
```

**Paramètres recommandés (Bois/MDF) :**

- Fraise 3mm : Avance 1500 mm/min, passe 1-2mm
- Fraise 6mm : Avance 1800 mm/min, passe 2-3mm
- Broche : 30,000 tr/min (fixe)

**⚠️ Important :** Notre défonceuse 800W est sur secteur 220V. Les commandes M3/M5 dans le G-code n'ont **aucun effet**. Démarrer/arrêter manuellement via le bouton de la défonceuse.

---

## 📋 Fichier G-code Analysé

> **Fichier :** [`clamp_wood_v4.nc`](clamp_wood_v4.nc)  
> **Source :** Fusion 360 - 3 opérations (Bore3, 2D Pocket1, 2D Contour1)  
> **Outil :** Fraise 6mm (T2)

### ✅ Compatibilité avec la mécanique

| Élément         | Valeur G-code | Limite GRBL      | Statut |
| --------------- | ------------- | ---------------- | ------ |
| **Course X**    | 52-98 mm      | $130 = 300 mm    | ✅ OK  |
| **Course Y**    | 22-128 mm     | $131 = 300 mm    | ✅ OK  |
| **Course Z**    | 0-27 mm       | $132 = 80 mm     | ✅ OK  |
| **Vitesse X/Y** | F1800 mm/min  | $110-$111 = 3000 | ✅ OK  |

### ⚠️ Points d'attention

**1. Vitesses Z (avec vis lead 8mm)**

Le G-code utilise :

- F333.3 mm/min (plongée)
- F450 mm/min (plongée standard)
- F900 mm/min (retrait)

Avec lead 8mm, ces vitesses sont élevées. **Si le Z saute des pas** :

- Réduire `$112` à 500-800 mm/min
- Ou utiliser l'override vitesse dans gSender (50%)

**2. Commandes broche M3/M5**

```gcode
S30000 M3    # Démarre broche → AUCUN effet (défonceuse 220V)
M5           # Arrête broche → AUCUN effet
```

**Workflow manuel requis :**

```
M0 (pause) → Vérifier fraise serrée → ALLUMER défonceuse → Continuer
...
M5 (fin) → ÉTEINDRE défonceuse manuellement
```

**3. G28 - Nécessite fin de course**

```gcode
G28 G91 Z0      # Home Z (début et fin)
G28 G91 X0 Y0   # Home XY (fin)
```

🔴 **SANS INTERRUPTEURS → G28 ÉCHOUERA**

Solution : Installer X-, Y-, Z- avant de lancer ce programme.

### 🎯 Séquence de lancement recommandée

1. **Connecter** à la CNC via gSender
2. **Faire homing** `$H` (nécessite fin de course)
3. **Jogger** pour positionner l'outil sur l'origine pièce
4. **Zero X/Y/Z** (définit le G54)
5. **Charger** `clamp_wood_v4.nc` dans gSender
6. **Visualiser** le parcours 3D
7. **Lancer** avec override vitesse à 50% (premier test)
8. **Au M0** : Vérifier fraise → Allumer défonceuse → Continuer
9. **Surveiller** l'usinage (ne jamais laisser sans surveillance)

---

## 🔌 Câblage Complet

> **📋 Schémas détaillés, longueurs de câble, connexions TB6600 et endstops → [CABLING.md](CABLING.md)**

### Branchement fin de course (résumé)

```
MS5-R :
├── COM → GND
└── NO  → Signal (X-, Y-, Z- en priorité)
```

**Test après branchement :**

```gcode
?           # Vérifier statut (limites visibles)
$H          # Homing vers X-, Y-, Z-
```

---

## ⚙️ Configuration GRBL Essentielle

### Commandes de base

```gcode
$$          # Afficher tous les paramètres
$X          # Unlock (déverrouiller)
$H          # Home (origine machine)
?           # Statut
```

### À calibrer selon mécanique

**Steps/mm :**

```gcode
$100=71.11  # X (courroie HTD 3M, poulie 15 dents, 1/16 microstep)
$101=71.11  # Y (idem)
$102=400    # Z (vis T8, lead 8mm/multifilets, 1/16 microstep)
             # ⚠️ Si vis simple filet (lead 2mm) : utiliser 1600
```

> **💡 Choix de la vis Z : Simple vs Multifilets**
>
> Ta vis T8 **lead 8mm** (multifilets) offre des mouvements Z plus rapides mais avec moins de force. C'est un bon compromis pour une CNC bois.
>
> | Caractéristique       | Vis simple filet<br>(Lead 2mm) | Vis multifilets<br>(Lead 8mm - Ta config)     |
> | --------------------- | ------------------------------ | --------------------------------------------- |
> | **Steps/mm** (`$102`) | 1600                           | **400**                                       |
> | **Vitesse Z**         | Lente                          | 4× plus rapide                                |
> | **Résolution**        | 0.00125mm/pas                  | 0.005mm/pas (suffisant bois)                  |
> | **Force**             | Élevée                         | Réduite (⚠️ éviter accélérations trop fortes) |
> | **Usage idéal**       | Métal, charges lourdes         | Bois, rapidité                                |
>
> **Réglages adaptés pour lead 8mm :**
>
> ```gcode
> $102=400    # Steps/mm
> $112=1000   # Vitesse max Z conservatrice
> $122=50     # Accélération Z faible
> ```

**Vitesses et accélérations :**

```gcode
$110-112=3000/3000/1000    # Vitesses max (mm/min)
$120-122=100/100/50        # Accélérations (mm/sec²)
$130-132=600/800/60        # Courses max (mm) - Raw Inventive
```

**Homing (si fin de course installés) :**

```gcode
$22=1       # Homing enable
$23=3       # Direction invert (X et Y)
$24=50      # Homing feed
$25=500     # Homing seek
```

---

## 🚀 Checklist Avant Premier Usinage

### Vérifications matérielles

- [ ] Interrupteurs fin de course branchés (X±, Y±, Z±)
- [ ] Homing ($H) testé et fonctionnel
- [ ] Câbles moteurs branchés (phasage correct)
- [ ] Drivers TB6600 réglés (2.8A — SW3 ON + SW6 ON, reste OFF)
- [ ] Alimentation 36V stable
- [ ] Fraise bien serrée
- [ ] Bouton d'arrêt d'urgence accessible

### Tests progressifs

1. **Air cut** : Programme sans broche, sans pièce
2. **Stylo** : Tracer sur papier pour vérifier forme
3. **Chute de bois** : Première passe réelle (profondeur réduite)
4. **Pièce finale** : Uniquement après tests validés

---

## 🐛 Dépannage Rapide

| Problème                 | Solution                                                  |
| ------------------------ | --------------------------------------------------------- |
| Machine ne répond pas    | Vérifier alimentation 12-24V, envoyer `$X` pour unlock    |
| Moteur tourne à l'envers | Modifier `$3` ou inverser 2 fils (A+ ↔ A-)                |
| Moteur saute des pas     | Augmenter courant driver, réduire `$120-$122`             |
| Cote pas bonne           | Recalibrer `$100-$102` avec règle, vérifier jeu mécanique |
| Double Y désynchronisé   | Vérifier direction moteurs, ajuster `$3`                  |
| G28 échoue               | Vérifier interrupteurs fin de course branchés             |

---

## 🔄 FluidNC (Option Future)

Pour plus tard si besoin de fonctionnalités avancées :

- Configuration YAML plus flexible
- Mises à jour OTA
- ⚠️ Nécessite compilation branche S3 (ESP32-S3)
- ⚠️ Écran TFT MKS peut ne pas être supporté

> Déjà sur **grblHAL** qui est le bon compromis entre le firmware MKS (limité) et FluidNC (plus complexe). Migrer seulement si besoin de fonctionnalités spécifiques.

---

## 📚 Ressources

- **`MKS DLC32 MAX Manual.pdf`** - Manuel officiel (schémas, paramètres)
- **[BOM.md](BOM.md)** - Liste matériel complète
- [MKS DLC32 Wiki](https://github.com/makerbase-mks/MKS-DLC32)
- [GRBL Configuration](https://github.com/gnea/grbl/wiki/Grbl-v1.1-Configuration)
- [Reddit r/hobbycnc](https://www.reddit.com/r/hobbycnc/)

---

## 🎯 Résumé du Setup

| Élément                  | Configuration                                   |
| ------------------------ | ----------------------------------------------- | ----------------- |
| **Projet**               | Raw Inventive CNC 3 axes                        |
| **Électronique**         | MKS DLC32 MAX + TB6600 + NEMA 23                |
| **Firmware**             | **grblHAL v1.1f** (build 20260506)                         |
| **Alimentation carte**   | Chargeur 19.2V/2A ✅                            |
| **Alimentation moteurs** | 36V 350W ⚠️ à installer                         |
| **Contrôle**             | **gSender 1.6.0** (USB/WiFi)                    |
| **Broche**               | Défonceuse 800W - **Manuel 220V**               |
| **Connexion**            | `/dev/tty.usbserial-130` ou WiFi `MKS_DLC61693` |
| **Sans écran TFT**       | Contrôle via gSender sur Mac                    |
| **Dual Y**               | **Firmware Y-ganged** → Connecteur Y=Y1, A=Y2 (synchro auto) |
| **Steps/mm**             | X/Y: 71.11 (configurée théorique)                                      | Z: 400 (configurée) |

**Statut** : 🟢 **FIRMWARE OK + Dual Y confirmé + EEPROM configurée (théorique) + Tests électriques OK  
⚠️ **À calibrer plus tard :** calibration réelle après montage mécanique  
**Prochaine étape** : 🟡 **Montage mécanique** (structure fer, plaques MDF, rails)

**Bonnes usinages !** 🎉🔧
