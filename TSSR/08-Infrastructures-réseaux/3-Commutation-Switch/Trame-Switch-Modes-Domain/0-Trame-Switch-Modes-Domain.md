# Trame/Switch/Modes/Domain

## **🧠 Trame Ethernet 802.3 : rôle et fonctionnement**

### 📦 **Rôle** : Transporte les paquets IP en **couche 2** du modèle OSI

### 🔄 **Fonctionnement** :

• Encapsule les paquets IP

• Ajoute un en-tête Ethernet (MAC source + MAC destination + type)

• Utilise le protocole **ARP** pour résoudre l’adresse MAC de destination

• Circule de **saut en saut** (hop-by-hop) jusqu’à l’hôte final

• Intègre un **contrôle d’erreurs** (FCS)

### 🧱 **Couche OSI** concernée : **Couche 2 - Liaison de données**



## **🖥️ Switch Ethernet : fonctionnement & couches OSI**

### 🔌 **Rôle** :

• Relie les terminaux d’un réseau local

• Sépare les hôtes en **domaines de collision distincts**

### 🧠 **Fonctionnement** :

• Interprète les trames Ethernet

• Lit les adresses MAC source → les **enregistre dans sa table**

• Consulte la table pour envoyer les trames uniquement sur le **port cible**

• Fonctionne **en full-duplex** par défaut

• **Scinde** le réseau en un seul domaine de **diffusion** par VLAN

### 🧱 **Couches OSI** :

• **Couche 1** (physique : transmission de bits)

• **Couche 2** (liaison : interprétation des trames)



### **🔄 Modes de communication : duplex et vitesse**

⚙️ **Duplex** (configurable sur chaque port) :

• **half-duplex** = bidirectionnel mais pas simultané (→ CSMA/CD actif)

• **full-duplex** = bidirectionnel **simultané** (→ CSMA/CD désactivé)

####🔧 **Commandes** :

• duplex {auto | full | half}

• speed {10 | 100 | 1000 | auto}

**🧩 Domaines de collision vs. diffusion**

### **🔁 Collision**

• Hub = **1 domaine de collision global**

• Switch (full-duplex) = **1 domaine par port**

• CSMA/CD **désactivé** en full-duplex

• Chaque port = une **zone isolée de collisions**

#### **📢 Diffusion**

• Switch = **1 domaine de diffusion par VLAN**

• Routeur = **1 domaine de diffusion par interface**



**⚔️ Comparatif visuel**

| **Équipement**     | **Domaine de collision** | **Domaine de diffusion** |
|--------------------|--------------------------|--------------------------|
| Hub                | 1 global                 | 1 global                 |
| Switch (par port)  | 1 par port               | 1 par VLAN               |
| Routeur (par port) | Isolement total          | 1 par interface          |

![](../../../media/Cours-Infrastructures-réseaux-Trame-Switch-Modes-Domain-image2.png)



