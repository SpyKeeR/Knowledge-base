# 📦 Debian 13 (Trixie) – Nouveau format deb822 pour les dépôts APT

## Introduction
Depuis août 2025, Debian 13 (Trixie) et APT utilisent par défaut le format deb822 pour les sources des dépôts.  
Ce format remplace l’historique /etc/apt/sources.list par un fichier structuré : /etc/apt/sources.list.d/debian.sources

## Commandes utiles
- Moderniser automatiquement les sources : `sudo apt modernize-sources`
  - Sauvegardes automatiques créées : /etc/apt/sources.list.bak et /etc/apt/sources.list.save
- Vérifier/éditer le fichier deb822 : `sudo nano /etc/apt/sources.list.d/debian.sources`
- Mettre à jour l’index : `sudo apt update`

Astuce : après modernisation, conserver Signed-By pointant vers /usr/share/keyrings/debian-archive-keyring.gpg (ou un trousseau dédié par dépôt) pour éviter les clés globalement “trusted”.


## Qu’est-ce que le format deb822 ?
Format structuré inspiré de RFC822, déjà utilisé par dpkg et les préférences APT.  
Chaque dépôt est défini dans un bloc de paires clé: valeur.

Bénéfices :
- Lisibilité accrue et maintenance facilitée
- Modularité (un bloc par dépôt, activation/désactivation simple)
- Sécurité renforcée via la directive Signed-By (clés explicites)
- Extensible et pérenne (multi-suites, multi-architectures)


## Directives principales (clés)
- Types : deb, deb-src
- URIs : une ou plusieurs URL du dépôt
- Suites : distribution(s) ciblée(s) (ex. trixie, trixie-updates, trixie-security)
- Components : main, contrib, non-free, non-free-firmware
- Signed-By : chemin du trousseau autorisé pour ce dépôt

### Directives utiles (optionnelles) :
- Architectures : ex. amd64 i386
- Enabled : yes/no pour activer/désactiver un bloc
- By-Hash : yes/no (intégrité et cache)
- Check-Valid-Until : yes/no (validation des métadonnées)

## Exemple deb822 (/etc/apt/sources.list.d/debian.sources)
#### Dépôt principal
Types: deb deb-src
URIs: http://deb.debian.org/debian/
Suites: trixie
Components: main contrib non-free non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg


#### Ancien format (sources.list) — rappel
```bash
deb http://deb.debian.org/debian/ trixie main contrib non-free non-free-firmware
deb-src http://deb.debian.org/debian/ trixie main contrib non-free non-free-firmware
```
