# Import/Export de VM**🚚 Exportation d’une machine virtuelle**

- 🔁 **Objectif** : Dupliquer ou déplacer une VM vers un autre hyperviseur
- 📍 Accessible depuis la **console Hyper-V**
- 📂 **Contenu exporté** :
  - Fichiers de configuration
  - Disques durs virtuels
  - Instantanés (le cas échéant)
- 🗂️ Destination : **Répertoire dédié à l’exportation**

**📥 Importation d’une machine virtuelle**

- ✅ **Condition** : Avoir l’**ensemble des fichiers de la VM**
- 🧭 Deux modes disponibles :

  1.  **Sans duplication**

      - **🧳 Déplacement pur : la VM quitte l’hyperviseur d’origine**
      - **⚠️ Un seul import possible**

  2.  **Avec duplication**

      - **🗃️ Copie complète de la VM vers l’hyperviseur cible**
      - **🔁 Génération de nouvelles adresses MAC pour éviter les conflits réseau**
      - **⚠️ Si la duplication échoue → risque de conflit d’adresses MAC**



**🔄 Étapes types d’un transfert de VM**

1.  📤 Exporter la VM vers un répertoire dédié
2.  🚚 Copier ce dossier sur un autre serveur Hyper-V
3.  📥 Importer sans duplication sur le nouvel hôte
4.  🧮 Possibilité de **réimporter** ou **dupliquer à nouveau** si les fichiers originaux sont conservés



**⚠️ Attention : gestion des fichiers**

- 📁 Il faut **sélectionner le dossier complet** contenant :
  - Fichiers de config
  - Disques
  - Snapshots
- ❌ Hyper-V **ne gère pas une VM via un seul dossier unique** → Plusieurs fichiers et répertoires interdépendants
- 🎯 Bien comprendre la **structure des fichiers** avant de manipuler
