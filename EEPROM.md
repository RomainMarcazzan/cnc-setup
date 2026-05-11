# 📝 EEPROM — Config GRBL Historique

Snapshot des paramètres EEPROM de la carte MKS DLC32 MAX.

---

## 🔍 État actuel — 2026-05-11 (v2 : configuration appliquée)

**Lecture directe depuis la carte via `/dev/ttyUSB0`**

### Version Firmware ($I)

```
[VER:1.1f.20260506:]
[OPT:VNMSL2,100,1024,3,0]
[AXS:3:XYZ]
[FIRMWARE:grblHAL]
[BOARD:MKS DLC32 MAX 1.0]
[DRIVER:ESP32-S3@240MHz]
```

### Paramètres EEPROM ($$)

| Paramètre | Valeur 2026-05-11 | Description |
|-----------|-------------------|-------------|
| **Step Pulse** | | |
| $0 | 5.0 | Step pulse time (μs) |
| $1 | 25 | Step idle delay (ms) |
| $2 | 0 | Step invert mask |
| $3 | 0 | Direction invert mask (000=X+Y+Z+) |
| $4 | 7 | Step enable invert |
| $5 | 0 | Limit pins invert |
| $6 | 0 | Probe pin invert |
| $10 | 511 | Status report mask |
| $11 | 0.010 | Junction deviation (mm) |
| $12 | 0.002 | Arc tolerance (mm) |
| $13 | 0 | Report in inches |
| **Homing** | | |
| $20 | 0 | Soft limits enable |
| $21 | 0 | Hard limits enable |
| $22 | 0 | Homing cycle enable |
| $23 | 0 | Homing dir invert mask |
| $24 | 25.0 | Homing locate feed (mm/min) |
| $25 | 500.0 | Homing seek rate (mm/min) |
| $26 | 250 | Homing switch debounce (ms) |
| $27 | 1.000 | Homing switch pull-off (mm) |
| **Cycles homing** | | |
| $43 | 1 | Homing passes |
| $44 | 4 | Homing cycle 1 (4 = Z) |
| $45 | 3 | Homing cycle 2 (3 = X+Y) |
| $46 | 0 | Homing cycle 3 (0 = none) |
| **Steps/mm** | | |
| $100 | **71.11** | X steps/mm (HTD 3M × 15T × 1/16) |
| $101 | **71.11** | Y steps/mm (idem) |
| $102 | **400.0** | Z steps/mm (T8 lead 8mm × 1/16) |
| **Vitesse/Accélération** | | |
| $110 | **3000.0** | X max rate (mm/min) |
| $111 | **3000.0** | Y max rate (mm/min) |
| $112 | **1000.0** | Z max rate (mm/min) |
| $120 | **100.0** | X acceleration (mm/sec²) |
| $121 | **100.0** | Y acceleration (mm/sec²) |
| $122 | **50.0** | Z acceleration (mm/sec²) |
| **Courses max** | | |
| $130 | **1200.0** | X max travel (mm) |
| $131 | **1200.0** | Y max travel (mm) |
| $132 | **80.0** | Z max travel (mm) |
| **Broche/Laser** | | |
| $32 | 0 | Laser mode disable (mode CNC) |
| $30 | 1000.000 | Max spindle speed (RPM) |
| $31 | 0.000 | Min spindle speed (RPM) |
| $33 | 5000.0 | Spindle PWM frequency (Hz) |
| **WiFi** | | |
| $322 | 192.168.5.1 | AP IP |
| $323 | 192.168.5.1 | STA IP |
| $324 | 255.255.255.0 | Netmask |

---

## 📋 Historique des snapshots

| Date | État | Fichier |
|------|------|---------|
| 2026-05-11 (v2) | **Config appliquée** : steps/mm, vitesses, accélérations, courses | Ce commit |
| 2026-05-11 (v1) | Valeurs usine (`$100-$102=250`) | Commit précédent `3cbc979` |

---

## ℹ️ Notes importantes

### Firmware
- **Compilé avec grblHAL Web Builder** le **2026-05-06**
- **Board**: MKS DLC32 MAX 1.0
- **Y-ganged activé** → Connecteur **Y = Y1**, Connecteur **A = Y2** (synchro auto par firmware)
- **Test confirmé** : Les deux moteurs Y bougent ensemble sur commande Y

### Config théorique vs calibration réelle
Ces valeurs sont des **calculs théoriques** :
```
X/Y: (200 pas/tour × 16 microstep) ÷ (15 dents × 3mm) = 71.11 steps/mm
Z:   (200 pas/tour × 16 microstep) ÷ 8mm = 400 steps/mm
```

⚠️ **À calibrer plus tard** :
1. Jog `X+100mm`, mesurer avec une règle ce que la machine fait réellement
2. Ajuster : `$100 = $100 × (distance mesurée ÷ 100)`
3. Recommencer pour Y et Z

---

## ⚠️ Points à faire

- [ ] Brancher endstops (X-, Y-, Z-)
- [ ] Activer homing (`$22=1`)
- [ ] Tester moteurs
- [ ] Calibrer steps/mm (mesure réelle)
