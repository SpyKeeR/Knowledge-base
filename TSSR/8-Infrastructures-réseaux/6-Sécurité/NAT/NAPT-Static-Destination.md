# NAPT Static/Destination**📬 NAPT statique (Destination NAT) – C’est quoi ?**

Ici, on ne traduit pas l’adresse source mais **l’adresse de destination**, en se basant sur **le port ciblé** :

- Permet de rendre accessibles des services internes (web, mail…) depuis Internet
- Une seule IP publique peut être utilisée pour **plusieurs hôtes privés**, via **des ports différents**
- Chaque règle associe une combinaison **IP privée + port privé ↔ IP publique + port public**

🧠 Exemple classique :

- TCP 192.0.2.50:80 → redirigé vers 172.25.10.1:80 (serveur Web)
- TCP 192.0.2.50:25 → vers 172.25.10.2:25 (SMTP)
- TCP 192.0.2.50:110 → vers 172.25.10.2:110 (POP3)

🎯 On parle aussi de **PAT statique** (Static Port Address Translation)



**🛠️ Configuration sur Cisco IOS – Port Forwarding**

Le principe est similaire au NAT dynamic, mais ici chaque port a sa **règle dédiée** :

- 🏠 ip nat inside → Déclare l’interface interne (LAN)
- 🌐 ip nat outside → Déclare l’interface externe (WAN)
- 🔁 ip nat inside source static tcp X.X.X.X portA Y.Y.Y.Y portB  
  → Redirige les connexions sur Y.Y.Y.Y:portB vers X.X.X.X:portA

Dans ton cas :

- Le serveur web (172.25.10.1:80) est mappé sur 192.0.2.50:80
- Le serveur SMTP (172.25.10.2:25) est mappé sur 192.0.2.50:25
- Le serveur POP3 (172.25.10.2:110) est mappé sur 192.0.2.50:110

🔐 ⚠️ Attention : ouvrir des ports vers des machines internes augmente

la **surface d’exposition**, bien sécuriser les services derrière !



**🔍 Vérification🎯**

- 📋 show ip nat translations → Affiche les mappings actifs (IP + port)
- 📊 show ip nat statistics → Statistiques NAT globales : connexions, translations, erreurs…

![](../../../media/Cours-Infrastructures-réseaux-NAPT-Static-Destination-image1.png)

