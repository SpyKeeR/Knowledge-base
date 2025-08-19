# L'authentification / 2FA- Méthodes de création efficaces :
  - 🗣️ **Méthode phonétique** :  
    *« J’ai acheté huit CD pour cent euros cet après-midi » → ght8CD%E7am*

  - 🔡 **Méthode des premières lettres** :  
    *« Un tien vaut mieux que deux tu l’auras » → 1tvmQ2tl’A*

L'authentification / 2FA

mercredi 23 juillet 2025

08:51

La sécurité des comptes dépend largement de la gestion des mots de passe :

- Utiliser un **mot de passe unique** pour chaque service, surtout entre vie pro/perso.
- Éviter les **informations personnelles** (nom, date de naissance, etc.).
- **Changer systématiquement** les mots de passe par défaut à la première utilisation.
- Renouveler les mots de passe tous les **90 jours** pour les services sensibles.
- Ne jamais :
  - stocker les mots de passe dans un fichier non sécurisé 📄
  - les écrire sur papier 📝
  - se les envoyer par mail 📧
  - les sauvegarder dans un navigateur 🌐



🧰 **Outils de gestion de mots de passe**

- Privilégier un **gestionnaire de mots de passe** sécurisé (type Bitwarden, KeePass...).
- Éviter la sauvegarde automatique par les navigateurs.
- Sensibiliser les utilisateurs à ne pas réutiliser les mots de passe professionnels ailleurs.



🔒 **Authentification Multifacteur (2FA)**

- Combine plusieurs preuves d'identité :
  - 🔑 Mot de passe
  - 📲 Code OTP (SMS / App)
  - 📞 Appel vocal
  - 👆 Notification push
  - 🧬 Données biométriques
- ⚙️ Activée **par défaut** depuis **février 2021** dans Microsoft 365.
- Gérée via **Azure Active Directory**.
- Renforcer l’inscription des utilisateurs à 2FA est crucial pour la sécurité globale.



💻 **Administration via PowerShell (Azure AD / M365 - Microsoft Graph)**

**✅ Définir un mot de passe (et forcer le changement au prochain login) :**

**🚫 Désactiver l’expiration du mot de passe :**

Ce paramètre ne s’applique **que pour les comptes cloud-only** et dans les **tenants sans politique de mot de passe par défaut (password policies)**.

Update-MgUser -UserId "user@domaine.com" -PasswordPolicies "DisablePasswordExpiration"

Pour **remettre l’expiration** : Update-MgUser -UserId "user@domaine.com" -PasswordPolicies "None"



**📆 Définir la durée de validité globale des mots de passe (tenant-wide) :**

⚠️ Cette fonctionnalité a été dépréciée dans Microsoft Graph. Microsoft recommande d'utiliser des **politiques de sécurité conditionnelle** ou des **stratégies d’authentification** (via Entra ID → Password Protection et Authentication Policies).

🧭 **Recommandations de l’ANSSI/Microsoft**

- Longueur minimale : **8 caractères**
- Éviter d'imposer des règles de composition trop contraignantes (*&@... inutiles).
- Ne pas forcer une **réinitialisation périodique** sauf incident.
- **Interdire les mots de passe communs** via liste noire.



📱 **Application Microsoft Authenticator**

- Permet l’authentification multifacteur via smartphone.
- Service gratuit inclus avec Microsoft 365.
- Fonctionne avec **Push**, **codes temporaires** (OTP), ou validation biométrique.
