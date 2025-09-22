# Algorithmes de Hashage en SSL/TLS

Les protocoles **SSL/TLS** garantissent la confidentialité, l'intégrité et l'authenticité des communications. Ils s’appuient sur un ensemble d’algorithmes cryptographiques :

• 🔑 **Chiffrement asymétrique** (échange de clés, signature) • 🧮 **Algorithmes de hachage** (vérification d'intégrité, signature numérique)

• 🔁 **Chiffrement symétrique** (échange de données après authentification)

Les versions récentes comme **TLS 1.3** privilégient des algorithmes modernes comme **ECDSA** ou **SHA-256** pour améliorer la sécurité et la performance.



## 🛡️ **Algorithmes de chiffrement asymétrique**

| **🔢 Algorithme** | **📝 Description** | **🛠️ Utilisation** | **🔐 Taille de clé typique** |
|----|----|----|----|
| 🔓 **RSA (Rivest-Shamir-Adleman)** | Basé sur la factorisation de grands nombres premiers. | Chiffrement des données et signatures numériques. | 2048 à 4096 bits |
| 🖊️ **DSA (Digital Signature Algo.)** | Signature numérique uniquement, conçu par le gov. US. | Authentification et signature de messages. | 1024 à 3072 bits |
| 💡 **ECDSA (Elliptic Curve DSA)** | Variante de DSA utilisant les courbes elliptiques. | Signatures numériques légères et sécurisées. | 256 bits = RSA 3072 bits |
| ⚡ **ED25519** | Variante de courbe elliptique rapide et sécure, performant. | Signatures numériques (notamment SSH, certificats). | 256 bits |

🔁 **Remarques**

- Les courbes elliptiques permettent un niveau de sécurité élevé avec des clés plus petites, donc un gain de performance.
- RSA reste très utilisé, mais tend à être remplacé par des alternatives plus légères comme **ECDSA** ou **ED25519** dans les systèmes modernes.



## 🧬 **Algorithmes de hachage cryptographique**

| **🧪 Algorithme** | **📚 Description** | **🛠️ Utilisation** | **🔒 Taille du hash** |
|----|----|----|----|
| ⚠️ **SHA-1** | Ancien standard (160 bits), vulnérable aux attaques de collision. | Anciennement utilisé dans SSL/TLS, maintenant déconseillé. | 160 bits |
| ✅ **SHA-256** | Partie de la famille SHA-2, sécurisé et largement utilisé. | Certificats SSL/TLS, signatures, intégrité des données. | 256 bits |
| 🔐 **SHA-3** | Nouvelle génération d'algorithmes de hachage (standardisé par NIST). | Applications modernes en cryptographie et blockchain. | Variable (224 à 512 bits) |
| 🚫 **MD5** | Ancien algorithme (128 bits), rapide mais vulnérable aux collisions. | Intégrité de fichiers (désormais obsolète). | 128 bits |

📌 **À retenir sur le hachage**

- Le **hachage** est un **processus unidirectionnel** : impossible de retrouver les données d’origine à partir du hash.

- Deux fichiers différents ne doivent **jamais produire le même hash** (principe de non-collision).

- Les algorithmes comme **SHA-256** ou **SHA-3** sont recommandés aujourd’hui.

- Le terme "hachage" est préféré au terme "hashage", anglicisme peu usité en français technique.



## 📚 **Applications concrètes**

- 🔐 **Certificats SSL/TLS** : ECDSA + SHA-256

- 🔑 **Authentification SSH** : ED25519

- 📁 **Vérification de fichiers** : SHA-256 / SHA-3

- 🧾 **Signatures numériques** : RSA / ECDSA / ED25519 + SHA-2 ou SHA-3

### 📊 **Comparatif rapide sécurité / performance**

| **Algorithme** | **Sécurité actuelle** |     **Performances**      |
|:--------------:|:---------------------:|:-------------------------:|
|      RSA       |  Élevée (si >2048b)  | Plus lent à grande taille |
|     ECDSA      |      Très élevée      |      Léger et rapide      |
|     SHA-1      |       Obsolète        |  Rapide mais vulnérable   |
|    SHA-256     |        Fiable         |       Bon compromis       |
|    ED25519     |      Très fiable      |   Très rapide et léger    |
