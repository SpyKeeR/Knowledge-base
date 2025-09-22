# Certificats SSL/TLS

Un **certificat** est une sorte de **carte d’identité numérique sécurisée**, qui sert à **authentifier un serveur** et à **garantir la sécurité des connexions HTTPS**.

📌 Il contient :

- 🔢 Un **numéro de série**
- 🏛️ Le nom de l’**autorité de certification (CA)** émettrice
- ⏳ Une **période de validité**
- 👤 Le **nom du titulaire** de la clé publique
- 🔑 La **clé publique**
- 🔑 L’**algorithme de chiffrement**
- ✍️ L’**algorithme de signature** (signature numérique)



## 🛡️ **🌍 Utilité d’un certificat SSL/TLS**

- 📬 Permet de **chiffrer** les échanges entre le navigateur et le serveur (HTTPS)
- 🔐 **Identifie avec certitude** le serveur contacté
- 🔒 Affiche le **cadenas dans la barre d’adresse** du navigateur
- 🧘‍♂️ Permet de **naviguer en toute confiance**

⚠️ Un site en HTTPS **doit obligatoirement utiliser un certificat SSL/TLS** valide.



## 🔗 **🏛️ La chaîne de certification (PKI)**

Un certificat repose sur une **chaîne de confiance** impliquant 3 acteurs :

1.  👨‍💻 **Le client** (navigateur)  
    → Demande l’accès à des ressources sécurisées

2.  🌐 **Le serveur Web  **
    → Fournit les ressources + un certificat numérique

3.  ✅ **L’autorité de certification (CA)  **
    → A émis ce certificat et permet sa validation  
    → Le client peut alors **vérifier et accepter** le certificat

✅ Si le certificat est reconnu et valide, la **connexion HTTPS est sécurisée**.



## 📜 **🔧 Obtenir un certificat : 3 méthodes possibles**

1.  🧪 **Certificat auto-signé** 
    - ✔️ Gratuit 
    - ❌ Pas reconnu par défaut (alerte navigateur)
2.  🏢 **Certificat d’une CA interne** 
    - ✔️ Utilisé en entreprise 
    - ❌ Nécessite une infra interne
3.  🌐 **Certificat d’une CA publique** 
    - ✔️ Reconnue par tous les navigateurs 
    - 💰 Payant (ou gratuit via Let’s Encrypt)

💡 Le choix dépend du **contexte** (test, intranet, production publique…) et du **niveau de confiance souhaité**.

Le processus implique :
- la **génération d'une clé privée**, 
- la **création d'une demande de signature** (CSR),
- puis l'**obtention du certificat final**
    - que ce soit par **auto-signature** 
    - ou via une **autorité externe**.
