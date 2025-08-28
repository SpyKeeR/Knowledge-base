# Gestion avancée DHCP

## **🛡️ Autorisation dans un domaine AD**

Un **serveur DHCP sous Windows Server doit être autorisé** dans Active Directory ✅

➡️ Sans autorisation, le service **ne démarre pas** 

➡️ Cette sécurité **évite les serveurs DHCP pirates** dans le domaine 

🔸 Ce n’est **pas requis** sur des serveurs DHCP non-Windows (Linux, pfSense...)



## **💾 Sauvegarde des données DHCP**

Deux types de sauvegardes pour préserver la base DHCP :

- **Synchrone** 🕒 : automatique, toutes les heures — stockée dans le dossier backup, utile en cas de plantage soudain
- **Asynchrone** 📁 : manuelle — lancée à la main via console ou PowerShell, à faire avant maj ou modif importante



## **🔄 Restauration de la base DHCP**

En cas de **corruption** ou de **perte**, tu peux restaurer une **sauvegarde valide** 

💡 Important : **vérifie l’intégrité** avant restauration (risque d'incohérence) 

⚠️ Évite les déplacements manuels du dossier de base — risque de perte ou erreur de registre



## **⚖️ Fractionnement d'étendue (Split Scope)**

Permet de **répartir une plage d'adresses** entre deux serveurs DHCP 🧩

➡️ Exemple : 192.168.0.1 à .254

- Serveur A : 20% (192.168.0.1 → .50)
- Serveur B : 80% (192.168.0.51 → .254)

🎯 Objectif : **tolérance de panne + répartition de charge** 

🧠 Très utile en cas d’indisponibilité temporaire d’un des serveurs ou pour éviter un point unique de défaillance.



## **🤝 DHCP Failover : haute dispo assurée**

Le **DHCP Failover** permet à un **second serveur DHCP** d’être prêt à prendre le relais

🔹 Il est **opérationnel**, mais ne distribue pas tant que le principal est actif 

🎯 Objectif : **continuité de service**, tolérance aux pannes, montée en charge rapide

💡 Config typique : **mode load balancing** (50/50) ou **hot standby** (serveur principal actif, secours en attente)



## **🔒 Sécurité renforcée avec filtrage MAC**

On peut restreindre l’accès IP par **filtrage d’adresse MAC**

➡️ Exemple : **Autoriser** l’attribution d’IP uniquement à certaines machines (MAC whitelist) / **Bloquer** les postes non autorisés  
🛡️ Ce filtrage peut aussi être géré **au niveau des switches**, pour une sécurité en couche 2 (NAC-like)



## **📋 Logs DHCP : une mine d’or à ne pas négliger**

Chaque jour, le serveur DHCP génère un fichier de **log dans son répertoire de base**

🕵️‍♂️ En cas de souci (baux non attribués, conflits...), pense à vérifier :

- **Journaux d’événements Windows** (console Événements)
- **Journaux DHCP** (dans %windir%\System32\dhcp\


