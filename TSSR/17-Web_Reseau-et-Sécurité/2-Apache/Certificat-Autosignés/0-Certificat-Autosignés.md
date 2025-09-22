# Certificat Autosignés

## **1️⃣ Création de l’arborescence de stockage des certificats 📁**

- Structure simple à créer manuellement : 
```bash
  /etc/ssl/  
          ├── certs # Autorités de certification connues  
          ├── certs-auto # Certificats auto-signés  
          ├── private # Clés privées  
          └── reqs # Demandes de signature de certificats
```



## **2️⃣ Création d’une clé privée 🔐**

- Commande OpenSSL : `openssl genrsa -des3 -out /etc/ssl/private/www.tssr.lcl.key 2048`
- Explications :
  - `genrsa` : génère une clé RSA (algorithme RSA = Rivest, Shamir, Adleman)
  - `-des3` : protège la clé avec une phrase de passe (très conseillé !)
  - `-out` : où et comment nommer ta clé privée
  - `2048` : longueur de la clé (2048 bits = minimum recommandé)



## **3️⃣ Création d’une demande de signature de certificat (CSR) ✉️**

- Commande : `openssl req -new -key /etc/ssl/private/www.tssr.lcl.key -out /etc/ssl/reqs/www.tssr.lcl.request.csr`
- Ce que tu remplis :
  - Pays, état, ville, organisation, unité, nom commun (le FQDN ou URL du site)
  - Adresse email et autres infos facultatives
- Important : le **Common Name** doit correspondre au nom de domaine utilisé (ex: [www.tssr.lcl](http://www.tssr.lcl))



## **4️⃣ Création du certificat autosigné 📝**

- Commande : `openssl x509 -req -days 90 -in /etc/ssl/reqs/www.tssr.lcl.request.csr -signkey /etc/ssl/private/www.tssr.lcl.key -out /etc/ssl/certs-auto/www.tssr.lcl.cert`
- Explications :
  - `x509` : commande pour gérer les certificats (autosignés ou CA)
  - `-req` : indique qu'on part d'une demande (CSR)
  - `-days 90` : durée de validité (ici 90 jours, modifiable)
  - `-in` : fichier CSR
  - `-signkey` : clé privée utilisée pour signer
  - `-out` : où sera le certificat créé
- Si ta clé privée est protégée par phrase de passe, elle te sera demandée.



## **5️⃣ Modification du fichier de configuration Apache ⚙️**

- Pour que ton site utilise ce certificat, il faut modifier la config Apache, par exemple dans un fichier default-ssl.conf ou celui de ton VirtualHost :  
  `SSLCertificateFile /etc/ssl/certs-auto/www.tssr.lcl.cert`
  `SSLCertificateKeyFile /etc/ssl/private/www.tssr.lcl.key`

- Ensuite, tu redémarres Apache pour appliquer les changements.
