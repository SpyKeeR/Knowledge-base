# Configuration des zones

## **🌍 Déclaration d'une zone DNS dans BIND9**

➡️ Tout se passe dans le fichier : **/etc/bind/named.conf.local :** Tu vas y **déclarer chaque zone** (directe ou inverse), en indiquant :

- le nom de la zone (zone)
- le **type** : master (primaire) ou slave (secondaire)
- le chemin du fichier de base de données DNS : file
- (optionnel) l’IP autorisée à faire un transfert de zone : allow-transfer



## **🧭 Zone DNS primaire (master)**

C’est ici que réside **la base de données officielle** de la zone.

**🔹 Zone directe (résolution nom ➡️ IP)** 💡 

Ici, le fichier db.mondomaine.local contient les enregistrements (A, MX, CNAME, etc.)
  ```bash
  zone "mondomaine.local" {  
  type master;  
  file "/etc/bind/db.mondomaine.local";  
  allow-transfer { 10.11.0.53; };  
  };
  ```
**🔹 Zone inverse (résolution IP ➡️ nom)** 

💡 Ce type de zone est utilisée pour les recherches **PTR** (Reverse DNS)
```bash
  zone "42.168.100.in-addr.arpa" {  
  type master;  
  file "/etc/bind/db.100.168.42";  
  };
```


## **🔁 Zone DNS secondaire (slave)** 💡 

masters indique l’IP du DNS maître, et le fichier est généré automatiquement lors du transfert.

Le serveur secondaire **copie la zone depuis un serveur maître**.
  ```bash
  zone "mondomaine.local" {  
  type slave;  
  masters { 10.15.3.10; };  
  file "/etc/bind/db.slave.mondomaine.local";  
  };
```
## **🔐 Accès aux données de zone**

- `allow-transfer` : contrôle les IP autorisées à copier la zone (ex. : 10.11.0.53)
- `allow-query` : contrôle les IP autorisées à faire des requêtes (ex. : any; ou sous-réseau défini)

