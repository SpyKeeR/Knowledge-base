# Restrictions d'accès MDT

🧭 **Vue d’ensemble**

- 🎯 Restreindre l’accès au partage MDT permet de **sécuriser les ressources critiques**
- 🔒 Contrôle des utilisateurs ou groupes ayant accès aux déploiements
- 🧑‍💼 Pratique courante dans les environnements d’entreprise pour éviter les accès non autorisés



## 👤 **Types de comptes utilisés**

- 👨‍💻 **Compte utilisateur standard** : suffisant pour un usage simple
- 🛠️ **Compte de service** (recommandé) : utilisé pour les déploiements automatisés
- 🌐 **Compte de domaine** :
  - 🔄 Permet l’intégration automatique des machines au domaine
  - ✅ Offre plus de **flexibilité** pour les déploiements centralisés



## 🛠️ **Contrôle d’accès : deux niveaux complémentaires**

1.  📁 **Partage réseau**

    - Configuration via l’onglet **"Sharing"**
    - Gère qui peut se connecter au **partage lui-même**

2.  📄 **Autorisations NTFS**

    - Permet de **filtrer plus finement** les droits sur les fichiers et dossiers
    - ⚠️ Indispensable pour garantir la **cohérence de la sécurité** sur le volume local

## 🧠 **À retenir**

- Restreindre l’accès au partage MDT = **mesure de sécurité incontournable**
- L’utilisation de comptes de service et de domaine permet une **gestion centralisée efficace**
- Combiner les droits **de partage** et **NTFS** assure une **protection granulaire** des ressources

🛡️ Un bon déploiement commence par un bon verrouillage des accès.
