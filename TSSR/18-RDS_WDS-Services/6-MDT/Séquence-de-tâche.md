# Séquence de tâche🧠 **Définition**

- 🔁 Une **séquence de tâches** (Task Sequence) est un **enchaînement d’actions automatisées** exécutées lors du déploiement d’un OS avec MDT
- 📋 Ce mécanisme **n’existe pas dans WDS**, il est spécifique à MDT
- ⚙️ **Obligatoire** pour tout déploiement, car elle orchestre le processus complet



🚀 **Utilité**

- 🤖 Permet d’**automatiser** toutes les étapes du déploiement : partitionnement, installation OS, drivers, applications, etc.
- 🔗 Elle est **liée directement** au système d’exploitation ciblé
- ✅ Simplifie le travail en standardisant les opérations



🛠️ **Création d’une séquence de tâches**

- 📑 Réalisée à partir d’un **modèle prédéfini**, souvent :
  - **Client Task Sequence** (modèle standard)
- 🧾 Lors de la création :
  - 🗂️ **Questions regroupées par catégorie** (OS, clé produit, langue, etc.)
  - 🔐 Intégration possible d’une **clé de licence**



📂 **Fichiers générés automatiquement**

- Unattend.xml : fichier de réponse Windows utilisé pour l’installation automatisée
- ts.xml : définit le détail de la séquence de tâches à exécuter

🔧 **Modification post-création**

- ✏️ Une fois créée, la séquence de tâches peut être **éditée, complétée ou personnalisée**
- 🎯 Cela permet d’**adapter précisément** le déploiement à un contexte spécifique



📌 **À retenir**

- La séquence de tâches est le **cœur du déploiement MDT**
- Elle structure, automatise et orchestre toutes les phases
- Sa **création assistée** est simple, mais elle peut être **hautement personnalisée**

🧩 Maitriser les séquences de tâches = déploiement fluide, rapide et sans erreur
