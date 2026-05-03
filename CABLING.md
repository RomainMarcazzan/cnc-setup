# 🔌 Câblage CNC Raw Inventive

Référence de câblage pour la boîte de contrôle centralisée.
**Hypothèse :** boîte posée à ~50cm de la machine, côté arrière-gauche.

---

## 🗺️ Vue d'ensemble

```
                        TABLE SUR ROULETTES
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│   ┌─────────────────────────────────────────────┐              │
│   │                  CNC 1220×1220mm             │              │
│   │                                              │              │
│   │  [Z motor]──────────────[X motor]            │              │
│   │      │ (gantry, mobile)      │               │              │
│   │      │                       │               │              │
│   │  [Y2 motor]            [Y1 motor]            │              │
│   │   arrière-droite        arrière-gauche        │              │
│   │  [endstop Y2-]         [endstop Y1-]         │              │
│   │                                              │              │
│   │  [endstop X-]    [endstop X+]               │              │
│   │  [endstop Z-]    [endstop Z+]               │              │
│   └─────────────────────────────────────────────┘              │
│                              ↕ ~50cm câbles                     │
│   ┌──────────────────────────────────┐                         │
│   │       BOÎTE DE CONTRÔLE          │                         │
│   │  [PSU 36V] [TB×4] [MKS DLC32]   │── USB/WiFi ──► MAC      │
│   │  [Chargeur 19.2V → MKS]         │                         │
│   └──────────────────────────────────┘                         │
│                    │ 220V                                        │
│                 [Prise murale]                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📦 Intérieur boîte de contrôle

Deux alimentations distinctes : le **chargeur Krisdonia** alimente la carte MKS (logique), le **PSU 36V** alimente les moteurs via les TB6600. Ils partagent la même prise 220V.

```
                        220V secteur
                            │
              ┌─────────────┴──────────────┐
              │                            │
     [Chargeur Krisdonia]           [PSU 36V 350W]
       19.2V / 2A                    VCC+  VCC-
              │                       │     │
              ▼                       ▼     ▼
        MKS VIN                ┌──────┴─────┴──────┐
        (logique)              │  (alim moteurs)   │
                               │                   │
                         [TB6600 X]          [TB6600 Y1]
                         [TB6600 Y2]         [TB6600 Z]
                               │
                         Step / Dir
                         (câbles Dupont)
                               │
                               ▼
                        [MKS DLC32 MAX]
                        connecteurs E S D G
                               │
                    ┌──────────┴──────────┐
                    │                     │
               Endstops              USB / WiFi
             (X-, Y-, Z-)             → Mac
```

**Résumé des connexions internes :**

| De                  | Vers                        | Câble                  |
| ------------------- | --------------------------- | ---------------------- |
| Prise 220V          | Chargeur Krisdonia          | Câble secteur          |
| Prise 220V          | PSU 36V                     | Câble secteur          |
| Chargeur Krisdonia  | MKS VIN (jack DC 5.5×2.1mm) | Jack DC existant ~50cm |
| PSU 36V V+/V-       | Domino + (et domino -)      | 2×1.5mm² CCA ~10cm     |
| Domino + / Domino - | TB6600 ×4 (VCC/GND)         | 2×1.5mm² CCA ~30cm ×4  |
| MKS E S D G (×4)    | TB6600 ×4 (PUL/DIR)         | Dupont 3 fils ~20cm ×4 |

**Distribution PSU → TB6600 via dominos :**

```
PSU 36V
  V+ ──► [domino +] ──┬──► TB6600 X  VCC
                       ├──► TB6600 Y1 VCC
                       ├──► TB6600 Y2 VCC
                       └──► TB6600 Z  VCC

  V- ──► [domino -] ──┬──► TB6600 X  GND
                       ├──► TB6600 Y1 GND
                       ├──► TB6600 Y2 GND
                       └──► TB6600 Z  GND
```

> Un domino 5 bornes suffit (1 entrée PSU + 4 sorties TB6600). Utiliser du **2×1.5mm²** sur tout ce circuit — le PSU peut débiter jusqu'à ~9A total (4 moteurs × 2.8A).

---

## ⚡ Connexions TB6600

### Schéma d'un TB6600 (HLTNC — labels réels)

```
                    TB6600 HLTNC
              ┌─────────────────┐
              │   ENA- (ENA)    ├──── laisser non connecté (moteur toujours actif)
              │   ENA+ (+5V)    ├──── laisser non connecté
              │   DIR- (DIR)    ├──┐
              │   DIR+ (+5V)    ├──│── MKS D (Direction)
              │   PUL- (PUL)    ├──┤← pont court sur TB6600 ──── MKS G (GND)
              │   PUL+ (+5V)    ├──│── MKS S (Step)
              │                 │  └─ (fil ~2cm entre PUL- et DIR-)
              │   B-            ├──► Moteur bobine B (fil -)
              │   B+            ├──► Moteur bobine B (fil +)
              │   A-            ├──► Moteur bobine A (fil -)
              │   A+            ├──► Moteur bobine A (fil +)
              │   GND           ├──── PSU 36V (-)
              │   VCC           ├──── PSU 36V (+)
              └─────────────────┘
```

> **Astuce :** ponter PUL- et DIR- avec un fil de ~2cm directement sur le TB6600 → un seul fil GND depuis MKS G suffit. **3 fils Dupont par TB6600** (S, D, G) au lieu de 4.

> ⚠️ **Niveau de signal : 3.3V** — La MKS DLC32 MAX est basée sur ESP32-S3, les sorties E S D G sont en **3.3V**. Le TB6600 est prévu pour 5V mais fonctionne généralement en common-cathode à 3.3V. Si tu observes des pas perdus ou comportements erratiques, passer en **common-anode** : connecter PUL+ et DIR+ au **pin 5V de la carte MKS** (régulateur interne), et PUL-/DIR- aux signaux S et D. Nécessite d'inverser la logique dans GRBL (`$2` et `$3`).

**Connexion MKS → TB6600 (résumé) :**

| MKS (E S D G) | →   | TB6600 HLTNC             |
| ------------- | --- | ------------------------ |
| S (Step)      | →   | PUL+ (+5V)               |
| D (Dir)       | →   | DIR+ (+5V)               |
| G (GND)       | →   | PUL- pontée à DIR- (2cm) |

> **ENA :** laisser non connecté = moteur toujours actif. Connecter ENA- au MKS E uniquement si tu veux désactiver les moteurs entre les usinages.

### Connecteurs physiques sur la carte MKS DLC32 MAX

La carte a **deux rangées de connecteurs moteurs** — ne pas confondre :

| Connecteur          | Emplacement      | Utilisation                                                           |
| ------------------- | ---------------- | --------------------------------------------------------------------- |
| J27/J29/J28/J30/J16 | Haut de la carte | Drivers **intégrés** — **non utilisés** dans notre config             |
| **E S D G** (×4)    | Milieu de carte  | Sorties drivers **externes** → **c'est ici qu'on branche les TB6600** |

Chaque groupe **E S D G** correspond à un axe (X, Y, Z, A) :

- **E** = Enable (ENA+)
- **S** = Step (PUL+)
- **D** = Direction (DIR+)
- **G** = Ground (GND commun)

### Tableau de connexions MKS DLC32 MAX → TB6600

| MKS (broche E S D G) | Fil | TB6600 X         | TB6600 Y1        | TB6600 Y2        | TB6600 Z         |
| -------------------- | --- | ---------------- | ---------------- | ---------------- | ---------------- |
| X → S (Step)         | →   | PUL+             |                  |                  |                  |
| X → D (Dir)          | →   | DIR+             |                  |                  |                  |
| Y → S (Step)         | →   |                  | PUL+             | PUL+             |                  |
| Y → D (Dir)          | →   |                  | DIR+             | DIR+             |                  |
| Z → S (Step)         | →   |                  |                  |                  | PUL+             |
| Z → D (Dir)          | →   |                  |                  |                  | DIR+             |
| G (GND)              | →   | PUL-, DIR-, ENA- | PUL-, DIR-, ENA- | PUL-, DIR-, ENA- | PUL-, DIR-, ENA- |

> **Y1 et Y2 partagent les mêmes signaux Step/Dir** — branchés en parallèle depuis les mêmes broches MKS. Si un moteur tourne à l'envers : inverser A+ ↔ A- sur ce TB6600 uniquement (ne pas changer `$3`).

---

## 🔧 Réglages DIP switches TB6600 HLTNC

Les switches sont numérotés **1→6 de gauche à droite** sur le bloc physique. ON = poussé vers le bas (ON↓).

- **SW1 SW2 SW3** → Microstep
- **SW4 SW5 SW6** → Courant

### Réglage pour notre config (1/16 microstep + 2.8A)

Moteur 57HS82 nominal = **3.0A**. On règle à **2.8A** (93% du nominal — bon compromis couple/chaleur).

```
        ← gauche                 droite →
          1     2     3     4     5     6
         ↑     ↑     ↓     ↑     ↑     ↓
        OFF   OFF    ON   OFF   OFF    ON
        └───microstep───┘ └───courant───┘
             1/16               2.8A
```

> Switches **3 et 6** vers le bas (ON). Les 4 autres vers le haut (OFF).

### Table complète (étiquette HLTNC)

**Microstep (SW1 SW2 SW3) :**

| Microstep | Pas/tr   | SW1     | SW2     | SW3                   |
| --------- | -------- | ------- | ------- | --------------------- |
| 1         | 200      | ON      | ON      | OFF                   |
| 2/A       | 400      | ON      | OFF     | ON                    |
| 2/B       | 400      | OFF     | ON      | ON                    |
| 4         | 800      | ON      | OFF     | OFF                   |
| 8         | 1600     | OFF     | ON      | OFF                   |
| **16**    | **3200** | **OFF** | **OFF** | **ON** ← notre config |
| 32        | 6400     | OFF     | OFF     | OFF                   |

**Courant (SW4 SW5 SW6) :**

| Courant  | Pic      | SW4     | SW5     | SW6                   |
| -------- | -------- | ------- | ------- | --------------------- |
| 0.5A     | 0.7A     | ON      | ON      | ON                    |
| 1.0A     | 1.2A     | ON      | ON      | OFF                   |
| 1.5A     | 1.7A     | ON      | OFF     | ON                    |
| 2.0A     | 2.2A     | ON      | OFF     | OFF                   |
| 2.5A     | 2.7A     | OFF     | ON      | ON                    |
| **2.8A** | **2.9A** | **OFF** | **OFF** | **ON** ← notre config |
| 3.0A     | 3.2A     | OFF     | ON      | OFF                   |
| 3.5A     | 4.0A     | OFF     | OFF     | OFF                   |

---

## 🔩 Connexions moteurs NEMA 23

Les moteurs ont 4 fils (2 bobines). Les couleurs varient selon le lot HLTNC — à identifier avec un multimètre si non étiquetés.

```
NEMA 23
───────
Bobine A :  fil 1 ──► TB6600 A+
            fil 2 ──► TB6600 A-

Bobine B :  fil 3 ──► TB6600 B+
            fil 4 ──► TB6600 B-
```

**Couleurs des fils (57HS82 HLTNC) :**

| Couleur | Bobine |
| ------- | ------ |
| Rouge   | A+     |
| Vert    | A-     |
| Jaune   | B+     |
| Bleu    | B-     |

**Pour identifier les bobines :** toucher deux fils ensemble — s'ils offrent une résistance (moteur dur à tourner à la main), c'est la même bobine.

**Si le moteur tourne à l'envers :** inverser A+ ↔ A- (ou B+ ↔ B-) sur le TB6600, **ne pas modifier `$3` dans GRBL** sauf si les deux Y tournent à l'envers en même temps.

---

## 🔴 Interrupteurs fin de course (MS5-R)

### Branchement

```
MS5-R (micro-switch 3 broches)
           ┌──────┐
     COM ──┤      ├── GND  ──► GND du MKS
      NO ──┤      ├── Signal ──► Pin endstop MKS
      NC ──┤      │   (non utilisé)
           └──────┘
```

### Connecteurs endstop sur la carte MKS DLC32 MAX

Les endstops sont sur la **droite de la carte** (J9, J10, J11, J12) — étiquetés **X-, Y-, Z-, A-**.  
Chaque connecteur a 2 broches : **Signal** + **GND**.

### Tableau de câblage endstops

| Switch | Position sur la machine | Connecteur MKS (physique) | Priorité      |
| ------ | ----------------------- | ------------------------- | ------------- |
| Z-     | Butée basse axe Z       | Z- (J11) — droite carte   | 🔴 En premier |
| X-     | Butée gauche axe X      | X- (J9) — droite carte    | 🟡 Homing     |
| Y-     | Butée arrière axe Y1    | Y- (J10) — droite carte   | 🟡 Homing     |
| X+     | Butée droite axe X      | X+                        | 🟢 Limite     |
| Y+     | Butée avant axe Y       | Y+                        | 🟢 Limite     |
| Z+     | Butée haute axe Z       | Z+                        | 🟢 Limite     |

> **Commencer avec Z-, X-, Y-** seulement — suffisant pour le homing. Les X+/Y+/Z+ peuvent attendre.

### Test endstop depuis gSender

```gcode
?        → vérifier que les Pn: (pins) apparaissent quand tu appuies sur un switch
$22=1    → activer homing
$H       → lancer homing (Z d'abord, puis X et Y)
```

---

## 📏 Longueurs de câble estimées

**Hypothèse :** boîte à 50cm de la machine, côté arrière-gauche + marge de routage.

| Câble                    | De → À                        | Longueur estimée    | Nb fils          | Câble à utiliser                           |
| ------------------------ | ----------------------------- | ------------------- | ---------------- | ------------------------------------------ |
| Alim PSU → TB6600 ×4     | Interne boîte                 | ~30cm ×4            | 2                | 2×1,5mm² CCA                               |
| Moteur Y1                | Boîte → arrière-gauche        | ~80cm               | 4                | Câble origine moteur + rallonge si besoin  |
| Moteur Y2                | Boîte → arrière-droite        | ~180cm              | 4                | Câble origine + rallonge 2×1,5mm² (2 runs) |
| Moteur X                 | Boîte → gantry (mobile Y)     | ~130cm + 30cm slack | 4                | Câble origine + rallonge                   |
| Moteur Z                 | Boîte → carriage (mobile X+Y) | ~160cm + 30cm slack | 4                | Câble origine + rallonge                   |
| Endstops ×3 (Z-, X-, Y-) | Boîte → switches              | ~80 à 180cm         | 2 chacun         | Câble fin (Dupont ou 0,5mm²)               |
| MKS → chaque TB6600      | Interne boîte                 | ~20cm ×4            | 3 (Step/Dir/GND) | Câbles Dupont                              |
| Alimentation MKS         | Chargeur → MKS                | ~50cm               | 2                | Jack DC existant                           |

> **Mesure avant de couper** — ces longueurs sont des estimations. Ajoute toujours 15-20cm de marge.

---

## 🗂️ Routage des câbles

```
                    CHAÎNE PORTE-CÂBLES
                    (à installer — BOM)
                         │
Boîte ──────── 50cm ─────┤──── long du cadre ────► Moteurs Y1/Y2
                         │
                         └──── chaîne Y ─────────► Moteur X
                                                        │
                                              chaîne X ─┘──► Moteur Z + Endstops Z
```

**Câbles mobiles (dans chaîne porte-câbles) :**

- Moteur X → doit bouger sur toute la course Y (~1220mm)
- Moteur Z → doit bouger sur course X (~1220mm) + Y

**Câbles fixes (le long du cadre) :**

- Moteurs Y1 et Y2
- Endstops Y-, X-

---

## ⚠️ Points d'attention

- **Ne pas brancher/débrancher les moteurs sous tension** — risque de griller le TB6600
- **Démarrer avec courant TB6600 réduit (~1.5A)** pour les premiers tests, puis augmenter si le moteur manque de couple
- **Vérifier le sens de rotation avant d'assembler** — plus facile de corriger les fils à vide
- **Slack câbles axes mobiles** — prévoir 30cm de mou supplémentaire pour les axes X et Z qui bougent
- **Ventilation boîte** — les 4 TB6600 sous charge dégagent de la chaleur → prévoir grille ou petit ventilateur 12V
