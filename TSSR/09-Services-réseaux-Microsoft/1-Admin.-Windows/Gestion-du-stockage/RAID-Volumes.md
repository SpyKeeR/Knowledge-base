# RAID / Volumes**🧱 Limitations des disques de base (MBR)**

Un **disque de base** peut contenir :

- **4 partitions principales**, ou
- **3 principales + 1 étendue**, contenant plusieurs **lecteurs logiques**.

❗ **Limite importante** : impossible d’installer un OS sur un lecteur logique.

📏 Les partitions peuvent être **étendues/réduites**, mais seulement sur **l’espace contigu** du même disque.



**🔀 Types de volumes sur disque dynamique**

Le **disque dynamique** permet de créer des volumes plus souples et adaptés aux besoins pro :

- **Volume simple** : sur 1 seul disque, peut être étendu.
- **Volume fractionné (spanned)** : étendu sur 2+ disques, sans redondance.
- **Volume agrégé par bandes (striped / RAID 0)** : 2+ disques, écriture alternée, + rapide, mais ⚠️ perte totale si un disque tombe.
- **Volume en miroir (RAID 1)** : duplication sur 2 disques, haute tolérance de panne, capacité divisée par 2.
- **Volume RAID 5** : 3+ disques, écritures réparties + parité, équilibre **performance / redondance / capacité**.



**🚀 Focus sur les niveaux de RAID logiciels**

| **Type** | **Disques min.** | **Redondance** | **Capacité dispo** | **Performance** |
|----|----|----|----|----|
| RAID 0 | 2 | ❌ | 100% | 🔥 Très rapide |
| RAID 1 | 2 | ✅ (1 panne) | 50% | ⚡ Lecture rapide |
| RAID 5 | 3 | ✅ (1 panne) | (n-1) x taille | ⚡ Bon mix perf/sécurité |

📌 Les **données** et la **parité** sont réparties intelligemment (ex : A1, A2, AP…) pour permettre une **reconstruction** si un disque meurt.
