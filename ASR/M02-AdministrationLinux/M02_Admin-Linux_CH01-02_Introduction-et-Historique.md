# M02 - Administration Linux

## CH01 - Introduction

### Conventions typographiques du support

La syntaxe des commandes suit la notation classique :

```
Commande [options] <Argument>
```

- `[options]` : facultatif
- `<Argument>` : obligatoire

Les deux prompts à reconnaître :

| Prompt | Signification |
|--------|---------------|
| `$` | Utilisateur standard |
| `#` | Compte root (administrateur) |

---

## CH02 - Historique de Red Hat et ses dérivés

### Origines

Red Hat naît de la rencontre de deux profils complémentaires :

- **Marc Ewing** (technique) : crée la première distribution **Red Hat Linux** en 1994. Le nom vient du fédora rouge qu'il portait à l'université Carnegie Mellon (Pittsburgh).
- **Bob Young** (commercial) : fonde AAC Corporation en 1995, spécialisée dans les produits Linux/Unix. Il rachète l'entreprise d'Ewing la même année pour former **Red Hat Software** et y ajoute le modèle de souscription par abonnement.

### Le modèle RHEL

Red Hat propose une distribution commerciale orientée entreprise, déclinée en deux versions :

- `RHEL Server` (Red Hat Enterprise Linux Server)
- `RHEL Workstation` (Red Hat Enterprise Linux Workstation)

📌 Points clés du modèle :
- Support de **10 ans** par version
- Possibilité de support **24/7** (pas uniquement du break/fix, aussi du conseil en amont sur l'architecture du SI)
- Options et tarifs variables selon la souscription choisie

### 2.1 - Historique des versions

**1995-2000** : Red Hat Linux, distribution unique pour particuliers et professionnels (jusqu'à la version 6).

**2000-2003** : scission en deux branches :
- La version entreprise devient **RHEL** (Red Hat Enterprise Linux) avec support **LTS** (Long-Term Support)
- La version particuliers continue sous le nom Red Hat Linux

**2003** : Red Hat abandonne la branche particuliers pour se concentrer sur l'entreprise.
- Naissance de **Fedora**, distribution communautaire gratuite composée de ~25% de l'équipe Red Hat
- Fedora sert de plateforme de développement **upstream** pour RHEL (logiciels en dernières versions)
- En parallèle, **CentOS** apparaît comme un fork communautaire de RHEL, sans souscription, publié quelques semaines/mois après chaque release RHEL

**2014** : Red Hat rachète le projet CentOS.

**2018** : **IBM achète Red Hat**.

**2019** : RHEL 8 sort le 7 mai, basée sur Fedora 28.

**Décembre 2021** : Red Hat annonce la fin de CentOS 8 (arrêt au 31/12/2021) et change sa roadmap en propulsant **CentOS Stream**.

### La chaîne de développement actuelle

```
Fedora (upstream, particuliers)
    └──> CentOS Stream (plateforme en continu)
              └──> RHEL (version stabilisée, entreprise)
```

⚠️ CentOS Stream n'est **plus** un fork de RHEL post-release. C'est désormais un flux continu **en amont** de RHEL. Les mises à jour arrivent dans CentOS Stream *avant* d'être intégrées à RHEL.

### 2.2 - Les forks de RHEL

Suite à l'abandon de CentOS tel qu'il existait, plusieurs alternatives sans souscription sont apparues :

- **Oracle Linux** : fork maintenu par Oracle (utilisé dans les ateliers de cette formation)
- **Rocky Linux** : initié par **Gregory Kurtzer**, le créateur originel de CentOS, en réponse directe à l'annonce de Red Hat en 2021
- **AlmaLinux** : autre fork communautaire, maintenu par CloudLinux

💡 En formation, on utilise **Oracle Linux 9** car c'est un fork binaire-compatible RHEL. Tout ce qui est vu sur Oracle Linux s'applique directement sur RHEL (et inversement).

---

### 📌 À retenir

1. **RHEL** est la distribution Linux de référence en entreprise, avec un modèle de souscription et un support de 10 ans.
2. **Fedora** est la plateforme upstream (dernières versions), **CentOS Stream** est le flux intermédiaire, **RHEL** est la version stabilisée.
3. Depuis 2021, CentOS n'est plus un fork post-release de RHEL mais une plateforme en continu en amont.
4. **Oracle Linux** et **Rocky Linux** sont les principaux forks compatibles RHEL sans souscription.
5. En contexte de formation ASR, Oracle Linux 9 est utilisé comme équivalent RHEL 9.
