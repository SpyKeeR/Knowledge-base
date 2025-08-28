# **🌍 Qu’est-ce que la résolution DNS ?**

Pour communiquer, une machine doit convertir les **noms de domaine** en **adresses IP**. Cette résolution peut se faire :

- 🏠 **Localement** via /etc/hosts
- 📡 **Via un serveur DNS** (défini dans /etc/resolv.conf ou avec nmcli)



## **🧠 Ordre d’interprétation**

L'ordre est défini dans /etc/nsswitch.conf (clé hosts:).

Exemple typique :

- 🔎 hosts: files dns → on lit d’abord /etc/hosts, puis on interroge les DNS

Sur une machine avec interface graphique, c’est souvent :

- 🧾 files mdns4_minimal [NOTFOUND=return] dns myhostname  
  👉 ordre : /etc/hosts → mDNS (*.local) → DNS → nom local du poste



## **⚙️ Configuration du client DNS**

Tu peux configurer le DNS de 2 façons :

- 🧰 **Avec nmcli (NetworkManager)** → `nmcli connection modify <nom_connexion> ipv4.dns "10.0.0.3 10.100.0.3"`
- 🧾 **Dans /etc/resolv.conf** :
  - `nameserver` : définit un ou plusieurs serveurs DNS (ex: 10.0.0.3)
  - `search` : suffixes DNS à ajouter automatiquement (ex: demo.uni, ad.campus-uni.fr)
  
  Résultat : ping pc1 testera pc1.demo.uni, puis pc1.ad.campus-uni.fr



## **⚠️ Attention avec le DHCP**

Si la config IP vient du **DHCP**, le fichier /etc/resolv.conf peut être **écrasé automatiquement**.

Donc même si tu mets tes propres DNS dedans, ils risquent d’être remplacés 😬

