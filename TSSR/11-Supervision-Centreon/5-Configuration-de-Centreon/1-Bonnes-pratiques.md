## 🔁 **Variabiliser les commandes**

➡️ L’idée : ne **pas figer de valeurs** dans la ligne de commande. On utilise des **macros** pour injecter dynamiquement des infos selon l’hôte/service.

➡️ Exemples typiques de variables à utiliser dans une commande Centreon :

→ `$HOSTADDRESS$` (adresse IP ou nom DNS), `$ARG1$`, `$ARG2$` (paramètres personnalisés), `$SNMPVERSION$`, `$SNMPCOMMUNITY$`

➡️ Exemple pratique : une commande de check CPU Windows via SNMP

→ Doit intégrer : IP hôte, communauté SNMP, version SNMP, seuil warning, seuil critique → tout ça **doit être passé en variables**

Pourquoi ? Tu pourras **réutiliser la même commande** sur des dizaines de machines avec des paramètres différents, **sans dupliquer la config** 💡



## 🏷️ **Nommer les commandes selon une convention claire**

➡️ En production, il y aura **des dizaines (voire centaines) de commandes** → le nom doit te parler **en 2 secondes**.

➡️ Convention recommandée : check_<protocole>_<fonction>

→ Exemples : check_snmp_cpu, check_nrpe_disk, check_ssh_load

➡️ Pourquoi "check" ? Parce que ce sont **toutes des vérifications** (services, états, etc.)

➡️ Le protocole est **clé pour identifier** rapidement le fonctionnement en cas de debug

➡️ Même logique à appliquer aux **autres objets** (templates, services, etc.) → tout doit suivre une **nomenclature stricte**



## **🧪 Test impératif avant écriture**

Avant toute chose, la **commande doit être testée directement dans le shell** du serveur de supervision **en tant qu’utilisateur centreon-engine**.

🎯 Objectif : s’assurer qu’elle fonctionne, renvoie un résultat cohérent, et surtout qu’elle respecte les droits et l’environnement d’exécution nécessaires.

⚠️ Une fois enregistrée dans Centreon, **il n’est plus possible de la tester directement** : d’où l’importance cruciale de cette étape de validation.



## **👤 Exécution avec l’utilisateur centreon-engine**

Pourquoi cet utilisateur ? ➡️ C’est **l’utilisateur système** qui exécute les plugins et les commandes de supervision.

Cela garantit :

- 🔐 Droits corrects sur les fichiers, services ou sockets utilisés
- 📦 Bon environnement système (PATH, variables, etc.)
- ✅ Comportement identique à l’exécution réelle dans Centreon

Pour simuler cela, on utilise la commande : `sudo -u centreon-engine <commande>`

## **🛠️ Création de la commande dans Centreon**

Une fois la commande validée : 📍 Aller dans **Configuration → Commands → Checks**

Puis cliquer sur **"Add"** pour créer une nouvelle commande de vérification.

Champs principaux à renseigner :

- **Nom** : unique, explicite (ex : check_http_api)
- **Type** : **Check command**
- **Command line** : ligne complète validée dans le shell (avec les variables si besoin : $ARG1$, etc.)




