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
- **Carte** : Makerbase MKS DLC32 MAX
- **Firmware** : MKS DLC32 MAX Firmware Officiel V1.0.10 (GRBL 1.1h)
- **Connectivité** : USB + WiFi

> **⚠️ Pourquoi pas FluidNC ?** Firmware MKS officiel choisi pour sa simplicité (fichier .bin prêt, double Y natif, écran TFT supporté). FluidNC possible plus tard pour utilisateurs avancés.

### Électronique de puissance
- **Moteurs** : NEMA 23 2.2N 82mm (x4) - 2x sur Y, 1x X, 1x Z
- **Drivers** : TB6600
- **Alimentation** : 350W 36V

### Broche et outils
- **Défonceuse** : 800W 30,000 tr/min, pince 1/4" (6.35mm), 220V EU
- **Fraises ARDEN** : Ø 3mm (gravure) et Ø 6mm (dégrossissage)

---

## 🔥 État Actuel & Prochaines Étapes

### ✅ Déjà fait

| Étape | Statut | Détail |
|-------|--------|--------|
| Conception Fusion 360 | ✅ Fait | 3 opérations, G-code généré |
| Alimentation carte | ✅ Fait | Chargeur 19.2V/2A - LED stable |
| Flashage firmware | ✅ Fait | V1.0.10 sur ESP32-S3 |
| Configuration GRBL | ✅ Fait | Mode CNC (`$32=0`, `$45=2`, `$47=0`) |
| gSender | ✅ Installé | v1.6.0 Apple Silicon natif |

### ⚠️ Prochaines étapes

| Étape | Priorité | Détail |
|-------|----------|--------|
| Interrupteurs fin de course | 🔴 **URGENT** | Brancher X±, Y±, Z± avant tests moteurs |
| Test mécanique | 🟡 Important | Brancher moteurs, tester jog/homing |
| Calibration steps/mm | 🟡 Important | Ajuster `$100-$102` selon mécanique |
| Montage mécanique | 🟢 Quand matériel reçu | Assembler structure Raw Inventive |
| Test usinage | 🟢 Final | Premier test avec G-code généré |

---

## 🚀 Démarrage Rapide

### Alimentation

**Chargeur Krisdonia 19.2V/2A (testé et validé)**
```
Chargeur mural → DC jack 5.5x2.1mm → MKS DLC32 MAX (12-24V)
```

⚠️ **L'USB seul ne suffit pas !** La carte a besoin de 12-24V sur VIN.

### Connexion

**USB :** `/dev/tty.usbserial-XXXX` (115200 baud)  
**WiFi :** Se connecter à `MKS_DLC32_XXXX` → `http://192.168.4.1`

### Configuration GRBL Actuelle

```
$32=0      → Mode CNC (pas laser)
$45=2      → Mode XYZ (3 axes)
$47=0      → Sans écran offline
$100-102=80 → Steps/mm par défaut
$110-112=10000/10000/8000 → Vitesses max
$120-122=500/500/500 → Accélérations
$130-132=300/300/80 → Courses max
```

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

**Workflow :**
1. Ouvrir fichier `.gcode` dans gSender → Visualiser 3D
2. Connecter à la CNC (USB ou WiFi)
3. Homing ($H)
4. Jogger → Positionner outil → Zero X/Y/Z
5. Lancer usinage

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

| Élément | Valeur G-code | Limite GRBL | Statut |
|---------|---------------|-------------|--------|
| **Course X** | 52-98 mm | $130 = 300 mm | ✅ OK |
| **Course Y** | 22-128 mm | $131 = 300 mm | ✅ OK |
| **Course Z** | 0-27 mm | $132 = 80 mm | ✅ OK |
| **Vitesse X/Y** | F1800 mm/min | $110-$111 = 3000 | ✅ OK |

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

## 🔌 Interrupteurs de Fin de Course

> **⚠️ SÉCURITÉ :** Sans fin de course, pas de homing possible. **OBLIGATOIRE** avant tests moteurs.

### Branchement MKS DLC32 MAX

```
Micro-switch 3 broches :
├── COM → GND (borne GND commune)
└── NO  → Signal (X+, X-, Y+, Y-, Z+, Z-)
```

**Ordre de priorité :**
1. Z- (butée basse) - sécurité critique
2. X-, Y- (homing)
3. X+, Y+, Z+ (limites max)

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
> | Caractéristique | Vis simple filet<br>(Lead 2mm) | Vis multifilets<br>(Lead 8mm - Ta config) |
> |-----------------|--------------------------------|-------------------------------------------|
> | **Steps/mm** (`$102`) | 1600 | **400** |
> | **Vitesse Z** | Lente | 4× plus rapide |
> | **Résolution** | 0.00125mm/pas | 0.005mm/pas (suffisant bois) |
> | **Force** | Élevée | Réduite (⚠️ éviter accélérations trop fortes) |
> | **Usage idéal** | Métal, charges lourdes | Bois, rapidité |
> 
> **Réglages adaptés pour lead 8mm :**
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
- [ ] Drivers TB6600 réglés (~2A)
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

| Problème | Solution |
|----------|----------|
| Machine ne répond pas | Vérifier alimentation 12-24V, envoyer `$X` pour unlock |
| Moteur tourne à l'envers | Modifier `$3` ou inverser 2 fils (A+ ↔ A-) |
| Moteur saute des pas | Augmenter courant driver, réduire `$120-$122` |
| Cote pas bonne | Recalibrer `$100-$102` avec règle, vérifier jeu mécanique |
| Double Y désynchronisé | Vérifier direction moteurs, ajuster `$3` |
| G28 échoue | Vérifier interrupteurs fin de course branchés |

---

## 🔄 FluidNC (Option Future)

Pour plus tard si besoin de fonctionnalités avancées :
- Configuration YAML plus flexible
- Mises à jour OTA
- ⚠️ Nécessite compilation branche S3 (ESP32-S3)
- ⚠️ Écran TFT MKS peut ne pas être supporté

> Commencer avec firmware MKS officiel, migrer vers FluidNC une fois la CNC maîtrisée.

---

## 📚 Ressources

- **`MKS DLC32 MAX Manual.pdf`** - Manuel officiel (schémas, paramètres)
- **[BOM.md](BOM.md)** - Liste matériel complète
- [MKS DLC32 Wiki](https://github.com/makerbase-mks/MKS-DLC32)
- [GRBL Configuration](https://github.com/gnea/grbl/wiki/Grbl-v1.1-Configuration)
- [Reddit r/hobbycnc](https://www.reddit.com/r/hobbycnc/)

---

## 🎯 Résumé du Setup

| Élément | Configuration |
|---------|---------------|
| **Projet** | Raw Inventive CNC 3 axes |
| **Électronique** | MKS DLC32 MAX + TB6600 + NEMA 23 |
| **Firmware** | MKS V1.0.10 (GRBL 1.1h) |
| **Alimentation carte** | Chargeur 19.2V/2A ✅ |
| **Alimentation moteurs** | 36V 350W ⚠️ à installer |
| **Contrôle** | **gSender 1.6.0** (USB/WiFi) |
| **Broche** | Défonceuse 800W - **Manuel 220V** |
| **Connexion** | `/dev/tty.usbserial-130` ou WiFi `MKS_DLC61693` |
| **Sans écran TFT** | Contrôle via gSender sur Mac |

**Statut** : 🟢 Carte configurée et prête  
**Prochaine étape** : 🔴 **Brancher interrupteurs fin de course** puis tester moteurs

**Bonnes usinages !** 🎉🔧
