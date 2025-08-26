# Création d'un objet

Avant même de cliquer sur "Ajouter", faut réfléchir un peu 🧠 : La gestion de parc dans GLPI, c’est pas juste “je mets un PC dans une liste” — on structure, on organise, on pense pour que ce soit clair et maintenable dans le temps.

Voici ce que tu dois préparer avant de commencer : 
- 📛 **Convention de nommage** : pour éviter les doublons et reconnaître un objet d’un coup d’œil.
- 🧾 **Numérotation d’inventaire** : définie par la DSI (souvent avec un format comme <PC-\y\m-####>)
- 🏢 **Structure des groupes** : quels groupes auront accès à quoi ?
- 🧑‍🔧 **Rôles et profils des techniciens** : qui fait quoi.
- ⏱️ **SLA et escalades** : quels délais ? Qui intervient si le premier niveau ne suffit pas ?
- 📑 **Gabarits & catégories** : pour accélérer les saisies futures et avoir une arbo logique.
- 🔍 **Base de connaissances** : liée à certains types d’objets ou pannes récurrentes.
- 🧩 **Modèles et composants** : connaître ce qu’on gère, ce qu’on surveille.

🎯 L’objectif : avoir une saisie standardisée, claire, et un parc **propre et exploitable**.



## 🗂️ **🖥️ Menu « Parc » de GLPI : C’est quoi ?**

GLPI ne se limite pas aux ordinateurs ! 

Voici les types d’objets que tu peux gérer : 
- Ordinateurs, Moniteurs, Logiciels, Matériels réseau, Périphériques, Imprimantes,
- 📦 Cartouches, Consommables,
- 📱 Téléphones, Cartes SIM,
- 🗄️ Baies, Châssis, PDU,
- 🧷 Équipements passifs (bandeaux, etc.), non gérés,
- 🔌 Câbles,
- 🌍 Et une vue globale !

📊 **Tableau de bord** : Vue synthétique de tout ton parc, avec filtres et affichages personnalisables. 

💡 Petit contexte : Imagine une DSI qui gère 300 postes, 50 imprimantes et 20 serveurs. Sans structure, ça devient vite le bazar. Grâce à GLPI, tout est visible, lié, et surtout… exploitable pour faire du support ou des bilans de renouvellement.



## 🖱️ **🆕 Création d’un nouvel objet « Ordinateur »** 
### 👉 Accueil > Parc > Ordinateurs > **+ Ajouter**

Tu peux : Créer à partir d’un **gabarit vide ou** Créer à partir d’un **gabarit existant** (déjà prérempli = gain de temps)

Voici les champs essentiels à renseigner : 
- 🔹 **Nom de l’ordinateur**
- 🔹 **Lieu** (par défaut : en stock)
- 🔹 **Technicien responsable**
- 🔹 **Groupe responsable**
- 🔹 **Utilisateur (utilisé à la sortie du stock)**
- 🔹 **Statut** (par défaut : En stock)
- 🔹 **Type** (Portable / Bureau / Serveur…)
- 🔹 **Fabricant / Modèle** (tu peux lier à une fiche modèle avec visuel, poids, dimensions, etc.)
- 🔹 **Numéro de série / Numéro d’inventaire** (automatisé avec des formats dynamiques)
- 🔹 **Réseau / UUID** (optionnel mais utile pour de la traçabilité fine)



## 🧬 **📄 Contenu détaillé d’une fiche objet (Ordinateur)**

Voici tout ce que peut contenir la fiche une fois complétée : 
- 🔍 **Analyse d’impact** : relations avec d’autres objets (ex : dépendances)
- 🖥️ **Systèmes d’exploitation** : OS installé
- 🧩 **Composants** : RAM, CPU, disque… (défini manuellement ou par inventaire)
- 💽 **Volumes** : partitions du disque
- 💿 **Logiciels** : installés manuellement ou via l’inventaire automatique
- 🔗 **Connexions** : moniteurs, téléphones, imprimantes liés
- 🌐 **Ports réseau / Connecteurs**
- 📡 **Contrôle à distance** (ex : IP d’accès VNC/RDP)
- 📁 **Gestion / Financier / Contrats / Documents**
- 🧱 **Virtualisation** (VM hébergées dessus)
- 🛡️ **Antivirus** 📚 **Base de connaissances** liée
- 🎟️ **Tickets / Problèmes / Changements** liés
- 🔒 **Certificats**
- 🗒️ **Notes**
- 📅 **Réservations**
- 🌐 **Domaines d’appartenance**
- 🛠️ **Applicatifs / Bases de données liées**



💡 **Petit cas concret pour illustrer :** 👉 Ton entreprise reçoit 10 nouveaux PC portables Dell pour le service compta.

Tu as défini un gabarit “Portable Dell Compta 2024”, Tu crées les objets à partir de ce gabarit, Tu attribues les utilisateurs, le groupe “Comptabilité”, l’état “En stock”, Tu prépares les fiches pour les mettre à jour quand ils seront déployés : IP, OS, logiciels installés, etc.

⏩ Résultat : en 10 minutes, tu as une trace claire, consultable et exploitable pour tout le service.



### 🔁 **🔧 Autres objets : même logique, parfois sans gabarit**

➡️ **Avec gabarit possible** : Moniteurs, Matériels réseau, Périphériques, Imprimantes, Téléphones, Baies, Châssis, PDU, Équipements passifs

➡️ **Sans gabarit** : Consommables, Équipements non gérés, Câbles, Cartes SIM


