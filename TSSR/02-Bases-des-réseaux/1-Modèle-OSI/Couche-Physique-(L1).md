# **🧳 Couche Physique - Modèle OSI**

## **🔌 Connexion Réseau :**

- **Rôle : Connecte les nœuds réseau 🖥️🔗**
- **Couche 1 : Connectique & modulation des données**
- **Couche 2 : Adresse physique (MAC unique)**


## **🔧 Fonction de la Couche Physique**

- **📡 Support de Transmission :**
  - Types : Câbles Ethernet, Fibre Optique, Wi-Fi
  - Détermine caractéristiques (longueur câble, fréquence, etc.)
- **🖧 Encodage & Synchronisation :**
  - Encode données en signaux 🟢🔴 (électrique, lumineux, radio)
  - Synchronise émetteur et récepteur pour transmission précise
- **⚡ Débit de Transfert :**
  - Contrôle la vitesse de transmission (bps) 🏎️💨
- **🌍 Topologie Physique du Réseau :**
  - Structure du réseau : étoile, bus, anneau, mesh 🌐
- **🔒 Gestion Connexions :**
  - Supervise connexion (établissement, maintien, coupure) 🚪
  - Détection collision sur réseaux partagés ⚠️


## **🔧 Matériel de la Couche Physique**

- **🔌 Câbles :** Ethernet, Fibre Optique, Coaxial
- **🔌 Connecteurs :** RJ45
- **📶 Émetteurs & Récepteurs radio** (Wi-Fi)
- **🔄 Hubs & Répéteurs** (amplification signal)
- **🔲 Modems** (conversion signaux analogiques/numériques)


## **Type de câbles**

1.  **UTP (Unshielded Twisted Pair)**
    - **Pas de blindage → Plus léger, mais vulnérable aux interférences ⚡**

2.  **STP (Shielded Twisted Pair)**
    - **Avec blindage → Protégé contre interférences 💪**


### **🧑‍🔧 Câble Droit (Straight-Through Cable)**

- **Utilisation :**
  - Ordinateur ↔ Commutateur
  - Ordinateur ↔ Routeur
  - Commutateur ↔ Routeur
- **Schéma :** T568A ou T568B (même ordre aux deux extrémités) 🔄

### **🔀 Câble Croisé (Crossover Cable)**

- **Utilisation :**
  - Ordinateur ↔ Ordinateur
  - Commutateur ↔ Commutateur
  - Routeur ↔ Routeur
- **Schéma :**
  - **T568A ↔ T568B** → Croisement des fils TX/RX 🔁

### **⚙️ Choisir le Bon Câble**

- **Câble Droit :** Connecter appareils différents (Ex : Ordinateur ↔ Commutateur)
- **Câble Croisé :** Connecter appareils similaires (Ex : Ordinateur ↔ Ordinateur)

**Auto MDI-X** : Permet de se passer du câble croisé, moderne ⚙️


