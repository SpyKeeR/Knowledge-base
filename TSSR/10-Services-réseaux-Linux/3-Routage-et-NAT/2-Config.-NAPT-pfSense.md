# Config. NAPT pfSense

## **🌐 Introduction au NAT dans pfSense**

Le **NAT (Network Address Translation)** dans pfSense se configure via le menu **Firewall / NAT**.

Il existe **4 types de règles NAT** utilisables selon le besoin :

- **Port Forward** 🎯 : redirection de ports entrants (souvent sur l'interface WAN, mais pas que)
- **1:1 NAT** 🔁 : liaison stricte IP interne <-> IP publique
- **Outbound NAT** 📤 : traduction des adresses IP sortantes (trafic LAN → WAN)
- **NPt (IPv6)** 🧬 : NAT spécifique pour IPv6, mappage entre préfixes

📌 En gros : **Inbound** = "Port Forward" / **Outbound** = "Outbound NAT"



## **⚙️ Paramétrage du NAT de source (Outbound)**

Le **NAT sortant** permet aux hôtes internes (LAN) d’accéder à Internet via une IP publique du WAN.

### **✅ Mode automatique (par défaut)**

- **Activé après installation**
- Appliqué à tous les réseaux privés connus du routeur (interfaces locales + routes statiques)
- Les règles sont **non modifiables** dans ce mode
- Fonctionne bien pour un usage simple (accès Internet de base)

### **✍️ Modes personnalisables**

Pour éditer ou ajouter des règles manuellement, tu dois passer en :

- **Hybrid** 🔧 : pfSense garde les règles auto + tu peux en ajouter
- **Manual** 🛠️ : toutes les règles sont gérées à la main (mode expert)
- **Disable outbound NAT** ❌ : désactive complètement le NAT sortant (risqué si non maîtrisé)

📍 Accès : Firewall > NAT > Onglet **Outbound** 📍 Bouton radio en haut : choix du **mode de génération**

## **🚪 NAT de destination (Port Forward)**

Le **Port Forward** permet d’exposer des services internes à l’extérieur. Exemple : un serveur web local joignable via le port 80 public.

- Gère le **trafic entrant vers le LAN** depuis le WAN
- Permet de **rediriger un port WAN** vers une IP et un port LAN (avec ou sans translation de port)
- Peut aussi être utilisé entre réseaux internes

📍 Accès : Firewall > NAT > Onglet **Port Forward**

Tu peux y associer automatiquement une **règle de firewall** pour autoriser le flux correspondant (coché par défaut à la création).

## **🧬 Cas spécial : NAT IPv6 (NPt)**

- **NPt (Network Prefix Translation)** = NAT de préfixe IPv6
- Utilisé pour **mapper un préfixe interne vers un préfixe global**
- Fonctionne uniquement avec des **préfixes bien alignés** (même taille)

📍 Accès : Firewall > NAT > Onglet **NPt**

