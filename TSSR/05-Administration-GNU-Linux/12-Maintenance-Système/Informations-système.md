# Informations système**🔍 Version du système**

- cat /etc/debian_version ou /etc/os-release → Affiche la version Debian (ex : 10.9)  
  💡 D'autres distributions ont des fichiers similaires dans /etc.



**🧠 Infos sur le noyau**

- uname -a → Donne le noyau, l’archi, le nom d’hôte, la date de build, etc.



**🔧 Processeur**

- Lscpu → Résumé détaillé : architecture, modèle, threads, cores, etc.



**🔧 Mémoire**

- Lsmem → Résumé détaillé



**💻 Matériel PCI**

- Lspci → Liste les composants PCI : cartes réseau, son, GPU, etc.



**🔌 Périphériques USB**

- Lsusb → Affiche tous les périphériques USB connectés.



**💽 Gestion des disques**

- df -h → Vue de l’espace disque utilisé/dispo par partition (format lisible)
- du -sh / → Taille totale utilisée par / (donne une idée rapide de l’encombrement global)
- Lsblk → Arborescence des disques/partitions (montages, tailles, types)



**📂 Fichiers et périphériques**

- file <fichier> → Détecte le type réel d’un fichier (binaire, texte, script, etc.)
- lsof (List Open Files) → Liste **tous les fichiers ouverts** par les processus  
  💡 Utile pour savoir qui utilise un fichier ou pour diagnostiquer un disque occupé
