# Configuration BIND9

## 📦 **Installation de BIND9**

Le serveur DNS utilisé est **BIND9**, très répandu pour les services DNS sous Linux.

👉 Paquet à installer : bind9 

👉 Service système : bind9 (géré via systemctl)



## 🧩 **Fichier de configuration principal**

Le fichier principal est **/etc/bind/named.conf**. Il ne contient que des *includes*, pour segmenter la conf :
```bash
include /etc/bind/named.conf.options ; # options globales
include /etc/bind/named.conf.local ; # zones locales (à voir plus tard)
include /etc/bind/named.conf.default-zones ; # zones par défaut et serveurs racines (à ne pas modifier)
```

📝 Ces fichiers sont lus dans l’ordre, attention à ne pas les perturber.

Les modifications principales se font dans named.conf.options.



## 🔐 **Définir des ACLs (listes de contrôle d'accès)**

💡 On utilise des *ACLs* (Access Control List) pour restreindre les requêtes : 

Exemple : `acl "resx_tss" { 127.0.0.1; 192.168.0.0/16; };`

➡️ Cette ACL peut ensuite être référencée dans les options pour contrôler les IP autorisées à interroger le serveur.



## ⚙️ **Configuration du fichier named.conf.options**

Ce fichier permet d'ajuster plusieurs éléments clés du résolveur :
```bash
forwarders { 9.9.9.9; }; # définit les DNS publics vers lesquels rediriger (ex : Quad9, Google, DNS FAI, etc.)
allow-query { "resx_tss"; }; # autorise les requêtes uniquement depuis les IPs de l’ACL
allow-recursion { "resx_tss"; }; # autorise la récursivité sur le même périmètre
version "unknown"; # masque la version de BIND pour des raisons de sécurité
dnssec-validation no; # désactive DNSSEC si pas nécessaire
```

🔒 On évite d'exposer trop d'infos sur le serveur, surtout en usage public.



## 📁 **Autres fichiers à connaître**

- /etc/bind/named.conf.default-zones → contient la conf des zones par défaut et la liste des serveurs DNS racines (à **laisser tel quel**)
- /etc/bind/named.conf.local → sera utilisé plus tard pour les zones locales



## 🧪 **Commandes utiles pour l’administration**

- `named-checkconf` → vérifie la **syntaxe globale** des fichiers de conf
- `named-checkzone` → vérifie la **syntaxe des fichiers de zones** (utilisé plus tard)
- `rndc reload` → recharge la configuration sans redémarrer le service
- `journalctl -u bind9` ou /var/log/syslog → voir les logs si souci de démarrage

## 📛 BIND est **très strict** sur la syntaxe : 
- points ; en fin de lignes,
- blocs bien fermés {}.
- Un oubli = erreur de démarrage.

