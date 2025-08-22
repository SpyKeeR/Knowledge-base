##  **📌 1. Rôle et Fonctionnement**

🔹 Deuxième couche du modèle OSI, elle assure le **transfert fiable** des données entre deux dispositifs d’un même réseau local.

🔹 Encapsule les paquets de la couche **Réseau (L3)** sous forme de **trames** 📦.

🔹 Utilise des **adresses MAC** 🏷️ pour identifier les équipements sur le réseau.

🔹 Gère les **erreurs de transmission** et contrôle l’accès au support.



## **🏗 2. Les 2 Sous-couches**

### 🔹 **LLC (Logical Link Control) 🖧**

- Gère les **connexions logiques** entre appareils.
- Permet d'utiliser plusieurs **protocoles réseau** sur un même support physique (ex: IP, IPX).

### 🔹 **MAC (Media Access Control) 🎛**

- Définit les **règles d’accès** au support réseau (ex: CSMA/CD pour Ethernet).
- Assigne une **adresse MAC** unique à chaque périphérique.



## **🔍 3. Fonctions principales**

📦 **Encapsulation en trames** → Ajout des adresses MAC (source/destination) et d’un contrôle d’erreur.

🛑 **Contrôle d’erreurs** → Utilisation du **CRC (Cyclic Redundancy Check)** pour vérifier l’intégrité des trames.

🚦 **Contrôle de flux** → Régule la vitesse d’envoi des données pour éviter la surcharge du récepteur.

🔀 **Contrôle d’accès au support** → Empêche les collisions en gérant l’ordre des transmissions.



## **🔗 4. Adresses MAC & Protocole ARP**

### 📌 **Adresse MAC** : 48 bits (6 octets)

- **3 premiers octets** 🏭 → **OUI** (Organizationally Unique Identifier) = Identifiant constructeur.
- **3 derniers octets** 📟 → **Identifiant unique** du périphérique.

### 🔄 **ARP (Address Resolution Protocol)** : Résolution d’adresse MAC à partir d’une IP 📡

1️⃣ **PC A** envoie une requête **broadcast** 🔊 (FF:FF:FF:FF:FF:FF) pour trouver l’adresse MAC associée à une IP.

2️⃣ **PC B** répond en **unicast** 📩 avec son adresse MAC.

3️⃣ **PC A** enregistre cette association dans sa **table ARP** 🗂.



## **🖧 5. Équipements utilisant la couche Liaison**

🖥 **Switch (commutateur)** → Aiguillage des trames en fonction des adresses MAC

>< **Bridge (pont)** → Relie plusieurs segments de réseau et filtre les trames.



## **⚙️ 6. Exemple de communication locale**

📤 **PC A** veut envoyer un fichier à **PC B** 📥

🔄 **Encapsulation en trame** : Ajout de l’adresse MAC de **PC B** et d’un contrôle d’erreur.

⏳ **Contrôle d’accès au support** : Vérifie que la transmission est possible.

✅ **Vérification** : **PC B** reçoit la trame, vérifie l’intégrité (CRC) et accepte les données.

