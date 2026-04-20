# 🛠️ Setup CNC - Documentation
Configuration complète pour la CNC bois 3 axes basée sur le projet **Raw Inventive** avec contrôleur MKS DLC32 MAX.

---

## 📐 Base du projet

Cette CNC est basée sur le projet **Raw Inventive** de [rawcnc.com](https://rawcnc.com). La mécanique et la structure suivent ce design éprouvé, avec une mise à jour électronique moderne (MKS DLC32 MAX à la place de l'Arduino+CNCshield d'origine).

**Ressources officielles :**
- [Vidéo tutoriel](https://youtu.be/DFU-zd2rKJA) (électronique alternative avec Arduino Mega)
- [Site officiel RawCNC](https://rawcnc.com) - Plaques MDF et hardware kits disponibles
- [OpenBuilds Control](https://us.openbuilds.com/BlackBox-Motion-Control-System-X32) - Solutions alternatives GRBL

---

## 📦 Matériel

> **📋 Liste complète du matériel avec checkboxes → [BOM.md](BOM.md)**

### Contrôleur (Upgrade par rapport au BOM original)
- **Carte** : Makerbase MKS DLC32 MAX *(remplace Arduino + CNCshield)*
- **Firmware** : **MKS DLC32 MAX Firmware Officiel** (GRBL-based) *[Voir note ci-dessous pourquoi pas FluidNC]*
- **Connectivité** : USB + WiFi
- **Avantage** : Plus moderne, drivers intégrés ou externes TB6600, interface web, **écran TFT supporté**

> **⚠️ Pourquoi pas FluidNC ?** Nous avons envisagé FluidNC (firmware moderne pour ESP32), mais plusieurs **retours d'utilisateurs sur des forums** nous ont fait privilégier le firmware MKS officiel pour une première installation :
> - La DLC32 MAX utilise un **ESP32-S3** qui nécessite actuellement une compilation depuis la branche expérimentale (pas de release pré-compilée simple)
> - **Selon des utilisateurs**, FluidNC ne supporte pas l'écran TFT 3.5" MKS (seulement OLED) - *À vérifier avec les dernières versions*
> - Des utilisateurs ont rapporté des **difficultés de synchronisation** des 2 moteurs Y (auto-squaring) avec FluidNC sur cette carte spécifique
> - Le firmware MKS officiel gère nativement le double moteur Y et l'écran TFT, avec une installation plus simple (fichier .bin prêt)
> - **Note** : FluidNC reste une excellente option pour les utilisateurs avancés. Nous pourrons y migrer plus tard si besoin.
> - **Solution recommandée** : Utiliser **gSender** (logiciel de contrôle) qui offre une meilleure interface que l'interface web MKS intégrée

### Électronique de puissance
- **Moteurs** : NEMA 23 2.2N 82mm (x4) - 2x sur Y, 1x X, 1x Z
- **Drivers** : TB6600 *(conseillé pour plus de puissance avec les NEMA 23)*
- **Alimentation** : 350W 36V *(⚠️ Obligatoire même pour tester la carte sans moteurs - voir section "Démarrage rapide")*
- **Double moteur Y** : 2x NEMA 23 synchronisés

### Broche et outils
- **Défonceuse** : 800W 30,000 tr/min, pince 1/4" (6.35mm), 220V EU
- **Fraises ARDEN** (2 cannelures, carbure, tige 6.35mm) :
  - **Ø 3mm** `1/4x3` - Longueur totale 45mm, cannelures 7mm - Gravure, détails fins
  - **Ø 6mm** `1/4x6` - Longueur totale 57mm, cannelures 20mm - Dégrossissage, poches
- **Applications** : Bois, MDF, contreplaqué, gravure

---

## 🚀 Démarrage rapide (Sans moteurs)

Tu as reçu la carte **MKS DLC32 MAX** mais pas encore les moteurs ni l'alimentation ? Pas de problème ! Tu peux déjà préparer et tester la carte.

### ⚡ Alimentation obligatoire

**❌ L'USB seul ne suffit PAS !** Même pour tester la communication, la carte a besoin de **12-24V** sur son bornier VIN.

| Test | Alimentation nécessaire | Ampérage |
|------|------------------------|----------|
| Communication USB/WiFi | 12-24V | 0.5A minimum |
| Configuration GRBL | 12-24V | 0.5A minimum |
| Mouvement moteurs | 24-36V | 2-5A selon moteurs |

### 🔋 Solution d'alimentation testée et validée ✅

**Configuration actuelle fonctionnelle :**
```
Chargeur mural Krisdonia 19.2V / 2A (38W)
        ↓
Connecteur 5.5x2.1mm
        ↓
DC jack MKS DLC32 MAX (12-24V input)
        ↓
LED rouge allumée = ✅ Alimentation OK
```

**Pourquoi cette solution ?**
- ✅ Tension stable 19.2V (dans la plage 12-24V)
- ✅ Courant 2A suffisant (carte seule ~0.5A)
- ✅ Pas de coupure automatique (contrairement aux powerbanks)
- ✅ Connecteur 5.5x2.1mm compatible

**Alternatives possibles :**
- **Alimentation PC ATX** : Fils jaunes 12V + fils noirs GND
- **Alimentation 24V 15A 350W** (pour usage final avec moteurs)

### 🔌 Branchement carte MKS DLC32 MAX

```
ALIMENTATION 12-24V
         │
         ├───► VIN+ (bornier rouge sur MKS DLC32 MAX)
         │
         └───► GND (bornier noir sur MKS DLC32 MAX)

USB (Mac avec USB-C)
         │
         └───► Port USB-B sur MKS DLC32 MAX
               ↓
        Câble USB-C vers USB-B nécessaire
        (ou adaptateur USB-C → USB-A + câble USB-B)
```

### 🖥️ Connexion Mac → MKS DLC32 MAX

#### Étape 1 : Identifier le port USB
```bash
# Ouvrir Terminal sur Mac
ls /dev/tty.usb* /dev/tty.SLAB* 2>/dev/null

# Résultat attendu : /dev/tty.usbserial-XXXX ou /dev/tty.SLAB_USBtoUART
```

#### Étape 2 : Tester la communication
```bash
# Se connecter en ligne de commande
screen /dev/tty.usbserial-XXXX 115200

# Envoyer une commande GRBL
$$              # Affiche tous les paramètres
?               # Statut de la machine
$X              # Unlock (si en alarme)
```

Si tu vois des réponses → **La carte fonctionne !** ✅

### 📶 Configuration WiFi (Recommandé)

La MKS DLC32 MAX crée un point d'accès WiFi automatiquement :

1. **Cherche le réseau** sur ton Mac : `MKS_DLC32_XXXX`
2. **Mot de passe** : `12345678` (par défaut)
3. **Ouvre un navigateur** : `http://192.168.4.1`
4. **Interface web GRBL** s'affiche !

**Avantages du WiFi :**
- ✅ Plus besoin de câble USB après config
- ✅ Contrôle depuis n'importe quel appareil
- ✅ Upload G-code sans fil
- ✅ Interface visuelle complète

### 📱 Pas besoin d'écran TFT !

L'écran TFT est **optionnel**. Dans notre configuration **sans écran** :
- **Contrôle** : Via **gSender** sur Mac (USB ou WiFi)
- **Pas besoin** d'écran tactile 3.5" MKS
- **Avantage** : Interface plus complète sur l'ordinateur

> **Configuration actuelle** : MKS DLC32 MAX sans écran + gSender sur Mac

### ✅ Ce que tu peux faire MAINTENANT (sans moteurs)

| Tâche | Possible ? | Utilité |
|-------|-----------|---------|
| Vérifier communication | ✅ Oui | Confirmer la carte marche |
| Configurer GRBL | ✅ Oui | Préparer les paramètres |
| Mettre à jour firmware | ✅ Oui | Dernière version |
| Configurer WiFi | ✅ Oui | Mode STA (box internet) |
| Installer Fusion 360 | ✅ Oui | Prêt pour la conception |
| Tester moteurs | ❌ Non | Attendre les NEMA 23 |

---

## 🔥 État Actuel & Prochaines Étapes

### ✅ Ce qui est déjà fait

| Étape | Statut | Détail |
|-------|--------|--------|
| **Conception Fusion 360** | ✅ **FAIT** | 3 opérations (Bore3, 2D Pocket1, 2D Contour1) |
| **Post-process** | ✅ **FAIT** | G-code généré : `clamp_wood_v4.nc` dans `Documents/Fusion 360/NC Programs/` |
| **Choix firmware** | ✅ **FAIT** | MKS DLC32 MAX Firmware Officiel (pas FluidNC) |
| **Alimentation carte** | ✅ **FAIT** | Chargeur Krisdonia 19.2V/2A - LED rouge stable |
| **Flashage firmware** | ✅ **FAIT** | Firmware V1.0.10 flashé avec esptool sur ESP32-S3 |
| **Communication USB** | ✅ **FAIT** | Port `/dev/tty.usbserial-130` détecté, GRBL 1.1h répond |
| **Configuration GRBL** | ✅ **FAIT** | Paramètres CNC configurés : `$32=0`, `$45=2`, `$47=0` |
| **Interface Web MKS** | ✅ **FAIT** | Testée et fonctionnelle sur `192.168.4.1` (WiFi AP `MKS_DLC61693`) |
| **Outil GUI** | ✅ **CHOISI** | Interface Web MKS (ESP32-WEB) - jog, settings, terminal intégrés |
| **Nettoyage** | ✅ **FAIT** | bCNC, esptool-env, UGS temporaires supprimés. picocom conservé. |

### ⚠️ Ce qui reste à faire

| Étape | Priorité | Détail |
|-------|----------|--------|
| **Test mécanique** | 🔴 **URGENT** | Brancher moteurs, tester jog/homing quand matériel reçu |
| **Calibration steps/mm** | 🟡 **Important** | Ajuster `$100-$102` selon vis trapézoïdales réelles |
| **Montage mécanique** | 🟢 **Quand matériel reçu** | Assembler la structure Raw Inventive |
| **Test usinage** | 🟢 **Final** | Premier test avec le G-code généré |

### ✅ Alimentation - PROBLÈME RÉSOLU

**Solution validée :** Chargeur mural Krisdonia 19.2V / 2A
- ✅ LED rouge allumée stable (pas de coupure)
- ✅ Tension 19.2V dans la plage 12-24V acceptée
- ✅ Courant 2A suffisant
- ✅ Connecteur 5.5x2.1mm compatible

**Note :** La powerbank Krisdonia 25000mAh n'était pas adaptée (coupure auto après 1 min avec faible consommation).

### 🎯 Configuration GRBL Actuelle (CNC 3 Axes)

La carte est **configurée et prête** pour une CNC bois sans écran :

```
$32=0    → Mode CNC (pas laser) - CRITIQUE pour sécurité
$45=2    → Mode XYZ (3 axes)
$47=0    → Sans écran offline (headless)
$100-102=80    → Steps/mm par défaut (à calibrer)
$110-112=10000/10000/8000 → Vitesses max (mm/min)
$120-122=500/500/500 → Accélérations
$130-132=300/300/80 → Courses max (à ajuster)
```

### 🛠️ Outils de Contrôle - Solution Retenue ✅

| Outil | Statut | Commentaire |
|-------|--------|-------------|
| **Interface Web MKS** | ✅ **SOLUTION FINALE** | Interface ESP32-WEB complète : jog circulaire, terminal GRBL, settings, upload G-code |
| **picocom** | ✅ **Conservé** | Léger (159KB), installé via Homebrew. Utilisé pour config initiale GRBL. Debug uniquement. |
| **screen** | ❌ Abandonné | Trop complexe à quitter, interface datée |
| **bCNC** | ❌ Supprimé | Python, installé puis supprimé (pas nécessaire) |
| **CNCjs** | ❌ Bloqué | Gatekeeper macOS bloque l'app (.cask deprecated) |
| **UGS** | ❌ Abandonné | Java, lourd, pas adapté workflow moderne |
| **esptool** | ✅ **Supprimé** | Utilisé une fois pour flasher, esptool-env nettoyé |
| **gSender** | ⏳ **Option future** | Pas compilé - l'interface Web MKS suffit pour l'instant. Peut être compilé plus tard si besoin. |

### 🎯 Interface Web MKS - Solution GUI Validée

**✅ TESTÉE ET FONCTIONNELLE !** L'interface web intégrée est excellente et remplace avantageusement les logiciels externes.

**Accès :**
- **WiFi AP** : `MKS_DLC61693` (mot de passe: `12345678`)
- **URL** : `http://192.168.4.1`
- **Interface** : ESP32-WEB (moderne, responsive)

**Fonctionnalités complètes :**
- ✅ **Jog circulaire** : Pad directionnel intuitif pour X/Y/Z
- ✅ **Positions temps réel** : MX, MY, MZ affichés en direct
- ✅ **Terminal GRBL intégré** : Envoyer commandes, voir réponses ($$, $#, etc.)
- ✅ **Settings complets** : Tableau éditable de tous les paramètres GRBL ($0-$135)
- ✅ **Contrôles rapides** : Soft Home, Hard Home, Unlock, Position XY/Z
- ✅ **Upload G-code** : Menu "SD File" pour charger fichiers .nc
- ✅ **Réglages vitesse/distance** : Sliders pour speed (mm/s) et distance (mm)

**Avantages :**
- ✅ Pas d'installation logicielle sur Mac
- ✅ Interface moderne et fluide
- ✅ Accès depuis n'importe quel appareil (Mac, iPad, iPhone)
- ✅ Pas de Gatekeeper ni de problèmes de compatibilité
- ✅ Directement intégrée au firmware MKS

**⚠️ Note :** Boutons "Laser Mode" présents mais inactifs (car $32=0 mode CNC). Normal et sécurisé.

---

## 🔧 Configuration spécifique MKS DLC32 MAX

### Avantages vs Arduino+CNCshield
| Feature | Arduino+CNCshield | MKS DLC32 MAX |
|---------|-------------------|---------------|
| Firmware | GRBL 1.1 | GRBL 1.1/1.2 |
| Drivers | DRV8825 (petits) | TB6600 intégrés ou externes |
| Puissance | Limitée | NEMA 23 full power |
| Interface | PC obligatoire | WebUI intégrée + USB |
| WiFi | Non | Oui |
| Écran | Non | TFT optionnel |

### Branchement MKS DLC32 MAX

```
MKS DLC32 MAX
├── X Axis → Driver TB6600 → Moteur X NEMA 23
├── Y Axis → Driver TB6600 → Moteur Y1 NEMA 23
├── Y Axis (clone) → Driver TB6600 → Moteur Y2 NEMA 23
├── Z Axis → Driver TB6600 → Moteur Z NEMA 23
├── A Axis (optionnel) → 4ème axe rotatif
├── Probe (optionnel)
├── Limit Switches (fin de course - recommandé)
└── Spindle PWM ⚠️ MAX 20W @ 24V (voir note ci-dessous)
```

#### ⚠️ Important : Limitation Broche SPINDLE

> **Le connecteur SPINDLE de la MKS DLC32 MAX a des limites strictes :**
> - **Max 10W** à 12V
> - **Max 20W** à 24V
> 
> **Ta défonceuse 800W ne peut PAS être branchée directement sur la carte !**
> 
> **Solution :** 
> - Brancher la défonceuse **séparément** sur une prise 220V
> - Démarrage/arrêt **manuel** via le bouton de la défonceuse
> - La carte contrôle uniquement les **moteurs XYZ** (et éventuellement une broche faible puissance)

---

## 🔄 Workflow Complet (gSender)

```
┌─────────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────┐
│  MAC (Fusion)   │ ──► │   FICHIER    │ ──► │  gSender     │ ──► │   CNC    │
│                 │     │   G-CODE     │     │  (Contrôle)  │     │          │
│ • Dessin (CAD)  │     │   (.gcode)   │     │ • Visualize  │     │ • Moteurs│
│ • Usinage (CAM) │     │              │     │ • Jog/Zero   │     │ • Broche │
│ • Post-proc     │     │              │     │ • Execute    │     │          │
└─────────────────┘     └──────────────┘     └──────────────┘     └──────────┘
         │                    USB/WiFi              │
         │                                          │
         └──────────────────► MKS DLC32 MAX ◄──────┘
                                 (GRBL)
```

**Étapes :**
1. **Fusion 360** → Post-process avec "Generic GRBL" → fichier `.gcode`
2. **gSender** → Ouvrir le fichier → Visualisation 3D du parcours
3. **gSender** → Jogging pour positionner l'outil → Mettre à zéro (Zero)
4. **gSender** → Lancer l'usinage avec monitoring complet

---

## 💻 Étape 1 : Préparation sur Mac

### Logiciels à installer

#### 1. Fusion 360 (CAD/CAM)
```bash
# Télécharger depuis :
https://www.autodesk.com/products/fusion-360/personal

# Version : Personal Use (gratuite pour hobby)
# Revenus < $1000/an avec le logiciel
```

#### 2. gSender (Recommandé ⭐)
**Le meilleur contrôleur G-code pour CNC** avec interface moderne et complète :
```bash
# Télécharger depuis :
https://github.com/Sienci-Labs/gsender

# Disponible pour Mac (Intel & Apple Silicon)
# Compatible USB et WiFi (en mode réseau)
# Visualisation 3D du parcours, macros, contrôle complet
```

> **Pourquoi gSender ?** L'interface web intégrée MKS est limitée. gSender offre :
> - Visualisation 3D du G-code avant exécution
> - Jogging précis avec raccourcis clavier
> - Macros personnalisées (ex: probe, homing)
> - Interface moderne et stable
> - Contrôle broche PWM

#### 3. Alternatives : CNCjs ou Universal G-Code Sender (UGS)
```bash
# CNCjs - Via npm
npm install -g cncjs

# UGS - Téléchargement direct
https://winder.github.io/ugs_website/
```

---

## 🎨 Étape 2 : Création dans Fusion 360

### 1. Mode Design (CAD)
- Créer ton modèle 3D ou esquisse 2D
- Utiliser les outils d'esquisse pour définir les contours

### 2. Mode Manufacture (CAM)
```
1. Passer en environnement "Manufacture"
2. Créer un nouveau Setup :
   - WCS (système de coordonnées)
   - Box (taille brute)
   - Model (modèle final)
```

### 3. Opérations d'usinage recommandées pour bois

| Opération | Usage | Outil |
|-----------|-------|-------|
| **2D Contour** | Découpe extérieure | Fraise ARDEN 6mm |
| **2D Pocket** | Évidement / Poches | Fraise ARDEN 6mm |
| **2D Adaptive** | Évidement rapide | Fraise ARDEN 6mm |
| **Drill** | Perçage | Foret |
| **Trace** | Gravure | Fraise ARDEN 3mm |

**Paramètres de départ (Bois/MDF) :**
- **Broche** : 30,000 tr/min fixe (pas de variateur)
- **Fraise 3mm** : Avance 1500 mm/min, chip load 0.025mm/dent
- **Fraise 6mm** : Avance 1800 mm/min, chip load 0.030mm/dent
- **Passe de profondeur** : 1-2mm max par passe

**✅ G-code généré avec succès !** Voir section "Post-Processeur GRBL" ci-dessous pour les détails.

### 4. Post-Processeur GRBL

#### Configuration dans Fusion 360

```
Manufacture → Post Process (NC Program)
├── Post : Generic GRBL
│   └── Chercher "grbl" dans la Post Library
│   └── Sélectionner "Grbl" (Generic milling post)
│   └── Copier vers "My Posts" si demandé
├── Name/number : 1001 (ou ID de ton choix)
├── File Name : 1001 (sera sauvegardé comme 1001.nc)
├── Output folder : Documents/Fusion 360/NC Programs
└── Unit : Document units (mm)
```

#### Post Properties (options du post-processeur)

Une fois "Generic GRBL" sélectionné, les options suivantes s'affichent :

| Option | Valeur recommandée | Explication |
|--------|-------------------|-------------|
| **Split file** | No splitting | Un seul fichier pour toutes les opérations |
| **Output M6** | Non coché | Pas de commande M6 (changeur automatique) |
| **Output tool number** | Coché | Affiche les numéros d'outil dans les commentaires |
| **Safe Retracts** | G28 | Position de retrait sécurisée |

#### ⚠️ Warning WCS Offset (normal !)

Lors du post-process, tu peux voir ce message :

> *"Warning: In operation 'Bore3': The Work Coordinate System (WCS) Offset is set to 0, which defaults to using G54"*

**C'est normal et correct !** ✅

- **WCS Offset = 0** → Utilise **G54** (système de coordonnées par défaut)
- C'est exactement ce qu'il faut pour une CNC GRBL standard
- G54 est le WCS le plus couramment utilisé
- Tu peux ignorer ce warning, c'est juste une information

#### Paramètres importants à vérifier

- **Units** : Millimeters (G21) ✅
- **Tool Change** : Ignore / Manual (pas de changeur auto) ✅
- **Program Start/End** : Standard GRBL (avec G28 home) ✅

#### Fichier généré

Le fichier `.nc` (ou `.gcode`) contiendra :
```gcode
(1001)                                    ← Nom du programme
(T2 D=6 CR=0 - ZMIN=0 - flat end mill)    ← Info outil
G90 G94 G17 G21                           ← Config initiale
G28 G91 Z0                                ← Home Z
...
M5                                        ← Arrêt broche
M30                                       ← Fin programme
```

---

### 📊 Analyse du G-code `clamp_wood_v4.nc` - Points critiques avant lancement

> **⚠️ IMPORTANT :** Le fichier `clamp_wood_v4.nc` (1885 lignes, 3 opérations) a été analysé. Voici ce qu'il faut vérifier avant de lancer :

#### ✅ Points positifs (compatibilité OK)

| Élément | Valeur dans G-code | Configuration actuelle | Statut |
|---------|-------------------|------------------------|--------|
| **Vitesse max** | F1800 mm/min | $110-112 = 10000/8000 | ✅ OK (inférieur aux limites) |
| **Course X** | X: 52-98 mm | $130 = 300 mm | ✅ OK |
| **Course Y** | Y: 22-128 mm | $131 = 300 mm | ✅ OK |
| **Course Z** | Z: 0-27 mm | $132 = 80 mm | ✅ OK |
| **Mode CNC** | $32=0 configuré | Pas de mode laser | ✅ OK |

#### 🔴 Points critiques - À vérifier IMPÉRATIVEMENT

**1. M0 - Pause manuelle au début (Ligne 10) - À adapter**
```gcode
M0
(MANUAL TOOL CHANGE TO T2)
S30000 M3
```
⚠️ **Le programme s'arrête au début**, mais avec notre configuration **défonceuse sur secteur** :

| G-code | Effet sur MKS | Action requise |
|--------|--------------|----------------|
| **M0** | ✅ Pause active | Appuyer "Continuer" dans l'interface Web MKS après vérification |
| **S30000 M3** | ❌ **AUCUN effet** | La défonceuse 800W est sur secteur 220V, pas connectée à la carte |

**Workflow adapté pour défonceuse manuelle :**
```
M0 (pause) → VÉRIFIER fraise 6mm serrée → ALLUMER défonceuse manuellement (bouton 220V)
     ↓
Appuyer "Continuer" sur interface Web
     ↓
Usinage commence
     ↓
M5 (fin) → ÉTEINDRE défonceuse manuellement (bouton 220V)
```

> **💡 Note :** La défonceuse étant branchée séparément sur 220V (pas sur le connecteur SPINDLE 20W max de la MKS), vous devez la démarrer/arrêter **manuellement** via son propre bouton.

**2. G28 - Homing requis (Lignes 6, 1880, 1882)**
```gcode
G28 G91 Z0      (Home Z au début)
...
G28 G91 Z0      (Home Z à la fin)
G28 G91 X0 Y0   (Home XY à la fin)
```
🔴 **SANS INTERRUPTEURS DE FIN DE COURSE → G28 ÉCHOUERA !**
- La machine doit connaître sa position "machine" (origine absolue)
- Brancher au minimum X-, Y-, Z- avant de lancer ce programme

**3. G54 - Origine pièce (Ligne 14)**
```gcode
G54
```
⚠️ **Vous devez définir l'origine pièce avant !**
- Positionner l'outil au coin de votre brut
- Faire "Set X=0 Y=0" (Position XY) dans l'interface
- Descendre Z jusqu'à toucher le haut de la pièce
- Faire "Set Z=0" (Position Z)

#### 🎯 Séquence complète pour lancer `clamp_wood_v4.nc` (avec défonceuse manuelle) :

```
1. ALLUMER alimentation 19.2V → LED rouge allumée
        ↓
2. SE CONNECTER au WiFi MKS_DLC61693 (perte internet)
        ↓
3. OUVRIR interface Web → 192.168.4.1
        ↓
4. FAIRE HOMING → $H (ou bouton "Hard Home")
   (vérifie que les interrupteurs X-, Y-, Z- fonctionnent)
        ↓
5. POSITIONNER l'outil au-dessus du brut (Jog)
        ↓
6. SET ZERO → Position XY + Position Z (définit G54)
        ↓
7. INSTALLER fraise 6mm → Serrer correctement (clé dynamométrique)
        ↓
8. CHARGER fichier → Menu SD File → Upload clamp_wood_v4.nc
        ↓
9. LANCER → Play ▶️
        ↓
10. ATTENDRE M0 (pause automatique)
    ├─ Vérifier fraise bien serrée
    ├─ Vérifier aspiration copeaux branchée
    ├─ ⚡ ALLUMER DÉFONCEUSE (bouton 220V manuel)
    └─ Appuyer "Continuer" dans l'interface Web MKS
        ↓
11. USINAGE EN COURS → SURVEILLER (NE JAMAIS LAISSER SEUL)
        ↓
12. FIN programme (M5) → ⚡ ÉTEINDRE DÉFONCEUSE (bouton 220V manuel)
        ↓
13. G28 ramène en Home position
```

**⚠️ Points d'attention avec défonceuse manuelle :**
- La vitesse est **fixe à 30,000 tr/min** (pas de variateur sur la défonceuse)
- Vous devez **démarrer/arrêter manuellement** la broche (M3/M5 dans le G-code n'ont aucun effet)
- **Attendre que la broche atteigne sa vitesse** avant de continuer après M0
- **Jamais laisser la machine sans surveillance** (pas d'arrêt d'urgence automatique)

#### 🛡️ Tests obligatoires avant usinage réel

**Test 1 : Air Cut (à vide, sans broche)**
- Lancer le programme sans broche allumée
- Sans pièce sur la table
- Vérifier que les déplacements sont corrects
- Vitesse réduite pour observer

**Test 2 : Stylo (simulation)**
- Mettre un stylo/marqueur à la place de la fraise
- Tracer sur une feuille de papier
- Vérifier la forme et les dimensions

**Test 3 : Chute de bois**
- Première passe sur une chute de contreplaqué/MDF
- Profondeur réduite (0.5mm au lieu de 2mm)
- Vérifier comportement réel

**Test 4 : Pièce finale**
- Uniquement après les 3 tests précédents validés

---

## 📤 Étape 3 : Transfert vers la CNC

### Option A : Clé USB (Standalone)
```
1. Copier le fichier .gcode sur clé USB
2. Insérer dans le port USB de la MKS DLC32
3. Utiliser l'écran TFT pour sélectionner le fichier
4. Lancer l'usinage
```

### Option B : USB Direct avec gSender (Recommandé ⭐)
```
1. Connecter Mac ↔ MKS DLC32 MAX via USB
2. Ouvrir gSender
3. Sélectionner le port USB (ex: /dev/tty.usbserial-xxx) ou IP WiFi
4. Charger le fichier G-code
5. Visualiser le parcours en 3D
6. Jogger pour positionner l'outil
7. Mettre à zéro les axes (Zero X/Y/Z)
8. Lancer l'usinage avec monitoring complet
```

### Option C : WiFi avec gSender
```
1. Configurer la MKS DLC32 MAX en mode STA (connexion à ta box)
   - Se connecter au WiFi AP de la carte (MKS_DLC32_XXXX)
   - Aller à http://192.168.4.1
   - Configurer le WiFi dans les paramètres réseau
2. Ouvrir gSender → Sélectionner connexion "Network" → Entrer l'IP de la carte
3. Charger le G-code et usiner sans fil
```

> **💡 Astuce** : L'interface web MKS intégrée existe mais est limitée. Préférer gSender pour le contrôle quotidien et utiliser l'interface web MKS uniquement pour les réglages WiFi de base.

---

## ⚙️ Étape 4 : Configuration GRBL

### Connexion à la carte
```bash
# Via terminal ou logiciel de contrôle
# Port série : /dev/tty.usbserial-XXX (Mac)
# Baud rate : 115200
```

### Commandes de base
```gcode
$$              # Afficher tous les paramètres
$X              # Unlock (déverrouiller)
$H              # Home (origine machine)
?               # Statut
G0 X0 Y0 Z0     # Déplacement rapide origine
```

### ⚠️ Configuration à calibrer OBLIGATOIREMENT

```gcode
# Pas par mm (Steps/mm) - À CALCULER selon TA mécanique
$100=80         # X steps/mm
$101=80         # Y steps/mm  
$102=400        # Z steps/mm (plus élevé)

# Vitesses maximales (mm/min)
$110=3000       # X max speed
$111=3000       # Y max speed
$112=1000       # Z max speed

# Accélérations (mm/sec²)
$120=100        # X acceleration
$121=100        # Y acceleration
$122=50         # Z acceleration

# Courses maximales (mm)
$130=600        # X max travel (Raw Inventive ~600mm)
$131=800        # Y max travel (Raw Inventive ~800mm)
$132=60         # Z max travel

# Inversion de direction (si moteur tourne à l'envers)
$3=0            # Direction port invert mask

# Homing (si fin de course installés)
$22=1           # Homing enable
$23=3            # Homing direction invert (X et Y inversés)
$24=50           # Homing feed rate
$25=500          # Homing seek rate
$26=250          # Homing debounce
$27=5            # Homing pull-off
```

### 💡 Calcul des steps/mm pour Raw Inventive

**Axes X et Y (courroie HTD 3M) :**
```
Steps/mm = (Pas moteur × Microstepping) / (Pas courroie × Dents poulie)

Avec :
- Moteur : 200 pas/tour (1.8°/pas)
- Microstepping : 1/16
- Poulie : 15 dents
- Courroie HTD 3M : 3mm pas

Steps/mm = (200 × 16) / (3 × 15) = 3200 / 45 = 71.11 steps/mm
```

**Axe Z (vis trapézoïdale) :**
```
Steps/mm = (Pas moteur × Microstepping) / Pas vis

Avec :
- Microstepping : 1/16
- Vis T8 (typique) : 8mm/tour (pas de 2mm)

Steps/mm = (200 × 16) / 2 = 3200 / 2 = 1600 steps/mm
```

**⚠️ À vérifier et ajuster selon TA mécanique exacte !**

---

## 🔌 Interrupteurs de Fin de Course (Limit Switches) - OBLIGATOIRE

> **⚠️ SÉCURITÉ :** Sans interrupteurs de fin de course, vous ne pouvez PAS faire de homing ni avoir de sécurité anti-dépassement. C'est **dangereux** de lancer un programme sans ces protections.

### 📍 Emplacement des connecteurs sur la MKS DLC32 MAX

Les borniers sont situés sur le côté de la carte et sont étiquetés :
- **X+** / **X-** (fin de course X positif et négatif)
- **Y+** / **Y-** (fin de course Y positif et négatif)  
- **Z+** / **Z-** (fin de course Z positif et négatif)
- **PROBE** (sonde Z tactile - optionnel)
- **DOOR** (capteur porte ouverte - optionnel)
- **FLAME** (détection flamme - pour laser)

*Référence : Voir `MKS DLC32 MAX Manual.pdf` dans ce dossier, page sur les connexions.*

### 🔧 Type d'interrupteur recommandé

**Micro-switch 3 broches** (standard imprimante 3D / CNC) :
```
Micro-switch        →    MKS DLC32 MAX
├── COM (Common)         →    GND (borne GND commune)
├── NC (Normally Closed) →    Pas utilisé si NO
└── NO (Normally Open)   →    Signal (X+, X-, Y+, Y-, Z+, Z-)
```

### ⚡ Câblage - Deux options :

#### Option A : Normally Open (NO) - Configuration actuelle ($5=1)
```
État normal (non appuyé)    → Circuit OUVERT
État appuyé (butée touchée) → Circuit FERMÉ → Signal détecté
```
- ✅ Simple à câbler
- ⚠️ Si fil coupé = pas de détection d'erreur

#### Option B : Normally Closed (NC) - Plus sécurisé ($5=0)
```
État normal (non appuyé)    → Circuit FERMÉ
État appuyé (butée touchée) → Circuit OUVERT → Signal détecté
```
- ✅ Détecte un fil coupé (se déclenche comme si butée atteinte)
- ✅ Recommandé pour la sécurité

### 📋 Schéma de montage Raw Inventive

```
MACHINE CNC Raw Inventive
       │
       ├── X+ (butée droite) ─────────┐
       ├── X- (butée gauche/homing) ──┼──► MKS DLC32 MAX
       │                                │    ├── X+ (borne X+)
       ├── Y+ (butée avant) ──────────┤    ├── X- (borne X-)
       ├── Y- (butée arrière/homing) ─┤    ├── Y+ (borne Y+)
       │   │                          │    ├── Y- (borne Y-)
       │   └── Un seul Y- pour les    │    ├── Z+ (borne Z+)
       │       2 moteurs Y (synchro)    │    └── Z- (borne Z-)
       ├── Z+ (butée haute/MAX) ──────┤
       └── Z- (butée basse/MIN) ──────┘

GND commune : Tous les COM des interrupteurs → GND sur MKS
```

### 🔨 Ordre de priorité de branchement

1. **Z-** (butée basse) → **LE PLUS IMPORTANT** pour la sécurité Z
2. **X-** et **Y-** (homing) → Pour le référencement machine
3. **X+**, **Y+**, **Z+** (limites max) → Pour la sécurité anti-dépassement
4. **PROBE** (sonde Z) → Pour le réglage automatique du zéro Z (optionnel mais pratique)

### 💡 Tests après branchement

```gcode
?           # Vérifier statut (doit montrer les limites)
$H          # Homing (doit bouger vers X-, Y-, Z- puis s'arrêter)
G0 X0 Y0    # Aller à l'origine pièce
```

Si homing échoue → Vérifier les connexions et le paramètre $23 (direction)

---

## 🚀 Checklist avant premier usinage

### Vérifications matérielles
- [ ] **🔴 INTERRUPTEURS DE FIN DE COURSE branchés (X±, Y±, Z±)** - OBLIGATOIRE
- [ ] **🔴 HOMING testé ($H) et fonctionnel** - OBLIGATOIRE avant premier usinage
- [ ] CNC montée et vissée correctement
- [ ] Tous les roulements tournent librement
- [ ] Jeu mécanique minimum (serrer les écrous anti-backlash)
- [ ] Câbles moteurs branchés correctement (phasage !)
- [ ] Drivers TB6600 réglés (courant moteur ~2A)
- [ ] Alimentation 36V allumée, tension stable
- [ ] **Bouton d'arrêt d'urgence (E-Stop)** accessible et testé
- [ ] Fraise correctement serrée (clé dynamométrique)
- [ ] Sécurité : lunettes, aspiration copeaux, chaussures fermées

### Vérifications logicielles
- [ ] Connexion USB/WiFi établie
- [ ] Communication GRBL OK (réponse à $$)
- [ ] Homing ($H) fonctionne (si fin de course installés)
- [ ] Déplacements manuels OK (X, Y, Z dans le bon sens)
- [ ] Steps/mm calibrés (test à la règle)
- [ ] Origine pièce définie (G54 ou X0 Y0 Z0)
- [ ] Simulation G-code vérifiée (CNCjs visualise le parcours)
- [ ] Vitesse de broche réglée (démarrer à 50%)

### Tests progressifs
1. **Test à vide** : Faire tourner le programme sans broche, sans pièce
2. **Test stylo** : Mettre un stylo à la place de la fraise, tracer sur papier
3. **Test air cut** : Fraiser dans le vide pour vérifier vitesses
4. **Test bois de récupération** : Chute de contreplaqué ou MDF
5. **Pièce finale** : Une fois tous les paramètres validés

---

## 📚 Ressources utiles

### Documentation
**📄 Documentation locale (dans ce dossier) :**
- **`MKS DLC32 MAX Manual.pdf`** (4.4MB) - Manuel officiel Makerbase avec schémas de connexion, paramètres GRBL spécifiques MKS, tableaux de broches
- **[BOM.md](BOM.md)** - Liste complète du matériel avec checkboxes

**Liens externes :**
- [MKS DLC32 Wiki GitHub](https://github.com/makerbase-mks/MKS-DLC32)
- [GRBL Configuration](https://github.com/gnea/grbl/wiki/Grbl-v1.1-Configuration)
- [Fusion 360 CAM Tutorial](https://www.autodesk.com/products/fusion-360/learn-cam)
- [Raw Inventive BOM & Video](https://rawcnc.com)

### Communautés
- [Reddit r/hobbycnc](https://www.reddit.com/r/hobbycnc/)
- [Forum GRBL](https://github.com/gnea/grbl/issues)
- [Discord CNC France](https://discord.gg/cnc)

### Calculateurs
- [F&S Calculator (Chip Load)](https://www.precisebits.com/articles/calculating feeds and speeds.htm)
- [GRBL Settings Calculator](https://github.com/gnea/grbl/wiki/Grbl-v1.1-Configuration#grbl-settings)

---

## 🔄 FluidNC - Option avancée (Pour plus tard)

Si tu souhaites migrer vers **FluidNC** une fois la CNC fonctionnelle avec le firmware MKS :

### Pourquoi envisager FluidNC plus tard ?
- Configuration via fichier YAML (plus flexible que les commandes `$`)
- Interface web intégrée plus moderne (mais l'écran TFT MKS ne fonctionnerait plus)
- Mises à jour OTA (Over-The-Air)
- Communauté active sur Discord

### Points d'attention (basés sur retours utilisateurs forums)
| Aspect | Ce qu'il faut vérifier |
|--------|------------------------|
| **Compilation** | Nécessite branche S3 expérimentale pour ESP32-S3 de la DLC32 MAX |
| **Écran TFT** | Support incertain du TFT MKS 3.5" - vérifier compatibilité |
| **Double Y** | Quelques utilisateurs rapportent des synchros parfaites, d'autres non - dépend du réglage |
| **Installation** | Plus technique (fichier YAML vs commandes `$` simples) |

> **💡 Notre démarche** : Commencer simple avec firmware MKS officiel, maîtriser la CNC, puis évaluer FluidNC quand tout fonctionne. Il est toujours possible de changer de firmware plus tard !

---

## 🐛 Dépannage courant

### La machine ne répond pas
```
→ Vérifier le port USB dans CNCjs (port COM sur Mac/Linux)
→ Vérifier que l'alimentation 36V est allumée
→ Envoyer $X pour unlock (déverrouiller GRBL)
→ Vérifier les branchements des drivers TB6600
```

### Les moteurs tournent à l'envers
```
→ Modifier $3 (direction invert mask)
  $3=1 pour inverser X
  $3=2 pour inverser Y
  $3=4 pour inverser Z
  $3=3 pour inverser X+Y, etc.
→ Ou inverser physiquement 2 fils (A+ et A-) sur un moteur
```

### Le moteur "saute" des pas
```
→ Augmenter le courant sur le driver TB6600 (potentiomètre)
→ Réduire l'accélération ($120-$122)
→ Réduire la vitesse max ($110-$112)
→ Vérifier la tension d'alimentation (doit être stable à 36V)
→ Vérifier que les poulies/couplages ne glissent pas
```

### La cote n'est pas bonne
```
→ Recalculer les steps/mm ($100-$102) avec une règle
→ Vérifier qu'il n'y a pas de jeu mécanique (serrer)
→ Vérifier le serrage des poulies/couplages
→ Vérifier la tension des courroies (ni trop lâche, ni trop tendue)
```

### Double moteur Y ne fonctionne pas
```
→ Vérifier que les deux moteurs sont bien branchés
→ Vérifier qu'ils tournent dans le même sens (synchronisation)
→ Si axe X crooked : un moteur tourne à l'envers
→ Ajuster $3 ou inverser les fils d'un moteur Y
```

### La broche/défonceuse ne démarre pas avec M3/M5
```
→ C'est NORMAL avec une défonceuse 800W sur secteur 220V
→ Le connecteur SPINDLE de la MKS ne supporte que 20W max @ 24V
→ La défonceuse doit être branchée séparément sur une prise 220V
→ Démarrage/arrêt MANUEL via le bouton de la défonceuse
→ Les commandes M3 (start) et M5 (stop) dans le G-code n'ont aucun effet
→ Workflow : M0 (pause) → Allumer défonceuse → Continuer → M5 (fin) → Éteindre défonceuse
```

---

## 💡 Astuces

1. **Toujours** faire un homing ($H) avant chaque session si fin de course installés
2. **Jamais** laisser la machine sans surveillance en cours d'usinage
3. **Documenter** tes paramètres qui fonctionnent bien (speeds & feeds)
4. **Sauvegarder** tes projets Fusion 360 sur le cloud
5. **Tester** toujours sur une chute de bois avant la pièce finale
6. **Aspirer** les copeaux régulièrement (évite l'incendie et maintient la précision)
7. **Lubrifier** les guides si besoin (graphite sur Delrin, huile légère sur métal)
8. **Vérifier** la tension des courroies tous les mois

---

## 📅 Mise à jour

- **Date de création** : 2026-04-18
- **Version** : 2.0 - **Restructuration + BOM séparé**
- **Changements v2.0** :
  - ✅ **Renommé** `README.md` → `AGENTS.md`
  - ✅ **BOM extrait** dans un fichier dédié [BOM.md](BOM.md) avec checkboxes
- **Version précédente v1.9** : Analyse G-code + Documentation Usinage
- **Version v1.8** : Documentation interrupteurs + Manuel PDF déplacé
- **Version v1.6** : Firmware flashé & configuré, tests GUI en cours
- **Version v1.5** : Alimentation OK (chargeur 19.2V), carte prête
- **Version v1.4** : G-code généré + Plan d'action flashage firmware
- **Prochaine étape immédiate** : **Brancher moteurs NEMA 23** et tester mouvements réels
- **Puis** : Calibration steps/mm → Montage mécanique → Premier usinage !

---

## 🎯 Résumé du setup

**Projet** : Raw Inventive CNC 3 axes  
**Électronique** : MKS DLC32 MAX + TB6600 + NEMA 23  
**Firmware** : MKS DLC32 MAX Officiel V1.0.10 ✅ (GRBL 1.1h - ESP32-S3 flashé)  
**Alimentation carte** : ✅ Chargeur Krisdonia 19.2V/2A (LED rouge stable)  
**Alimentation moteurs** : ⚠️ 24V/36V 350W à installer  
**Outils** : Défonceuse 800W (30,000 tr/min fixe) + Fraises ARDEN 3mm et 6mm  
**Software** : Fusion 360 → **Interface Web MKS** → GRBL  
**Workflow broche** : ⚡ **Manuel 220V** (M3/M5 G-code sans effet, démarrage/arrêt via bouton défonceuse)  
**Matériaux** : Bois, MDF, contreplaqué  
**Connexion** : USB (`/dev/tty.usbserial-130`) ou **WiFi (AP `MKS_DLC61693`)** ✅  
**Interface** : **Sans écran TFT** - **Interface Web MKS (ESP32-WEB)** ✅  
**Fichier G-code** : `clamp_wood_v4.nc` analysé ✅ (3 opérations, workflow défonceuse manuelle documenté)  
**Paramètres GRBL** : ✅ Configurés pour CNC 3 axes (`$32=0`, `$45=2`, `$47=0`)  
**Interface GUI** : ✅ **ESP32-WEB testée** - Jog, Terminal, Settings, Upload G-code

**Statut** : 🟢 **CARTE 100% PRÊTE** - Firmware + Config + Interface GUI opérationnels  
**Documentation** : ✅ **Manuel PDF + Guide interrupteurs + Analyse G-code** complets  
**G-code** : ✅ `clamp_wood_v4.nc` analysé - 3 opérations, prêt après tests  
**Prochaine étape** : 🔴 **Acheter et brancher interrupteurs de fin de course** (OBLIGATOIRE avant tests moteurs)

**Bonnes usinages !** 🎉🔧
