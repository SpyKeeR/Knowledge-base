# Config snmpd.conf - OLD

🔧 Paquets à installer ➤ snmp, snmpd

📁 Sauvegarde du fichier d'origine ➤ `cp /etc/snmp/snmpd.conf /etc/snmp/snmpd.conf.bak`

🧼 Conseil : repartir d’un fichier **vierge** pour éviter la confusion



## **📡 com2sec ➤ Définir l’accès primaire (v1/v2c)**

com2sec = *Community to security name* 

Fait le lien entre une **communauté SNMP** (genre "public") et un **nom de sécurité**, depuis une IP ou sous-réseau

- Exemple : `com2sec notSecure 127.0.0.1 public`

🔐 On donne un nom (notSecure) à la communauté public accessible uniquement en local 

🌐 On peut limiter à localhost, ou élargir à 192.168.1.0/24 selon les besoins 

🧱 C’est la **porte d’entrée** du système SNMP v1/v2c



## **🧩 group ➤ Lier un utilisateur à un groupe**

group = Associe un **nom de sécurité** (com2sec) à un **groupe SNMP** 

Un groupe regroupe plusieurs users ayant les **mêmes droits**

- Exemple : `group readonlyGroup v2c notSecure`
- v1 et v2c nécessitent chacun une entrée group, même si c’est le même nom
- 🔑 Le groupe est ensuite lié à une **view** et à des **droits d'accès** (voir plus bas)



## **🔎 view ➤ Définir ce qu’on peut consulter**

view = Filtre qui définit **quelles parties de la MIB** sont visibles pour un groupe > Exemple : view complete included .1 ➤ accès à **toute la MIB**

- On peut restreindre : `view systemOnly included .1.3.6.1.2.1.1` (info système uniquement)

☂️ Une view sert à cloisonner les infos selon les besoins : sécurité, confidentialité, granularité



## **🔐 access ➤ Définir les droits d’accès finaux**

access = Associe un **groupe** à une **view**, en définissant les **droits** : read (lecture), write (écriture), notify (trap), modèle de sécurité, précision...

Exemple complet : `access readonlyGroup "" any noauth exact complete none none`

- "readonlyGroup" ➤ groupe défini
- "" ➤ nom d’utilisateur (vide ici car v1/v2c)
- any ➤ n’importe quel contexte SNMP
- noauth ➤ pas d’authentification (cas de v1/v2c)
- exact ➤ correspondance exacte entre utilisateur/contexte
- complete ➤ **view autorisée en lecture**
- none ➤ pas de view en écriture
- none ➤ pas de view pour les traps



### **🧪 Tester et valider la config**

1.  🔁 Redémarrer le service ➤ `systemctl restart snmpd`
2.  🔍 Tester avec une requête simple : `snmpwalk -v2c -c public 127.0.0.1 1.3.6.1.2.1.1.1.0` → Doit renvoyer le sysDescr
3.  💡 Pour plus d’infos système : `snmpwalk -v2c -c public 127.0.0.1 1.3.6.1.2.1.25.1.1`



