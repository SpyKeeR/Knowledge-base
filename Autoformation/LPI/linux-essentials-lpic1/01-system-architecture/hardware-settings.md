# 🔧 Déterminer et Configurer le Matériel

---

## 🖥️ lspci - Explorer les périphériques PCI

### Qu'est-ce qu'un bus PCI ?
Imaginez votre ordinateur comme une ville 🏙️. Le bus PCI est l'autoroute qui connecte tous les quartiers (périphériques) : carte graphique, carte réseau, contrôleur SATA, etc.

### La commande de base

```bash
lspci
```

**Ce que vous voyez** : Une liste de TOUS vos périphériques internes
```
00:00.0 Host bridge: Intel Corporation
00:1f.2 SATA controller: Intel Corporation
01:00.0 VGA compatible controller: NVIDIA Corporation
```

### 🎯 Décoder une adresse de bus

Prenons `00:1f.2` :
```
00   :   1f   .   2
│        │       │
│        │       └─── Fonction (quelle "sous-partie" du device)
│        └─────────── Bus (en hexadécimal)
└──────────────────── Domaine (presque toujours 00)
```

**💡 Astuce mémo** : Pensez à une adresse postale : Ville:Rue.Numéro

### Les options qui changent tout

#### `-s` : Cibler UN périphérique précis
```bash
lspci -s 00:1f.2
# Affiche uniquement le contrôleur SATA
```
**Quand l'utiliser ?** Quand vous voulez des infos sur UN périphérique spécifique

#### `-v` : Mode bavard (verbose)
```bash
lspci -v
# Beaucoup plus d'infos : IRQ, mémoire allouée, capacités...
```

#### `-k` : "Quel driver est utilisé ?"
```bash
lspci -k
```
**Affiche** : Le module kernel qui pilote chaque périphérique
```
01:00.0 VGA compatible controller: NVIDIA Corporation
        Kernel driver in use: nvidia
        Kernel modules: nvidiafb, nvidia
```

**🔥 Combo puissant** :
```bash
lspci -s 01:00.0 -k
# "Montre-moi quel driver utilise ma carte graphique"
```

---

## 🔌 lsusb - Explorer les périphériques USB

### La commande de base

```bash
lsusb
```

**Résultat typique** :
```
Bus 001 Device 003: ID 046d:c52b Logitech, Inc. Unifying Receiver
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
```

### Anatomie d'une ligne USB
```
Bus 001 Device 003: ID 046d:c52b
    │      │          │    │
    │      │          │    └─── Product ID
    │      │          └──────── Vendor ID
    │      └─────────────────── Numéro du périphérique sur ce bus
    └────────────────────────── Numéro du bus USB
```

### Options essentielles

#### `-d` : Chercher par ID vendeur:produit
```bash
lsusb -d 046d:c52b
# "Montre-moi uniquement mon dongle Logitech"
```

#### `-s` : Chercher par bus:device
```bash
lsusb -s 1:3
# "Périphérique 3 sur le bus 1"
```

#### `-v` : Informations détaillées
```bash
lsusb -v
# ATTENTION : Très verbeux ! Des pages d'infos
```

#### `-t` : Vue en arbre 🌳 (LA commande à retenir)
```bash
lsusb -t
```

**Affiche** :
```
/:  Bus 01.Port 1: Dev 1, Class=root_hub
    |__ Port 3: Dev 3, If 0, Class=Human Interface Device
    |__ Port 4: Dev 4, If 0, Class=Mass Storage
```

**Pourquoi c'est génial ?** Vous voyez le **multiplexage** : comment vos ports USB se divisent et se connectent physiquement

---

## 🧩 Modules du Kernel - Les "Drivers" Linux

### C'est quoi un module ?
Un module = un **morceau de code** qu'on peut ajouter/retirer du kernel sans redémarrer
**Analogie** : Des plugins qu'on branche dans le kernel 🔌

---

### lsmod - "Quels modules sont actifs ?"

```bash
lsmod
```

**Résultat** :
```
Module                  Size  Used by
nvidia              12345678  42
bluetooth             123456   5
```

**Lecture** :
- `nvidia` est chargé et utilisé par 42 autres composants
- `bluetooth` est chargé et utilisé par 5 composants

---

### modinfo - "Dis-moi tout sur ce module"

#### Info générale
```bash
modinfo bluetooth
```

**Vous obtenez** : Auteur, description, licence, dépendances...

#### 🎯 L'option magique : `-p` (paramètres)
```bash
modinfo -p snd_hda_intel
```

**Affiche** :
```
model:Pretend to have a different codec model
position_fix:Fix DMA pointer
```

**Pourquoi c'est important ?** Ces paramètres peuvent être configurés pour **résoudre des problèmes** ou **optimiser** le matériel

---

### modprobe - Charger/Décharger des modules

#### Décharger un module (le retirer)
```bash
modprobe -r nom_du_module
```

**Cas concret** : Votre Wi-Fi ne fonctionne plus ?
```bash
modprobe -r iwlwifi   # Décharger
modprobe iwlwifi      # Recharger
# → Ça peut résoudre le problème !
```

**⚠️ Attention** : Certains modules sont utilisés par d'autres → il faut d'abord décharger les dépendances

#### Charger un module
```bash
modprobe nom_du_module
```

---

## ⚙️ Configuration Permanente des Modules

### 📁 Où configurer ?

#### Option 1 : `/etc/modprobe.conf`
- Fichier unique (ancien style)
- Peut ne pas exister sur les distributions récentes

#### Option 2 : `/etc/modprobe.d/*.conf` ⭐ (Recommandé)
- Un fichier `.conf` par thématique
- Plus organisé et maintenable

### Exemple concret : Configurer la carte son

**Créez** : `/etc/modprobe.d/sound.conf`
```bash
options snd-hda-intel model=auto power_save=1
```

**Effet** : Au prochain chargement du module, il utilisera ces paramètres

---

### 🚫 Blacklister des modules

**But** : Empêcher un module de se charger au démarrage

**Fichier** : `/etc/modprobe.d/blacklist.conf`

```bash
# Le haut-parleur système (bip agaçant)
blacklist pcspkr

# Bluetooth que je n'utilise jamais
blacklist bluetooth

# Webcam pour raisons de sécurité
blacklist uvcvideo
```

**Syntaxe** : Une ligne = un module
```
blacklist nom_du_module
```

### 🧪 Testez la blacklist
1. Ajoutez `blacklist pcspkr` dans `/etc/modprobe.d/blacklist.conf`
2. Redémarrez
3. `lsmod | grep pcspkr` → Aucun résultat = ça marche !
4. Débranchez-la et refaites `lsusb` → Elle a disparu !

---

## 📌 Gestion des Modules du Kernel

### Lister les modules chargés

```bash
# Afficher tous les modules actuellement chargés dans le kernel
lsmod
```

### Obtenir des informations sur un module

```bash
# In� Bonus : fgrep - Recherche "Bête et Méchante"

### Le problème avec grep normal
```bash
grep "192.168.1.1" fichier.txt
# Oups ! Les points sont des "n'importe quel caractère" en regex
# Ça trouve aussi "192x168x1x1"
```

### La solution : fgrep
```bash
fgrep "192.168.1.1" fichier.txt
# Cherche EXACTEMENT "192.168.1.1"
# Aucune interprétation, aucune magie
```

**Équivalent** : `grep -F`

**Quand utiliser fgrep ?**
- Chercher des IPs : `192.168.1.1`
- Chercher des regex littérales : `.*test`
- Chercher des caractères spéciaux : `$variable[0]`

**Astuce** : Si vous ne voulez PAS que grep réfléchisse, utilisez fgrep !

---

## 📋 Antisèche - Commandes du Jour

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `lspci` | Voir tous mes périphériques PCI | `lspci` |
| `lspci -k` | Savoir quel driver utilise ma carte graphique | `lspci -k \| grep -A 3 VGA` |
| `lsusb` | Lister mes périphériques USB | `lsusb` |
| `lsusb -t` | Voir l'arborescence USB | `lsusb -t` |
| `lsmod` | Voir les modules chargés | `lsmod` |
| `modinfo -p` | Voir les paramètres d'un module | `modinfo -p iwlwifi` |
| `modprobe -r` | Décharger un module | `modprobe -r bluetooth` |
| `fgrep` | Chercher sans regex | `fgrep "192.168.1.1" /etc/hosts` |

---

## 🎓 Points Clés pour l'Examen

✅ **Adresse de bus PCI** : Format `domain:bus:device.function`  
✅ **lspci -k** : Montre quel driver/module est utilisé  
✅ **lsusb -t** : Affiche la topologie USB  
✅ **Blacklist** : Fichier `/etc/modprobe.d/blacklist.conf`  
✅ **Syntaxe blacklist** : `blacklist nom_module` (une par ligne)  
✅ **modprobe -r** : Décharge temporairement, blacklist = permanent  
✅ **modinfo -p** : Montre les paramètres configurables  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Ma carte Wi-Fi ne fonctionne pas
```bash
1. lspci -k | grep -i network    # Quel driver est utilisé ?
2. lsmod | grep iwlwifi          # Le module est-il chargé ?
3. modprobe -r iwlwifi           # Décharger
4. modprobe iwlwifi              # Recharger
```

### Scénario 2 : Je veux désactiver le bip système
```bash
1. echo "blacklist pcspkr" >> /etc/modprobe.d/blacklist.conf
2. reboot
```

### Scénario 3 : Identifier une clé USB qui vient d'être branchée
```bash
1. lsusb                         # Avant branchement
2. (brancher la clé)
3. lsusb                         # Après → noter le nouveau device
4. lsusb -s 2:5 -v              # Détails sur ce périphérique
```

---

**🎯 Prochaine étape** : Boot the System - Comment Linux démarre

*Dernière mise à jour: 21 janvier 2026*
