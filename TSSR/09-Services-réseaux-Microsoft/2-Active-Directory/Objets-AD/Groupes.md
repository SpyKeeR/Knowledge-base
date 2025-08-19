# Groupes**🧩 Les groupes AD : objets essentiels**

Dans AD, les **groupes** permettent de gérer les accès et les permissions de façon centralisée.

Chaque groupe a :

- 🔹 un **type** (Sécurité ou Distribution)
- 🔸 une **étendue** (Globale, Domaine local, Universelle, Locale)

**🛡️ Groupes de sécurité vs groupes de distribution**

- 🛡️ **Groupes de sécurité** : ont un **SID**, donc utilisables pour les **droits d’accès (ACL)**
- ✉️ **Groupes de distribution** : sans SID, **uniquement pour la messagerie** (ex : listes de diffusion)

➡️ Pour les permissions, on se concentre **uniquement sur les groupes de sécurité**.



**🌍 Étendues de groupe (Sécurité)**

1.  **Globale (GG)** ➜ contient : utilisateurs / ordis / groupes globaux **du même domaine**
2.  **Domaine local (DL)** ➜ utilisé pour **accorder des droits sur une ressource** du domaine
3.  **Universelle (GU)** ➜ traverse **plusieurs domaines**, plus flexible mais plus gourmande
4.  **Locale (LG)** ➜ uniquement valable sur la **machine locale** (hors domaine AD)

**🧑‍💼 Utilité des groupes globaux**

Les **groupes globaux (GG)** servent à **regrouper des comptes partageant une même fonction** :

Ex : gg_commercial_nantes, gg_support_technique...

Ils peuvent être **membres d'autres groupes globaux**, **mais pas de groupes locaux d'autres domaines**.



**🗂️ Groupes de domaine locaux (DL)**

Les **groupes DL** servent à **attribuer des permissions sur une ressource spécifique**, ex :

- dl_marketing_sur_srvfic_ct = accès complet à un dossier du service marketing

➡️ Ils **accueillent** les **groupes globaux**, puis sont **associés aux ressources via ACL NTFS**.



**🔁 La méthode AGDLP (à connaître par cœur)**

AGDLP =

👤 **A**ccounts (utilisateurs/ordis)

➡️ 👥 **G**roupes **G**lobaux

➡️ 🗂️ **D**omain **L**ocal **G**roups

➡️ 🔒 **P**ermissions (ACL sur ressource)

💡 **Pourquoi ?** C’est la méthode **recommandée par Microsoft** pour garder un contrôle clair, évolutif et sécurisé.
