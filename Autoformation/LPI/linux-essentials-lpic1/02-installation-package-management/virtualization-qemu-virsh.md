# 🖥️ Gestion de Machines Virtuelles - QEMU/virsh

---

## 🎯 QEMU : Quick EMUlator

**QEMU** = **Q**uick **EMU**lator

**Rôle** : Émulateur + virtualiseur pour KVM

**Deux modes** :
1. **Émulation pure** : Émule CPU complet (lent, compatible tout)
2. **Virtualisation KVM** : Accélération matérielle (rapide, nécessite VT-x/AMD-V)

**Analogie** 🎮 :
- **QEMU seul** = Émulateur console (PlayStation sur PC)
- **QEMU + KVM** = Console native (performances quasi-matériel)

---

### QEMU vs KVM

| Mode | Performance | Pré-requis | Usage |
|------|-------------|------------|-------|
| **QEMU pur** | ⭐⭐ Lent | Aucun | Émulation architectures différentes |
| **QEMU + KVM** | ⭐⭐⭐⭐⭐ Rapide | VT-x/AMD-V | Virtualisation production |

**💡 En production** : Toujours QEMU + KVM

---

### Architecture QEMU-KVM

```
┌──────────────────────────────────┐
│   Machine Virtuelle (Guest)      │
├──────────────────────────────────┤
│   QEMU (émulation périphériques) │ ← Disque, réseau, carte vidéo
├──────────────────────────────────┤
│   KVM (module noyau)             │ ← Accélération CPU/mémoire
├──────────────────────────────────┤
│   Linux Host                     │
├──────────────────────────────────┤
│   Matériel Physique (VT-x/AMD-V) │
└──────────────────────────────────┘
```

**QEMU** : Émule périphériques (disque, réseau, USB...)  
**KVM** : Accélère CPU et mémoire (instructions natives)

---

## 🔧 virsh : Interface CLI Gestion VMs

**virsh** = **vir**tual **sh**ell

**Rôle** : Ligne de commande pour gérer VMs via **libvirt**

**Équivalent** :
- VirtualBox → `VBoxManage`
- VMware → `vmrun`
- virsh → KVM/Xen/QEMU

**Avantages** :
- ✅ Scriptable (automatisation)
- ✅ Accès SSH distant
- ✅ Pas d'interface graphique nécessaire
- ✅ Gestion fine ressources

**Analogie** 🎛️ : Cockpit d'avion (contrôles précis) vs tableau de bord (virt-manager GUI)

---

## 📦 Installation Prérequis

### Paquets Requis

**Ubuntu/Debian** :
```bash
sudo apt-get update
sudo apt-get install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
```

**RHEL/CentOS** :
```bash
sudo yum install qemu-kvm libvirt libvirt-client virt-install
```

**openSUSE** :
```bash
sudo zypper install qemu-kvm libvirt libvirt-client
```

---

### Démarrer et Activer libvirtd

```bash
# Démarrer daemon
sudo systemctl start libvirtd

# Activer au boot
sudo systemctl enable libvirtd

# Vérifier statut
sudo systemctl status libvirtd
# ● libvirtd.service - Virtualization daemon
#    Active: active (running)
```

---

### Ajouter Utilisateur au Groupe libvirt

```bash
# Ajouter user au groupe
sudo usermod -aG libvirt $USER

# Recharger groupes (ou déconnexion/reconnexion)
newgrp libvirt

# Vérifier
groups
# user sudo libvirt
```

**💡 Sans groupe libvirt** : Nécessite `sudo` pour chaque commande virsh

---

## 📝 Fichier XML Définition VM

### Structure Minimale

**Format** : XML (eXtensible Markup Language)

**Emplacement** : N'importe où (temporaire), sera copié dans `/etc/libvirt/qemu/` lors `define`

**Fichier minimal** : `TestMachine.xml`

```xml
<domain type="qemu">
  <name>TestMachine</name>
  <memory unit="GiB">1</memory>
  <vcpu>1</vcpu>
  <os>
    <type arch="x86_64">hvm</type>
  </os>
</domain>
```

---

### Explication Balises

| Balise | Valeur | Signification |
|--------|--------|---------------|
| `<domain type="qemu">` | `qemu`, `kvm` | Type hyperviseur |
| `<name>` | Texte | Nom unique de la VM |
| `<memory unit="GiB">` | Nombre + unité | RAM allouée (KiB, MiB, GiB) |
| `<vcpu>` | Nombre | CPUs virtuels |
| `<os><type arch="">` | `x86_64`, `i686` | Architecture CPU |
| `hvm` | Hardware Virtual Machine | Virtualisation complète |

---

### Fichier XML Complet (Production)

```xml
<domain type='kvm'>
  <name>web-server</name>
  <uuid>a1b2c3d4-e5f6-7890-abcd-1234567890ab</uuid>
  <memory unit='GiB'>2</memory>
  <currentMemory unit='GiB'>2</currentMemory>
  <vcpu placement='static'>2</vcpu>
  
  <os>
    <type arch='x86_64' machine='pc-q35-6.2'>hvm</type>
    <boot dev='hd'/>
  </os>
  
  <features>
    <acpi/>
    <apic/>
  </features>
  
  <cpu mode='host-passthrough' check='none'/>
  
  <clock offset='utc'>
    <timer name='rtc' tickpolicy='catchup'/>
    <timer name='pit' tickpolicy='delay'/>
    <timer name='hpet' present='no'/>
  </clock>
  
  <on_poweroff>destroy</on_poweroff>
  <on_reboot>restart</on_reboot>
  <on_crash>destroy</on_crash>
  
  <devices>
    <emulator>/usr/bin/qemu-system-x86_64</emulator>
    
    <!-- Disque virtuel -->
    <disk type='file' device='disk'>
      <driver name='qemu' type='qcow2'/>
      <source file='/var/lib/libvirt/images/web-server.qcow2'/>
      <target dev='vda' bus='virtio'/>
    </disk>
    
    <!-- CD-ROM (ISO) -->
    <disk type='file' device='cdrom'>
      <source file='/var/lib/libvirt/images/ubuntu-22.04.iso'/>
      <target dev='sda' bus='sata'/>
      <readonly/>
    </disk>
    
    <!-- Réseau -->
    <interface type='network'>
      <source network='default'/>
      <model type='virtio'/>
    </interface>
    
    <!-- Console série -->
    <serial type='pty'>
      <target type='isa-serial' port='0'>
        <model name='isa-serial'/>
      </target>
    </serial>
    
    <!-- Console virtuelle -->
    <console type='pty'>
      <target type='serial' port='0'/>
    </console>
    
    <!-- Carte graphique -->
    <graphics type='vnc' port='-1' autoport='yes' listen='127.0.0.1'>
      <listen type='address' address='127.0.0.1'/>
    </graphics>
    
    <video>
      <model type='qxl' ram='65536' vram='65536' vgamem='16384' heads='1' primary='yes'/>
    </video>
  </devices>
</domain>
```

**Sections importantes** :
- `<devices>` : Matériel virtuel (disque, réseau, graphique)
- `<disk>` : Disques virtuels (qcow2, raw)
- `<interface>` : Cartes réseau
- `<graphics>` : VNC ou Spice (accès graphique)

---

## 🆘 virsh help : Aide Commandes

### Aide Générale

```bash
virsh help
```

**Résultat** (extrait) :
```
Grouped commands:

 Domain Management (help keyword 'domain'):
    attach-device                  attach device from an XML file
    autostart                      autostart a domain
    console                        connect to the guest console
    create                         create a domain from an XML file
    define                         define (but don't start) a domain from an XML file
    destroy                        destroy (stop) a domain
    dominfo                        domain information
    list                           list domains
    reboot                         reboot a domain
    reset                          reset a domain
    shutdown                       gracefully shutdown a domain
    start                          start a (previously defined) inactive domain
    undefine                       undefine a domain

 Domain Monitoring (help keyword 'monitor'):
    domblklist                     list all domain blocks
    domiflist                      list all domain virtual interfaces
    domstate                       domain state
    vcpucount                      domain vcpu counts

 Host and Hypervisor (help keyword 'host'):
    capabilities                   capabilities
    nodeinfo                       node information
    uri                            print the hypervisor canonical URI
    version                        show version

 Network Filter (help keyword 'filter'):
    nwfilter-define                define or update a network filter from an XML file
    nwfilter-list                  list network filters

 Networking (help keyword 'network'):
    net-define                     define an inactive persistent virtual network from an XML file
    net-destroy                    destroy (stop) a network
    net-list                       list networks
    net-start                      start a (previously defined) inactive network

 Storage Pool (help keyword 'pool'):
    pool-create                    create a pool from an XML file
    pool-define                    define an inactive persistent storage pool from an XML file
    pool-list                      list pools

 Storage Volume (help keyword 'volume'):
    vol-create                     create a vol from an XML file
    vol-delete                     delete a vol
    vol-list                       list vols
```

**Catégories** :
- **Domain Management** : Gestion VMs (start, stop, define...)
- **Domain Monitoring** : Surveillance (état, ressources...)
- **Networking** : Réseaux virtuels
- **Storage** : Stockage (pools, volumes)

---

### Aide Commande Spécifique

```bash
virsh help subcommand
```

**Exemple** :
```bash
virsh help start
```

**Résultat** :
```
  NAME
    start - start a (previously defined) inactive domain

  SYNOPSIS
    start <domain> [--console] [--paused] [--autodestroy] [--bypass-cache] 
          [--force-boot] [--pass-fds <string>]

  DESCRIPTION
    Start a domain, either from the last managedsave
    state, or via a fresh boot if no managedsave state
    is present.

  OPTIONS
    [--domain] <string>  name of the inactive domain
    --console         attach to console after creation
    --paused          leave the guest paused after creation
    --autodestroy     automatically destroy the guest when virsh disconnects
    --bypass-cache    avoid file system cache when loading
    --force-boot      force fresh boot by discarding any managed save
    --pass-fds <string>  pass file descriptors N,M,... to the guest
```

**💡 Usage** : `virsh help <commande>` pour détails syntaxe/options

---

## 📋 virsh define : Définir VM

### Syntaxe

```bash
virsh define fichier.xml
```

**Rôle** : Enregistrer définition VM dans libvirt (sans démarrer)

**Équivalent** : Créer blueprint VM (plans, pas construction)

---

### Exemple

```bash
# 1. Créer XML
cat > TestMachine.xml << 'EOF'
<domain type="kvm">
  <name>TestMachine</name>
  <memory unit="GiB">1</memory>
  <vcpu>1</vcpu>
  <os>
    <type arch="x86_64">hvm</type>
  </os>
</domain>
EOF

# 2. Définir dans libvirt
virsh define TestMachine.xml
```

**Résultat** :
```
Domain 'TestMachine' defined from TestMachine.xml
```

**Ce qui se passe** :
- ✅ Fichier copié vers `/etc/libvirt/qemu/TestMachine.xml`
- ✅ VM enregistrée (état : `shut off`)
- ✅ Visible dans `virsh list --all`

---

### Vérifier Définition

```bash
# Lister toutes VMs
virsh list --all
# Id   Name          State
# ---------------------------
# -    TestMachine   shut off

# Voir XML généré
virsh dumpxml TestMachine
```

---

## 🗑️ virsh undefine : Supprimer Définition VM

### Syntaxe

```bash
virsh undefine nom-vm
```

**Rôle** : Supprimer définition VM (désenregistrer)

**⚠️ Important** : Fichiers disque **restent** (pas supprimés)

---

### Exemple Simple

```bash
virsh undefine TestMachine
```

**Résultat** :
```
Domain 'TestMachine' has been undefined
```

**Ce qui est supprimé** :
- ✅ `/etc/libvirt/qemu/TestMachine.xml`
- ✅ Enregistrement libvirt

**Ce qui reste** :
- ⚠️ Disques virtuels (`/var/lib/libvirt/images/TestMachine.qcow2`)
- ⚠️ Snapshots

---

### virsh undefine --remove-all-storage : Suppression Complète

```bash
virsh undefine --remove-all-storage nom-vm
```

**Rôle** : Supprimer définition **+ tous les disques**

**⚠️ DANGER** : Suppression **définitive** (pas de corbeille)

---

**Exemple** :

```bash
virsh undefine --remove-all-storage TestMachine
```

**Résultat** :
```
Domain 'TestMachine' has been undefined
Volume 'vda'(/var/lib/libvirt/images/TestMachine.qcow2) removed.
```

**Ce qui est supprimé** :
- ✅ Définition XML
- ✅ **Tous les disques** virtuels
- ✅ Snapshots associés

**💡 Équivalent** : Supprimer VM + nettoyer complètement

---

### Comparaison undefine

| Commande | Supprime XML | Supprime Disques | Usage |
|----------|--------------|------------------|-------|
| `virsh undefine vm` | ✅ | ❌ | Garder données |
| `virsh undefine --remove-all-storage vm` | ✅ | ✅ | Nettoyage total |

---

## 📋 virsh list : Lister VMs

### virsh list : VMs Actives

```bash
virsh list
```

**Rôle** : Afficher VMs **en cours d'exécution**

**Résultat** :
```
 Id   Name        State
---------------------------
 1    web-server  running
 2    db-server   running
```

**Colonnes** :
- **Id** : Numéro temporaire (change à chaque boot)
- **Name** : Nom VM
- **State** : État (running, paused)

---

### virsh list --all : Toutes les VMs

```bash
virsh list --all
```

**Rôle** : Afficher **toutes** VMs (actives + inactives)

**Résultat** :
```
 Id   Name          State
-----------------------------
 1    web-server    running
 2    db-server     running
 -    test-vm       shut off
 -    backup-vm     shut off
```

**États possibles** :
- `running` → VM démarrée
- `shut off` → VM arrêtée
- `paused` → VM suspendue (RAM gelée)
- `in shutdown` → Arrêt en cours

**💡 Usage quotidien** : `virsh list --all` (voir tout)

---

### Options Utiles

```bash
# Seulement VMs inactives
virsh list --inactive

# Avec ID persistent
virsh list --all --persistent

# Format tableau
virsh list --all --title
```

---

## ▶️ virsh start : Démarrer VM

### Syntaxe

```bash
virsh start nom-vm
```

**Rôle** : Démarrer VM définie (état `shut off` → `running`)

---

### Exemple

```bash
virsh start TestMachine
```

**Résultat** :
```
Domain 'TestMachine' started
```

**Vérifier** :
```bash
virsh list
# Id   Name          State
# 1    TestMachine   running
```

---

### Options Utiles

```bash
# Démarrer + attacher console
virsh start TestMachine --console

# Démarrer en pause (RAM allouée mais CPU gelé)
virsh start TestMachine --paused
```

---

## 🔄 virsh reboot : Redémarrer Proprement

### Syntaxe

```bash
virsh reboot nom-vm
```

**Rôle** : Redémarrage **propre** (ACPI shutdown + boot)

**Analogie** 🔌 : Bouton redémarrage Windows/Linux (clean)

---

### Exemple

```bash
virsh reboot TestMachine
```

**Résultat** :
```
Domain 'TestMachine' is being rebooted
```

**Processus** :
1. ✅ Envoyer signal ACPI reboot
2. ✅ OS invité arrêt propre (flush disque, ferme services)
3. ✅ VM redémarre

**⚠️ Pré-requis** : ACPI activé dans VM (standard)

---

## ⚡ virsh reset : Forcer Reset

### Syntaxe

```bash
virsh reset nom-vm
```

**Rôle** : Reset **brutal** (équivalent bouton reset matériel)

**Analogie** 🔌 : Bouton reset PC (pas d'arrêt propre)

---

### Exemple

```bash
virsh reset TestMachine
```

**Résultat** :
```
Domain 'TestMachine' was reset
```

**Processus** :
- ❌ **Pas d'arrêt propre** OS invité
- ⚡ Coupure immédiate + redémarrage

**⚠️ Risques** :
- Corruption fichiers ouverts
- Perte données en RAM

**💡 Usage** : VM bloquée, ne répond plus

---

### Comparaison reboot vs reset

| Commande | Type | Arrêt OS | Risque corruption |
|----------|------|----------|-------------------|
| `virsh reboot` | Propre | ✅ Oui | ❌ Non |
| `virsh reset` | Brutal | ❌ Non | ⚠️ Oui |

---

## 🛑 virsh shutdown : Arrêt Propre

### Syntaxe

```bash
virsh shutdown nom-vm
```

**Rôle** : Arrêter VM **proprement** (ACPI shutdown)

**Analogie** 🔌 : Menu Démarrer → Arrêter (Windows/Linux)

---

### Exemple

```bash
virsh shutdown TestMachine
```

**Résultat** :
```
Domain 'TestMachine' is being shutdown
```

**Processus** :
1. ✅ Envoyer signal ACPI shutdown
2. ✅ OS invité arrêt propre (ferme services, flush cache)
3. ✅ VM s'arrête (état `shut off`)

**Temps** : 10-60 secondes (dépend OS invité)

---

### Options Utiles

```bash
# Attendre fin shutdown (timeout 60s)
virsh shutdown TestMachine --mode acpi

# Mode agent (nécessite qemu-guest-agent dans VM)
virsh shutdown TestMachine --mode agent
```

---

## 💥 virsh destroy : Arrêt Forcé

### Syntaxe

```bash
virsh destroy nom-vm
```

**Rôle** : Arrêter VM **immédiatement** (brutal)

**⚠️ NOM TROMPEUR** : Ne supprime PAS la VM, juste l'arrête brutalement

**Analogie** 🔌 : Débrancher câble alimentation PC

---

### Exemple

```bash
virsh destroy TestMachine
```

**Résultat** :
```
Domain 'TestMachine' destroyed
```

**Processus** :
- ❌ **Pas d'arrêt propre** OS
- ⚡ Coupure immédiate

**⚠️ Risques** :
- Corruption fichiers
- Perte données non sauvegardées

**💡 Usage** :
- VM bloquée (ne répond pas à `shutdown`)
- Arrêt d'urgence
- Scripts automatisés (rapide)

---

### Comparaison shutdown vs destroy

| Commande | Type | Temps | Arrêt OS | Risque |
|----------|------|-------|----------|--------|
| `virsh shutdown` | Propre | 10-60s | ✅ Oui | ❌ Non |
| `virsh destroy` | Brutal | <1s | ❌ Non | ⚠️ Oui |

**💡 Préférer** : `shutdown` (propre) sauf urgence

---

## 🔄 virsh autostart : Démarrage Automatique

### Activer Autostart

```bash
virsh autostart nom-vm
```

**Rôle** : VM démarre **automatiquement** au boot de l'hôte

**Analogie** 🚗 : Démarrage automatique moteur quand on ouvre portière

---

**Exemple** :

```bash
virsh autostart TestMachine
```

**Résultat** :
```
Domain 'TestMachine' marked as autostarted
```

**Effet** :
- ✅ Lien symbolique créé : `/etc/libvirt/qemu/autostart/TestMachine.xml`
- ✅ Au boot hôte → VM démarre automatiquement

**Vérifier** :
```bash
ls /etc/libvirt/qemu/autostart/
# TestMachine.xml -> /etc/libvirt/qemu/TestMachine.xml
```

---

### Désactiver Autostart

```bash
virsh autostart --disable nom-vm
```

**Exemple** :

```bash
virsh autostart --disable TestMachine
```

**Résultat** :
```
Domain 'TestMachine' unmarked as autostarted
```

**Effet** :
- ✅ Lien symbolique supprimé
- ✅ VM ne démarre plus automatiquement

---

### Vérifier Autostart

```bash
virsh dominfo TestMachine | grep Autostart
# Autostart:      enable
```

**Ou** :
```bash
virsh list --all --autostart
```

---

## ℹ️ virsh dominfo : Informations VM

### Syntaxe

```bash
virsh dominfo nom-vm
```

**Rôle** : Afficher **informations détaillées** VM

---

### Exemple

```bash
virsh dominfo TestMachine
```

**Résultat** :
```
Id:             1
Name:           TestMachine
UUID:           a1b2c3d4-e5f6-7890-abcd-1234567890ab
OS Type:        hvm
State:          running
CPU(s):         2
CPU time:       34.5s
Max memory:     2097152 KiB
Used memory:    2097152 KiB
Persistent:     yes
Autostart:      enable
Managed save:   no
Security model: apparmor
Security DOI:   0
Security label: libvirt-a1b2c3d4-e5f6-7890-abcd-1234567890ab (enforcing)
```

**Informations clés** :
- **Id** : ID temporaire (si running)
- **State** : État actuel
- **CPU(s)** : vCPUs alloués
- **Max memory** : RAM maximale
- **Used memory** : RAM actuellement utilisée
- **Autostart** : Démarrage auto activé/désactivé
- **Persistent** : VM définie de façon permanente

**💡 Usage** : Diagnostiquer config VM, vérifier ressources

---

## 🔧 virsh setvcpus : Modifier vCPUs

### Syntaxe

```bash
virsh setvcpus nom-vm nombre [--config] [--maximum]
```

**Rôle** : Changer nombre de vCPUs

**Options** :
- `--config` : Modification permanente (effet au prochain boot)
- `--live` : Modification immédiate (VM en cours)
- `--maximum` : Définir maximum vCPUs

---

### Ajouter vCPUs (Permanent)

```bash
virsh setvcpus TestMachine 2 --config
```

**Résultat** :
```bash
# Aucune sortie = succès
```

**Effet** : Au **prochain boot**, VM aura 2 vCPUs

**Vérifier** :
```bash
virsh dominfo TestMachine | grep CPU
# CPU(s):         2
```

---

### Définir Maximum vCPUs

```bash
virsh setvcpus TestMachine 4 --config --maximum
```

**Résultat** :
```bash
# Maximum vCPUs défini à 4
```

**Usage** : Permettre hotplug vCPU jusqu'à 4

**Puis ajuster vCPUs actuels** :
```bash
virsh setvcpus TestMachine 2 --config
```

---

### Hotplug vCPU (Live)

```bash
# Ajouter vCPU sans redémarrer (si supporté)
virsh setvcpus TestMachine 4 --live

# Permanent + live
virsh setvcpus TestMachine 4 --config --live
```

**⚠️ Limitations** :
- Hotplug vCPU : Supporté Linux (ajouter)
- Hotunplug vCPU : **Pas supporté** (retirer nécessite reboot)

---

## 💾 virsh setmaxmem : Modifier RAM

### Syntaxe

```bash
virsh setmaxmem nom-vm taille --config
```

**Unités** : `K` (KiB), `M` (MiB), `G` (GiB)

**Rôle** : Définir RAM **maximale** VM

---

### Exemple

```bash
virsh setmaxmem TestMachine 2048M --config
```

**Ou** :
```bash
virsh setmaxmem TestMachine 2G --config
```

**Résultat** :
```bash
# Aucune sortie = succès
```

**Effet** : Au prochain boot, RAM max = 2 GiB

---

### Vérifier RAM

```bash
virsh dominfo TestMachine | grep memory
# Max memory:     2097152 KiB  (2 GiB)
# Used memory:    2097152 KiB
```

---

### Modifier RAM Actuelle (setmem)

```bash
# Modifier RAM utilisée (doit être ≤ maxmem)
virsh setmem TestMachine 1024M --config
```

**Différence** :
- `setmaxmem` → RAM **maximale** possible
- `setmem` → RAM **allouée** au démarrage

---

### Balloon Memory (Dynamique)

**Concept** : Ajuster RAM à chaud avec `virtio-balloon`

```bash
# Réduire RAM utilisée (live)
virsh setmem TestMachine 1G --live
# (nécessite driver balloon dans VM)
```

**💡 Avancé** : Permet ajustement RAM sans reboot

---

## 📋 Antisèche - Commandes virsh

| Commande | Je veux... | Exemple |
|----------|-----------|---------|
| `virsh help` | Aide générale | `virsh help` |
| `virsh help cmd` | Aide commande | `virsh help start` |
| `virsh define xml` | Enregistrer VM | `virsh define vm.xml` |
| `virsh undefine vm` | Supprimer définition | `virsh undefine test` |
| `virsh undefine --remove-all-storage vm` | Supprimer VM + disques | `virsh undefine --remove-all-storage test` |
| `virsh list` | Lister VMs actives | `virsh list` |
| `virsh list --all` | Lister toutes VMs | `virsh list --all` |
| `virsh start vm` | Démarrer VM | `virsh start web` |
| `virsh reboot vm` | Redémarrer proprement | `virsh reboot web` |
| `virsh reset vm` | Forcer reset | `virsh reset web` |
| `virsh shutdown vm` | Arrêter proprement | `virsh shutdown web` |
| `virsh destroy vm` | Arrêter brutalement | `virsh destroy web` |
| `virsh autostart vm` | Activer autostart | `virsh autostart web` |
| `virsh autostart --disable vm` | Désactiver autostart | `virsh autostart --disable web` |
| `virsh dominfo vm` | Infos VM | `virsh dominfo web` |
| `virsh setvcpus vm N --config` | Modifier vCPUs | `virsh setvcpus web 2 --config` |
| `virsh setmaxmem vm SIZE --config` | Modifier RAM max | `virsh setmaxmem web 2G --config` |

---

## 🎓 Points Clés pour l'Examen

✅ **QEMU** : Quick EMUlator (émulateur + virtualiseur)  
✅ **virsh** : CLI gestion VMs via libvirt  
✅ **Paquets** : `qemu-kvm`, `libvirt`, `libvirt-clients`  
✅ **XML minimal** : `<domain>`, `<name>`, `<memory>`, `<vcpu>`, `<os>`  
✅ **virsh define** : Enregistrer VM depuis XML  
✅ **virsh undefine** : Supprimer définition (garde disques)  
✅ **virsh undefine --remove-all-storage** : Supprimer VM + disques  
✅ **virsh list** : VMs actives uniquement  
✅ **virsh list --all** : Toutes VMs (actives + inactives)  
✅ **virsh start** : Démarrer VM  
✅ **virsh reboot** : Redémarrage propre (ACPI)  
✅ **virsh reset** : Reset brutal (comme bouton reset)  
✅ **virsh shutdown** : Arrêt propre (ACPI)  
✅ **virsh destroy** : Arrêt brutal (ne supprime PAS la VM)  
✅ **virsh autostart** : Démarrage auto au boot hôte  
✅ **virsh dominfo** : Informations détaillées VM  
✅ **virsh setvcpus --config** : Modifier vCPUs (permanent)  
✅ **virsh setmaxmem --config** : Modifier RAM max (permanent)  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Créer VM Basique

```bash
# 1. Créer disque virtuel
sudo qemu-img create -f qcow2 /var/lib/libvirt/images/web-vm.qcow2 20G

# 2. Créer XML
cat > web-vm.xml << 'EOF'
<domain type='kvm'>
  <name>web-vm</name>
  <memory unit='GiB'>2</memory>
  <vcpu>2</vcpu>
  <os>
    <type arch='x86_64'>hvm</type>
    <boot dev='cdrom'/>
    <boot dev='hd'/>
  </os>
  <devices>
    <disk type='file' device='disk'>
      <driver name='qemu' type='qcow2'/>
      <source file='/var/lib/libvirt/images/web-vm.qcow2'/>
      <target dev='vda' bus='virtio'/>
    </disk>
    <disk type='file' device='cdrom'>
      <source file='/var/lib/libvirt/images/ubuntu-22.04.iso'/>
      <target dev='sda' bus='sata'/>
      <readonly/>
    </disk>
    <interface type='network'>
      <source network='default'/>
      <model type='virtio'/>
    </interface>
    <graphics type='vnc' port='-1' autoport='yes'/>
  </devices>
</domain>
EOF

# 3. Définir VM
virsh define web-vm.xml

# 4. Démarrer
virsh start web-vm

# 5. Connexion VNC (port auto)
virsh vncdisplay web-vm
# :0 → port 5900
```

---

### Scénario 2 : Cycle Vie VM

```bash
# Définir
virsh define vm.xml

# Démarrer
virsh start ma-vm

# Vérifier état
virsh list
# ma-vm   running

# Infos
virsh dominfo ma-vm

# Redémarrer proprement
virsh reboot ma-vm

# Arrêter proprement
virsh shutdown ma-vm

# Attendre arrêt
while virsh list | grep -q ma-vm; do sleep 1; done

# Vérifier
virsh list --all
# ma-vm   shut off

# Supprimer (garde disques)
virsh undefine ma-vm
```

---

### Scénario 3 : Autostart Production

```bash
# VMs production doivent démarrer automatiquement

# Activer autostart
virsh autostart web-server
virsh autostart db-server
virsh autostart cache-server

# Vérifier
virsh list --all --autostart
# web-server     yes
# db-server      yes
# cache-server   yes

# Test : Redémarrer hôte
sudo reboot

# Après reboot hôte
virsh list
# Toutes VMs autostart sont running
```

---

### Scénario 4 : Augmenter Ressources VM

```bash
# VM nécessite plus de CPU et RAM

# Arrêter VM
virsh shutdown web-server

# Attendre arrêt
while virsh list | grep -q web-server; do sleep 1; done

# Augmenter vCPUs (2 → 4)
virsh setvcpus web-server 4 --config --maximum
virsh setvcpus web-server 4 --config

# Augmenter RAM (2G → 4G)
virsh setmaxmem web-server 4G --config
virsh setmem web-server 4G --config

# Vérifier config
virsh dominfo web-server
# CPU(s):         4
# Max memory:     4194304 KiB

# Redémarrer
virsh start web-server
```

---

### Scénario 5 : Clonage VM

```bash
# Cloner VM existante

# 1. Arrêter VM source
virsh shutdown template-vm

# 2. Cloner (virt-clone)
virt-clone \
  --original template-vm \
  --name prod-vm-01 \
  --file /var/lib/libvirt/images/prod-vm-01.qcow2

# 3. Démarrer clone
virsh start prod-vm-01

# Liste
virsh list --all
# template-vm    shut off
# prod-vm-01     running
```

---

### Scénario 6 : VM Bloquée - Arrêt Forcé

```bash
# VM ne répond plus

# Tenter arrêt propre
virsh shutdown vm-bloquee
# (attend 60s)

# Toujours running
virsh list
# vm-bloquee   running

# Forcer arrêt
virsh destroy vm-bloquee

# Vérifier
virsh list --all
# vm-bloquee   shut off

# Redémarrer proprement
virsh start vm-bloquee
```

---

### Scénario 7 : Connexion Console VM

```bash
# Accéder console série VM

# Démarrer + console
virsh start ma-vm --console

# OU attacher console VM running
virsh console ma-vm

# Sortir console : Ctrl + ]

# Alternative : VNC
virsh vncdisplay ma-vm
# :0

# Connexion VNC
vncviewer localhost:5900
```

---

### Scénario 8 : Suppression Complète VM

```bash
# Supprimer VM + tous fichiers

# 1. Arrêter si running
virsh destroy ma-vm

# 2. Supprimer définition + disques
virsh undefine --remove-all-storage ma-vm

# 3. Vérifier
virsh list --all
# (ma-vm n'apparaît plus)

ls /var/lib/libvirt/images/
# (disque ma-vm.qcow2 supprimé)
```

---

### Scénario 9 : Modifier XML Existant

```bash
# Modifier config VM

# 1. Exporter XML
virsh dumpxml ma-vm > ma-vm-backup.xml

# 2. Éditer
virsh edit ma-vm
# (ouvre éditeur, modifier, sauvegarder)

# Alternative : éditer fichier
cp ma-vm-backup.xml ma-vm-new.xml
nano ma-vm-new.xml
# (modifier config)

# 3. Redéfinir
virsh undefine ma-vm
virsh define ma-vm-new.xml
```

---

### Scénario 10 : Monitoring Ressources VM

```bash
# Surveiller ressources

# CPU
virsh cpu-stats ma-vm

# Mémoire
virsh dommemstat ma-vm
# actual 2097152
# swap_in 0
# swap_out 0
# rss 1048576

# Disques
virsh domblklist ma-vm
# Target   Source
# vda      /var/lib/libvirt/images/ma-vm.qcow2

# Interfaces réseau
virsh domiflist ma-vm
# Interface   Type     Source   Model    MAC
# vnet0       network  default  virtio   52:54:00:xx:xx:xx

# Stats réseau
virsh domifstat ma-vm vnet0
# vnet0 rx_bytes 1234567
# vnet0 rx_packets 8901
# vnet0 tx_bytes 7654321
# vnet0 tx_packets 5678
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Permission Denied

```
error: failed to connect to the hypervisor
error: Failed to connect socket to '/var/run/libvirt/libvirt-sock': Permission denied
```

**Solution** :
```bash
# Ajouter user au groupe libvirt
sudo usermod -aG libvirt $USER

# Recharger groupes
newgrp libvirt

# Ou utiliser sudo
sudo virsh list --all
```

---

### Erreur 2 : VM Déjà Définie

```
error: Failed to define domain from vm.xml
error: operation failed: domain 'ma-vm' already exists
```

**Solution** :
```bash
# Supprimer définition existante
virsh undefine ma-vm

# Redéfinir
virsh define vm.xml
```

---

### Erreur 3 : Fichier Disque Manquant

```
error: failed to start domain 'ma-vm'
error: Cannot access storage file '/path/to/disk.qcow2': No such file or directory
```

**Solution** :
```bash
# Créer disque
qemu-img create -f qcow2 /var/lib/libvirt/images/ma-vm.qcow2 20G

# Ou corriger chemin dans XML
virsh edit ma-vm
# Modifier <source file='...'/>
```

---

### Erreur 4 : VM Ne S'Arrête Pas (shutdown)

```
# virsh shutdown ma-vm
# (VM reste running après 60s)
```

**Causes** :
- ACPI non supporté dans VM
- OS invité bloqué

**Solution** :
```bash
# Forcer arrêt
virsh destroy ma-vm
```

---

### Erreur 5 : Ressources Insuffisantes

```
error: Failed to start domain 'ma-vm'
error: internal error: process exited while connecting to monitor: ... Cannot allocate memory
```

**Solution** :
```bash
# Réduire RAM VM
virsh setmaxmem ma-vm 1G --config

# Ou libérer RAM hôte
virsh shutdown autre-vm
```

---

## 🗂️ Arborescence Fichiers libvirt

```
/etc/libvirt/
├── qemu/                     → Définitions VMs
│   ├── web-server.xml
│   ├── db-server.xml
│   └── autostart/            → Liens VMs autostart
│       ├── web-server.xml -> /etc/libvirt/qemu/web-server.xml
│       └── db-server.xml -> /etc/libvirt/qemu/db-server.xml
├── qemu.conf                 → Config QEMU/KVM
└── libvirtd.conf             → Config daemon libvirt

/var/lib/libvirt/
├── images/                   → Disques virtuels
│   ├── web-server.qcow2
│   ├── db-server.qcow2
│   └── ubuntu-22.04.iso
├── qemu/                     → Runtime VMs
│   └── save/                 → États sauvegardés
└── dnsmasq/                  → DHCP réseau default

/var/log/libvirt/
├── qemu/                     → Logs VMs
│   ├── web-server.log
│   └── db-server.log
└── libvirtd.log              → Logs daemon
```

---

**🎯 Prochaine étape** : Work on the Command Line (Module 3)

*Dernière mise à jour: 1 février 2026*
