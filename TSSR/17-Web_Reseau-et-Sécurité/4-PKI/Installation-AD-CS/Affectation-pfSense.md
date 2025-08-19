# Affectation pfSense- ✅ DNS interne configuré avec l’enregistrement de l’AD.
- ✅ pfSense utilise **le DNS interne** permettant la résolution AD.



**🔐 Création et enregistrement d’une demande de certificat**

1.  Aller dans **System > Cert. Manager > Certificates**.
2.  Cliquer sur **Add/Sign** > **Create a Certificate Signing Request**.
3.  Remplir les champs :

    - Common Name : FQDN de pfSense (ex. pfsense.rouliere.eni)
    - Type : **Server Certificate**

4.  Générer la **demande de signature (CSR)**.
5.  Copier le **contenu du CSR** généré.



**🌐 Signature via l’interface PKI (certsrv)**

1.  Depuis un poste client, accéder au serveur PKI :

    - URL :

2.  Demander un certificat :

    - **Demande avancée**
    - Coller le **contenu du CSR**
    - Choisir le **modèle "Serveur Web"**

3.  Générer le certificat signé et **copier son contenu** (format Base-64).



**📎 Import et activation du certificat sur pfSense**

1.  Retourner dans **System > Cert. Manager > Certificates**.
2.  Modifier le certificat correspondant à la CSR.
3.  Coller le **certificat signé** dans le champ prévu.
4.  Sauvegarder.



**🖥️ Application au WebConfigurator de pfSense**

1.  Aller dans **System > Advanced > Admin Access**.
2.  Dans la section **SSL/TLS Certificate**, sélectionner le certificat nouvellement importé.
3.  Sauvegarder.
4.  Recharger la page WebGUI (le certificat HTTPS doit désormais être **signé et reconnu** si l’AC est dans les autorités du navigateur).

lundi 28 juillet 2025

15:00

**📥 Récupération et préparation du certificat**

- Récupérer le **certificat signé** depuis le serveur PKI Windows.
- L’ouvrir avec **le Bloc-notes**.
- Exporter le certificat en **X.509 Base-64 (.CER)**.
- Copier **tout le contenu** du certificat.



**🔓 Ouverture du port sécurisé LDAP (LDAPS)**

- Créer une **règle entrante sur le pare-feu** PKI pour autoriser **636** (LDAPS).
- Protocole : TCP
- Source : pfSense ou réseau interne
- Destination : serveur Active Directory
- Port : 636



**🏛️ Import du certificat d’autorité sur pfSense**

1.  Aller dans **System > Cert. Manager > CAs**.
2.  Cliquer sur **Add**.
3.  Renseigner :

    - Nom : ex. CA-ADCS
    - Méthode : **Import an existing Certificate Authority**
    - Champ "Certificate data" : coller le contenu du certificat racine.

4.  Sauvegarder pour **activer la CA** sur pfSense.



**👤 Configuration de l’authentification LDAP AD**

1.  Aller dans **System > User Manager > Authentication Servers**.
2.  Ajouter un nouveau serveur :

    - Hostname/IP : IP de l’AD
    - Port : **636** (LDAPS)
    - Transport : **SSL - Encrypted**
    - Base DN : conteneur LDAP à interroger (ex. DC=rouliere,DC=eni)
    - Authentication containers : chemin LDAP vers les utilisateurs autorisés
    - Bind Credentials :
      - Un compte de service **AD avec droits de lecture**
      - Format utilisateur : user@domaine.local

3.  Tester la connectivité :

    - Via **Diagnostics > Command Prompt** :

openssl s_client -showcerts -connect AD_server:636

- Vérifier que la chaîne de certificats s’affiche sans erreur.

4.  Tester l’authentification : Via **Diagnostics > Authentication**.
