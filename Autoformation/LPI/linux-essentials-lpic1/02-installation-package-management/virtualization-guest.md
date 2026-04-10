# 🖥️ Linux comme Invité de Virtualisation

---

## 🎯 Virtualisation : Vue d'Ensemble

**Virtualisation** = Exécuter plusieurs **systèmes d'exploitation** sur un seul **matériel physique**

**Analogie** 🏢 : 
- **Serveur physique** = Immeuble
- **Hyperviseur** = Gérant de l'immeuble
- **Machines virtuelles** = Appartements (isolés mais partagent infrastructure)

**Avantages** :
- ✅ Consolidation serveurs (moins de matériel)
- ✅ Isolation (crash d'une VM n'affecte pas les autres)
- ✅ Snapshots (sauvegardes instantanées)
- ✅ Portabilité (déplacer VM entre hôtes)
- ✅ Tests/développement (environnements jetables)

---

## 🔧 Types d'Hyperviseurs

### Type 1 : Bare-Metal (Sur le Métal Nu)

**Définition** : Hyperviseur s'exécute **directement** sur le matériel

```
┌─────────────────────────────────────┐
│  VM 1    │   VM 2    │   VM 3      │ ← Machines virtuelles
├──────────┴───────────┴─────────────┤
│        Hyperviseur Type 1           │ ← Directement sur matériel
├─────────────────────────────────────┤
│       Matériel Physique             │
└─────────────────────────────────────┘
```

**Caractéristiques** :
- ✅ Performances optimales (pas d'OS intermédiaire)
- ✅ Sécurité renforcée (surface attaque minimale)
- ✅ Idéal production/datacenter

**Exemples** :
- **Xen** (open source)
- **VMware ESXi** (commercial)
- **Microsoft Hyper-V** (commercial)
- **Proxmox VE** (Debian + KVM, open source)

**Usage** : Serveurs d'entreprise, cloud providers (AWS, Azure)

---

### Type 2 : Hosted (Hébergé)

**Définition** : Hyperviseur s'exécute **sur un OS** existant

```
┌─────────────────────────────────────┐
│  VM 1    │   VM 2    │   VM 3      │ ← Machines virtuelles
├──────────┴───────────┴─────────────┤
│        Hyperviseur Type 2           │ ← Application sur OS
├─────────────────────────────────────┤
│        OS Hôte (Windows/Linux)      │
├─────────────────────────────────────┤
│       Matériel Physique             │
└─────────────────────────────────────┘
```

**Caractéristiques** :
- ⚠️ Performances moindres (couche OS supplémentaire)
- ✅ Facile à installer (comme application)
- ✅ Idéal développement/tests

**Exemples** :
- **VirtualBox** (Oracle, cross-platform, gratuit)
- **VMware Workstation** (commercial)
- **VMware Fusion** (macOS)
- **Parallels Desktop** (macOS)

**Usage** : Postes développeurs, tests, démonstrations

---

### Comparaison Type 1 vs Type 2

| Critère | Type 1 (Bare-Metal) | Type 2 (Hosted) |
|---------|---------------------|-----------------|
| Installation | Directement sur matériel | Sur OS existant |
| Performances | ⭐⭐⭐⭐⭐ Excellent | ⭐⭐⭐ Bon |
| Complexité | Élevée | Faible |
| Sécurité | Très élevée | Moyenne |
| Coût matériel | Dédié (serveur) | Partagé (PC) |
| Usage typique | Production, datacenter | Dev, tests, desktop |
| Exemples | Xen, ESXi, Hyper-V | VirtualBox, VMware Workstation |

---

## 🚀 Hyperviseurs Principaux

### Xen : Hyperviseur Type 1

**Xen** = Hyperviseur open source **bare-metal**

**Architecture** :
```
┌──────────────────────────────────────────┐
│ VM (DomU)  │ VM (DomU)  │  VM (DomU)    │ ← Invités (Domain Unprivileged)
├────────────┴────────────┴───────────────┤
│         Dom0 (Domain 0)                  │ ← VM privilégiée (admin)
├──────────────────────────────────────────┤
│         Xen Hypervisor                   │ ← Couche hyperviseur
├──────────────────────────────────────────┤
│         Matériel Physique                │
└──────────────────────────────────────────┘
```

**Composants** :
- **Xen Hypervisor** : Couche matérielle (très légère)
- **Dom0** (Domain 0) : VM privilégiée (contrôle, gestion)
- **DomU** (Domain Unprivileged) : VMs invitées normales

**Points clés** :
- ✅ Très performant (minimal overhead)
- ✅ Utilisé par AWS (EC2)
- ✅ Supporte paravirtualisation + virtualisation complète
- ⚠️ Configuration complexe

**Distributions Xen** :
- Citrix Hypervisor (anciennement XenServer)
- Xen Orchestra (interface web)

---

### KVM : Type 1 ET Type 2

**KVM** = **K**ernel-based **V**irtual **M**achine

**Particularité unique** : **Module du noyau Linux**

**Architecture** :
```
┌──────────────────────────────────────────┐
│  VM 1      │   VM 2     │   VM 3        │ ← Processus QEMU
├────────────┴────────────┴───────────────┤
│         Linux OS (kernel + KVM)          │ ← OS + module KVM
├──────────────────────────────────────────┤
│         Matériel Physique                │
└──────────────────────────────────────────┘
```

**Pourquoi Type 1 ET Type 2 ?**
- **Type 1** : Module noyau (pas de couche logicielle séparée)
- **Type 2** : Cohabite avec Linux (pas bare-metal pur)

**💡 Classification** : Souvent considéré **Type 1** car intégré au noyau

**Composants** :
- **KVM** : Module noyau (`/dev/kvm`)
- **QEMU** : Émulateur matériel (CPU, disque, réseau)
- **libvirt** : API/daemon gestion VMs

**Points clés** :
- ✅ Intégré Linux (pas d'install hyperviseur séparé)
- ✅ Performances excellentes (accélération matérielle)
- ✅ Standard RHEL/CentOS/Ubuntu
- ✅ libvirt pour gestion (virsh, virt-manager)

**Utilisateurs** :
- Red Hat Virtualization (RHV)
- Proxmox VE
- OpenStack

---

### VirtualBox : Type 2 Cross-Platform

**VirtualBox** = Hyperviseur **hosted** gratuit (Oracle)

**Architecture** :
```
┌──────────────────────────────────────────┐
│  VM Linux  │   VM Windows  │  VM macOS  │
├────────────┴───────────────┴────────────┤
│         VirtualBox Application           │
├──────────────────────────────────────────┤
│    OS Hôte (Windows/Linux/macOS)         │
├──────────────────────────────────────────┤
│         Matériel Physique                │
└──────────────────────────────────────────┘
```

**Points clés** :
- ✅ **Cross-platform** (Windows, Linux, macOS)
- ✅ Gratuit (open source + extension pack propriétaire)
- ✅ Interface graphique intuitive
- ✅ Snapshots, clonage, export OVA
- ⚠️ Performances < KVM/Xen (Type 2)

**Cas d'usage** :
- Développement local
- Tests multi-OS
- Labs formation
- Environnements jetables

**Commandes** :
```bash
# CLI (VBoxManage)
VBoxManage list vms
VBoxManage startvm "MaVM"
VBoxManage snapshot "MaVM" take "avant-modif"
```

---

## 🔄 Migration de Machines Virtuelles

### Migration à Froid (Cold Migration)

**Définition** : Déplacer VM **éteinte** entre hôtes

**Processus** :
```
Hôte A                          Hôte B
┌─────────┐                    ┌─────────┐
│  VM 1   │ 1. Arrêt           │         │
│ (running)─────────────────────→         │
└─────────┘ 2. Copie fichiers  │         │
            3. Démarrage        │  VM 1   │
                                │ (running)
                                └─────────┘
```

**Étapes** :
1. ✅ Arrêter VM (shutdown)
2. ✅ Copier disques virtuels vers hôte B
3. ✅ Copier configuration VM
4. ✅ Démarrer VM sur hôte B

**Avantages** :
- ✅ Simple (pas de complexité technique)
- ✅ Compatible tous hyperviseurs
- ✅ Pas de risque corruption

**Inconvénients** :
- ⚠️ **Downtime** (VM inaccessible pendant migration)
- ⚠️ Temps long (copie complète disques)

**Usage** : Maintenance planifiée, migration occasionnelle

**Exemple KVM** :
```bash
# Hôte A : arrêter VM
virsh shutdown ma-vm

# Copier vers hôte B
scp /var/lib/libvirt/images/ma-vm.qcow2 root@hote-b:/var/lib/libvirt/images/
scp /etc/libvirt/qemu/ma-vm.xml root@hote-b:/etc/libvirt/qemu/

# Hôte B : définir et démarrer
virsh define /etc/libvirt/qemu/ma-vm.xml
virsh start ma-vm
```

---

### Migration à Chaud (Live Migration)

**Définition** : Déplacer VM **en cours d'exécution** entre hôtes

**Processus** :
```
Hôte A                          Hôte B
┌─────────┐                    ┌─────────┐
│  VM 1   │ 1. Copie mémoire   │         │
│ (running)────────────────────→ (synchro)
│         │ 2. Copie delta     │         │
│         │ 3. Pause très      │         │
│ (pause) │    courte (~ms)    │ (start) │
│         │ 4. Switch réseau   │  VM 1   │
└─────────┘                    │ (running)
                                └─────────┘
```

**Étapes** :
1. ✅ Copie mémoire RAM (VM continue de tourner)
2. ✅ Copie pages modifiées (delta)
3. ✅ Pause VM très brève (<100ms)
4. ✅ Transfert état final + bascule
5. ✅ VM reprend sur hôte B

**Pré-requis** :
- ✅ Stockage **partagé** (SAN, NFS, Ceph) ou copie storage
- ✅ Réseau rapide (1Gbps minimum, 10Gbps recommandé)
- ✅ CPU compatibles (même famille)
- ✅ Hyperviseur supporte (KVM, Xen, VMware)

**Avantages** :
- ✅ **Zéro downtime** (ou micro-coupure imperceptible)
- ✅ Maintenance sans interruption service
- ✅ Load balancing dynamique

**Inconvénients** :
- ⚠️ Complexe (infrastructure requise)
- ⚠️ Besoin stockage partagé
- ⚠️ Réseau rapide obligatoire

**Usage** : Production 24/7, haute disponibilité, cloud

**Exemple KVM** :
```bash
# Avec stockage partagé (NFS/Ceph)
virsh migrate --live ma-vm qemu+ssh://hote-b/system
```

---

### Comparaison Migrations

| Critère | Migration à Froid | Migration à Chaud (Live) |
|---------|-------------------|--------------------------|
| VM pendant migration | ❌ Éteinte | ✅ En cours d'exécution |
| Downtime | ⚠️ Minutes/heures | ✅ <100ms (imperceptible) |
| Complexité | Simple | Élevée |
| Pré-requis | Aucun | Stockage partagé, réseau rapide |
| Vitesse | Lente | Rapide |
| Usage | Maintenance planifiée | Production 24/7 |

---

## 🖥️ Types de Virtualisation

### Virtualisation Complète (Fully Virtualized)

**Définition** : VM exécute OS **non modifié** (comme sur matériel physique)

**Principe** :
- ✅ OS invité **ne sait pas** qu'il est virtualisé
- ✅ Aucune modification noyau invité
- ✅ Instructions CPU **traduites** par hyperviseur

**Analogie** 🎭 : 
- VM croit parler au matériel réel
- Hyperviseur = Traducteur invisible

**Architecture** :
```
┌──────────────────────────┐
│   OS Invité (non modifié)│ ← Croit être sur matériel réel
│   (Windows, Linux...)    │
├──────────────────────────┤
│   Hyperviseur            │ ← Traduit instructions
│   (Trap & Emulate)       │
├──────────────────────────┤
│   Matériel Physique      │
│   (CPU avec VT-x/AMD-V)  │
└──────────────────────────┘
```

**Pré-requis matériel** :
- ✅ **Intel VT-x** (Virtualization Technology)
- ✅ **AMD-V** / **SVM** (Secure Virtual Machine)

**Extensions CPU** :
- **VT-x** (Intel) : Instructions VMXON, VMLAUNCH
- **AMD-V** (AMD) : Instructions VMRUN, VMSAVE

**Vérifier support** :
```bash
# Linux : vérifier flags CPU
grep -E '(vmx|svm)' /proc/cpuinfo
# vmx = Intel VT-x
# svm = AMD-V

# Si résultat vide → Pas de support ou désactivé BIOS
```

**Avantages** :
- ✅ OS invité **non modifié** (Windows, Linux standard)
- ✅ Compatible large gamme OS
- ✅ Isolation complète

**Inconvénients** :
- ⚠️ Performances moindres (traduction instructions)
- ⚠️ Overhead hyperviseur

**Exemples** :
- KVM (avec VT-x/AMD-V)
- VirtualBox (avec VT-x/AMD-V)
- VMware ESXi

**💡 Point clé** : Standard aujourd'hui grâce aux extensions matérielles CPU

---

### Paravirtualisation (PVM)

**Définition** : OS invité **modifié** pour collaborer avec hyperviseur

**Principe** :
- ✅ OS invité **sait** qu'il est virtualisé
- ✅ Noyau et drivers **modifiés**
- ✅ **Hypercalls** directs vers hyperviseur (pas de traduction)

**Analogie** 🤝 : 
- VM et hyperviseur parlent **même langue**
- Pas besoin de traducteur → Plus rapide

**Architecture** :
```
┌──────────────────────────┐
│   OS Invité MODIFIÉ      │ ← Sait qu'il est virtualisé
│   (noyau PV-aware)       │
│   ┌──────────────┐       │
│   │  Hypercalls  │       │ ← Appels directs hyperviseur
│   └──────┬───────┘       │
├──────────┼────────────────┤
│   Hyperviseur            │ ← Pas de traduction
│   (Xen, KVM)             │
├──────────────────────────┤
│   Matériel Physique      │
│   (CPU sans VT-x/AMD-V)  │ ← Pas obligatoire !
└──────────────────────────┘
```

**Modifications OS invité** :
- **Noyau** : Remplacer instructions privilégiées par hypercalls
- **Drivers** : Drivers réseau/disque virtuels (virtio)

**Avantages** :
- ✅ **Performances supérieures** (pas de traduction)
- ✅ Fonctionne **sans VT-x/AMD-V** (CPU ancien)
- ✅ Overhead minimal

**Inconvénients** :
- ⚠️ OS invité doit être **modifié** (Linux OK, Windows difficile)
- ⚠️ Moins flexible (dépendance noyau custom)

**Exemples** :
- **Xen PV** (Paravirtualized Xen)
- **Linux KVM** (avec drivers virtio)

**Noyau Linux PV** :
```bash
# Vérifier si noyau supporte paravirtualisation
ls /boot/config-$(uname -r) | grep CONFIG_PARAVIRT
# CONFIG_PARAVIRT=y → Support activé
```

**💡 Point clé** : Utilisé historiquement avant VT-x/AMD-V, moins courant aujourd'hui

---

### Virtualisation Hybride

**Définition** : **Combine** virtualisation complète + paravirtualisation

**Principe** :
- ✅ OS invité **non modifié** (virtualisation complète)
- ✅ **Drivers optimisés** pour I/O (paravirtualisation)

**Analogie** 🚗 :
- Voiture standard (OS non modifié)
- Pneus haute performance (drivers I/O optimisés)

**Architecture** :
```
┌──────────────────────────────┐
│   OS Invité (non modifié)    │ ← Standard (Windows, Linux)
│   ┌──────────────────────┐   │
│   │ Drivers Paravirtualisés  │ ← Réseau, disque optimisés
│   │ (VirtIO, VMware Tools)   │
│   └──────────┬───────────┘   │
├──────────────┼────────────────┤
│   Hyperviseur               │
│   (Full-virt + paravirt I/O)│
├──────────────────────────────┤
│   Matériel Physique          │
└──────────────────────────────┘
```

**Composants** :
- **CPU/Mémoire** : Virtualisation complète (VT-x/AMD-V)
- **I/O (disque, réseau)** : Drivers paravirtualisés

**Drivers paravirtualisés** :

| Hyperviseur | Nom drivers | Installation |
|-------------|-------------|--------------|
| KVM | **VirtIO** | Kernel Linux / Windows drivers |
| VirtualBox | **Guest Additions** | ISO installé dans VM |
| VMware | **VMware Tools** | Package installé dans VM |
| Xen | **PV Drivers** | Kernel Xen-aware |

**Avantages** :
- ✅ **OS standard** (pas de modification noyau)
- ✅ **Performances I/O excellentes** (quasi-natives)
- ✅ Meilleur des deux mondes

**Inconvénients** :
- ⚠️ Nécessite installation **drivers invités**
- ⚠️ Maintenance drivers (mises à jour)

**💡 Standard aujourd'hui** : Presque toutes VMs production utilisent hybride

---

### Comparaison Types Virtualisation

| Type | OS Modifié | VT-x/AMD-V Requis | Performances | Facilité |
|------|------------|-------------------|--------------|----------|
| **Complète** | ❌ Non | ✅ Oui | ⭐⭐⭐ Bon | ⭐⭐⭐⭐⭐ Facile |
| **Paravirtualisation** | ✅ Oui | ❌ Non | ⭐⭐⭐⭐⭐ Excellent | ⭐⭐ Complexe |
| **Hybride** | ❌ Non (+ drivers) | ✅ Oui | ⭐⭐⭐⭐⭐ Excellent | ⭐⭐⭐⭐ Facile |

**💡 Recommandation** : **Hybride** (virtualisation complète + drivers paravirtualisés)

---

## 🔧 Drivers Paravirtualisés

### VirtIO (KVM/QEMU)

**VirtIO** = Framework drivers paravirtualisés pour KVM

**Composants** :
- `virtio-net` → Carte réseau virtuelle
- `virtio-blk` → Disque virtuel
- `virtio-scsi` → Contrôleur SCSI
- `virtio-balloon` → Gestion mémoire dynamique

**Installation Linux** :
```bash
# Vérifier si modules chargés
lsmod | grep virtio
# virtio_net
# virtio_blk
# virtio_pci

# Installer si absent (Debian/Ubuntu)
sudo apt-get install linux-image-extra-virtual

# RHEL/CentOS
sudo yum install kernel-modules-extra
```

**Installation Windows** :
```
1. Télécharger ISO VirtIO drivers
   https://fedorapeople.org/groups/virt/virtio-win/

2. Monter ISO dans VM

3. Installer drivers réseau/stockage
```

**Performances** :
- ✅ **Réseau** : 10Gbps+ (vs 1Gbps émulé e1000)
- ✅ **Disque** : IOPS x5-10 vs IDE émulé

---

### Guest Additions (VirtualBox)

**Rôle** :
- ✅ Drivers vidéo optimisés (résolution native)
- ✅ Dossiers partagés hôte ↔ invité
- ✅ Presse-papiers partagé
- ✅ Drag & drop fichiers

**Installation Linux** :
```bash
# 1. Installer dépendances
sudo apt-get install build-essential dkms linux-headers-$(uname -r)

# 2. Insérer CD Guest Additions (menu VirtualBox)
# Devices → Insert Guest Additions CD Image

# 3. Monter et exécuter
sudo mount /dev/cdrom /mnt
sudo /mnt/VBoxLinuxAdditions.run

# 4. Redémarrer
sudo reboot
```

**Installation Windows** :
```
1. Menu Devices → Insert Guest Additions CD Image
2. Exécuter D:\VBoxWindowsAdditions.exe
3. Redémarrer
```

---

### VMware Tools

**Rôle** :
- ✅ Drivers VMXNET3 (réseau paravirtualisé)
- ✅ PVSCSI (disque paravirtualisé)
- ✅ Synchronisation horloge
- ✅ Outils admin (shutdown propre)

**Installation Linux** :
```bash
# Moderne : open-vm-tools (repository)
sudo apt-get install open-vm-tools
sudo systemctl enable open-vm-tools

# Legacy : VMware Tools (CD)
# (méthode obsolète)
```

---

## 📦 libvirt : Gestion Unifiée VMs

**libvirt** = API/daemon pour gérer machines virtuelles

**Hyperviseurs supportés** :
- KVM/QEMU
- Xen
- LXC (conteneurs)
- VirtualBox

**Composants** :
- **libvirtd** : Daemon gestion VMs
- **virsh** : CLI (commande)
- **virt-manager** : GUI (interface graphique)

**Architecture** :
```
┌──────────────────────────────────┐
│  virt-manager  │     virsh       │ ← Clients
├────────────────┴─────────────────┤
│           libvirt API            │ ← API unifiée
├──────────────────────────────────┤
│         libvirtd daemon          │ ← Daemon
├──────────────────────────────────┤
│  KVM  │  Xen  │  QEMU  │  LXC   │ ← Hyperviseurs
└──────────────────────────────────┘
```

**Commandes virsh courantes** :
```bash
# Lister VMs
virsh list --all

# Démarrer VM
virsh start ma-vm

# Arrêter proprement
virsh shutdown ma-vm

# Arrêt forcé
virsh destroy ma-vm

# Infos VM
virsh dominfo ma-vm

# Console VM
virsh console ma-vm

# Snapshot
virsh snapshot-create-as ma-vm snapshot1
```

**Fichiers de configuration** :
- `/etc/libvirt/qemu/*.xml` → Définitions VMs
- `/var/lib/libvirt/images/` → Disques virtuels

---

## ☁️ cloud-init : Déploiement Automatisé

### Concept

**cloud-init** = Outil **configuration automatique** VMs au premier démarrage

**Analogie** 📋 : 
- Image VM = Formulaire vierge
- cloud-init = Remplir automatiquement formulaire au boot

**Cas d'usage** :
- ✅ Configuration hostname
- ✅ Création utilisateurs + clés SSH
- ✅ Installation paquets
- ✅ Exécution scripts
- ✅ Configuration réseau

**Utilisateurs** :
- AWS EC2
- Google Cloud
- Azure
- OpenStack
- Proxmox

---

### Format cloud-config

**Format** : **YAML** (`.yaml` ou `.yml`)

**Fichier typique** : `cloud-config` ou `user-data`

**Structure** :
```yaml
#cloud-config

# Configuration réseau
hostname: web-server-01
fqdn: web-server-01.example.com

# Utilisateurs
users:
  - name: admin
    sudo: ALL=(ALL) NOPASSWD:ALL
    shell: /bin/bash
    ssh_authorized_keys:
      - ssh-rsa AAAAB3Nza...

# Paquets à installer
packages:
  - nginx
  - git
  - vim

# Commandes à exécuter
runcmd:
  - systemctl enable nginx
  - systemctl start nginx
  - echo "Setup complete" > /var/log/cloud-init-done
```

---

### Sections cloud-config Courantes

#### 1. Hostname

```yaml
#cloud-config
hostname: web-server
fqdn: web-server.example.com
manage_etc_hosts: true
```

---

#### 2. Utilisateurs + SSH

```yaml
#cloud-config
users:
  - name: deploy
    gecos: Deploy User
    sudo: ALL=(ALL) NOPASSWD:ALL
    groups: sudo, docker
    shell: /bin/bash
    ssh_authorized_keys:
      - ssh-rsa AAAAB3NzaC1yc2EA... user@laptop

# Désactiver login root SSH
ssh_pwauth: false
disable_root: true
```

---

#### 3. Installation Paquets

```yaml
#cloud-config
package_update: true
package_upgrade: true

packages:
  - nginx
  - postgresql
  - python3-pip
  - docker.io
```

---

#### 4. Fichiers

```yaml
#cloud-config
write_files:
  - path: /etc/nginx/sites-available/default
    owner: root:root
    permissions: '0644'
    content: |
      server {
        listen 80;
        server_name example.com;
        root /var/www/html;
      }
```

---

#### 5. Commandes

```yaml
#cloud-config
runcmd:
  - systemctl enable docker
  - systemctl start docker
  - docker pull nginx:latest
  - echo "Deploy complete" | wall
```

---

### Exemple Complet

```yaml
#cloud-config

# Métadonnées instance
hostname: prod-web-01
fqdn: prod-web-01.example.com

# Timezone
timezone: Europe/Paris

# Utilisateurs
users:
  - name: devops
    sudo: ALL=(ALL) NOPASSWD:ALL
    groups: sudo, docker
    shell: /bin/bash
    ssh_authorized_keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAB...

# Désactiver password auth
ssh_pwauth: false
disable_root: true

# Mise à jour + paquets
package_update: true
package_upgrade: true

packages:
  - nginx
  - git
  - docker.io
  - python3-pip

# Fichiers config
write_files:
  - path: /etc/nginx/conf.d/app.conf
    content: |
      server {
        listen 80;
        server_name app.example.com;
        location / {
          proxy_pass http://localhost:3000;
        }
      }

# Commandes post-install
runcmd:
  - systemctl enable nginx docker
  - systemctl start nginx docker
  - usermod -aG docker devops
  - git clone https://github.com/company/app.git /opt/app
  - pip3 install -r /opt/app/requirements.txt
  - echo "Instance ready" > /var/log/init-complete

# Redémarrer après init
power_state:
  mode: reboot
  message: "Cloud-init complete, rebooting"
  timeout: 30
```

---

### Déploiement cloud-init

#### Méthode 1 : ISO (NoCloud)

```bash
# 1. Créer user-data
cat > user-data << EOF
#cloud-config
hostname: test-vm
users:
  - name: admin
    sudo: ALL=(ALL) NOPASSWD:ALL
    ssh_authorized_keys:
      - ssh-rsa AAAA...
EOF

# 2. Créer meta-data
cat > meta-data << EOF
instance-id: vm-001
local-hostname: test-vm
EOF

# 3. Créer ISO
genisoimage -output cloud-init.iso \
  -volid cidata -joliet -rock \
  user-data meta-data

# 4. Attacher ISO à VM
# (au boot, cloud-init lit l'ISO)
```

---

#### Méthode 2 : KVM (virt-install)

```bash
virt-install \
  --name web-server \
  --memory 2048 \
  --vcpus 2 \
  --disk path=/var/lib/libvirt/images/web-server.qcow2,size=20 \
  --cloud-init user-data=/path/to/cloud-config.yaml \
  --os-variant ubuntu22.04 \
  --network bridge=virbr0
```

---

#### Méthode 3 : Cloud Provider

**AWS** :
```bash
# user-data passé lors lancement EC2
aws ec2 run-instances \
  --image-id ami-0123456789 \
  --instance-type t2.micro \
  --user-data file://cloud-config.yaml
```

**OpenStack** :
```bash
openstack server create \
  --image ubuntu-22.04 \
  --flavor m1.small \
  --user-data cloud-config.yaml \
  web-server
```

---

### Vérifier cloud-init

```bash
# Logs
sudo cat /var/log/cloud-init.log
sudo cat /var/log/cloud-init-output.log

# Statut
sudo cloud-init status
# status: done

# Détails modules exécutés
sudo cloud-init query -a
```

---

## 📋 Antisèche - Virtualisation

| Concept | Description | Exemples |
|---------|-------------|----------|
| **Type 1** | Hyperviseur sur matériel nu | Xen, ESXi, KVM |
| **Type 2** | Hyperviseur sur OS | VirtualBox, VMware Workstation |
| **Xen** | Type 1, Dom0 + DomU | AWS EC2, Citrix |
| **KVM** | Module noyau Linux | RHEL, Proxmox, OpenStack |
| **VirtualBox** | Type 2 cross-platform | Dev, tests locaux |
| **Migration froide** | VM éteinte, downtime | Maintenance planifiée |
| **Live migration** | VM active, <100ms downtime | Production 24/7 |
| **Virt complète** | OS non modifié, VT-x/AMD-V | Standard moderne |
| **Paravirtualisation** | OS modifié, hypercalls | Xen PV (legacy) |
| **Hybride** | OS standard + drivers PV | VirtIO, Guest Additions |
| **VirtIO** | Drivers PV pour KVM | Réseau/disque optimisés |
| **libvirt** | API gestion VMs | virsh, virt-manager |
| **cloud-init** | Config auto VMs (YAML) | Cloud, automation |

---

## 🎓 Points Clés pour l'Examen

✅ **Type 1 (Bare-Metal)** : Hyperviseur directement sur matériel (Xen, ESXi)  
✅ **Type 2 (Hosted)** : Hyperviseur sur OS (VirtualBox, VMware Workstation)  
✅ **Xen** : Type 1, Dom0 (admin) + DomU (invités)  
✅ **KVM** : Module noyau Linux, Type 1/2 hybride  
✅ **VirtualBox** : Type 2, cross-platform (Windows/Linux/macOS)  
✅ **Migration froide** : VM éteinte, downtime acceptable  
✅ **Live migration** : VM active, <100ms downtime, stockage partagé requis  
✅ **Virtualisation complète** : OS non modifié, VT-x/AMD-V requis  
✅ **Paravirtualisation** : OS modifié, hypercalls, performances max  
✅ **Virtualisation hybride** : OS standard + drivers paravirtualisés (VirtIO)  
✅ **VirtIO** : Drivers paravirtualisés KVM (réseau, disque optimisés)  
✅ **Guest Additions** : Drivers VirtualBox (dossiers partagés, clipboard)  
✅ **libvirt** : API gestion VMs (virsh, virt-manager)  
✅ **libvirtd** : Daemon gestion machines virtuelles  
✅ **cloud-init** : Configuration automatique VMs (YAML, cloud-config)  
✅ **VT-x** : Extensions virtualisation Intel  
✅ **AMD-V/SVM** : Extensions virtualisation AMD  

---

## 💡 Scénarios Pratiques

### Scénario 1 : Vérifier Support Virtualisation CPU

```bash
# Vérifier flags CPU
grep -E '(vmx|svm)' /proc/cpuinfo

# vmx → Intel VT-x présent
# svm → AMD-V présent
# Aucune sortie → Pas de support ou désactivé BIOS

# Compter cores virtualization-capable
grep -c -E '(vmx|svm)' /proc/cpuinfo
# 8
```

**Action** : Si vide, activer VT-x/AMD-V dans BIOS

---

### Scénario 2 : Installation KVM + libvirt

```bash
# Ubuntu/Debian
sudo apt-get install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager

# RHEL/CentOS
sudo yum install qemu-kvm libvirt virt-install virt-manager

# Démarrer libvirtd
sudo systemctl enable libvirtd
sudo systemctl start libvirtd

# Vérifier
sudo virsh list --all
# (liste vide au début)

# Ajouter user au groupe libvirt
sudo usermod -aG libvirt $USER
```

---

### Scénario 3 : Créer VM avec cloud-init

```bash
# 1. Créer cloud-config
cat > cloud-config.yaml << 'EOF'
#cloud-config
hostname: web-vm
users:
  - name: admin
    sudo: ALL=(ALL) NOPASSWD:ALL
    ssh_authorized_keys:
      - ssh-rsa AAAAB3NzaC1yc2EA...
packages:
  - nginx
runcmd:
  - systemctl enable nginx
  - systemctl start nginx
EOF

# 2. Créer VM (Ubuntu Cloud Image)
virt-install \
  --name web-vm \
  --memory 2048 \
  --vcpus 2 \
  --disk path=/var/lib/libvirt/images/web-vm.qcow2,size=20 \
  --cloud-init user-data=cloud-config.yaml \
  --os-variant ubuntu22.04 \
  --network bridge=virbr0 \
  --graphics none

# 3. Connexion SSH (après boot)
ssh admin@<IP-VM>
```

---

### Scénario 4 : Installer VirtIO Windows

```bash
# 1. Télécharger ISO VirtIO
wget https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/latest-virtio/virtio-win.iso

# 2. Créer VM Windows
virt-install \
  --name win10 \
  --memory 4096 \
  --vcpus 2 \
  --disk path=/var/lib/libvirt/images/win10.qcow2,size=50,bus=virtio \
  --disk path=/path/to/virtio-win.iso,device=cdrom \
  --cdrom /path/to/Windows10.iso \
  --network network=default,model=virtio \
  --os-variant win10

# 3. Pendant installation Windows
# - Charger driver stockage VirtIO depuis ISO
# - Installer drivers réseau après boot
```

---

### Scénario 5 : Live Migration KVM

```bash
# Pré-requis : Stockage partagé (NFS)

# Hôte A et B : Monter NFS
sudo mount -t nfs storage-server:/vms /var/lib/libvirt/images

# Hôte A : Démarrer VM
virsh start ma-vm

# Hôte A : Live migrate vers Hôte B
virsh migrate --live --persistent ma-vm qemu+ssh://hote-b/system

# Vérifier sur Hôte B
ssh hote-b
virsh list
# ma-vm running
```

---

### Scénario 6 : Installer Guest Additions VirtualBox

```bash
# Dans VM Linux

# 1. Installer dépendances
sudo apt-get update
sudo apt-get install build-essential dkms linux-headers-$(uname -r)

# 2. Menu VirtualBox : Devices → Insert Guest Additions CD

# 3. Monter et installer
sudo mount /dev/cdrom /mnt
cd /mnt
sudo ./VBoxLinuxAdditions.run

# 4. Redémarrer
sudo reboot

# 5. Vérifier
lsmod | grep vbox
# vboxsf, vboxvideo, vboxguest
```

---

### Scénario 7 : Configuration Réseau cloud-init

```yaml
#cloud-config

# Configuration réseau statique
network:
  version: 2
  ethernets:
    eth0:
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1

hostname: static-vm
```

---

### Scénario 8 : Snapshot VM avec libvirt

```bash
# Créer snapshot
virsh snapshot-create-as ma-vm snapshot-avant-update \
  --description "Avant mise à jour système"

# Lister snapshots
virsh snapshot-list ma-vm

# Restaurer snapshot
virsh snapshot-revert ma-vm snapshot-avant-update

# Supprimer snapshot
virsh snapshot-delete ma-vm snapshot-avant-update
```

---

### Scénario 9 : Cloud-init Multi-Utilisateurs

```yaml
#cloud-config

users:
  - name: admin
    gecos: Administrator
    sudo: ALL=(ALL) NOPASSWD:ALL
    groups: sudo
    shell: /bin/bash
    ssh_authorized_keys:
      - ssh-rsa AAAA... admin@laptop

  - name: developer
    gecos: Developer User
    groups: docker
    shell: /bin/bash
    ssh_authorized_keys:
      - ssh-rsa BBBB... dev@laptop

  - name: monitoring
    gecos: Monitoring User
    shell: /bin/bash
    ssh_authorized_keys:
      - ssh-rsa CCCC... monitoring@server

# Désactiver login password
ssh_pwauth: false
```

---

### Scénario 10 : Vérifier Drivers Paravirtualisés

```bash
# Linux : Vérifier VirtIO
lsmod | grep virtio
# virtio_net, virtio_blk, virtio_pci

# Voir périphériques PCI virtio
lspci | grep -i virtio
# 00:03.0 Ethernet controller: Red Hat, Inc. Virtio network device
# 00:04.0 SCSI storage controller: Red Hat, Inc. Virtio block device

# Vérifier driver réseau
ethtool -i eth0
# driver: virtio_net

# Performances réseau (avec virtio vs e1000)
iperf3 -s  # Sur hôte
iperf3 -c <IP-hote>  # Dans VM
# VirtIO: 8-10 Gbps
# e1000: ~1 Gbps
```

---

## 🔄 Workflow Complet

### Déploiement VM Production avec cloud-init

```
1. PRÉPARER IMAGE
   - Télécharger image cloud (Ubuntu Cloud, CentOS Cloud)
   - Vérifier intégrité (checksum)

2. CRÉER CLOUD-CONFIG
   - Hostname, users, SSH keys
   - Paquets, configuration réseau
   - Scripts post-install

3. LANCER VM
   - virt-install avec --cloud-init
   - OU créer ISO NoCloud

4. VÉRIFIER
   - cloud-init status
   - Connexion SSH
   - Services démarrés

5. OPTIMISER
   - Installer drivers paravirtualisés (VirtIO)
   - Configurer autostart
   - Créer snapshot initial
```

---

## ⚠️ Erreurs Courantes

### Erreur 1 : Virtualisation Désactivée BIOS

```
KVM: disabled by BIOS
```

**Solution** :
1. Redémarrer, entrer BIOS (F2/Del)
2. Chercher "Virtualization Technology" ou "VT-x" ou "AMD-V"
3. Activer (Enabled)
4. Sauvegarder et redémarrer

---

### Erreur 2 : VM Ne Démarre Pas (cloud-init)

```
cloud-init[...]: WARNING: No instance data found
```

**Solution** :
```bash
# Vérifier format YAML
yamllint cloud-config.yaml

# Vérifier première ligne
head -1 cloud-config.yaml
# Doit être : #cloud-config

# Vérifier logs
sudo tail -f /var/log/cloud-init.log
```

---

### Erreur 3 : Drivers VirtIO Absents Windows

```
No drives found during Windows installation
```

**Solution** :
1. Attacher ISO VirtIO à VM
2. Pendant install Windows : "Load driver"
3. Naviguer vers ISO → viostor → win10 → amd64
4. Installer driver stockage

---

### Erreur 4 : Live Migration Échoue

```
error: migration failed: access denied
```

**Solution** :
```bash
# Vérifier SSH entre hôtes
ssh hote-b

# Vérifier libvirt écoute TCP
sudo nano /etc/libvirt/libvirtd.conf
# listen_tls = 0
# listen_tcp = 1

sudo systemctl restart libvirtd

# Ou utiliser SSH (recommandé)
virsh migrate --live ma-vm qemu+ssh://hote-b/system
```

---

**🎯 Prochaine étape** : Work on the Command Line (Module 3)

*Dernière mise à jour: 1 février 2026*
