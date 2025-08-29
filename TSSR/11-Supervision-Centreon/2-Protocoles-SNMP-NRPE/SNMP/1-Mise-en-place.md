# Mise en place

## **🪟 Mise en place du protocole SNMP sous Windows**

- Installation via PowerShell ➤ `Install-WindowsFeature -Name SNMP -IncludeManagementTools` (Avant Win 10 20H2/11)
- Autre méthode ➤ `Add-WindowsCapability -Online -Name "SNMP.Client~~~~0.0.1.0"`
- Accès à la config ➤ `services.msc`, clic droit sur "Service SNMP" → onglet **Sécurité**
- Configuration limitée à **SNMPv1/v2c**, pas de SNMPv3 natif (📌 choix de Microsoft pour pousser **WMI**)
- **Redémarrage du service nécessaire** après modification
- SNMP sous Windows reste limité : peu d'options, pas de chiffrement, pas d’authentification forte
- Pour utiliser **SNMPv3**, il faut passer par des **solutions tierces**



## **🐧 Mise en place du protocole SNMP sous Debian**

- Installation ➤ `sudo apt-get install snmpd`
- Fichier de configuration ➤ `/etc/snmp/snmpd.conf`
- Configurable : **communautés**, **adresses IP autorisées**, **MIB exposées**, **access control**
- Redémarrage du service requis ➤ `sudo systemctl restart snmpd`
- Compatible **SNMPv1/v2c/v3** → 🔐 SNMPv3 possible avec gestion d’utilisateur, authentification, chiffrement



## **🧰 Outils d’interrogation SNMP (Linux)**

SNMP fonctionne sur la base de **MIBs** (arborescence hiérarchique d'objets identifiables par des OID)

- `snmpwalk` ➤ interroge une branche complète à partir d’un OID de base  
    🟢 Exemple ➤ `snmpwalk -v2c -c public localhost` 
    🔍 Variante ➤ -Of pour noms, -On pour OIDs

- `snmpget` ➤ récupère une info précise via un OID ciblé  
    🟢 Exemple ➤ `snmpget -v2c -c public localhost <OID>`

- Options importantes :  
    • -v1 / -v2c / -v3 → version du protocole  
    • -c → nom de la **communauté** (comme un mot de passe en v1/v2c)

