# Gestion des ressources🧩 **Principe général**

- Les ressources (CPU, RAM) d’un datacenter sont le **cumul** de celles de tous les hôtes ESXi.
- Il est possible d’**affecter plus de ressources** aux VMs que ce que l’hôte possède réellement (overcommit), car toutes les VMs ne tournent pas en même temps.
- Risque : une VM prioritaire peut **ne pas démarrer** faute de ressources disponibles.



🔐 **Réservation de ressources : un mécanisme de protection**

- Objectif : **éviter la saturation** du datacenter par une VM trop gourmande ou malveillante.
- Par défaut, les VMs n’ont **aucune limite** → risque d’absorption totale des ressources.
- La réservation permet de **garantir un minimum de ressources** à certaines VMs ou groupes de VMs.

Réservation possible à 3 niveaux :

1.  💻 **Machine virtuelle** individuelle
2.  🧺 **Pool de ressources** (ensemble de VMs)
3.  🖧 **Cluster** entier

🗂️ **Pool de ressources (Resource Pool - RP)**
- Objet **logique** auquel on affecte une partie des ressources physiques (CPU/RAM).
- Créé sur un **hyperviseur autonome** ou un **datacenter vSphere**.
- Contient une ou plusieurs machines virtuelles.

Utilité :

✅ Réserver des ressources selon l’usage (ex : environnement de prod)

🚫 Limiter les ressources pour des environnements de test (ex : sandbox)



🚨 **Gestion des saturations**

- Si RP VM Prod consomme toutes les ressources disponibles :  
  ❌ Les VMs du RP VM Test ne pourront pas fonctionner.

- Les **limites** imposées au RP Test permettent d’**isoler les charges** et **protéger les services critiques**.
- La configuration des pools de ressources est une stratégie de **qualité de service (QoS)** interne au datacenter.

🧪 **Exemple de configuration**

Datacenter : **DC1**

Hyperviseur : **172.18.11.13**

Deux pools de ressources créés :

- **RP VM Prod**
  - Contient : W12 DC1, W2K8 R2-Appli 1
  - **Réservation CPU : 8000 MHz**
  - Pas de limite haute ➜ peut utiliser tout ce qui est disponible
- **RP VM Test**
  - Contient : CLW7 Test 1, CLXP Test 2
  - **Réservation CPU : 0 MHz**
  - **Limite CPU : 2000 MHz** ➜ impossible d’aller au-delà
