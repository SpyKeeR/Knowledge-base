# OVF**🧩 Définition du format OVF**

Le **format OVF (Open Virtualization Format)** est un **standard ouvert** permettant de décrire des machines virtuelles de façon portable entre différentes plateformes de virtualisation.

Ce format facilite l'**interopérabilité** entre solutions, ce qui en fait un choix idéal pour le **transport ou l’échange de VM**.



**🔄 Différences entre OVF et VMTX**

- **VMTX** : spécifique à VMware vSphere, riche en fonctionnalités, dédié au **déploiement automatisé** dans un datacenter.
- **OVF** : standard universel, plus simple, orienté **portabilité** entre plateformes.

OVF = souplesse multiplateforme

VMTX = puissance dans l’écosystème vSphere



**🛠️ Fonctionnalités principales du modèle OVF**

- Contient :
  - La **configuration de la VM**
  - Un ou plusieurs fichiers de disque virtuel (ex : **.vmdk**)
- Permet :
  - **L’export** d’une VM depuis un hyperviseur
  - **L’import** dans un autre environnement
  - **Le stockage long terme** ou l’archivage

**Exemple de commande avec OVF Tool :** ovftool source_vm.vmware.com destination.ovf
