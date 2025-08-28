# Fichiers de configuration

## **📁 /etc/bind/named.conf — Fichier principal**
```bash
acl rsxclts { 127.0.0.0/8; 192.168.53.0/24; 192.168.1.0/24; }; // Déclaration d'une ACL nommée 'rsxclts' regroupant les réseaux clients autorisés

include "/etc/bind/named.conf.options"; // Inclut les options globales du service  
include "/etc/bind/named.conf.local"; // Inclut les zones locales (créées par l'admin)  
include "/etc/bind/named.conf.default-zones"; // Inclut les zones par défaut (ex: localhost, loopback)
```

**⚙️ /etc/bind/named.conf.options — Options générales**
```bash
options {  
directory "/var/cache/bind"; // Répertoire de travail de BIND (où il stocke les fichiers temporaires)  
forward only; // Ne demande JAMAIS les serveurs racines, utilise SEULEMENT les forwarders  
forwarders { 9.9.9.9; }; // DNS vers lequel rediriger les requêtes (ex: Quad9)  
allow-query { rsxclts; }; // Autorise les requêtes uniquement depuis les IPs définies dans 'rsxclts'  
allow-recursion { rsxclts; }; // Autorise la récursivité (résolution complète) uniquement pour ces IPs  
dnssec-validation no; // Désactive la vérification DNSSEC  
version "none"; // Masque la version de BIND dans les réponses DNS  
};
```


## **📦 /etc/bind/named.conf.default-zones — Zones par défaut**
```bash
zone "localhost" {  
type master; // Le serveur est maître de cette zone  
file "/etc/bind/db.local"; // Fichier de zone contenant les enregistrements  
};

zone "127.in-addr.arpa" {  
type master;  
file "/etc/bind/db.127"; // Zone de reverse DNS pour 127.0.0.1  
};

zone "0.in-addr.arpa" {  
type master;  
file "/etc/bind/db.0"; // Zone de reverse pour 0.0.0.0 (rarement utilisée)  
};

zone "255.in-addr.arpa" {  
type master;  
file "/etc/bind/db.255"; // Zone de reverse pour 255.255.255.255 (broadcast)  
};
```

## **🛰️ /etc/bind/named.conf.local — Zone personnalisée & redirecteur conditionnel**
```bash
zone "demo.eni" {  
type master; // Serveur maître de la zone  
file "/etc/bind/db.demo.eni"; // Fichier de zone défini manuellement (à créer)  
};

zone "macharmantevoisine.eni" {  
type forward; // Déclare une zone de redirection conditionnelle  
forward only; // Utilise uniquement le redirecteur défini  
forwarders { 10.0.0.53; }; // Serveur DNS vers lequel rediriger les requêtes pour ce domaine  
};
```
