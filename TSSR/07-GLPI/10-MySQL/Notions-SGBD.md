# Notions SGBD**🧠 Qu’est-ce qu’une base de données (B.D) ?**

🔹 Une **base de données** est un **espace de stockage** 🗄️

🔹 Elle permet de **stocker**, **administrer** et **manipuler** des données 📊

🔹 Elle peut être **interrogée** via des **requêtes** (ex : pour afficher ou reformater des infos)



**🛠️ SGBD & SGBDR**

- Un **SGBD** (Système de Gestion de Base de Données) permet de gérer l'accès aux données
- Un **SGBDR** (relationnel) organise les données en **tables liées entre elles** 📚

**Exemples de SGBDR :**

**🏢 Propriétaires : Oracle, SQL Server**

**🆓 Libres : MySQL, PostgreSQL, MariaDB**



**🧬 MySQL : origine et histoire**

- Créé en **1994** par **Michael Widenius**
- **My** vient du prénom de sa fille, **SQL** pour **Structured Query Language**
- Double licence : **GPL (libre)** + **propriétaire** via **Oracle** (qui a racheté Sun Microsystems en 2009)



**🌱 MariaDB : la suite logique**

- **Fork** de MySQL par la communauté suite au rachat par Oracle 🙅‍♂️
- Repris par **Widenius**
- **Maria** est le prénom de sa deuxième fille 👧



**💬 Le langage SQL : fonctionnement**

- Contrairement à des scripts classiques (comme Bash), le **SQL ne s’exécute pas ligne par ligne**
- Il est **analysé globalement**, puis exécuté par le **moteur de base de données** selon l’indexation 📈
- Résultat : les données sont **retrouvées et retournées efficacement**



**🧱 La structure d'une B.D : on ne fait pas ça à l'arrache !**

Avant de créer des tables ➡️ **réflexion obligatoire** :

**Étapes :**

1.  Réfléchir à l’organisation des données 🧩
2.  Créer un **MCD** (Modèle Conceptuel de Données) 🧠
3.  Construire la structure **avant d’ajouter les données**

🧠 *Exemple d’image mentale : on fabrique d’abord les étagères (structure), on les place à vide pour tester l’agencement, avant d’ajouter les livres (les données)* 📚
