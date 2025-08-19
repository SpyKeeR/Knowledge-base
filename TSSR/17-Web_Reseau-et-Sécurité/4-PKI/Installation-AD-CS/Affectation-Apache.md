# Affectation Apache14:20

**📝 Création du fichier de configuration de la demande de certificat**

- **Créer un fichier d’options** nommé fic.txt pour configurer la demande de certificat.
- Structure du fichier fic.txt :
  - [ req ] : Configure les options par défaut pour la demande.
  - [ dn ] (ou [ req_distinguished_name ]) : Contient les informations du **Nom Distingué (DN)** à inclure dans le certificat.
  - [ req_ext ] : Définie des **extensions spécifiques** pour la demande de signature de certificat (CSR).
  - [ alt_names ] : Spécifie les **noms alternatifs du sujet (SAN)**, notamment le DNS.



**🔑 Création de la demande de certificat (CSR)**

- Utilisation de la **clé privée** pour générer une demande de certificat auprès du serveur PKI.
- Commande OpenSSL à utiliser : openssl req -new -key domain.key -out domain.csr -config fic.txt
  - -new : Crée une nouvelle demande.
  - -key domain.key : Utilise la clé privée existante.
  - -out domain.csr : Génère le fichier CSR.
  - -config fic.txt : Utilise le fichier de configuration pour définir les options.

**🌐 Processus de génération et de validation du certificat via PKI AD CS**

1.  **Copier la demande** : Copier le contenu du fichier **CSR** généré (ex. : contenu de intra.rouliere.eni.pem).
2.  **Accès au serveur PKI** : Depuis un navigateur, se rendre sur l’URL du serveur PKI (ex. : ).
3.  **Demande de certificat** :

    - **Sélectionner "Demander un certificat" puis choisir "Demande de certificat avancée".**
    - **Coller le contenu du fichier CSR.**
    - **Choisir le modèle "Serveur Web" dans la liste proposée.**

4.  **Téléchargement** : Télécharger le certificat **signé (.cer)** émis par l’AD CS.



**⚙️ Intégration dans Apache**

1.  **Déposer le certificat** :

    - **Copier le certificat signé dans le dossier /etc/ssl.**

2.  **Mise à jour de la configuration Apache** :

    - **Modifier le fichier de configuration du VirtualHost Apache pour mettre à jour les directives :**
      - **SSLCertificateFile : Chemin du certificat signé.**
      - **SSLCertificateKeyFile : Chemin de la clé privée.**

3.  **Redémarrage du serveur Apache** :

    - **Tester la configuration avec apache2ctl configtest.**
    - **Redémarrer Apache pour appliquer les modifications.**

