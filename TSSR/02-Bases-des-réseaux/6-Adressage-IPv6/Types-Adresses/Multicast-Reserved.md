# Multicast ReservedLes **adresses de multidiffusion** (ou **multicast addresses**) en IPv6 permettent d'envoyer un paquet à **un groupe spécifique de nœuds** plutôt qu'à un seul destinataire (monodiffusion) ou à tous les nœuds d'un réseau (diffusion). C'est une méthode efficace pour économiser de la bande passante quand plusieurs destinataires doivent recevoir le même flux de données. Une adresse de multidiffusion permet d'identifier un **groupe de nœuds**. Un paquet envoyé à une adresse de multidiffusion est transmis à **tous les nœuds** faisant partie du groupe associé à cette adresse.



**🛠️ Format d’une Adresse de Multidiffusion IPv6**

Les adresses de multidiffusion IPv6 appartiennent à la plage **FF00::/8**. Elles sont structurées comme suit :

| **8 bits (Préfixe)** | **4 bits (Flags)** | **4 bits (Scope)** | **112 bits (Identifiant de groupe)** |
|----|----|----|----|
| **FF** | Flags | Scope | Identifiant du groupe |

- **Préfixe** : Toujours "FF", indiquant qu'il s'agit d'une adresse de multidiffusion.
- **Flags** : Indiquent des propriétés spécifiques de l'adresse (ex: si elle est routable, etc.).
- **Scope** : Définit la portée de la multidiffusion (locale, site, global, etc.).
- **Identifiant de groupe** : Identifie un groupe spécifique de nœuds destinataires.



**🎯 Exemples d’Adresses de Multidiffusion**

**1️⃣ FF01::/16 - Multicast Interface-Local  **
Les adresses de multidiffusion de nœud local sont destinées à la communication entre des nœuds au sein d’une même interface. Elles sont limitées à un périphérique et ne peuvent pas être routées.

- **FF01::1** : Tous les nœuds locaux / **FF01::2** : Tous les routeurs locaux / **FF01::FB** : mDNS pour nœuds locaux

**2️⃣ FF02::/16 - Multicast Link-Local  **
Les adresses de multidiffusion de lien local concernent des nœuds sur un même lien ou réseau physique. Elles ne peuvent pas traverser les routeurs, communication entre les nœuds sur le même réseau local.

- **FF02::1** : Tous les nœuds du lien local / **FF02::2** : Tous les routeurs du lien local / **FF02::1:2** : Serveurs/Agents relais DHCP / **FF02::FB** : mDNS pour lien local

**3️⃣ FF05::/16 - Multicast Site-Local  **
Les adresses de multidiffusion de site local concernent un site. Elles sont routables à l’intérieur d’un site (réseau interne d’un site ou un sous-réseau) et ne traversent pas les frontières du site.

- **FF05::1** : Tous les nœuds du site local / **FF05::2** : Tous les routeurs du site local / **FF05::1:2** : Serveurs/Agents relais DHCP / **FF05::FB** : mDNS pour site local

**4️⃣ FF08::/16 - Multicast Organization-Local  **
Les adresses de multidiffusion org-local sont utilisées pour la diffusion à travers plusieurs sites interconnectés dans une organisation. Adresses sont routables sur plusieurs sous-réseaux ou sites d'une organisation.

- **FF08::1** : Tous les nœuds de l'organisation / **FF08::2** : Tous les routeurs de l'organisation / **FF08::1:2** : Serveurs/Agents relais DHCP / **FF08::FB** : mDNS pour organisation

**5️⃣ FF0E::/16 - Multicast Global  **
Les adresses de multidiffusion global sont destinées à la diffusion sur Internet. Elles sont routables mondialement, manque de prise en charge par les opérateurs de réseau.

- **FF0E::1** : Tous les nœuds globaux / **FF0E::2** : Tous les routeurs globaux / **FF0E::1:2** : Serveurs/Agents relais DHCP / **FF0E::FB** : mDNS global



**🌍 Utilisations des Adresses de Multidiffusion**

Les adresses de multidiffusion sont utilisées dans de nombreux contextes nécessitant la diffusion simultanée vers plusieurs destinataires, par exemple :

1.  **Diffusion Vidéo et Audio (Streaming)** : Envoyer un flux multimédia à plusieurs utilisateurs sans répéter l'envoi à chaque destinataire.
2.  **Routage Multicast** : Des protocoles comme **PIM** (Protocol Independent Multicast) utilisent des adresses multicast pour gérer des groupes de diffusion.
3.  **Découverte de Services** : Des protocoles comme **mDNS** (Multicast DNS) ou **SSDP** (Simple Service Discovery Protocol) utilisent des adresses multicast pour découvrir des périphériques ou services sur un réseau local.
4.  **Protocoles Réseaux Internes** : **OSPF** (Open Shortest Path First) utilise des adresses multicast pour échanger des informations entre routeurs.
