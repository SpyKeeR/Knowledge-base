# Objet - Logiciel🛠️ **Créer un objet Logiciel** ↳ Accueil > Parc > Logiciels > + Ajouter

Tu as deux façons de procéder : **📄 Gabarit vide** : tout à remplir toi-même ou **📑 Gabarit existant** : une base déjà pré-remplie que tu ajustes

👣 **Étapes à suivre (à partir d’un gabarit vide)** : **Nom** du logiciel → exemple : *Microsoft Office 2021 /* **Lieu** : par défaut "stock"

**💡 Important** : un objet logiciel n’est **jamais** affecté directement à un poste (comme un ordi ou un serveur), mais peut être lié à des installations.

- **Éditeur** → ex : *Microsoft /* **Utilisateur** → souvent vide, sauf si c’est une licence nominative / **Technicien & Groupe responsable** → pour la gestion du suivi / **Image** → facultative, mais utile visuellement / **Catégorie** → ex : Bureautique, Système, Sécurité, etc.

💬 Tu peux aussi définir si un logiciel peut être **associé à des tickets**, ce qui te permet de centraliser les interventions ou incidents liés à ce logiciel.



🔎 **Fiche d’un objet Logiciel : ce qu’elle contient**

Voici les onglets principaux d’un objet logiciel : **🧠 Analyse d’impact** : visualise les dépendances. Utile si un soft tombe en panne, pour anticiper les effets domino sur les machines ou services liés. / **📦 Versions** : plusieurs versions possibles du même logiciel (ex : Office 2016 / 2019 / 2021) / **📜 Licences** : lier chaque version à ses licences correspondantes / **💻 Installations** : lien vers les ordis où la version est installée / **💰 Gestion** : infos financières, facturation, fournisseurs / **📃 Contrats** : contrats liés à ce logiciel / **📁 Documents** : manuel, contrat PDF, bons de commande, etc. / **🧠 Base de connaissances** : articles d’aide ou procédures liés à ce soft /**🛠️ Tickets, Problèmes, Changements** : le suivi ITIL si tu actives ces modules / **🔗 Liens** : permet de relier cet objet à d'autres objets GLPI / **📝 Notes, Réservations, Domaines, Applicatifs, Regroupement** : pour des usages avancés ou en entreprise



🔐 **Créer une Licence >** ↳ Accueil > Gestion > Licences > + Ajouter

💡 Une licence est un objet à part entière dans GLPI. Elle est **liée à un logiciel et à une version**.

📝 **Infos de base à remplir** : **Logiciel associé / Nom de la licence** → ex : *Office 2021 Pro Volume /* **Type de licence** : **Boîte** : achetée en physique, **Volume** : en gros, pour plusieurs postes, **OEM** : préinstallée, liée à une machine, **Libre** : ex : LibreOffice / **Numéro de série / Nombre de licences / Version utilisée / Expiration (si abonnement) / Technicien + groupe responsable / Lieu + numéro d'inventaire / Autoriser le dépassement** : si on peut dépasser le quota de licences sans bloquer

🔗 **Tu peux aussi lier :** Des objets (PC utilisant la licence) / Des contrats / Des tickets ou changements / Des documents et certificats (ex : fichier de clé, facture, attestation d'achat…)



🎯 **Exemple concret pour mieux visualiser :**

Tu gères 50 postes dans une entreprise. Tu as acheté : 50 licences **Office 2021 Volume /** 10 licences **Adobe Acrobat Pro** version 2020 / 1 licence **LibreOffice** (logiciel libre, installé sur les postes restants)

Dans GLPI : Tu crées 3 objets "Logiciel" → *Office 2021*, *Acrobat Pro 2020*, *LibreOffice,* Tu ajoutes leurs **versions** respectives, Tu lies les **licences** correspondantes, Puis tu crées les **installations** en les reliant aux postes concernés.

Ainsi, tu peux à tout moment savoir : ✅ Combien de licences sont utilisées ✅ Où elles sont installées ✅ Quand elles expirent ✅ Qui est responsable
