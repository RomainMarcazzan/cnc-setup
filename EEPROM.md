# 📝 EEPROM — Config GRBL Historique

Snapshot des paramètres EEPROM de la carte MKS DLC32 MAX.

---

## 🔍 État actuel — 2026-05-11

**Lecture directe depuis la carte via `/dev/ttyUSB0`**

### Version Firmware ($I)

```
[VER:1.1f.20260506:]
[OPT:VNMSL2,100,1024,3,0]
[AXS:3:XYZ]
[FIRMWARE:grblHAL]
[BOARD:MKS DLC32 MAX 1.0]
[DRIVER:ESP32-S3@240MHz]
[WIFI STA MAC:98:88:e0:17:fd:f0]
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
| **Vitesse/Accélération** | | |
| $110 | 500.000 | X max rate (mm/min) |
| $111 | 500.000 | Y max rate (mm/min) |
| $112 | 500.000 | Z max rate (mm/min) |
| $120 | 10.000 | X acceleration (mm/sec²) |
| $121 | 10.000 | Y acceleration (mm/sec²) |
| $122 | 10.000 | Z acceleration (mm/sec²) |
| **Steps/mm (⚠️ À CALIBRER)** | | |
| $100 | 250.00000 | X steps/mm (usine) |
| $101 | 250.00000 | Y steps/mm (usine) |
| $102 | 250.00000 | Z steps/mm (usine) |
| **Courses max** | | |
| $130 | 200.000 | X max travel (mm) |
| $131 | 200.000 | Y max travel (mm) |
| $132 | 200.000 | Z max travel (mm) |
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

## ⚠️ Notes importantes

### Firmware
- **Compilé avec grblHAL Web Builder** le **2026-05-06**
- **Board**: MKS DLC32 MAX 1.0
- **Y-ganged activé** → Connecteur **Y = Y1**, Connecteur **A = Y2** (synchro auto par firmware)
- **Test confirmé** : Les deux moteurs Y bougent ensemble sur commande Y

### Valeurs USINE (à modifier)
Les `$100-$102` et `$110-$132` sont aux **valeurs par défaut** du Web Builder. Elles correspondent à rien dans notre config mécanique.

### Configuration cible (selon BOM)
À configurer plus tard (après test mécanique) :

```
$100=71.11   ; X (HTD 3M × 15T × 1/16)
$101=71.11   ; Y (idem)
$102=400     ; Z (T8 lead 8mm × 1/16)
```

---

## 📋 Historique

| Date | Action | Fichier |
|------|--------|---------|
| 2026-05-11 | Première lecture EEPROM depuis Linux | Ce fichier |
