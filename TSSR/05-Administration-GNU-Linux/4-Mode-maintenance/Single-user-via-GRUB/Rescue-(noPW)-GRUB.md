# Rescue (noPW) - GRUB**1️⃣ Pourquoi utiliser cette méthode ?**
- Quand **tu as perdu** ou **tu ne connais pas** le mot de passe root 🔑.
- Elle te permet d'accéder **en root direct**, sans galère.

2️⃣ **Modifier la ligne de boot**

➡️ Sélectionne ton kernel avec les flèches ⬆️⬇️. ➡️ Appuie sur **E** pour éditer.

➡️ **Sur la ligne** qui commence par linux, ajoute à la fin : *init=/bin/bash*

**Important** : Pas d'espace entre init=/bin/bash et le reste de la ligne ❌.



**3️⃣ Démarrer en mode shell root**

➡️ Valide avec **CTRL+X** ou **F10**. ➡️ Bim 💥 tu arrives directement en **shell root** ➔ **Aucun mot de passe demandé** !



**4️⃣ Monter le système en écriture**

Le système de fichiers est en **lecture seule** (ro) ➔ donc tu dois remonter la racine en lecture/écriture : *mount -o remount,rw /*

**⚠️ Attention** : Si tu oublies ça, impossible de modifier des fichiers ou des mots de passe !



**5️⃣ Synchroniser les données**

Avant de tout couper : *sync*

Ça force l’écriture des données du cache vers le disque dur ➔ évite la perte de données 🛡️.



6️⃣ **Démontage propre du système**

Une fois les modifications effectuées, pour éviter toute corruption de données, **démonte proprement** la racine avant d’éteindre la machine : *umount /*

Cela garantit que tous les fichiers sont correctement libérés et prêts à être déconnectés sans risque !



**7️⃣ Éteindre la machine**

➡️ Comme on est en mode maintenance (pas de gestionnaire d'arrêt propre), on est obligé de forcer l'arrêt :

- Appuie quelques secondes sur **Power** 🔘 pour éteindre OU *echo 1 > /proc/sys/kernel/sysrq && echo b > /proc/sysrq-trigger*

**⚠️ Prudence** : Éteindre brutalement peut être risqué ➔ **Toujours faire sync puis umount / avant** !
