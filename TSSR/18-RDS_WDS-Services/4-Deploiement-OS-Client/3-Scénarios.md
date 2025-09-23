# Scénarios

Le processus de déploiement d’un système d’exploitation peut s’appliquer à différents **contextes** et **situations**. Chaque scénario répond à un besoin précis en entreprise, qu’il s’agisse d’installer, de réparer ou de mettre à jour un poste.



## 🆕 **1. Nouvelle installation (Bare Metal)**

- 🖥️ Poste neuf, tout juste sorti du carton
- 💾 Disque dur vierge, sans système d’exploitation
- 🎯 Objectif : déployer l’OS, les pilotes, les applications
- 👷 Permet de rendre un poste pleinement **opérationnel**
- 🔄 Peut aussi s’appliquer à un redéploiement complet, **écrasant les données existantes**



## ♻️ **2. Réinstallation d’un système (Actualiser / Wipe and Load)**

- 💡 Système existant, mais instable ou corrompu
- 🗂️ Objectif : **réinstaller l’OS** tout en **conservant les données et applications existantes**
- 🔧 Utilisé pour réparer un poste sans perdre les fichiers de l’utilisateur
- 🛠️ Nécessite une sauvegarde et une méthode de restauration fiable

## 🔁 **3. Remplacement de matériel (Replace)**

- 💻 Ancien poste remplacé par un nouveau
- 📤 Capture des données de l’ancien PC
- 📥 Restauration sur le nouveau matériel
- 🔄 Assure une **transition fluide** pour l’utilisateur final
- 🧳 Permet de conserver l’environnement de travail



## ⏫ **4. Mise à niveau (In-place Upgrade)**

- 📦 Application d’une nouvelle version de Windows (nouveau Build)
- 🔁 L’environnement actuel est **conservé** (paramètres, données, apps)
- 🧼 Pas de formatage, l’OS est mis à jour sur place
- 📈 Scénario recommandé dans le cadre d’évolutions internes du parc



| **Scénario** | **Type de déploiement** | **Données conservées** | **Objectif principal** |
|----|----|----|----|
| 🆕 Nouvelle installation | Bare Metal | ❌ Non | Préparer un poste neuf |
| ♻️ Réinstallation | Wipe and Load | ✅ Oui | Réparer un système existant |
| 🔁 Remplacement | Replace | ✅ Oui | Migrer vers un nouveau matériel |
| ⏫ Mise à niveau | In-place Upgrade | ✅ Oui | Mettre à jour vers une nouvelle version |
