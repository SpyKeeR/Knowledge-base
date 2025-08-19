# Spécificités**🚀 1️⃣️Avantages de VMware Workstation**

🔹 **Interface intuitive** et facile à prendre en main.

🔹 **VMware Tools** : améliore l'expérience utilisateur (glisser-déposer, copier-coller entre hôte et VM, synchronisation de l’heure).

🔹 **Pause/Resumption** : permet de suspendre une VM pour libérer des ressources.

🔹 **Snapshots** : capture un état de la VM à un instant T avant une modification critique.

- **Rollback facile** en cas de problème après une mise à jour ou un changement système.

📌 **Exemple d’utilisation des snapshots** : ➡️ Mise à jour critique d’une VM ➝ Snap avant l’update ➝ Update échoue ? ➝ Retour au snapshot sans perte.



**📑 2️⃣️ Clonage et Maquettes Virtuelles**

🔹 **Clonage rapide** : création de multiples copies d’une VM en quelques minutes.

🔹 **Maquettes complètes** en dupliquant une VM modèle (ex: simulateur de réseau d’entreprise).

📌 **Utilisation courante** : ✅ Tester des architectures réseau. ✅ Simuler un environnement client-serveur. ✅ Développer et tester des applications sur différents OS.



**⚠️ 3️⃣ Limitations de VMware Workstation**

❌ **Pas adapté pour la production** :

- Destiné aux **tests et maquettes**, pas à l’hébergement de services critiques.
- Nécessite des alternatives comme **VMware ESXi** pour de la production.

❌ **Logiciel propriétaire et payant** :

- Nécessite une **licence** (contrairement à des solutions open-source comme VirtualBox).
- Pas de modification possible du code source.

❌ **Courbe d’apprentissage** :

- Concepts comme la **gestion des snapshots, réseau virtuel, BIOS/UEFI** à assimiler.



**🔧 4️⃣️ Premiers Pas avec VMware Workstation**

🛠️ **BIOS/UEFI** : possibilité de modifier la séquence de démarrage de la VM.

🔌 **Gestion des périphériques USB** : une clé USB branchée sur l’hôte peut être détectée automatiquement dans la VM.

⌨️ **Capture du clavier et de la souris** :

- Pour sortir de la VM ➝ **CTRL + ALT**.
- Pour envoyer un **CTRL + ALT + SUPPR** dans la VM ➝ bouton dédié.

📌 **Options utiles** dans la barre de VMware Workstation :

✅ Adaptation automatique de l’affichage.

✅ Gestion des snapshots en un clic.
