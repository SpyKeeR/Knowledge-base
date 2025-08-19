# Disques virtuels**🖥️ 1️⃣️ Introduction aux Disques Virtuels**

Aujourd’hui, avec la montée en puissance de la **virtualisation**, nous utilisons de plus en plus de **disques durs virtuels (VHD)**.

📌 Un disque virtuel est un **fichier** qui simule un **disque dur physique**.

📌 Il peut être utilisé dans une **machine virtuelle (VM)** ou **monté sur une machine physique**.

💡 **Exemple** : Un disque virtuel fonctionne comme un fichier **ISO** que l’on monte pour accéder à son contenu.



**📂 2️⃣️ Formats de Disques Virtuels**

Microsoft propose son propre format de disque dur virtuel pour **Hyper-V** :

✅ **VHD (Virtual Hard Disk)**

✅ **VHDX (Virtual Hard Disk Extended)**

💡 **VHDX** est une évolution du VHD avec **meilleure tolérance aux pannes** et prise en charge des **gros volumes (jusqu’à 64 To)**.



**🔄 3️⃣️ Types de Disques Virtuels**

Un **VHD/VHDX** peut être :

✔ **Statique (Fixed Size)** : Taille fixe, plus performant mais prend de l’espace.

✔ **Dynamique (Dynamically Expanding)** : Ne prend que l’espace nécessaire, s’agrandit à l’usage.

📌 **Les disques virtuels sont bootables**, donc ils peuvent contenir un **système d’exploitation**.



**⚡ 4️⃣ Avantages des Disques Virtuels**

✅ **Portabilité** : Un simple **fichier** que l’on peut **copier, déplacer, sauvegarder**.

✅ **Facilité d’usage** : Peut être **monté** sous Windows comme un disque classique.

✅ **Flexibilité** : Peut être utilisé sur une **VM ou un PC physique**.

✅ **Gestion de l’espace** : Avec un disque dynamique, **on optimise l’utilisation du stockage**.
