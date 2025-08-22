##  **📌 1. Rôle et Fonctionnement**

🔹 **Quatrième couche** du modèle OSI, entre la couche Réseau et la couche Session.

🔹 Assure la **communication fiable** entre deux appareils connectés.

🔹 Utilise des **sockets** (Adresse IP + Port + Protocole) pour établir les connexions.

🔹 Gère la transmission des **segments** et leur réassemblage à l’arrivée.


## **🔗 2. Communication entre deux Postes**

🖥️ **Un client communique avec un serveur** via des sockets :

📍 **Socket client** → Adresse IP + Port source + Protocole.

📍 **Socket serveur** → Adresse IP + Port destination + Protocole.

🔄 Une connexion peut gérer **plusieurs communications simultanées**.


## **🛠 3. Fonctions Principales de la Couche Transport**

📦 **Segmentation & Réassemblage** → Découpe les données en segments et les numérote.

🚦 **Contrôle de Flux** → Régule la vitesse d’envoi pour éviter la surcharge.

🔍 **Contrôle d’Erreur** → Vérifie et corrige les erreurs de transmission.

🔗 **Gestion des Connexions** → Établit, maintient et termine les connexions.

🔄 **Multiplexage** → Permet à plusieurs applications d’utiliser le réseau en parallèle via les ports.


## **📡 4. Protocoles de la Couche Transport**

💾 **TCP (Transmission Control Protocol)**

✅ **Orienté connexion** → Vérifie l’ordre et l’intégrité des segments.

✅ **Fiable** → Relance les segments perdus.

✅ **Utilisation** → Transfert de fichiers, navigation web, emails.

⚡ **UDP (User Datagram Protocol)**

❌ **Sans connexion** → Pas de vérification de réception.

❌ **Pas de contrôle d’erreur** → Possibilité de perte de données.

⚡ **Rapide** → Utilisé pour les communications en temps réel.

🎤 **Utilisation** → Streaming, VoIP, jeux en ligne.


## **📂 5. Exemple Pratique : Envoi d’un Fichier**

📍 **Ordinateur A envoie un fichier à Ordinateur B**

🔹 **Segmentation** → Le fichier est découpé en segments numérotés.

🔹 **Transmission et Contrôle d’Erreur** → Chaque segment est vérifié.

🔹 **Réassemblage** → B reconstruit le fichier une fois tous les segments reçus.
