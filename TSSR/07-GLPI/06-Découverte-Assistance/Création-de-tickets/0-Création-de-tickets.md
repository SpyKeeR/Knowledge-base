# Création de tickets

## **🛠️ Méthodes de création de tickets**

### **➤ Via GLPI (interface web)**

GLPI propose plusieurs **interfaces adaptées** selon le public :

| **Interface** | **Pour qui ?** | **Caractéristiques** |
|----|----|----|
| 🧑‍💻 **Standard** | Utilisateurs authentifiés, techniciens | Complète, avec tous les champs disponibles |
| ✏️ **Simplifiée** | Utilisateurs peu techniques | Plus intuitive, allégée |
| 👤 **Anonyme** | Utilisateurs sans compte | Pas besoin de se connecter, formulaire de base |



### **✉️ Par mail (automatisé)**

Une des méthodes les plus pratiques : **l’utilisateur envoie un mail** à une adresse spéciale (ex. support@tondomaine.fr).

**📬 Fonctionnement :**

- GLPI surveille une **boîte mail (collecteur)**
- Lorsqu’un mail arrive → un **ticket est automatiquement créé**
- Possibilité de rattacher le mail à un utilisateur s’il est connu

🔧 Nécessite une **configuration serveur IMAP/POP** dans GLPI.



### **☎️ Par téléphone**

L’utilisateur appelle la DSI ou le support.

**Comment ça marche :**

- Le **technicien écoute la demande**
- Puis il **crée manuellement le ticket** via l’**interface standard**

📝 Ce type de ticket doit être **clairement rédigé**, car l’utilisateur ne le fait pas lui-même.



### **🌐 Focus : Tickets anonymes**

**📍 URL :** http(s)://<@glpi>/front/helpdesk.html

**🔓 Particularités :**

- **Pas besoin de s’authentifier**
- Pratique pour les **utilisateurs occasionnels** ou les invités
- **Peut être désactivé** dans les réglages si besoin
- L’apparence est **personnalisable** (HTML/CSS)

📌 Tous les tickets anonymes sont **rattachés par défaut à l’entité racine**.

