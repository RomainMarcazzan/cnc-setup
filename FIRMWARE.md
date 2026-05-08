# Firmware MKS DLC32 MAX — Configuration CNC

## Firmware actuel : grblHAL

| | |
|---|---|
| **Firmware** | grblHAL v1.1f (build 20260506) |
| **Board** | MKS DLC32 MAX 1.0 |
| **Driver** | ESP32-S3 @ 240MHz |
| **Source** | [grblHAL Web Builder](http://svn.io-engineering.com:8080/) — Driver: ESP32, Board: MKS DLC32 MAX v1 8MB+ flash |
| **Config** | 3 axes XYZ, Y ganged (Y2 sur connecteur A), WiFi STA, SD card |
| **Statut** | ✅ Validé — gSender 1.6.0 connecté, jog X/Y/Z fonctionnel, dual Y confirmé |

### Flasher (Mac)

Les binaires sont dans le dossier `firmware/` du projet.

```bash
cd firmware
esptool --chip esp32s3 --port /dev/cu.usbserial-120 --baud 115200 --no-stub write-flash \
  0x0     bootloader.bin \
  0x8000  partitions.bin \
  0x10000 firmware.bin
```

### Connexion série

```bash
screen /dev/cu.usbserial-120 115200
# Quitter : Ctrl+A puis K puis Y
```

---

## Pourquoi grblHAL plutôt que le firmware MKS

Le firmware MKS (LAS V1.0.10) est conçu pour les machines laser. Il ne supporte pas le slaving A→Y (dual Y indépendant). Avec grblHAL :

- **Y ganged** : Y1 (connecteur Y) + Y2 (connecteur A) bougent ensemble sur commande Y
- **Auto-squaring** : homing indépendant Y1/Y2 pour équerre le portique (nécessite endstop sur Y- et A-)
- **Inversion Y2** configurable sans toucher aux câbles (`$3`)
- Meilleur support gSender

---

## Câblage moteurs (validé)

| Connecteur MKS | TB6600 | Moteur | Sens testé |
|----------------|--------|--------|------------|
| X (E S D G) | TB6600 X | Moteur X | Anti-horaire sur X+ |
| Y (E S D G) | TB6600 Y1 | Moteur Y1 | Anti-horaire sur Y+ |
| A (E S D G) | TB6600 Y2 | Moteur Y2 | Horaire sur Y+ (sens opposé = normal pour portique) |
| Z (E S D G) | TB6600 Z | Moteur Z | Horaire sur Z+ |

> Les sens seront inversés si besoin après assemblage via `$3` (grblHAL) sans toucher aux câbles.

---

## Steps/mm — calcul

Configuration : **1/16 microstep + courroie GT2 + poulie 20 dents**

```
200 pas/tour × 16 microsteps = 3200 pas/tour
Poulie 20 dents × 2mm pas = 40 mm/tour
→ 3200 ÷ 40 = 80 pas/mm
```

---

## Paramètres à configurer après assemblage

### 1. Courses max (à mesurer sur la machine)

```
$130=1200   → X max travel (mm)
$131=1200   → Y max travel (mm)
$132=???    → Z max travel (à mesurer)
```

### 2. Direction moteurs si sens incorrect

```
$3=0   → X+ Y+ Z+ (défaut)
$3=1   → X- Y+ Z+
$3=2   → X+ Y- Z+
$3=3   → X- Y- Z+
... etc (valeur binaire : bit0=X, bit1=Y, bit2=Z)
```

### 3. Homing (après installation endstops)

```
$22=1   → activer homing
$23=0   → direction homing (à vérifier selon position endstops)
$H      → lancer homing
```

### 4. WiFi (optionnel)

```
$SSID=NomDuReseau
$PASSWORD=MotDePasse
```

---

## Commandes utiles

```
$I     → version firmware
$$     → tous les paramètres
$J=G91 X10 F1000   → jog X +10mm
$J=G91 Y10 F1000   → jog Y +10mm
$J=G91 Z5 F500     → jog Z +5mm
```

