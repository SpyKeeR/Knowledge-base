# Recup MDP > ROMmon

## **🧾 Registre de configuration : rôle et valeur par défaut**

Le **registre de configuration** (Config Register) contrôle la séquence de boot. Il peut :

- Ignorer la config de démarrage
- Passer en mode ROMmon
- Choisir l'image IOS à charger

🔍 Pour voir sa valeur : `show version`

🧩 Valeur par défaut = **0x2102** → boot normal avec lecture de la NVRAM



## **🛠 Modifier le registre de configuration**

2 méthodes :

1.  **Depuis l’IOS** :

    - **En mode conf : `config-register 0x2142` → Ignorer la startup-config**
    - **La nouvelle valeur s’applique après un redémarrage**

2.  **Depuis le ROMmon** :

    - `confreg 2142` → **même effet, utile en cas de perte de mot de passe**

🧠 **0x2142** = ignore la config sauvegardée

🧠 **0x2102** = valeur normale (à remettre après la manip)



## **⚙️ Séquence de démarrage et lien avec la config**

![](../../../media/Cours-Infrastructures-réseaux-Recup-MDP-ROMmon-image1.png)

Ordre normal du boot :

1.  **POST** (tests matos)
2.  **Chargement de l’IOS depuis la Flash**
3.  **Lecture du registre de config (boot normal ? spécial ?)**
4.  Si **0x2102** : il charge la **startup-config**
5.  Si **0x2142** : il **ignore** la startup → prompt sans config

👀 Le **mot de passe enable** est dans la startup-config → on peut le contourner en la **copiant dans la running**, sans reboot.



## **🔓 Récupération de mot de passe (procédure)**

1.  **Accès au mode ROMmon** : Faire un **Ctrl + Break** au démarrage
2.  **Ignorer la config de démarrage** : Dans ROMmon : `confreg 0x2142`

    - **Puis reset pour rebooter**

3.  **Copier la startup-config dans la running** : `copy startup-config running-config` → on récupère la conf sans appliquer les passwords
4.  **Changer le mot de passe enable** : `enable secret Nouveau-MDP`
5.  **Sauvegarder la nouvelle conf** : `copy running-config startup-config`
6.  **Remettre le registre à 0x2102** : `config-register 0x2102` + `copy run start` + `reload`

🔁 L’équipement redémarrera ensuite en mode normal, avec le nouveau mot de passe actif.
