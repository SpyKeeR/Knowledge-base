# Fichiers de réponses🧠 **Rôle général**

- 🔧 Les fichiers de réponses permettent de **personnaliser et automatiser** le déploiement des OS dans MDT
- ⚙️ Ils servent à spécifier des **paramètres avancés** qui ne sont pas visibles dans l’interface graphique de MDT
- 📁 Ces fichiers complètent la **séquence de tâches** en ajoutant des instructions supplémentaires

📜 **Fichier principal : Unattend.xml**

- 🧩 C’est le **fichier de réponse principal** utilisé dans MDT
- 🔍 Il peut être édité depuis l’onglet **OS Info** dans les propriétés de la séquence de tâches
- ✏️ Bouton : **Edit Unattended XML**
- 📌 Il contient :
  - Des paramètres liés au déploiement automatisé
  - Des personnalisations système
  - Des étapes supplémentaires invisibles dans l’interface



⚙️ **Utilité dans les séquences de tâches**

- 🔁 Permet de **configurer les actions** qui seront exécutées pendant le déploiement
- 🔄 Offre la **flexibilité nécessaire** pour adapter les séquences aux besoins métier
- 📦 Peut gérer des configurations variées : product keys, nom machine, timezone, etc.



📏 **Contenu variable**

- ✂️ **Minimaliste** : quelques lignes pour un déploiement basique et rapide
- 📚 **Complet** : plusieurs centaines de lignes pour des déploiements complexes
- 🧘‍♂️ Le cours se concentre ici sur les **fichiers de réponse minimalistes**, simples et efficaces



📌 **À retenir**

- Les fichiers de réponse sont **indispensables** pour un déploiement automatisé avancé
- Unattend.xml est le cœur de cette automatisation
- ✅ MDT permet leur **édition directe**, facilitant leur gestion
- 🔒 Bien configurés, ils assurent un **déploiement fiable, sans intervention manuelle**
