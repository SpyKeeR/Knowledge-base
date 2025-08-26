# Items & reservation

## **🏢 Centres de données (Datacenters)**

Les **centres de données** sont des lieux physiques spécialisés :

- Contiennent **plusieurs salles**
- Chaque salle contient des **baies de stockage** (racks)

Dans ces baies, on peut retrouver :

- **Ordinateurs**
- **Switchs**
- **Routeurs**
- **PDU (multiprises intelligentes)**
- **Onduleurs (UPS)**

### 🧩 Gestion dans GLPI :

- Chaque **élément est lié à une baie**
- Chaque **baie est liée à une salle**
- Chaque **salle est liée au datacenter**

📐 Permet de cartographier avec précision l’emplacement des équipements dans une **infrastructure physique complexe**.



## **📅 Réservations d’équipements**

GLPI permet de **réserver certains équipements** depuis : **Outils > Réservations**

### 🔓 Pour rendre un élément réservable :

1.  Aller dans l’élément (ex. : vidéoprojecteur)
2.  Onglet **"Réservation"**
3.  Cocher **"Autoriser la réservation"**

Une fois activé :

- Il devient visible dans le menu de réservation
- On peut le **réserver avec une date de début, une durée, un utilisateur**

### 🚫 Quand un équipement est réservé :

- Il **disparaît temporairement** de la liste
- Il réapparaît **automatiquement** à la fin de la période

### ✅ Possibilités supplémentaires :

- **Annuler une réservation**
- **Rendre un élément à nouveau disponible manuellement**
- **Désactiver complètement la réservation** sur un item

## **📱 Téléphones**

Les **téléphones** sont des **items de configuration à part entière**, avec des **attributs spécifiques** :

- Peuvent avoir **un ou plusieurs combinés**
- Peuvent être reliés à **une ou plusieurs lignes téléphoniques**
- Ont un **rôle** (ex : fixe, mobile, IP…)
- Peuvent être associés à un **utilisateur**
- Peuvent intégrer des accessoires : **casque**, **haut-parleur**
- Associables à des **tickets** (incident ou demande)

📝 On peut gérer ces téléphones **globalement ou individuellement**.



## **🖱️ Périphériques**

Les **périphériques** regroupent **tout ce qu’on peut connecter à un ordinateur**.

Exemples :

- **Caméra IP**
- **Vidéoprojecteur**
- **Clé USB**
- **Scanner**
- **Disque dur externe**, etc.

### 🛠️ Configuration possible :

- Définir leur **type** via les **intitulés** (depuis *Configuration > Intitulés*)
- Spécifier leur **type de connexion** (USB, HDMI, etc.)
- Possibilité de **réserver** le périphérique (pratique s’il est **amovible**)
- Associer un ou plusieurs **usagers habituels**
- Toujours possible de lier à un **ticket**

### 📦 Chaque périphérique a aussi :

- Un **modèle**
- Un **numéro de série**
- Un **numéro d’inventaire**
- Un **constructeur**

