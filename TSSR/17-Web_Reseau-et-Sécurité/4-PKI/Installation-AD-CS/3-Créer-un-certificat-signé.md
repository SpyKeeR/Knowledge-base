# Créer un certificat signé

## **1. 🎯 Créer un modèle de certificat sur l’AD CS**

À faire une seule fois si le modèle n’existe pas

- Sur la **CA (Autorité de Certification)** : `certtmpl.msc`
- Dupliquer le modèle **Web Server** → nomme-le WebCert_TSSR (ou ce que tu veux).
- Onglet **General** : Donne un nom clair.
- Onglet **Request Handling** : ➕ Cocher “Allow private key to be exported” si tu veux pouvoir exporter en .pfx.
- Onglet **Subject Name** : ✅ Cocher “Supply in the request” (tu rempliras le FQDN plus tard).
- Onglet **Security** : Autoriser les groupes/machines à **Enroll** (Inscription).
- Enregistre.
- 

## **2. ➕ Publier le modèle dans l’AD CS**

- Sur la **CA** : `certsrv.msc`
- Clic droit sur “Certificate Templates” → **New > Certificate Template to Issue**
- Choisir ton modèle (WebCert_TSSR)



## **3. 🧾 Faire la demande de certificat depuis le client**

- Sur le serveur cible : `certlm.msc` (certificats de l’ordinateur local)
- Aller dans **Personnel > Certificates**
- Clic droit > **All Tasks > Request New Certificate**
- Suivre l’assistant :
  - **Sélectionner ton modèle** (WebCert_TSSR)
  - Renseigner le **Nom commun (CN)** : ex. [www.monserveur.local](http://www.monserveur.local)
  - Ajouter les **SAN (Subject Alternative Names)** si nécessaires [DNS.1] (ex: monserveur, monserveur.domain.local)



## **4. 📦 Exporter le certificat en .pfx avec la clé privée**

- Toujours dans `certlm.msc`, clic droit sur le certificat > **All Tasks > Export**
- Choisir **Yes, export the private key**
- Format : .pfx avec mot de passe
- Sauvegarder le fichier dans un emplacement sûr



## **5. 🔧 Appliquer le certificat (ex. sur IIS)**

- Ouvre **IIS Manager**
- Va sur ton site > **Bindings**
- Modifier le **binding HTTPS** :
  - Sélectionne le nouveau certificat importé
- Redémarre le site si besoin
