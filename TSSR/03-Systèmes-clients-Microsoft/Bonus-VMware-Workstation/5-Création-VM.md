## **🏁 1️⃣️Lancement de VMware Workstation**

📌 **Accueil de Workstation** :

🔹 Accès rapide aux actions principales (Créer une nouvelle VM, Ajouter une VM existante).

🔹 Liste des VM disponibles (vide si installation récente).



## **⚙️ 2️⃣ Création d’une Nouvelle VM**

📌 **Méthode recommandée** :
- ✅ **"I will install the operating system later"** ➝ Permet de personnaliser la configuration de la VM.

📌 **Méthodes alternatives (moins flexibles)** :

❌ **Depuis un DVD** ➝ Workstation détecte l’OS automatiquement.

❌ **Depuis une ISO** ➝ Configuration partiellement automatique.



## **🛠️ 3️⃣️ Configuration de la VM**

🔹 **Choix du système d’exploitation** ➝ Influence la configuration matérielle recommandée.

🔹 **Nom et emplacement** : 
- ✅ Définition du nom de la VM.
- ✅ Hébergement des fichiers sur un volume dédié (idéalement un SSD).

🔹 **Taille et format du disque dur virtuel** :
- ✅ Taille recommandée (ex: **64 Go pour Windows 10**).
- ✅ **Format VMDK** (Virtual Machine Disk).
- ✅ **Stockage sous forme de fichier unique** pour plus de simplicité.



## **🚀 4️⃣️ Démarrage et Configuration Matérielle**

📌 **Récapitulatif avant finalisation** : 
- 🔹 **CPU, RAM, Carte réseau** prédéfinis par Workstation.
- 🔹 **Personnalisation via "Customize Hardware"** :
  - ✅ Ajustement de la **RAM** et des **cœurs CPU**.
  - ✅ Configuration du **réseau** (ex: mode "Host-only").

📌 **Finalisation** :

- ✅ **Finish** ➝ La VM est créée et ajoutée à la bibliothèque.
- ✅ **Démarrage** ➝ La VM démarre mais bloque car aucun OS n’est installé.



## **🔍 5️⃣️ Vérification des Fichiers de la VM**

📌 **Arrêt de la VM** : 
- ➡️ **Clic droit sur la VM** ➝ **Power > Shutdown Guest**.

📌 **Fichiers générés** :

📂 **Dossier VM** contient plusieurs fichiers :

- **.vmx** ➝ Fichier de configuration (modifiable via un éditeur de texte).
- **.vmdk** ➝ Disque dur virtuel (taille dynamique, grandit selon l’usage).

