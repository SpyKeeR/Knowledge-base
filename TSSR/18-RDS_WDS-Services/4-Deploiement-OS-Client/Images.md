# Images📦 **Deux types principaux d’images**

Le processus de déploiement repose sur l’utilisation d’**images système**, et plus précisément :

1.  🧱 **Les images de partition**
2.  💿 **Les images d’installation (WIM)**

🧱 **Images de partition**

• Créées à partir de la **copie complète d’une partition** d’un disque dur

• Génèrent un **fichier image** contenant :

- Le système d’exploitation configuré
- Les pilotes (drivers)
- Les applications installées  
  • Ce fichier est ensuite utilisé pour déployer le système sur d’autres postes

🔧 **Spécificités**

• Les **caractéristiques matérielles** des postes cibles doivent être **identiques ou très proches** de celles du poste source

• Objectif : éviter les **incompatibilités matérielles** dues à une image trop spécifique

💿 **Images d’installation (WIM)**

• Utilisées notamment dans les systèmes Microsoft

• Provenance : 📀 CD/DVD d’installation de Windows

• Basées sur un **processus d’installation dynamique** qui détecte automatiquement le matériel pendant le déploiement

🔄 **Avantages**

• ✅ **Grande flexibilité** : une même image peut être utilisée sur des matériels variés

• 🤖 **Automatisation** : détection automatique du matériel et adaptation du système

• ⏱️ **Gain de temps** et indépendance par rapport aux spécificités matérielles

🧠 **À retenir**

• Les **images de partition** permettent un déploiement rapide d’un environnement totalement configuré, mais nécessitent une **homogénéité matérielle**

• Les **images d’installation (WIM)** sont plus **souples** et adaptées à des **parcs hétérogènes**, au prix d’un temps d’installation plus long

🎯 **Choix de l’image**

Le choix entre image de partition et image WIM dépend de deux facteurs principaux :

• 🖥️ **Homogénéité matérielle** du parc informatique

• 🔁 **Niveau de flexibilité souhaité** pour le déploiement
