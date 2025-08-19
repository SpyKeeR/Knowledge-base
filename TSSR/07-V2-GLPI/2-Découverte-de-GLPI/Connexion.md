# Connexion🔐 **🌍 Accéder à l’interface Web de GLPI**

GLPI est une application web, donc **tout commence dans un navigateur** !

Dans le contexte de ton cours, le portail GLPI est accessible ici : 📎 **URL :** <http://glpi.helptech.bzh>

Cette adresse pointe vers **l’interface de connexion**, hébergée sur le serveur de l’organisation.



🖼️ **💡 Page de connexion – Ce que tu vas voir**

Dès que tu accèdes à l’URL :

- 🟨 Tu verras le **logo officiel de GLPI**
- 👤 Un **formulaire de connexion** avec plusieurs champs :
  - **Identifiant** : ça peut être un **compte interne** (créé dans GLPI) ou un **compte AD** (Active Directory, si l’authentification est déléguée à un serveur Windows)
  - **Mot de passe** : celui associé au compte
  - **Source de connexion** : il faut indiquer si tu te connectes à la *base interne de GLPI* ou à une *source externe* (ex : LDAP ou AD) comme Helptech.bzh
  - ✅ **Se souvenir de moi** : si tu coches cette option, ton navigateur gardera tes infos d’authentification pendant un certain temps (comme un cookie de session prolongé)



🔄 **🔐 Système d’authentification – Ce qu’il faut comprendre**

GLPI peut **gérer ses propres utilisateurs**, mais aussi **s’appuyer sur une base externe** comme :

- ✅ **LDAP/Active Directory** : très utilisé en entreprise pour que les utilisateurs se connectent avec leur identifiant Windows habituel
- 🔒 **Base interne de GLPI** : utile pour des comptes locaux (admin technique, comptes test, utilisateurs externes…)

💡 *Contexte utile :*

Dans une vraie boîte, les techniciens s’identifient souvent via Active Directory.

Par exemple, "jdupont" pourra se connecter sur GLPI **avec les mêmes identifiants que sur son PC Windows pro**, et ça c’est top pour la simplicité et la sécurité 🔐



📋 **Résumé express**

| 🌍 Adresse d'accès | <http://glpi.helptech.bzh> |
|----|----|
| 🧾 Champs de login | Identifiant + mot de passe |
| 📂 Sources possibles | Interne (GLPI) ou Active Directory |
| 🔁 Session persistante | "Se souvenir de moi" = connexion gardée sur plusieurs jours |



🎓 **Petit conseil pour la pratique :**

➡️ *Si tu es admin GLPI*, pense toujours à tester les **2 types de comptes** :

- Un **compte interne** (admin@local)
- Un **compte AD**, pour vérifier que la **synchronisation LDAP** fonctionne bien 👍
