# FileSystems

## **🛠️ 1️⃣️Formatage et Systèmes de Fichiers**

Après avoir **partitionné un disque**, il faut **le formater**.

📌 **Formater**, c'est **installer un système de fichiers** sur une partition, pas simplement effacer les données.

📌 Le **système de fichiers** est un **index** qui permet d’organiser l’accès aux données et leur emplacement sur le disque.

📌 Exemples d’indexation : comme dans un **magasin suédois**, où vous récupérez un produit en suivant un emplacement précis.



## **🗄️ 2️⃣️ Notion de Volumes et Organisation des Données**

✔ Une **partition formatée** devient un **volume**.

✔ Le système de fichiers stocke les **droits d’accès**, l’emplacement des fichiers, et parfois des métadonnées avancées (sécurité, compression...).

✔ Le **système d’exploitation** consulte en permanence cet index pour lire et écrire des données.



## **📂 3️⃣️Les Différents Systèmes de Fichiers**

Chaque **système de fichiers** a des avantages et inconvénients.



### 🔹 **NTFS (New Technology File System)** (Windows)

✅ Par défaut sur Windows (ex : Windows 10 ne s’installe que sur un volume **NTFS**).

✅ Sécurisé via les **ACLs** (listes de contrôle d’accès).

✅ Permet le **chiffrement**, la **compression**, la **déduplication** et la **gestion avancée des permissions**.

✅ Peut gérer des **volumes jusqu’à 256 To (Win10)**.



### 🔹 **FAT32 (File Allocation Table 32)**

✅ Ancien système de fichiers, **universel** et compatible avec **Windows, macOS et Linux**.

❌ Pas sécurisé et **limité à des fichiers de 4 Go maximum** (2TO/volume)

❌ Ne supporte pas les fonctionnalités avancées de NTFS.

✔ Utile pour **les clés USB et disques externes** destinés à plusieurs OS.



### 🔹 **EXT4 (Fourth Extended Filesystem)**

✅ **Standard sur Linux**.

✅ Gère **de grands fichiers et disques**, journalisation avancée.

✅ Plus fiable que NTFS sur **Linux**.



### 🔹 **ReFS (Resilient File System - Windows Server)**

✅ Amélioré pour la **résilience aux erreurs** et la **récupération automatique**.

❌ Moins compatible avec les anciennes versions de Windows.



### 🔹 **UDF (Universal Disk Format)**

✅ Utilisé pour les **CD/DVD** et **Blu-ray**.

✅ Lecture seule après gravure.
