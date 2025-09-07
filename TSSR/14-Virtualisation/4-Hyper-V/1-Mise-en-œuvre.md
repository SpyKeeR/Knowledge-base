# Mise en œuvre

## **🖥️ Création & gestion des machines virtuelles**

- Hyper-V permet de **créer et gérer des VMs** exploitant les ressources de l’hôte.
- Deux outils de gestion principaux :
  - 📋 **Console MMC (Microsoft Management Console)**
  - 💻 **PowerShell** avec le **module Hyper-V**



## **🎮 Interaction avec le système hôte**

- 🕹️ **Touches de bascule clavier/souris** vers la VM : Ctrl + Alt + Gauche
- ⌨️ Pour simuler Ctrl + Alt + Suppr dans la VM : utiliser Ctrl + Alt + Fin
- 🧰 **Services d’intégration** disponibles selon le système invité



## **🔌 Utilisation des périphériques externes**

- 📦 Les **médias amovibles** (clés USB, disques durs externes) sont utilisables dans Hyper-V
- ⚠️ **Attention** : non pris en charge dans **Hyper-V v2 (W2008 R2)**



## **⚙️ Fonctionnalités des machines virtuelles**

- 🧮 Chaque VM s’affiche dans une **console séparée**
- 🧬 Configuration poussée :
  - BIOS
  - Mémoire
  - Disques durs
  - Lecteurs DVD
- 📸 **Instantanés** (Snapshots) disponibles : Recommandé **machine éteinte** (évite de stocker la RAM et de gonfler la taille)



## **🔁 Importation & exportation**

- 🔄 Possible entre solutions Hyper-V, **à partir de la version 2**
- ⚠️ L’export **d’une version 1 vers version 2** n’est **pas supporté (**⚠️ Le **choix de la génération (1 ou 2)** est irréversible.)
- 📁 Utilisé pour **transférer ou sauvegarder des VMs**



## **🧩 Spécificités d'Hyper-V**

- 🧭 Outil **orienté administrateur**, à usage **serveur**
- 🔒 Moins convivial que les solutions desktop (type VirtualBox)
- 📂 Fichiers de configuration peu maniables :

