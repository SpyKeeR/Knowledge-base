# **🏠 DNS hébergeur : le gardien d’un espace de noms**

🔐 Contrairement au **DNS resolver**, qui va chercher l’info, le **DNS hébergeur (aussi appelé "autoritaire")** détient **les infos officielles** sur un ou plusieurs domaines.

Il est responsable de **répondre aux requêtes** concernant les noms qu’il gère. Et s’il ne gère pas le domaine demandé, il **ne répond pas**.

📌 Il agit donc comme une **source d’autorité**, ce qui implique une **base de données locale bien à jour**.



## **📚 Rôle du DNS hébergeur : l’autorité qui a la réponse**

🧠 On peut résumer sa logique par :

**"Je connais ce domaine, je t'envoie l’info directe."**

Il peut fournir :

- Adresses IP (type A / AAAA)
- Alias (CNAME)
- Serveurs mail (MX)
- Enregistrements TXT, SRV, etc.

🔄 Il ne cherche **jamais** ailleurs : **il ne fait pas de résolution récursive**.



## **🧩 Gestion des hôtes : zoom sur les sous-domaines**

📦 Le DNS hébergeur gère les **enregistrements précis liés aux hôtes** :

ex : www.example.com, mail.example.com, etc.

📌 Chaque sous-domaine = **ligne spécifique** dans la zone DNS.

Lorsqu'une requête tombe sur le DNS hébergeur pour un hôte qu’il gère :

✅ Il répond avec l’info exacte (IP, CNAME, etc.)

⛔ Sinon, il n’envoie rien.



## **🔎 Exemple d’enregistrement type**

📍 example.com. IN A 192.0.2.1

→ Ce type de ligne est appelé **enregistrement de ressource (Resource Record)**. Ici :

- **Nom** : example.com
- **Type** : A (IPv4)
- **Valeur** : 192.0.2.1

💡 Ces enregistrements forment le **contenu de la zone DNS** gérée par le serveur hébergeur.

