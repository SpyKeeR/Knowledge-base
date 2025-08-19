# Objet - Cartouches📦 **1. Créer une cartouche dans GLPI** ↳ Accueil > Parc > Cartouches > + Ajouter

Tu vas ici créer un **objet consommable** (cartouche, toner, ruban, etc.) que tu pourras suivre dans ton stock, associer à des imprimantes et gérer en approvisionnement.

📝 **Étapes de création :**

- **Nom** : le nom du modèle de cartouche (ex : *Canon 725 noir*)
- **Type de cartouche** : *Toner* pour les imprimantes laser, *Cartouche* pour les jet d'encre et *Bande* pour les unités de sauvegarde (ex : LTO)
- **Technicien / Groupe responsable**
- **Seuil d’alerte** 🔔 : stock minimum → déclenche une notification (ex : "Prévenir à partir de 2 unités")
- **Objectif de stock** 🎯 : stock cible → combien tu veux toujours avoir en réserve
- **Lieu de stockage** : pièce, armoire, local, etc.
- **Référence constructeur**
- **Fabricant** : Canon, HP, Epson…
- **Commentaires**
- **Image** : pour repérer visuellement le modèle

💡 **Contexte** : Imagine une entreprise avec 10 imprimantes de différents modèles. Pour chaque cartouche, tu veux savoir combien t'en as, combien t'en consommes, et lesquelles sont compatibles avec quoi. C’est exactement ce que te permet ce module.

🧾 **2. Onglets disponibles dans la fiche "Cartouches"**

Une fois la fiche créée, elle s’enrichit de plusieurs sections :

- **🧺 Cartouches** : Liste des cartouches **neuves**
  - Liste des cartouches **usagées**, avec le **nombre de pages imprimées** (utile pour analyser la durée de vie réelle par modèle)
- **🖨️ Modèles d’imprimantes** :
  - Tu dois **lier chaque cartouche à un ou plusieurs modèles d’imprimantes**
  - **⚠️ Obligatoire** : sans cette liaison, tu ne pourras **pas "installer" la cartouche sur une imprimante** dans GLPI
  - Tu fais donc le lien consommable ↔ imprimante
- **📋 Gestion** : infos financières et administratives (fournisseur, prix, date d’achat, etc.)
- **📎 Documents** : manuels, bon de commande, fiches techniques…
- **🔗 Liens** : connexions éventuelles à d'autres objets GLPI
- **🗒️ Notes** : ajouts manuels, informations complémentaires internes

📌 **Exemple concret**

👉 Tu es gestionnaire IT dans une école avec : 5 imprimantes **HP LaserJet 1320 et** 3 imprimantes **Brother HL-L2375DW**

Tu crées deux objets "cartouche" : *HP 49A Toner Noir et Brother TN-2420 Toner Noir*

Tu indiques : Seuil alerte = 2 / Objectif = 5 / Lieu = Réserve du RDC / Technicien = Maxime 😎

Puis tu **lies chaque cartouche à son ou ses modèles d’imprimantes compatibles**.

Quand tu changes une cartouche, tu la déclares comme *usagée*, et hop ! Suivi fait 📉
