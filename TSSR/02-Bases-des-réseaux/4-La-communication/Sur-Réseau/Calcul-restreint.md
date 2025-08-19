# Calcul restreint**📌 Données**

Réseaux à regrouper : **212.56.146.0/24 - 212.56.147.0/24- 212.56.148.0/24 - 212.56.149.0/24**



**🔍 Méthode détaillée**

1️⃣ **Combien de réseaux à supernetter ?**

- 4 réseaux /24 → **besoin d'un supernet qui couvre 4 sous-réseaux**



2️⃣ **Combien de bits emprunter ?**

- **2² = 4 sous-réseaux (2bits)**
- Passer de **/24** à **/22**



3️⃣ **Le /22 fonctionne-t-il ?**

- **NON** car **146(de 212.56.146.0) n'est pas un multiple de 4  **

**🛠 Solutions possibles**

**✅ Solution 1 : Supernetting précis (sans adresse superflue)**

- **Découper en deux /23 (Bien verif si Multiple du nombre de réseaux a englober dans le scope du /23)**
  - **212.56.146.0/23** (couvre 146.0 → 147.255)
  - **212.56.148.0/23** (couvre 148.0 → 149.255)
  - 

**✅ Solution 2 : Supernetting global (avec adresses inutilisées)**

- **Prendre un supernet plus large**
  - **212.56.144.0/21 (Car 146 n'est pas un multiple de 8 (3bits pris a /24)**
  - Couvre **144.0 à 151.255**
  - Contient des adresses non utilisées
