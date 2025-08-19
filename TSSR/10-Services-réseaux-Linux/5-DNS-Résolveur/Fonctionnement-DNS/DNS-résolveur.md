# DNS résolveur**🛠️ Rôle du DNS résolveur complet**

- 🧠 Il **répond aux requêtes des clients** (PC, serveurs, applis)
- 🧭 Il **interroge d'autres serveurs DNS** pour trouver la réponse
- ❌ Il **ne possède aucune info officielle** : pas d'autorité sur les zones, il n’est qu’un intermédiaire intelligent



**🔁 Fonctionnement de la résolution itérative**

Le **résolveur complet** fonctionne avec des requêtes **itératives** (il interroge, reçoit une piste, interroge à nouveau...) :

1.  Serveur racine (ex : « qui gère le .com ? »)
2.  Serveur TLD (ex : « qui gère indiatimes.com ? »)
3.  Serveur faisant autorité (ex : « c’est moi, voici l’@IP de www »)

💡 Il peut **répéter ce processus pour chaque nom de domaine demandé**



**🚀 Les redirecteurs DNS (forwarders)**

Le résolveur peut **déléguer** tout ou partie de son travail à un autre serveur DNS :

- **Redirecteur inconditionnel** : toutes les requêtes passent par lui
- **Redirecteur conditionnel** : seulement pour certains espaces de noms (ex : *.corp ou *.intra)

🧩 Les deux types peuvent coexister et être utilisés ensemble pour optimiser la résolution



**🧠 Mécanisme de cache**

Les réponses reçues sont **mémorisées temporairement** :

- 📥 Chaque réponse est **stockée en cache** pour accélérer les futures requêtes identiques
- ⏳ La **durée (TTL)** est définie par le **serveur faisant autorité** (TTL = Time To Live)

Résultat ? 👉 Moins de requêtes envoyées, plus de rapidité pour les clients 🏎️
