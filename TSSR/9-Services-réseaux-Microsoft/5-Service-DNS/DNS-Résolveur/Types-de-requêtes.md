# Types de requêtes**🔄 Requêtes récursives : R comme Réponse complète**

Une **requête récursive** demande une **réponse finale, complète** 🧾.

🧠 Astuce mémo : **R = Récursive = Réponse**

💡 Fonctionnement :

Le **client interroge son résolveur DNS** → si le résolveur connaît la réponse (grâce à un cache ou une zone), il **la renvoie directement au client**, par ex. [www.google.com](http://www.google.com) → 172.217.18.196.

🎯 Résultat : le client n'a qu'un seul interlocuteur = **résolveur DNS**



**📍 Requêtes itératives : I comme Indice par indice**

Une **requête itérative** ne donne pas une réponse complète directement, mais **oriente vers le prochain serveur** à contacter 📬.

🧠 Astuce mémo : **I = Itérative = Indice**

💡 Fonctionnement typique :

1.  Le résolveur ne connaît pas la réponse
2.  Il interroge les **serveurs DNS hiérarchiques** :

    - Serveur racine → indique le TLD (.fr)
    - TLD → indique le serveur du domaine (gouv.fr)
    - Domaine → renvoie l’IP de [www.amendes.gouv.fr](http://www.amendes.gouv.fr)

🎯 Résultat : **chaque serveur répond partiellement**, jusqu’à la réponse finale.



**📡 Flux DNS et impact réseau**

⚠️ Les requêtes itératives → **beaucoup d'échanges DNS**, surtout quand le cache est vide.

➡️ Cela **augmente la charge réseau** (mais faut vraiment un gros volume pour saturer).

Pour **éviter la surcharge** :

- ✅ **Cache DNS** : stocke les réponses temporairement
- ✅ **Serveur redirecteur** : fait office de relais intelligent pour réduire les résolutions itératives

💡 Bien **dimensionner son infra DNS** (nombre de serveurs, bande passante, TTL de cache...) = 🔑 pour garder un réseau fluide.
