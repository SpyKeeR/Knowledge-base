# **🎯 Objectifs**

### **📂 Administrer les éléments du datacenter**

### **🔐 Augmenter la sécurité en limitant les privilèges des utilisateurs**



### 🔐 **Sécurisation de l’environnement**

- Limitation des privilèges utilisateur pour renforcer la sécurité
- Définition fine des **rôles** et **autorisations** selon les responsabilités
- Centralisation de la gestion des comptes utilisateurs avec **vCenter**



### 🖥️ **Hyperviseur autonome vs Datacenter géré**

- Un **ESXi autonome** :
  - Gère uniquement ses **propres ressources**
  - Pas de visibilité ou d’action sur d’autres hôtes
  - Utilisation locale et limitée du **client web vSphere**
- Un **datacenter vSphere (via vCenter)** :
  - Regroupe plusieurs hyperviseurs sous une même gestion
  - Création possible de **pools de ressources globaux**
  - Accès centralisé à toutes les ressources partagées



### 🌐 **Client Web vSphere**

- Accessible via :
  - ESXi autonome ➜ accès limité à l’hôte
  - vCenter ➜ **accès complet** à tout le datacenter
- Interface d'administration pour l’ensemble des opérations sur les VMs, hôtes, datastores…



### 🚀 **Fonctionnalités avancées disponibles uniquement avec vCenter**

- **vMotion** : migration de VM à chaud d’un hôte à un autre
- **Storage vMotion** : migration d’un disque de VM entre datastores sans interruption
- **Modèles VMTX** : déploiement rapide de VMs à partir de templates
- **Rôles et privilèges globaux** : application centralisée des politiques de sécurité

