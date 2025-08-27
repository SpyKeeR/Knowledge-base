# Accès distant - SSH

**🔑 Pourquoi SSH ?**

- Telnet (port 23) ❌ = non chiffré ➜ risques de sécurité.
- SSH (port 22) ✅ = chiffré ➜ communication sécurisée avec l’équipement.

SSH est donc **le protocole à privilégier** pour l'administration distante des équipements Cisco.



## **🛠️ Étapes de configuration SSH**

Voici les 5 étapes à suivre pour une config SSH propre et fonctionnelle :

1.  **Nom de domaine** : `ip domain-name nom_du_domaine` (requis pour générer les clés RSA)
2.  **Clés RSA** : `crypto key generate rsa` (128 bits min, 2048 recommandé) ➜ Pour supprimer plus tard : `crypto key zeroize rsa`
3.  **Forcer SSH version 2** : `ip ssh version 2` (plus sécurisé que v1)
4.  **Utilisateur local** : `username nom_utilisateur secret mot_de_passe`
5.  **Activation sur les lignes VTY** : se placer sur les lignes (`line vty 0 4`) puis `login local` (pointer le magasin d'identifiant local) et `transport input ssh`.



### **🧪 Et après la config ?**

- Test SSH avec un client comme **PuTTY** ou en ligne de commande : `ssh nom_utilisateur@adresse_IP_du_switch`
- Tu peux vérifier que les connexions passent bien en SSH avec :  
  ➜ `show ip ssh` (vérifie la version, l’état, les timeouts)  
  ➜ `show run | section vty` (regarde bien transport input ssh)  
  ➜ `show users` (voir qui est connecté et comment)



### **🚫 Bonnes pratiques complémentaires**

- Désactiver Telnet si présent : `transport input ssh`
- Activer un **timeout** d'inactivité : `exec-timeout 5 0` (5 min)
- Limiter le nombre de connexions simultanées : `line vty 0 4` → `login local` + `access-class` si filtrage IP

![](../../media/Cours-Infrastructures-réseaux-Accès-distant-SSH-image1.png)



### **🔎 show version et modules de chiffrement**

Pour que **SSH fonctionne**, l’IOS doit inclure les **modules de chiffrement**.

Verification : `show version`

✅ Présence de **K9** dans le nom de l’image IOS → chiffrement disponible (exemple : c2960-lanbasek9-mz.150-2.SE2.bin)

Sans le K9 → pas de SSH possible (juste Telnet en clair ❌)



### **📋 Commandes de vérification SSH**

- `show ip ssh` → Affiche la **version SSH**, le **timeout**, le **nombre de tentatives de connexion**, etc.  
  Exemple : SSH Enabled - version 2.0
- `show ssh` → Affiche les **sessions actives** SSH et leurs détails (utilisateur, IP source, durée...)


