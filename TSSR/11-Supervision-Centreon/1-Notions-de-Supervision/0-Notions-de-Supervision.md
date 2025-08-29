# **🎯 Objectifs du module "Notions de supervision"**

Ce module va t'aider à :

- 🧾 **Définir clairement ce qu’est la supervision**
- 🎯 **Comprendre ses objectifs principaux**
- 🧱 **Identifier les niveaux de supervision** à mettre en place selon les besoins

Ces fondations sont essentielles pour ensuite bien maîtriser des outils comme **Centreon**.



## **🔍 Définir la supervision**

La **supervision** c’est l’art de **surveiller en temps réel** les composants d’un SI (systèmes, réseaux, services, etc.).

💡 *Pourquoi c’est clé ?*

Parce que sans supervision, tu travailles à l’aveugle. Grâce à elle :

- 👀 Tu vois l’état de ton infra (CPU, RAM, disques, services…)
- 🚨 Tu détectes les incidents avant qu’ils deviennent critiques
- 📊 Tu analyses la performance et la charge au fil du temps

Et c’est là qu’intervient **Centreon**, une solution open source de supervision orientée IT (héritée de Nagios).



## **🎯 Définir les objectifs de la supervision**

Voici les **3 objectifs principaux** à toujours garder en tête :

- ✅ **Surveiller l’état** des équipements et services (uptime, disponibilité, charge)
- ⚠️ **Détecter les anomalies** le plus tôt possible (rupture de service, dégradation)
- 📢 **Notifier les incidents** aux bonnes personnes (mail, SMS, tableau de bord)

Tout ça dans le but de **garantir la disponibilité**, **assurer les performances** et **réduire les temps d'interruption** 🕒



## **🧱 Identifier les niveaux de supervision**

On ne supervise pas tout au même niveau. Voici les **différents étages** à prendre en compte :

- 🧍 **Supervision de base** : disponibilité (est-ce que le service répond ?)
- ⚙️ **Supervision fonctionnelle** : état des services métiers (ex : base de données, site web, LDAP)
- 📈 **Supervision des performances** : taux de charge, CPU, RAM, latence
- 🧮 **Supervision applicative** : logs, transactions, comportements spécifiques
- 🧩 **Supervision réseau** : trafic, bande passante, erreurs, états de liens

💬 *Exemple :* un simple **ping** peut suffire pour dire qu’un serveur est up (niveau 1), mais pas s’il fait bien son boulot métier (niveau 2+).

