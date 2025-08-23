# Généralisation sysprep

**🎯 Définition**

Une **image de référence** (ou "master") est un système d'exploitation **prêt à l'emploi** :

- **Logiciels** de l'entreprise préinstallés.
- Configuration prête à démarrer.



## **🔧 Création d'une Image de Référence**

1.  **Installer l'OS** sur un ordinateur avec une configuration **standard** de l'entreprise.
2.  **Configurer** l'ordinateur :
    - **Installer les drivers nécessaires.**
    - **Rendre le système fiable (mise à jour, etc.).**
3.  **Installer les applications de base** et les **applications métiers** utilisées quotidiennement.
4.  Préparer le système à être cloné avec **Sysprep** :
    - Outil qui réinitialise les paramètres personnalisés.



##  **⚙️ Utilisation de Sysprep**

1.  Lancer **Sysprep** en mode **audit** pour la configuration initiale. (CTRL+SHIFT+F3 > durant OOBE)
2.  **Généraliser** l’image pour rendre chaque machine unique, en générant un **SID** différent pour chaque poste (via l'option /generalize).
3.  **Capturer l'image** à froid : Capture l’image du système sans modifications en cours / Cette image peut être déployée sur plusieurs machines.



## **💡 Commandes et Options de Sysprep**

- **Commandes principales** :
  - /oobe (Out-of-Box Experience) : Initialisation après installation.
  - /generalize : Prépare l’image pour la généralisation.
  - /shutdown : Éteint le système après préparation.
- **Audit Mode** : Prépare le système avant la capture pour installer des logiciels supplémentaires. (CTRL+SHIFT+F3 > durant OOBE)



## **⚠️ Considérations et Limitations**

- **Incompatibilités** avec certains rôles de serveur et **applications Microsoft**.
- **Applications liées aux profils utilisateurs** doivent être désinstallées avant la capture.
- **Comptes utilisateurs locaux** : peuvent être conservés ou supprimés, sauf le **compte administrateur** qui est réinitialisé.



## **📋 Astuce**

Pour conserver certains pilotes ou applications spécifiques :
- Utilisez la clé de registre HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Windows\PersistAllDeviceInstalls à 1 pour conserver

Pour consulter les journaux de Sysprep :
- Dossier C:\Windows\System32\Sysprep\Panther pour les erreurs et détails.

