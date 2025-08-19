# Machine hôte/Hardware**🎯 Rôle central de la machine hôte**

- La machine hôte est **le serveur physique** qui fournit les ressources matérielles (CPU, RAM, stockage) nécessaires à la virtualisation.
- Les performances des machines virtuelles dépendent directement de ses capacités et de son intégration avec la solution de virtualisation.

**⚙️ Contraintes matérielles indispensables**

- Que ce soit une **tour, un rack ou une lame**, la machine hôte doit disposer d’un ou plusieurs processeurs compatibles avec la virtualisation.
- Le CPU doit supporter des instructions spécifiques pour que la virtualisation fonctionne correctement.



**🛠️ Fonctionnalités CPU indispensables**

- **Intel :** présence de la fonctionnalité **VT-x** (Virtualization Technology)
- **AMD :** présence de l’instruction **AMD-V**
- Ces technologies permettent au CPU de gérer efficacement les machines virtuelles.



**💾 Gestion de la mémoire – Le rôle clé de SLAT**

- **SLAT** (Second Level Address Translation) optimise la gestion de la mémoire vive (RAM) entre la machine hôte et les machines virtuelles.
- Sans SLAT, la mémoire allouée à une VM est doublée (réservée deux fois : dans la VM et dans la machine hôte), ce qui gaspille beaucoup de ressources.



**🔍 Fonctionnement de SLAT**

- Sur Intel, SLAT s’appelle **EPT** (Extended Page Tables)
- Sur AMD, SLAT s’appelle **NPT** (Nested Page Tables) ou **RVI** (Rapid Virtualization Indexing), selon la génération
- SLAT évite la double réservation en permettant à la machine physique de référencer directement les données de la VM dans sa mémoire, sans duplication.

**✅ Vérification des prérequis**

- Avant de déployer une solution de virtualisation, il est crucial de **vérifier que la machine hôte supporte VT-x / AMD-V et SLAT**.
- Cela garantit un environnement virtuel performant, stable et optimisé en ressources matérielles.
