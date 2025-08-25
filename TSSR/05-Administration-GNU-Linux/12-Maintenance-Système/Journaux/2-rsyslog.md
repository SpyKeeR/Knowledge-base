# rsyslog

## **💡 Pourquoi utiliser rsyslog en plus de journald ?**

- journald = rapide, moderne, mais **temporaire**
- rsyslog = **archivage durable** dans /var/log/ → En prod, les deux sont souvent utilisés ensemble.



## **📚 Facilities = catégories de logs**

Une **facility** (ou “classe”) représente une source d’événements système, ex. :

| auth       | Authentification           |
|------------|----------------------------|
| authpriv   | Infos de connexion privées |
| cron       | Tâches planifiées          |
| daemon     | Services de fond           |
| kern       | Kernel                     |
| mail       | Service mail               |
| user       | Processus utilisateurs     |
| local0 à 7 | Pour ta propre utilisation |

## **🧩 Fonctionnement de rsyslog**

Il fonctionne avec deux notions :

- **Facility** : catégorie de message (auth, cron, daemon, user, etc.)
- **Priority** : niveau d’urgence du message (de emerg à debug)

**Syntaxe dans la conf :** facility.priority fichier_de_log

Exemples :

- auth,authpriv.* /var/log/auth.log → envoie tous les messages auth et authpriv dans auth.log
- authpriv.none /var/log/syslog → exclut les messages authpriv de syslog
- #cron.* /var/log/cron.log → ligne désactivée (commentée)

**Note :**

- \* = toutes les priorités
- ; = exclusion de la cible avant le ;, suivi des facilities et priorities exclues
- \*.=warn : spécifie une seule priority, si pas de = cible toutes les priorities supérieures
- none = exclusion totale de cette facility



## **🧪 Tester avec logger**

Tu peux simuler un message avec : logger -p cron.info "message de test" > Ça va écrire un message avec la facility cron et le niveau info.

