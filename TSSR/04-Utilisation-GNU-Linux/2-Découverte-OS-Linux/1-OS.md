# **🧩 Qu’est-ce qu’un Système d’exploitation ?**

Un **OS (Operating System)** est le **logiciel principal** d’un ordinateur. Il :

- Gère le **matériel** (clavier, souris, écran, son 🎧…)
- Permet de **lancer des applications**
- Assure la **communication entre l'utilisateur et la machine**



## **🏷️ Les types de systèmes :**

- **🔒 Systèmes propriétaires** : liés à un fabricant (ex : OS400 d’IBM). Limités à certaines applis spécifiques.
- **🖥️ Systèmes ouverts monoposte/monotâche** : comme **DOS**. Un seul utilisateur, une tâche à la fois.
- **🌐 Systèmes ouverts multi-utilisateurs/multitâches** : plusieurs utilisateurs ➕ plusieurs applis en parallèle (Linux, macOS, Windows, Unix…).



## **🧱 Structure d’un OS en couches :**

1.  **Matériel (Hardware)** 💻  
    Carte mère, CPU, RAM, disques, clavier, écran…

2.  **Noyau (Kernel)** 🧠  
    Le **cœur** du système :

    - **Gère les ressources (mémoire, processeur, périphériques)**
    - **Planifie les tâches (évite qu’un processus ne bloque tout)**

3.  **Shell** 💬  
    L’interface entre **utilisateur et OS** :

    - **Windows : interface graphique (GUI), parfois PowerShell ou CMD**
    - **Linux : surtout ligne de commande via Bash**

4.  **Applications** 🧾  
    Éditeurs de texte, navigateurs, lecteurs multimédias, etc.

5.  **Utilisateurs** 👤  
    Ils interagissent avec le shell pour lancer les apps et accéder aux données.



## **⚙️ Focus sur le Noyau Linux (Kernel)**

- C’est **le programme principal** du système.
- Il gère :
  - Les ressources matérielles
  - L’exécution des tâches/processus
- Il garantit une expérience fluide et stable pour l’utilisateur.
- On appelle souvent **"le noyau Linux"** l’ensemble du **kernel + modules + programmes liés**.

