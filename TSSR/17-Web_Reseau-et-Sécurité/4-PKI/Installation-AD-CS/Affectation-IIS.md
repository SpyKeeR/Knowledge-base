# Affectation IIS🧱 **Création d’un modèle de certificat dans certsrv**

🔁 Étapes :

1.  Ouvrir **certsrv.msc** sur le serveur ADCS
2.  Naviguer vers **Modèles de certificats** > clic droit > **Gérer**
3.  Rechercher un modèle existant proche du besoin (ex. : "Web Server")
4.  Clic droit > **Dupliquer le modèle**
5.  Remplir les champs :

    - 🏷️ **Nom complet du modèle** : ex. Certificat Web SSL Rouliere
    - 🧾 **Nom du modèle publié dans l'AD** : ex. webssl_rouliere

🔐 Sécurité :

- Dans l’onglet **Sécurité**, ajouter le **serveur IIS** (ou le groupe de serveurs)
- Donner les droits **Lecture** + **Inscription**

✅ Enregistrer et fermer



🗂️ **Publication du modèle personnalisé dans AD CS**

🔧 Dans **certsrv** > clic droit sur **Modèles de certificats** > **Nouveau** > **Modèle à délivrer**

📌 Sélectionner le modèle récemment créé (webssl_rouliere)

🖥️ **Demande d’un certificat depuis le serveur IIS (via certlm.msc)**

1.  Ouvrir **certlm.msc** (certificats locaux de l’ordinateur)
2.  Aller dans **Personnel** > clic droit > **Toutes les tâches** > **Demander un nouveau certificat**

🪪 Assistant de demande :

- Sélectionner le modèle webssl_rouliere
- Cliquer sur **Détails** > **Propriétés**
  - 🔤 Renseigner le **Nom Commun (CN)** : [www.rouliere.eni](http://www.rouliere.eni)
  - ➕ Ajouter un **Autre nom (Subject Alternative Name)** : DNS=www.rouliere.eni
- Valider, puis **Soumettre** ✅ Le certificat apparaît dans le conteneur **Personnel**



🌐 **Liaison du certificat dans IIS**

📍 Ouvrir le **Gestionnaire IIS** > **Sites** > clic droit sur le site ([www.rouliere.eni](http://www.rouliere.eni)) > **Modifier les liaisons…**

➕ Ajouter ou modifier la liaison HTTPS :

- Type : https
- Port : 443
- Certificat SSL : sélectionner le certificat issu de la PKI (webssl_rouliere)
- Valider et redémarrer le site si nécessaire
