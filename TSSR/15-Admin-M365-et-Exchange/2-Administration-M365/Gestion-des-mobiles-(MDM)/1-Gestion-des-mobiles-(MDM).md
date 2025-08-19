# Gestion des mobiles (MDM)La **gestion des appareils mobiles** (MDM) permet à l’entreprise de **contrôler, sécuriser et administrer** smartphones, tablettes et autres terminaux mobiles via une console **EMM (Enterprise Mobility Management)**. Elle assure une **protection des données**, même lorsque les terminaux sont utilisés en dehors du réseau d’entreprise.



🏢 **Acteurs principaux du marché**

- **Microsoft Intune** (intégré à Microsoft 365)
- **VMware AirWatch**
- **Ivanti**
- **MobileIron** (compatible avec Microsoft 365 via connecteurs)
- **BlackBerry** (précurseur, aujourd’hui marginalisé)



📊 **Chiffres clés du secteur**

- Le marché du MDM représentait **16,29 milliards de dollars en 2020**.



🔐 **Fonctionnalités clés d’un MDM**

- **Gestion des applications** : autorisation ou blocage (ex : réseaux sociaux).
- **Contrôle réseau** : restrictions selon le type de réseau utilisé.
- **Protection des données** : règles sur les types de données autorisées.
- **Sécurité** : politiques de mot de passe, verrouillage, effacement à distance, chiffrement.



📦 **Modèles de gestion des terminaux**

| **🔄 Modèle** | **📝 Description** |
|----|----|
| **BYOD** (Bring Your Own Device) | L’utilisateur utilise son matériel personnel pour le travail. 💰 Réduction des coûts mais pose des problèmes de sécurité. |
| **CYOD** (Choose Your Own Device) | L’utilisateur choisit un appareil parmi une **liste définie par l’entreprise**. 🤝 Compromis entre liberté et homogénéité. |
| **COPE** (Corporate Owned Personally Enabled) | Matériel fourni par l’entreprise, mais **utilisable à des fins perso.** 🔐 Contrôle par l’entreprise + flexibilité pour l’user. |



📋 **Exemples de stratégies configurables**

🛡️ **Sécurité** :

- 🔒 Empêcher les **mots de passe simples**
- 🔢 Longueur minimale du mot de passe
- ❌ Nombre maximal d’échecs avant effacement automatique
- ⏱️ Temps d’inactivité avant verrouillage
- 🗓️ Expiration du mot de passe + historique
- 🔐 **Exiger le chiffrement de l’appareil**

📡 **Enregistrements DNS requis** :

| **Type** | **Hôte**               | **Adresse cible**                         |
|----------|------------------------|-------------------------------------------|
| CNAME    | EnterpriseEnrollment   | EnterpriseEnrollment.manage.microsoft.com |
| CNAME    | EnterpriseRegistration | EnterpriseRegistration.windows.net        |



📵 **Restrictions** :

- 🖼️ Bloquer la synchronisation des **photos**
- 📸 Bloquer la **capture d’écran**
- 🛍️ Gérer l’accès au **magasin d’applications**
- 💾 Bloquer le **stockage amovible**

🎯 **Application des stratégies** :

- Les stratégies peuvent être **appliquées à des groupes de sécurité**
