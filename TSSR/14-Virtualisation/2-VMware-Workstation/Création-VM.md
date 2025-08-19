# Création VM**🧭 Étape 1 : Choix du mode d’installation**

- Sélectionner **« I will install the operating system later »** 🕒
- Évite que VMware configure l’OS automatiquement avec des **paramètres inadaptés**
- Permet une **maîtrise totale de la configuration** système

**💽 Étape 2 : Sélection du système d’exploitation**

- Choisir le **type de système d’exploitation** (Windows, Linux, etc.)
- Définir :
  - L’**architecture** (32/64 bits)
  - Le **type de processeur**
  - La **quantité de RAM** à allouer
- Adapter selon les **besoins spécifiques** de la VM



**🗃️ Étape 3 : Nommer la VM et définir son emplacement**

- Donner un **nom explicite** à la VM
- Définir le **répertoire de stockage** des fichiers :
  - ⚠️ Par défaut : Mes documents
  - ✅ Recommandé : **un dossier dédié par VM** pour plus d'organisation et de performances



**📦 Étape 4 : Configuration du disque virtuel**

- Définir la **taille du disque virtuel**
- Choisir le **mode de stockage** :
  - 🟢 Par défaut : **stockage dynamique** (croît selon l’usage)
  - 🔒 Préféré : **« Store virtual disk in a single file »** pour **éviter la fragmentation**
- Impacts directs sur les **performances** de la VM, notamment sous Windows

**🧰 Étape 5 : Personnalisation du matériel**

Via **« Customize Hardware »** :

- Ajouter / supprimer des **composants matériels** :
  - Lecteur DVD
  - Carte réseau (Host-only, NAT, etc.)
  - Disques durs supplémentaires
- Définir :
  - Le **nombre de CPU** 🧠
  - La **quantité de RAM** 💾
  - Le **type de connexion réseau**
  - Le **média d’installation** (ISO, partage réseau...)



**📀 Étape 6 : Gestion du média d’installation**

- Choisir la source d’installation :
  - ISO local
  - 📡 Partage réseau
  - CD/DVD physique
- Vérifier que la **VM dispose de tout pour démarrer l’installation**
