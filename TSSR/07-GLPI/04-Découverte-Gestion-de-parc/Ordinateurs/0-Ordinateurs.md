# Item Ordinateurs


Un **inventaire informatique**, c’est faire un **état des lieux du parc matériel** : 📦 Matériel physique (PC, imprimantes, serveurs, etc.) / 💻 Logiciels installés, avec ou sans licence / 🧰 Composants (RAM, disque, carte mère, etc.)

GLPI permet d’**organiser toutes ces informations dans un outil centralisé**, à travers une gestion informatisée.



### **🧠 Pourquoi faire un inventaire ?**

- 🧮 Suivre le **patrimoine informatique** (matériel/logiciel)
- 💸 Faire le lien avec la **comptabilité** et les **amortissements**
- 📍 Vérifier la **présence physique** des équipements (perdus, volés, recyclés ?)
- 🔄 Suivre leur **cycle de vie** : achat → stock → Préparation → affectation → utilisation → recyclage



## **🗂️ Historique et relations**

- Suivi des **modifications** dans le temps pour chaque item
- Association à des **OS** (Windows, Linux, macOS)
- Association à des **licences**, des **utilisateurs**, ou des **services**



## **🧩 Un ordinateur, un ensemble de composants**

Dans GLPI, un **ordinateur** est vu comme un **ensemble de composants** : 🧠 Processeur / 📏 RAM / 💾 Disque dur / 🎮 Carte graphique / 🔌 Alimentation, etc.

Ces composants sont listés dans le menu **Configuration > Composants**, avec des types **prédéfinis par GLPI** (⚠️ non modifiables).



## **🗺️ Cartographie réseau**

GLPI permet aussi une **cartographie** du matériel : / Association des machines à des **prises murales**, **switchs**, etc. / Vue d’ensemble du **réseau physique**



## **📅 Réservation de matériel**

GLPI gère aussi les **réservations** : Exemple : 📽️ réserver un vidéoprojecteur pour une réunion / Période définie + retour de l’équipement à la fin



## **🧾 Plan de nommage : la clé de l’organisation**

Créer un **plan de nommage cohérent** = essentiel pour un inventaire propre : 🔠 Structure du nom = type + service + date + index

Exemple : PC-COM-2023-001 - PC = type (portable, imprimante, etc.) / COM = service ou site (compta, RH, etc.) / 2023 = année d’achat / 001 = identifiant unique

Permet d'Identifier rapidement le matériel, Suivre les **lots** d’achat, Faciliter l’**amortissement** 💰



## **🛠️ Gabarits et inventaire automatisé**

Possibilité de créer des **gabarits** pour gagner du temps (modèle de fiche équipement)

Utilisation d’un outil **d’inventaire automatique** (ex : FusionInventory) 🧩 Installe un agent sur les machines et 📡 Fait remonter les données automatiquement dans GLPI



## **🧷 Logiciels et licences**

GLPI permet aussi de : Gérer les **licences /** Associer les logiciels aux équipements / Vérifier leur **conformité** (comptes, installations, affectations)

