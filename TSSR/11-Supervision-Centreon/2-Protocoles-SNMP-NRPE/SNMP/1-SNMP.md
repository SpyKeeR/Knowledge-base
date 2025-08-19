# SNMP📡 **Protocole SNMP – Fonctionnement et Architecture**

Le protocole **SNMP (Simple Network Management Protocol)**, défini par l’IETF, est un **pilier de la supervision réseau** 📊. Il permet d’interroger, surveiller et modifier des paramètres à distance sur des équipements via un serveur de supervision.



🔄 **🔧 Fonctionnement général**

SNMP repose sur 4 piliers :

• **Superviseur** (ex: Centreon)

• **Agents supervisés** (matériels ou OS ayant un service SNMP actif)

• **Variables** issues des **MIB** sous forme d’**OID**

• Le **protocole SNMP** (transports, commandes, versions)

➡️ Le superviseur interroge les agents via le port **161** (TCP ou UDP), et reçoit des **TRAPs** (alertes) sur le port **162**.



🔐 **🔢 Versions de SNMP**

• **v1** : ancienne, non sécurisée, à proscrire

• **v2c** : la plus utilisée ; sécurité par “**communauté**” (ex : public/private) à **modifier absolument**

• **v3** : plus sécurisée (authentification, chiffrement), mais encore **peu supportée** par certains matériels

✍️ Astuce : toujours **préférer v3 si possible**, sinon **v2c avec restriction IP + modification de la communauté**.



🗂️ **📁 MIB (Management Information Base)**

Les **MIB** sont des **bases hiérarchiques** contenues dans les agents, accessibles par le superviseur.

Elles décrivent **chaque info supervisable**, sous forme d’**OID** (Object Identifier).

Exemples :

• **1.3.6.1.2.1.2.2.1.2** → IF-Descr (nom interface réseau)

• **1.3.6.1.4.1.9** → Branche Cisco (entreprise enregistrée via IANA)

📍 Les **OID** sont des **suites numériques** (identifiants) normalisées.

Certaines MIB sont **génériques (MIB-2)**, d'autres sont **propriétaires** (Cisco, HP…).



🚨 **📬 Traps SNMP**

Les **TRAPs** sont des alertes **envoyées par les agents** au superviseur sur le port **162** (souvent en **UDP**) quand un événement anormal survient.

➡️ Exemple : perte de lien, température trop haute, surcharge CPU…

⚙️ Elles doivent être **activées/configurées** côté agent, et **écoutées** côté superviseur.
