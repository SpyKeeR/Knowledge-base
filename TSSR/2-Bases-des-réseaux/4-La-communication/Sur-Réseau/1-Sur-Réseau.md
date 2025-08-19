# Sur-RéseauLe **supernetting** est l’inverse du **subnetting** : il fusionne plusieurs sous-réseaux contigus en un réseau plus grand pour **réduire la taille des tables de routage** et **simplifier l’administration**.



**📌 1. Pourquoi Faire du Sur-Réseau ?**

✅ **Moins d’entrées en table de routage** → Optimisation du routage.

✅ **Administration simplifiée** → Moins de gestion des IP.

✅ **Moins de trafic de mise à jour des routes** → Efficacité sur Internet (CIDR).



**🛠️ 2. Principe et Exécution**

🔹 **Sans sur-réseau** (4 routes distinctes) :

192.168.0.0/24, 192.168.1.0/24, 192.168.2.0/24, 192.168.3.0/24

🔹 **Avec sur-réseau** (1 seule route) :

192.168.0.0/22

✅ Moins d’annonces, meilleure performance.

**Comment ?**

1️⃣ **Vérifier que les sous-réseaux sont contigus.**

2️⃣ **Trouver le préfixe CIDR** en repérant les bits communs.

- **Exemple** : 192.168.0.0 à 192.168.3.255 → /22 (22 bits fixes).  
  3️⃣ **Regrouper en un seul bloc** pour simplifier le routage.



**🎯 3. Avantages et Limites**

**✅ Avantages :**

- **Réduction des routes** → Moins de charge pour les routeurs.
- **Meilleure allocation des IP** → Moins de gaspillage.
- **Optimisation du routage Internet** (CIDR).

**❌ Limites :**

- **Moins de précision** dans l’allocation des IP.
- **Nécessité d’un bon alignement binaire**.
- **Potentiels conflits d’adresses** si mal planifié.
