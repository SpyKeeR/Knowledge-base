# OpenSSL

OpenSSL est une boîte à outils open source pour la gestion des protocoles **SSL** et **TLS**, intégrant une **bibliothèque cryptographique** et une **CLI** puissante.

### 🔧 **Fonctionnalités principales :**

1.  🗝️ Génération de **clés** et **certificats**
2.  🔐 Chiffrement / déchiffrement de fichiers
3.  ✍️ Création et vérification de **signatures numériques**
4.  🧪 Test de **connexions SSL/TLS**
5.  🧮 Calcul de **hachages** et **HMAC**



### 📄 **Exemples de commandes courantes**

| **💻 Commande** | **📌 Description** |
|----|----|
| `openssl genrsa -out private_key.pem 2048` | 🔑 Génère une **clé privée RSA** de 2048 bits |
| `openssl req -new -x509 -key private_key.pem -out certificate.pem -days 365` | 🪪 Génère un **certificat auto-signé** valable 1 an |
| `openssl enc -aes-256-cbc -in fichier.txt -out fichier.enc` | 🔒 Chiffre un fichier avec **AES-256-CBC** |
| `openssl enc -d -aes-256-cbc -in fichier.enc -out fichier_dechiffre.txt` | 🔓 Déchiffre un fichier AES-256 |
| `openssl dgst -sha256 fichier.txt` | 🔁 Calcule le **hachage SHA-256** d’un fichier |
| `openssl x509 -in certificate.pem -text -noout` | 🧾 Affiche les **détails** d’un certificat |
| `openssl req -new -key private_key.pem -out csr.pem` | 📩 Génère une **demande de certificat (CSR)** |
| `openssl s_client -connect example.com:443` | 📡 Teste une **connexion SSL/TLS** |



## 🛠️ **Étapes de création du certificat auto-signé**

### 📁 **1. Préparation du dossier :**

 `mkdir /etc/ssl/intra.rouliere.eni`

### 🔑 **2. Génération de la clé privée :** 

`openssl genrsa -des3 -out intranet.rouliere.eni.key 2048`
- `-des3` : protection par **mot de passe** (optionnel)
- `2048` : longueur de clé par défaut

### 📩 **3. Création de la CSR (demande de signature) :**

`openssl req -new -key intranet.rouliere.eni.key -out intranet.rouliere.eni.request.csr`

### 🔐 **4. Création du certificat auto-signé :**

`openssl x509 -req -days 120 -in intranet.rouliere.eni.request.csr -signkey intranet.rouliere.eni.key -out intranet.rouliere.eni.cert`
- `-days 120` : durée de validité (ici 120 jours, modifiable)

### 🔍 **5. Vérifications :** 
`openssl x509 -noout -in intranet.rouliere.eni.cert -issuer # (ou -dates)`

## 🌐 **Configuration DNS et Apache**

### 📌 **Entrée DNS**
Type **A** ou **CNAME** pointant vers l’IP du serveur.

### ⚙️ **Apache – Activation du module SSL :**
`a2enmod ssl`

### 🔁 **Test de la configuration et restart daemon :**
```bash
apache2ctl configtest
systemctl restart apache2
```

## 📁 **Exemple de configuration d’un VirtualHost SSL Apache**

```bash
<VirtualHost *:443>  
ServerName intra.rouliere.eni  
ServerAdmin admin@rouliere.eni  
DocumentRoot /var/www/intra  
SSLEngine on  
SSLCertificateFile /etc/ssl/intra.rouliere.eni/intranet.rouliere.eni.cert  
SSLCertificateKeyFile /etc/ssl/intra.rouliere.eni/intranet.rouliere.eni.key
SSLProtocol all -SSLv3 # désactive SSLv3 obsolète
</VirtualHost>
```