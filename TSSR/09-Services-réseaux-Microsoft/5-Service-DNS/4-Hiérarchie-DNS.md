# Hiérarchie DNS

Le système DNS est **hiérarchique** et fonctionne **du plus général au plus spécifique**. Chaque niveau est **séparé par un point (.)**.

### **🪐 Le domaine racine (Root)**

Tout commence au sommet 🌍

La **racine DNS** est symbolisée par un **point final (.)** invisible mais toujours présent.

🔹 Elle est gérée par **13 serveurs racine**, **répliqués mondialement** (NASA, ICANN, US Army, Universités...)

🔸 Ils **ne stockent pas tous les enregistrements DNS**, mais **redirectionnent vers les serveurs des TLD** (comme .fr, .com)

💡 Pour en savoir plus : recherche “DNS Root Servers” ou visite [www.root-servers.org](http://www.root-servers.org)

### **🏷️ TLD : Top-Level Domains**

Ce sont les domaines de **premier niveau** (juste après la racine) :

✅ .fr (France), .com (commercial), .org, .edu, .net, .de (Allemagne)… 🌐 Pour la **liste complète**, visite [www.iana.org](http://www.iana.org)

## **🧱 Structure complète d’un nom DNS**

Prenons [www.google.fr](http://www.google.fr).

- fr. = **TLD**
- google. = **domaine de second niveau**
- www. = **hôte (3ᵉ niveau)**
- . (final) = **racine (implicite)**

🎯 Le DNS lit **de droite à gauche** : d’abord la racine, puis le TLD, puis le domaine, puis l’hôte.

## **🛠️ Fonction d’un serveur DNS**

Un serveur DNS peut remplir plusieurs rôles :

### 🔹 **Résolveur DNS** (resolver) :

- Sert à **traduire les noms externes** (ex : [www.google.com](http://www.google.com))
- Interroge d'autres serveurs DNS si besoin

### 🔸 **Hébergeur DNS** (authoritative) :

- **Héberge une zone DNS** (ex : corp.local)
- Requis dans un environnement **Active Directory**

💡 Le même serveur peut jouer **les deux rôles**, ou ils peuvent être **séparés**, selon l’architecture réseau.



## **🔍 Bien définir les besoins avant le déploiement**

Avant d’installer un service DNS :

✅ Identifier si on a besoin de **résolution interne**, **externe**, ou les deux

✅ Déterminer s’il faut séparer les rôles ou les **centraliser**

✅ Vérifier si le DNS va servir pour un **AD** → dans ce cas, il doit **héberger** la zone AD

