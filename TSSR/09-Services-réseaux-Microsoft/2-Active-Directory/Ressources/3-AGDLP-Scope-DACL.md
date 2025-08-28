# **🧠 AGDLP, c’est quoi exactement ?**

AGDLP = **A**ccount > **G**lobal Group > **D**omain **L**ocal Group > **P**ermission

👉 C’est une **stratégie d'imbrication des groupes Active Directory**, recommandée par Microsoft, pour **structurer les autorisations NTFS** de manière propre et scalable.



### **👤 Étapes de fonctionnement**

1.  **A (Account)** : l’utilisateur est créé dans l’AD.
2.  **G (Global Group)** : il est intégré dans un **groupe global** (ex : GG_Marketing).
3.  **DL (Domain Local Group)** : ce groupe global est placé dans un **groupe de domaine local** (ex : DL_Marketing_Lecture).
4.  **P (Permission)** : c’est ce **DL** qui reçoit la permission NTFS (lecture, modification, etc.).



## **🏷️ Types de groupes DL à prévoir**

Par ressource partagée, on crée **4 groupes DL distincts** :

- DL_nomressource_CT → contrôle total
- DL_nomressource_Mod → modification
- DL_nomressource_Lec → lecture
- DL_nomressource_Refus → refus d’accès

📌 On applique **les droits NTFS** uniquement sur ces **groupes DL**.



## **⚙️ Avantages de l'AGDLP**

✅ **Centralisation** des permissions

✅ **Modularité** : on change les droits au niveau du DL, pas utilisateur par utilisateur

✅ **Gain de temps** pour l’admin (ex : changement de service, nouveaux arrivants)

✅ **Cohérence** dans toute l’entreprise (naming convention, documentation, etc.)



## **🧪 Exemple pratique**

👩‍💼 L'utilisateur Jeanne rejoint le service marketing.

🔁 On l’ajoute dans GG_Marketing

🔁 Ce groupe est déjà dans DL_Marketing_Lecture

🔐 Jeanne accède automatiquement en lecture à la ressource "Marketing"

Pas besoin de modifier les droits NTFS ni de toucher aux partages → simple, efficace.

