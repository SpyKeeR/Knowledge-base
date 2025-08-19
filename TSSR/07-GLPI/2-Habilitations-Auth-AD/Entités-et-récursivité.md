# Entités et récursivité**🏢 Notion d’entités**

Les **entités** dans GLPI permettent de **segmenter** les données et les utilisateurs.

Elles servent à **cloisonner les accès** et à organiser les ressources selon des **logiques métiers ou organisationnelles**.



**🔸 Exemples d’utilisation :**

- Une **entreprise de support technique** peut créer une entité **par client** (Client A, Client B, etc.).
- Chaque client n’aura **accès qu’à ses propres données** ✅.
- Les techniciens de la société mère pourront, eux, **voir tous les clients** s’ils en ont les droits 🔍.



**🌍 Autre cas typique : entreprises avec filiales**

- Une entreprise avec plusieurs **filiales internationales** peut créer une entité pour chaque filiale.
- Chaque filiale est **autonome** : elle gère son parc informatique sans voir les autres.
- La **maison mère**, grâce aux entités parentes, peut avoir **une vue globale**.

**🔁 Notion de récursivité**

La **récursivité** permet de définir si une entité **enfant** hérite ou non de certains éléments de l’entité **parente**.

**💡 Principe :**

- Si activée 🔄 : les sous-entités **voient ou héritent** des éléments définis au niveau supérieur (ex : fournisseurs, modèles, types).
- Si désactivée 🚫 : les entités enfants **restent totalement indépendantes**.
- 

**📦 Exemple concret : un fournisseur**

- Un **fournisseur commun** à toutes les entités est défini dans l’entité **racine (la plus haute)**.  
  → Grâce à la récursivité, **toutes les sous-entités** y ont accès 🌐.

- Un **fournisseur spécifique** à une seule entité est défini localement dans cette entité.  
  → Il ne sera **visible que dans cette entité** 🕵️‍♂️.



**🎯 Objectif principal des entités & récursivité**

**Cloisonner les accès, adapter la visibilité, et structurer les droits** selon les besoins opérationnels, tout en conservant une gestion centralisée possible.
