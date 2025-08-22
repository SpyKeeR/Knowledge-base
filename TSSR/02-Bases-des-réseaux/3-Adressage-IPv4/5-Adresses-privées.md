# Adresses privées

Les **adresses IP privées** sont utilisées pour **les réseaux internes** (domestiques, entreprises...), et ne sont **pas routables sur Internet**. Elles évitent l'épuisement des adresses IPv4 et améliorent la sécurité en bloquant l'accès direct depuis l'extérieur.


## **🗂 Les Plages d’Adresses IP Privées**

Selon la **RFC 1918**, trois plages ont été réservées :

| **Classe** | **Plage d'adresses** | **CIDR** | **Nombre d’adresses** | **Utilisation** |
|----|----|----|----|----|
| **A** | 10.0.0.0 - 10.255.255.255 | /8 | 16 777 216 | **Grandes entreprises**, infrastructures complexes |
| **B** | 172.16.0.0 - 172.31.255.255 | /12 | 1 048 576 | **Réseaux d’entreprises de taille moyenne** |
| **C** | 192.168.0.0 - 192.168.255.255 | /16 | 65 536 | **Réseaux domestiques et petites entreprises** |

### 🔹 **Pourquoi trois plages ?** → Pour s’adapter aux besoins :

✔ **Grandes entreprises** ➝ 10.0.0.0/8 (beaucoup de sous-réseaux)

✔ **Moyennes structures** ➝ 172.16.0.0/12

✔ **Petits réseaux** ➝ 192.168.0.0/16



## **🔍 Pourquoi Utiliser des Adresses Privées ?**

✔ **Économie d'adresses publiques** → Une seule IP publique pour **des milliers d’appareils** via **NAT**.

✔ **Sécurité** → **Pas accessibles directement** depuis Internet.

✔ **Flexibilité** → **Segmentation des réseaux** (sous-réseaux, VLAN...).

### 💡 **Attention** :

❌ **Une adresse privée ne permet pas d’accéder directement à Internet**.

❌ **Elles ne peuvent pas être routées en dehors du réseau interne**.



## **🔄 Comment une Adresse Privée Accède à Internet ?**

Un **routeur** (ou pare-feu) utilise le **NAT (Network Address Translation)** pour convertir une **adresse privée en adresse publique**.

### **Exemple : Réseau domestique**

- Routeur : 192.168.1.1
- PC : 192.168.1.10
- Connexion à un site web ➝ Le routeur **remplace** 192.168.1.10 par **son adresse IP publique**.

🛑 **Sans NAT, les adresses privées ne pourraient pas communiquer avec Internet** !

