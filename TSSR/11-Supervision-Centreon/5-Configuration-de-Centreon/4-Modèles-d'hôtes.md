## **🛠️ Définir un hôte sans modèle**

Il est **possible de créer un hôte directement** sans passer par un modèle, mais c’est **chronophage et non scalable**.

👉 D’où l’intérêt des modèles : **mutualiser les paramètres communs**, automatiser l’ajout de services, faciliter la maintenance.


## **⚙️ Paramètres obligatoires d’un hôte**

Voici les éléments clés à renseigner pour chaque hôte (modèle ou non) :

- **Nom** (affiché dans l’interface)
- **Adresse IP/DNS**
- **Check Period** ⏱️ (période pendant laquelle les checks sont actifs)
- **Normal Check Interval** (fréquence de vérif normale)
- **Max Check Attempts** (combien de fois on vérifie avant de dire "Down")
- **Retry Check Interval** (temps entre les tentatives avant de confirmer le "Down")
- **Checks actifs/passifs**
- **Check Command** (ex : check-host-alive, souvent un simple ping)



## **📡 Commande de vérification d’un hôte**

La commande ne cherche **pas à donner une valeur qualitative**, mais juste à savoir si l’hôte est "Up" ou "Down".

✅ Exemple : check-host-alive → envoie 1 ping ICMP avec **seuils Warning/Critical très larges** pour ne jamais alerter à cause de latence réseau.

🧠 **Attention** : Ne jamais utiliser de ping "critique", car un hôte peut fonctionner parfaitement même si le ping est lent (réseau chargé, routeur distant…).


## **🧬 Utilité des modèles d’hôtes**

Un **modèle d’hôte** sert de **base de configuration** pour tous les hôtes similaires. Quand on ajoute un nouvel hôte :

- Il **hérite automatiquement des paramètres et services** définis dans le modèle
- On gagne du temps et on évite les oublis dans les checks/services


## **🧱 Structuration des modèles d’hôtes**

Comme pour les services, on structure les modèles d’hôtes en 2 types :

**🧩 1. Modèles de configuration (HTC_XXX)** 

➡️ Contiennent les **paramètres génériques** : SNMP, intervalle de check, commandes, notifications, etc. 

💡 Exemple : HTC_DEFAULT

**🧩 2. Modèles fonctionnels (HTF_XXX)** 

➡️ Définis par **type ou rôle d’hôte** : OS (Linux, Windows, Mac), usage (serveur web, contrôleur de domaine…). 

💡 Exemple : HTF_LINUX, HTF_LINUX_SRVWEB, HTF_WIN_SRVWEB

🔗 Ces modèles peuvent **hériter du modèle HTC_DEFAULT**, ce qui évite les redondances.



## **🧭 Exemple d’héritage**

Modèle de base HTC_DEFAULT (SNMP, intervals, check cmd)

⬇️ Modèles fonctionnels : HTF_LINUX, HTF_WINDOWS, HTF_MAC (ajoutent des services A, B, C...)

⬇️ Modèles spécialisés par rôle : HTF_LINUX_SRVWEB, HTF_WIN_SRVWEB (ajoutent services G, H, etc.)

