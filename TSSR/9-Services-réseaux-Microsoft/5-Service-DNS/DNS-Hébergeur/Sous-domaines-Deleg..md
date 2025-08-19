# Sous-domaines / Deleg.🧠 *Utilité* : Permet de **structurer logiquement** les services, sites, ou départements → meilleur **pilotage** et **clarté**.



**🏗️ Fonctionnement des sous-domaines**

Un sous-domaine, c’est juste un **niveau hiérarchique en dessous** du domaine principal.

👉 Ex : srv1.nantes.mondomaine.local = 10.0.0.1

➡️ Ici, nantes est un sous-domaine de mondomaine.local, et srv1 est un hôte dans ce sous-domaine.

📌 Le **serveur DNS parent** (celui de mondomaine.local) peut :

- Gérer les sous-domaines **directement**
- Ou les **déléguer** à d’autres serveurs (ce qu’on va voir juste après)



**🎯 Délégation de sous-domaines**

Quand on parle **d’autonomie**, on parle **délégation DNS**.

💡 *But* : transférer la **gestion d’un sous-domaine** (ex : nantes.mondomaine.local) à un autre **serveur DNS dédié**.

🛠️ Le **serveur parent** :

- Crée une **délégation** (enregistrement NS)
- Indique quel **serveur est autoritaire** pour le sous-domaine

📍 Chaque site (ex : Nantes, Rennes...) peut donc avoir :

- Son **propre serveur DNS**
- Sa **zone propre** (nantes.mondomaine.local)
- Une **autonomie complète** dans la gestion locale



**🖥️ Configuration des serveurs DNS**

👑 Serveur principal : Gère **mondomaine.local**

- Délègue à chaque site via enregistrements **NS**

🧩 Serveur de site (ex : DNS de Nantes) : Gère la zone **nantes.mondomaine.local**

- Répond aux requêtes DNS locales
- Se synchronise avec le reste de l’architecture si besoin



**🔍 Résolution des requêtes**

Quand un client demande une IP (ex : srv1.nantes.mondomaine.local) :

1.  Le DNS principal est interrogé
2.  Il redirige vers le serveur DNS de Nantes
3.  Celui-ci répond avec l’IP correspondante

⚠️ La **zone parent** ne fait plus autorité pour le sous-domaine → elle **redirige** seulement vers le serveur dédié
