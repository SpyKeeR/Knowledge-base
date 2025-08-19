# Redirections resolveur**🔁 Redirection non conditionnelle : le raccourci par défaut**

🧭 Par défaut, un résolveur DNS doit suivre l’arborescence DNS (racine → TLD → domaine).

Mais avec une **redirection non conditionnelle**, on peut **forcer toutes les requêtes** vers un **redirecteur DNS spécifique** (souvent celui du FAI comme Orange, Bouygues...).

💡 Exemple :

Client → Résolveur interne → Redirecteur (DNS FAI) → Réponse IP (ex: google.com → 172.x.x.x) → Résolveur → Client

📌 Utile quand :

- On **ne veut pas surcharger notre propre infra DNS**
- On **bénéficie du cache massif du FAI** pour gagner en rapidité



**🎯 Redirection conditionnelle : ciblée sur un domaine**

🔒 Dans certains cas, on ne veut **rediriger que certaines requêtes** → c’est là qu’intervient la **redirection conditionnelle**.

💡 Principe : pour un domaine spécifique (ex : enigmes.local), le résolveur envoie la requête vers un **redirecteur précis** (ex : DNS d'une filiale, ou d’un domaine interne).

📌 Scénarios typiques :

- Résolution de **zones privées** (non publiques)
- Connexion inter-sites, **VPN, multisites**
- **Environnements hybrides** (cloud + local)

🎯 Permet de **segmenter finement la résolution DNS** sans interférer avec les domaines publics.



**🧠 Cache DNS : gain de perf... et piège parfois !**

📦 Chaque réponse (positive ou négative) peut être **mise en cache** par le résolveur, ce qui :

- ⚡ Accélère les prochaines requêtes similaires
- 🌐 Réduit le trafic réseau DNS
- 🧮 Allège la charge sur les redirecteurs

🕒 Le **TTL (Time To Live)** définit combien de temps une réponse reste en cache.

⛔ Même les **erreurs DNS (ex : domaine introuvable)** sont mises en cache (negative caching), ce qui peut ralentir le dépannage si un domaine devient accessible entre-temps.

🧪 Mécanisme :

1.  Requête reçue → le résolveur consulte **d’abord son cache**
2.  Si présent → renvoi immédiat
3.  Sinon → résolution normale (via redirecteur ou arbo DNS)
