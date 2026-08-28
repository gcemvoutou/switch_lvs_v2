# Migration d'un switch réseau

---

## 📁 Structure de la documentation

| Fichier | Contenu |
|---|---|
| `README.md` | Vue d'ensemble du projet (ce fichier) |
| `ETAPES.md` | Déroulé détaillé du projet, avec photos avant/après |
| `PROCEDURE.md` | Procédures techniques réutilisables (config VLANs Aruba, etc.) |
| `images/` | Photos et schémas du projet |

---

## 1. Contexte

Dans le cadre de la modernisation de l'infrastructure réseau de la Mairie de Saint-Égrève, une migration du switch réseau du Centre culturel LVS (cinéma municipal) a été réalisée.

L'ancien switch, un **Alcatel-Lucent 52 ports (26×2)**, était en réalité constitué de deux switches accolés, avec un brassage peu lisible et difficile à maintenir. Il a été remplacé par un switch **Aruba 2530**, déjà présent sur site mais non encore opérationnel.

Cette migration a également été l'occasion d'harmoniser le plan de VLANs du site avec le reste du parc réseau de la mairie : certains VLANs historiques ont été fusionnés ou renommés, d'autres nouvellement créés pour anticiper les besoins à venir.

> [!INFORMATION]
> Ce projet a été mené en autonomie dans le cadre de mon alternance.

---

## 2. Objectifs du projet

- Remplacer le switch de la baie (ancien Alcatel-Lucent 52 ports) par le nouveau switch **Aruba 2530 (LVS_197)**
- Réorganiser proprement le câblage de la baie informatique
- Mettre à jour le plan de VLANs, les anciens VLANs configurés sur l'ancien switch ayant évolué
- Intervenir dans le cadre du réaménagement de la banque d'accueil du LVS (avant / après)

---

## 3. Matériel concerné

| | Ancien équipement | Nouveau matériel |
|---|---|---|
| **Modèle** | Alcatel-Lucent OS 6.6.3.509.R01 | Switch Aruba 2530 |
| **Nom** | — | LVS_197 |
| **Ports** | 52 ports (26×2, deux switches accolés) | — |
| **IP de management** | 192.168.198.233 (ancienne) | 192.168.198.197 (nouvelle) |
| **Masque** | — | 255.255.255.0 |
| **Passerelle** | — | 192.168.198.1 |

---

## 4. Résultats obtenus

- Mise en production réussie du nouveau switch Aruba (LVS_197), **sans coupure de service** pendant les horaires d'ouverture du cinéma
- Baie informatique clarifiée, câblage réorganisé et étiqueté
- Plan de VLANs harmonisé avec le reste du parc réseau de la mairie
- Supervision LibreNMS et documentation technique mises à jour
- Réaménagement de la banque d'accueil mené sans incident

---

## 5. Compétences mobilisées et acquises

- Configuration d'un switch Aruba (première prise en main du matériel)
- Création et gestion de VLANs (ports access / trunk)
- Sécurisation d'un équipement réseau (mot de passe manager, VLAN de management dédié)
- Connexion et administration à distance en SSH
- Utilisation de LibreNMS pour l'analyse et la supervision réseau
- Organisation et documentation d'une migration réseau en environnement de production, avec contraintes d'exploitation (horaires d'ouverture au public)

---

➡️ Pour le déroulé détaillé étape par étape avec photos, voir **[ETAPES.md](./ETAPES.md)**
➡️ Pour les procédures techniques réutilisables, voir **[PROCEDURE.md](./PROCEDURE.md)**
