# Mode usine

## **🧨 Réinitialisation d’un routeur aux paramètres d’usine**

Pour repartir de zéro sur un **routeur**, tu dois :

1.  **Effacer la config enregistrée** :

    - **erase startup-config → supprime la config NVRAM**
    - **Confirmer (en tapant entrée)**

2.  **Vérifier** que la config a bien disparu :

    - **show startup-config → renvoie “not present” si c’est bon**

3.  **Redémarrer** le routeur :

    - **reload → confirmer aussi (entrée)**

4.  À la relance :

    - Cisco te propose le **System Configuration Dialog**
    - Réponds **no** pour ne pas rentrer dans le wizard de configuration

Résultat final : prompt Router> sans aucune config 🎉



## **🧹 Cas particulier : effacer un VLAN.dat sur un commutateur**

Sur les **switchs**, les VLAN sont stockés dans un fichier à part :

- Supprimer le fichier VLAN : delete flash:vlan.dat
- Confirmer deux fois
- Ensuite, redémarrer (reload) pour repartir avec la base VLAN par défaut

⚠️ Si tu oublies cette étape, les VLANs restent même après un erase startup-config !

