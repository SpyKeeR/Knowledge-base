# Autorisations de partage

## **🧰 Rôle des autorisations de partage**

Le **partage de fichiers** permet à un poste (souvent un **serveur**, parfois un simple **client**) d’agir comme **serveur de fichiers**.

Les autorisations de partage sont **complémentaires des autorisations NTFS** : elles interviennent **en amont**, lors de l'accès distant à un dossier partagé.



## **🛑 Étapes de contrôle d’accès**

Lorsqu’un utilisateur se connecte à un partage, il passe deux niveaux de contrôle :

1.  **🔒 Autorisations de partage** (niveau réseau) :  
    ➕ Lecture | Modification | Contrôle total  
    ➖ Refus (prioritaire, comme en NTFS)  
    👉 Ces droits filtrent l'accès dès la tentative de connexion.

2.  **🧱 Autorisations NTFS** (niveau système) :  
    ➕ Lecture, Écriture, Exécution, Appropriation, etc.  
    ➖ Refus (également prioritaire)

📌 Le **résultat final** (autorisation résultante) est **le plus restrictif** des deux niveaux. Si un seul bloque, l'accès est refusé.



## **📤 Pratiques recommandées**

💡 **Utiliser un seul niveau de permission dans le partage** (lecture/modif/contrôle total) pour simplifier la gestion.

💡 Appliquer les droits surtout via **groupes**, pas directement sur les utilisateurs.

💡 Refuser uniquement en cas de nécessité stricte.



## **🔍 Publication des partages**

Publier un partage permet de :

- ✅ Le rendre visible dans l’**Active Directory**
- ✅ Le rendre **facilement retrouvable** via une recherche réseau (Explorateur Windows, outils d’admin)
- ✅ Simplifier l’**automatisation des montages** (lecteurs réseau, scripts de login...)
- ✅ Faciliter l’**administration** centralisée et la documentation de l'infrastructure

📌 Ces partages peuvent être **associés à un objet ordinateur** ou regroupés dans une **OU dédiée** pour une recherche plus efficace.

