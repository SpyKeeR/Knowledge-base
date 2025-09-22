# Portail captif

Un **portail captif** est une interface web s’affichant automatiquement lorsqu’un utilisateur tente de se connecter à Internet via un réseau public ou contrôlé.

Il impose une interaction (authentification, validation, acceptation des CGU, etc.) avant d'autoriser la navigation complète.


## 🖥️ **Éléments typiques d’un portail captif**

- 📛 **Logo** de l’établissement ou de la marque
- 📃 **Conditions générales d’utilisation** (CGU) à accepter
- 📢 **Publicités** ou messages promotionnels
- 🧾 **Informations réseau** : règles d’usage, sécurité, politique de confidentialité, etc.
- 🔐 **Formulaire d’authentification** : nom d’utilisateur, mot de passe, code d’accès, etc.
- ⏳ **Limites d’utilisation** : durée de la session, volume de données autorisé, etc.
- 📞 **Support technique** : contact en cas de problème de connexion

## 🔄 **Fonctionnement du portail captif**

### 1️⃣ **Connexion au réseau Wi-Fi** (souvent ouvert ou avec mot de passe basique)
### 2️⃣ **Redirection automatique** vers la page du portail captif (via interception DNS ou HTTP)
### 3️⃣ **Interaction obligatoire** avec le portail (connexion, saisie de données, acceptation des CGU…)
### 4️⃣ ✅ **Validation** → L’accès au réseau est alors autorisé et les requêtes passent normalement

⚙️ Cela repose généralement sur des règles dans un firewall ou un routeur qui bloquent le trafic tant que l’utilisateur n’est pas authentifié (utilisation d’ACL ou règles NAT/Firewall).


## 🏨 **Usages courants du portail captif**

- 🏨 **Hôtels** : Authentification avec nom/chambre ou code fourni, accès limité dans le temps
- ☕ **Cafés/Restaurants** : Accès libre après acceptation de conditions ou affichage de publicité
- 🎓 **Établissements scolaires** : Connexion sécurisée via identifiants (LDAP/Active Directory)
- 🏢 **Entreprises** : Pour **les invités ou prestataires externes**, le portail captif permet un contrôle d’accès temporaire. Pour les employés, on privilégiera plutôt l’usage de **proxies filtrants** ou de **solutions ZTNA**.
- 🏛️ **Espaces publics** (bibliothèques, mairies, etc.) : Accès gratuit avec charte d’utilisation à accepter


## 🔐 **Avantages sécurité et conformité**

- Limite l’accès aux personnes identifiées
- Permet de **tracer les connexions** et garder des **logs** (obligations légales en France)
- Encourage l’utilisateur à **accepter une charte d’utilisation** ou politique de sécurité
- Peut **bloquer l'accès** aux appareils non autorisés ou non conformes



## 🧰 **Exemples de solutions portail captif**

- pfSense avec le package **Captive Portal**
- Cisco Meraki, Ubiquiti UniFi
- Solutions cloud ou intégrées à des hotspots Wi-Fi (Orange, Aruba, etc.)
- Portails captifs personnalisés avec **authentification RADIUS**, LDAP ou base de données locale