# DNS résolveur**🧭 Le rôle du DNS résolveur : l’intermédiaire malin**

Le **DNS résolveur** (ou récursif) est **le point de contact direct entre un poste client et l’univers DNS**.

Exemple 🧑‍💻 :

Toto tape intra.entreprise.local → la requête part vers le **résolveur DNS** → il interroge les bons serveurs **jusqu’à obtenir l’IP correspondante**, puis la retourne à Toto.

Il **ne stocke pas les enregistrements DNS** → il **les cherche pour toi**.



**🔁 Interaction client ↔️ résolveur**

💬 Le client (comme Toto) envoie une requête DNS → le **résolveur prend le relais**.

🎯 Son rôle :

- Recevoir la requête
- **Identifier** si l’adresse est **interne ou externe**
- **Interroger d'autres serveurs DNS** si besoin
- **Retourner l’IP correspondante** au client

💡 Résultat : les utilisateurs accèdent aux services **sans se soucier des IP**.



**🧠 Fonction principale : résolution de noms**

Le DNS résolveur sert à :

- Traduire **FQDN → IP**
- Communiquer avec les **serveurs autoritaires** (ceux qui ont les zones)
- Optimiser les requêtes avec un **cache DNS** (⚠️ pas toujours activé)

C’est le **"GPS du réseau"**, il connaît **le chemin vers les bons serveurs**.



**❗Ce que le résolveur *ne fait pas***

🚫 Il ne gère **aucune zone DNS** (pas d’espace de noms à lui)

🚫 Il ne stocke **aucun enregistrement officiel**

✅ Il **délègue** la recherche aux bons serveurs (racine → TLD → domaine...)

Il agit donc **comme un proxy DNS**, sans jamais détenir la vérité, juste en la recherchant 🔍
