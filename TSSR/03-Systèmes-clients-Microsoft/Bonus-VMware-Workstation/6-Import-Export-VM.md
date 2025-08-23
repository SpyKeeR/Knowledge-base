# Import/Export VM

## **📥 1️⃣️Importation d’une VM**

### 📌 **Pourquoi importer une VM ?**

✅ Récupérer une VM existante sans devoir la recréer.

✅ Utiliser une VM provenant d’un autre système ou d’un collègue.

### 📌 **Méthodes d’importation** :

#### 1️⃣ **Depuis un dossier existant** : 
- 🔹 **Ouvrir Workstation** ➝ **Fichier > Open** ➝ Sélectionner le fichier **.vmx**.
- 🔹 **Double-cliquer sur le fichier .vmx** dans l’explorateur pour l’ajouter directement.

#### 2️⃣ **Depuis une archive (OVA/OVF)** : 
- 🔹 **OVA (Open Virtual Appliance)** ➝ Format unique, facile à importer.
- 🔹 **OVF (Open Virtualization Format)** ➝ Ensemble de fichiers (OVF + VMDK).
- 🔹 **Importer via** : ➝ **File > Import** ➝ Sélectionner l’archive **.ova** ou **.ovf**



## **📤 2️⃣️Exportation d’une VM**

### 📌 **Pourquoi exporter une VM ?**

- ✅ Sauvegarder une machine virtuelle.
- ✅ Déplacer une VM sur un autre hôte.
- ✅ Partager une VM avec d’autres utilisateurs.

### 📌 **Méthodes d’exportation** :

#### 1️⃣ **Copie directe du dossier de la VM** (méthode simple) 
-🔹 Copier/coller le dossier contenant les fichiers **.vmx** et **.vmdk**.

#### 2️⃣ **Export en OVA/OVF** (méthode recommandée) 
-🔹 Ouvrir Workstation et sélectionner la VM. 🔹 **File > Export to OVF**.

🔹 Choisir le format **OVA** (un fichier) ou **OVF** (plusieurs fichiers).



📌 **Commande pour exporter une VM en OVA** : vmware-vdiskmanager -r <nom_VM>.vmdk -t 1 <nom_VM_export.ova>

