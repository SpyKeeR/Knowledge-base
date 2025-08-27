# Groupe appels/Ch. Conf.

## 🛠️📋 **Création et configuration d’un groupe d’appels**

👉 Objectif : faire **sonner plusieurs lignes** en appelant un seul numéro.

• Accès : **Services → Configuration → IPBX → Context → Sur défaut → Modifier**

• Dans l’onglet **Groupe**, on définit un **intervalle de numéros** réservé aux groupes.



## ➕📞 **Création d’un groupe d’appels**

Une fois l’intervalle défini :

• Aller dans **Groupe → Création de groupe**

• Remplir les champs requis : **nom, numéro, stratégie, membres, délais, etc.**

💡 Chaque groupe peut être ciblé via son numéro dédié.



## 🔁🧠 **Stratégies de sonnerie disponibles**

• **Tous** : toutes les lignes disponibles sonnent en même temps (défaut)

• **Moins récent** : priorité à celui qui n’a pas été appelé depuis longtemps

• **Moins d'appels** : priorité à celui qui a traité le moins d'appels

• **Cyclique** : rotation continue entre les membres

• **Ordre de définition** : toujours dans le même ordre

• **Aléatoire** : sonne au hasard

• **Aléatoire pondéré** : hasard pondéré selon les pénalités d’agents



## ⏱️⏳ **Paramètres de délais et durées**

• **Temps de sonnerie (global)** : durée max pour que l'appel reste dans le groupe (ex : 60s)

• **Délai d’attente (par membre)** : durée max avant de passer au suivant (ex : 15s)

• **Délai avant rappel** : temps d’attente avant de retenter un membre déjà appelé (ex : 10s)



## 🏗️🔢 **Création de l’intervalle pour chambres de conférence**

👉 Objectif : définir une plage de numéros utilisables pour créer des chambres.

• Aller dans **Services → Configuration IPBX → Contexte → Sur défaut → Modifier**

• Dans **Chambre de conférence**, renseigner **l’intervalle de numéros** à réserver.



## 🛠️📞 **Création d’une chambre de conférence**

• Aller ensuite dans **Services → Paramètres IPBX → Chambre de conférence**

• Cliquer sur **Créer une chambre**

• Remplir : **Nom**, **Numéro** (dans la plage définie précédemment)

💡 Ces chambres permettent des réunions à plusieurs via un numéro unique.

### 💾✅ **Sauvegarde de la configuration**

Une fois la chambre configurée → **Cliquer sur "Sauvegarder"**

📌 Les utilisateurs peuvent désormais accéder à la conférence via le numéro attribué.

