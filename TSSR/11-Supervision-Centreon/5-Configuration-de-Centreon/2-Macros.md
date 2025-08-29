## **🧩 Définition & rôle des macros**

Les **macros** sont des **variables dynamiques** utilisées dans les commandes, notifications, événements, etc. Elles permettent de **variabiliser** des valeurs (ex : IP, seuils, chemins) pour ne pas avoir à dupliquer des commandes statiques.

🧠 **Syntaxe** : elles sont toujours encadrées par des **dollars** : `$MACRO$`

👉 Elles sont remplacées **au moment de l’exécution de la commande** par leur valeur réelle.



## **📚 Les 5 types de macros**

### **🧱 1. Macros *standards***

- **Issues de Nagios**, intégrées nativement (non modifiables).
- Utilisées dans commandes, notifications, events, etc.
- Exemple : `$HOSTADDRESS$` = IP de l’hôte.
- 📖 Liste complète : [Nagios Macro Reference](https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/3/en/macrolist.html)



### **🧰 2. Macros *ressources***

- Définies manuellement → **visibilité globale** sur le serveur.
- Nom : `$USERn$` où n = 1 → 256.
- Utilisées pour les **chemins** de plugins, scripts, etc.
- Exemples :
  - `$USER1$` = /usr/lib64/nagios/plugins
  - `$CENTREONPLUGINS$` = /usr/lib/centreon/plugins (nouveau format accepté depuis 18.10)
- 🔧 Création via **Configuration → Pollers → Resources**



### **🧪 3. Macros *à la demande* (ou d’environnement)**

- Récupèrent une **valeur en temps réel** d’un **autre hôte ou service**.
- Syntaxe : `$HOSTSTATE:nomhôte$`
- ⚠️ Attention : **consomment beaucoup de ressources** → à utiliser avec parcimonie.



### **🧮 4. Macros *arguments***

- Représentent les **arguments d'une commande**.
- Nom : `$ARGn$` avec n = 1 → 32
- Exemple typique :
  - `--warning-average=$ARG1$`
  - `--critical-average=$ARG2$`
- De plus en plus **remplacées par les macros personnalisées** (plus claires, plus souples).

### **🎯 5. Macros *personnalisées***

- 🔥 Le top pour des commandes *lisibles et génériques*.
- Deux types :
  - `$_HOSTnomdelamacro$` → assignée à un hôte
  - `$_SERVICEnomdelamacro$` → assignée à un service
- Exemple :
  - `$_HOSTSNMPVERSION$`, `$_SERVICEWARNING$`
- ✅ Avantages :
  - *Noms explicites*
  - *Pas limité à 32 valeurs*
  - *Redéfinissables par hôte/service*

