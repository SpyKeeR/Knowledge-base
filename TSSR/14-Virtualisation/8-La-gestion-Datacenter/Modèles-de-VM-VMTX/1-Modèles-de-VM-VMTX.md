# Modèles de VM - VMTXLes modèles de VM permettent de déployer rapidement plusieurs machines virtuelles **identiques** à partir d'une configuration de référence.

Ils sont essentiels pour **standardiser**, **automatiser** et **accélérer** la création de VMs dans une infrastructure professionnelle.

🗃️ **Deux types de modèles principaux**

1️⃣ **Modèle OVF (Open Virtualization Format)**

- 📁 Format **standardisé** et **interopérable**
- 🔄 Compatible avec : VMware (Workstation, Player, vSphere), VirtualBox, XenServer
- ↕️ Permet **l'import/export** de VMs entre plateformes
- ⚠️ **Non reconnu comme modèle de déploiement automatisé**
- 🛠️ Nécessite une **importation manuelle** avant utilisation
- 🧳 Format OVA = **archive compressée** contenant les fichiers OVF

2️⃣ **Modèle VMTX (VMware Template)**

- 🔒 **Spécifique à VMware vSphere**
- 🔁 Permet le **clonage**, **déploiement**, **conversion** de VM vers modèle et inversement
- 🧰 Peut être **modifié** facilement (pas figé comme OVF)
- 💡 Conçu pour le **déploiement automatisé** dans un datacenter



🔄 **Gestion des modèles dans vSphere**

📌 Les modèles VMTX sont visibles depuis :

- L’inventaire **« VM et modèles »**
- Le menu **« Modèle »** depuis une VM

Deux actions possibles :

- 🔄 **Convertir** : transformer une VM en modèle ou inversement
- 📑 **Cloner** : dupliquer une VM ou un modèle en gardant l’original intact



🎯 **Personnalisation post-déploiement**

Permet d’automatiser :

- 🏷️ Renommage du nom machine
- 🌐 Modification de l’adressage IP
- 🔐 Régénération de l’ID de sécurité (SID Windows)

🧰 **Prérequis** :

- VMware Tools installés
- Sysprep intégré au SE invité ou fourni via le vCenter

📄 Un **fichier de personnalisation** peut être généré par l’assistant de personnalisation et exporté/importé.

📊 **Comparatif OVF vs VMTX**

| **🔍 Critère** | **🌍 OVF** | **🛠️ VMTX (vSphere)** |
|----|----|----|
| 📦 Compatibilité | Multi-plateformes | Exclusif à VMware vSphere |
| 🔄 Actions disponibles | Import/Export manuel | Clone, Convert, Déploiement |
| 🧠 Personnalisable | Non (statique) | Oui (modifiable à tout moment) |
| 🧰 Cas d’usage typique | Partage de VM | Déploiement standardisé en prod |
| 📁 Format associé | .ovf, .ova | .vmtx |



🔧 **Contenu d’un modèle VMTX**

- 💽 Système d’exploitation (Windows, Linux/Unix)
- 🖥️ Pilotes pour l’hyperviseur
- 🧩 Applications métiers
- 🛡️ Correctifs de sécurité et mises à jour
- 🧪 Personnalisé selon le **contexte de l’entreprise**

🛠️ **Création d’un modèle VMTX (master)**

1.  Créer une VM de référence
2.  Installer les **VMware Tools**
3.  Personnaliser la machine (applications, MAJ, pilotes)
4.  Exécuter sysprep /oobe /generalize /mode:vm (Windows)
5.  Convertir la VM en **modèle VMTX**

🔁 Possibilité de reconvertir un modèle VMTX en VM pour le mettre à jour, puis le reconvertir.
