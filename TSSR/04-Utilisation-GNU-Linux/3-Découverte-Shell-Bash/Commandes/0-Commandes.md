# Commandes

## **👤 Infos sur l’utilisateur :**

| **Commande** | **Description** | **Exemple de sortie** |
|----|----|----|
| logname | Nom de l’utilisateur connecté | user30 |
| id | UID / GID / groupes | uid=1060 gid=1060 groups=user30, any |
| id -u user5 | UID d’un autre utilisateur | uid=1061 |



## **👥 Connexions utilisateurs :**

| **Commande** | **Description**                             |
|--------------|---------------------------------------------|
| who          | Qui est connecté                            |
| who -H       | Affiche les **titres de colonnes**          |
| who -q       | Affiche le **nombre** de connexions actives |

last ➜ historique des connexions (basé sur /var/log/wtmp)



## **🔐 Gestion des mots de passe :**

| **Commande** | **Action**                                                 |
|--------------|------------------------------------------------------------|
| passwd       | Changer son mot de passe (ancien + nouveau + confirmation) |
| passwd -S    | Voir l’**état** du mot de passe                            |

### **🔏 Règles de complexité :**

- ✅ **Au moins un caractère spécial**
- ✅ Pas trop proche de l’ancien mot de passe
- ✅ Éviter les mots du dictionnaire

