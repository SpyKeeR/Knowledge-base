# Scénarios/Types déploiement🧠 **Préparation avant installation**

Avant toute installation du service RDS, il est indispensable de bien comprendre :

• Les **types de déploiement**

• Les **scénarios de déploiement**

• Les **services de rôle RDS** à affecter à chaque serveur

Une planification rigoureuse garantit une architecture adaptée aux besoins de l'organisation.



🚀 **Types de déploiement**

1️⃣ **Déploiement standard**

📌 Utilisé en **production**

🧱 Permet de **répartir les rôles RDS** (broker, gateway, host...) sur **plusieurs serveurs**

🎯 Offre une **meilleure performance** et une **meilleure évolutivité**

2️⃣ **Déploiement rapide (Quick Start)**

🔧 Utilisé pour les **tests** ou la **formation**

📦 Tous les services RDS sont installés sur **un seul serveur**

🕒 Simplifie et accélère le processus d’installation



🖥️ **Scénarios de déploiement**

🪟 **Bureaux basés sur une session**

🎯 Utilisation de **bureaux publiés** ou d’**applications déportées**

👥 Plusieurs utilisateurs se connectent à des **sessions distinctes** sur le même serveur

💼 Recommandé pour un usage classique avec peu de besoins en personnalisation machine

🖥️ **Bureaux basés sur des ordinateurs virtuels (VDI)**

🧰 Fournit des **machines virtuelles complètes** à chaque utilisateur

💻 Permet une **personnalisation totale** de l’environnement utilisateur

⚠️ Requiert une **infrastructure de virtualisation complexe** (non abordée dans ce module)



📌 **Conclusion**

Le choix du **type** et du **scénario** de déploiement RDS dépend :

- Du **contexte d’usage** (test, production)
- Des **ressources disponibles**
- Du **niveau de personnalisation** attendu par utilisateur  
  🎯 Une bonne planification permet un déploiement efficace et durable.
