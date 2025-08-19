# Modèles de services**🧩 Qu’est-ce qu’un service dans Centreon ?**

Un **service**, c’est un **point de contrôle** rattaché à un **hôte**. C’est ce qui va nous permettre de surveiller un élément (matériel, système, logiciel, processus…), de **collecter des indicateurs** (ex. : charge CPU, espace disque), d’en tirer un **état** : OK / Warning / Critical / Unknown. ⚠️ C’est *l’unité de base* de la supervision active.



**⚙️ Comment fonctionne la supervision des services dans Centreon ?**

Centreon exécute régulièrement des **commandes de vérification** (check commands) pour chaque service.

Selon le **résultat**, il peut : **mettre à jour l’état** du service (OK, Warning, Critical, etc.), **déclencher une alerte** (mais **seulement** si l’état est confirmé = **état HARD**).



**🧾 Que contient la configuration d’un service ?**

| **Nom du service** | Description lisible du service |
|----|----|
| **Hôte associé** | L’équipement auquel le service est rattaché |
| **Check period** | Période pendant laquelle la vérification est autorisée |
| **Normal check interval** | Fréquence des tests quand tout va bien (souvent 5 min par défaut) |
| **Retry check interval** | Fréquence des retests en cas de problème |
| **Max check attempts** | Nombre de retests avant de considérer que l’état est confirmé (HARD) |
| **Check command** | La commande de vérification (ex: check_http, check_snmp, etc.) |
| **Actif/Passif** | Si le service lance des checks automatiquement ou non |



**🚦 États SOFT vs HARD**

| SOFT | ❌ 0 Notif | Faible (provisoire) | Pendant les tentatives de confirmation |
|------|------------|---------------------|----------------------------------------|
| HARD | ✅ Notif   | Élevée (confirmée)  | Dès que l’état est confirmé            |

Tant que l’état est SOFT, pas d’alerte envoyée. > OK / UP sont **immédiatement** confirmés en HARD.



**🔧 Modèles de services (Service Templates)**

Les modèles permettent de **centraliser la configuration** et d’**éviter la redondance**. C'est Plus rapide, Moins d’erreurs, Indispensable pour les déploiements automatiques

| **Type** | **Utilité principale** | **Check command ?** |
|----|----|----|
| **Modèle de configuration** (ex: STC_DEFAULT) | Contient tous les **paramètres communs** (périodes, intervals, etc.) | ❌ Non |
| **Modèle fonctionnel** (ex: STF SNMP CPU Windows) | Contient la **vraie commande de check**, macros, etc. | ✅ Oui |

Les modèles fonctionnels héritent souvent des modèles de configuration.

**📛 Convention de nommage**

- STC_ → Service Template Configuration > STC_DEFAULT → modèle général sans commande
- STF_ → Service Template Fonctionnel > STF_SNMP_CPU_Windows → modèle spécifique avec commande

**⏲️ Vérifications : périodes et intervalles**

- **Check Period** : Quand on est autorisé à vérifier (ex: 24x7 ou horaires de bureau).
- **Normal Check Interval** : Ex : 5 = test toutes les 5 min quand tout va bien.
- **Retry Check Interval** : Ex : 1 = retest chaque minute en cas d’échec.
- **Max Check Attempts** : Ex : 3 = après 3 retests, on confirme l’état comme HARD.
