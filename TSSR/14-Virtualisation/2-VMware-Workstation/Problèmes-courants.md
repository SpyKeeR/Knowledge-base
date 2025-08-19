# Problèmes courants**💿 Gestion du lecteur CD/DVD**

- Après l’installation d’un OS, VMware **déconnecte automatiquement** le lecteur CD/DVD  
  → Évite le **redémarrage involontaire** sur le média d’installation

- Pour réinstaller un OS ou corriger une installation :
  - Accéder à **Settings** de la VM
  - Reconnecter manuellement le lecteur CD/DVD
- ⚠️ Ce comportement est **systématique après chaque installation**



**🌐 Problèmes de connexion réseau**

- Le **réseau peut devenir instable** ou inaccessible selon le mode sélectionné (Bridged, NAT, etc.)
- Solutions :
  - Vérifier que la carte réseau est bien activée dans les paramètres
  - Désactiver puis réactiver les cartes réseau de la **machine physique**
- 🔁 Ce type de bug peut survenir après des **changements fréquents de configuration réseau**



**🧮 Gestion de l’espace disque**

**💥 Espace disque hôte insuffisant**

- La VM utilise le stockage local de l’hôte → risque de **saturation** du disque physique
- À surveiller :
  - L’**emplacement** du dossier contenant les fichiers de la VM
  - Le **volume de données disponible** sur le disque hôte
- Solution :
  - Déplacer les fichiers de la VM vers un disque plus grand

**📦 Espace disque invité insuffisant**

- Une VM qui approche de la saturation peut **se figer ou s’arrêter brutalement**
- Solutions :
  - Ajouter un nouveau disque dur via les **paramètres de la VM**
  - Ou tenter un **redimensionnement** (plus complexe à gérer)
- 🧠 Important : bien **dimensionner le disque dès la création** de la VM



**🗄️ Solutions de stockage invité**

- Ajout de disques spécifiques dédiés aux VM :
  - Stockage sur disque local
  - Ou via un **partage réseau** (NAS, SAN, etc.)
- Certains protocoles comme **iSCSI** seront abordés dans un module ultérieur

**🖥️ Accès au BIOS de la VM**

- Accès possible via deux méthodes :
  - Appuyer sur **Échap** dès l’apparition du logo VMware
  - Ou via **Power > Power to Firmware** pour entrer directement dans le BIOS
- Utilité :
  - Modifier la **séquence de démarrage**
  - Configurer les **périphériques de boot**
