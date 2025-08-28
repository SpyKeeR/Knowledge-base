# **🌳 Qu’est-ce qu’une forêt Active Directory ?**

La **forêt (Forest)** est le **socle de toute l'infrastructure Active Directory**.

C’est **le tout premier élément créé** lors de l’installation d’AD → elle contient au minimum **un domaine racine** (Root Domain), qui sert de point central.

📌 Elle définit une **limite de sécurité, de configuration et de confiance**.

🧠 C’est dans cette forêt que vont vivre tous les domaines, les relations d'approbation, et le schéma commun.



## **🧬 Domaine racine & domaines enfants**

Un domaine est une unité logique d’AD. Le premier = **le domaine racine**.

Ensuite, on peut y ajouter des **domaines enfants**, hiérarchisés et nommés comme suit :

👉 Ex. conception.entreprise.local = enfant du domaine racine entreprise.local.

➡️ Chaque domaine possède ses **propres utilisateurs, groupes et politiques**, mais reste intégré à la forêt.



## **🔗 Relations d’approbation (Trusts)**

Entre les domaines d’une même forêt, les **relations d'approbation sont transitives et bidirectionnelles** :

✔️ Un utilisateur du domaine A peut accéder à des ressources du domaine B (selon les autorisations).

📥 Cela facilite le **partage inter-domaine** tout en gardant une **structure cloisonnée** si besoin.



### **🧠 Schéma unique partagé**

Tous les domaines d’une forêt partagent le **même schéma Active Directory** :

📘 Le schéma, c’est la "base de règles" : il définit les types d’objets et d’attributs utilisables (ex. : utilisateur, groupe, mot de passe max...).

💡 Cela garantit une **cohérence globale** : pas de conflit entre les objets, même répartis sur plusieurs domaines.

