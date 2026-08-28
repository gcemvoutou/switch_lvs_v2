# Étapes du projet — Migration switch LVS

Ce document détaille le déroulé chronologique de la migration, illustré par les photos prises avant et après l'intervention.

---

## Étape 1 — Repérage et réorganisation du câblage réseau

Face à un câblage initial réalisé "au fil de l'eau" et devenu peu lisible, un **tableau de correspondance** a été mis en place. Celui-ci permet de :

- lier chaque port de l'ancien switch à sa prise murale correspondante,
- identifier le VLAN actif sur chaque port,
- planifier un nouveau branchement propre et ordonné en assignant à chaque prise un nouveau port sur le nouveau matériel.

> [!NOTE]
> Le tableau de repérage complet est disponible en annexe (`images/tableau-correspondance.xlsx` ou équivalent).

**📷 Baie informatique — AVANT intervention**

`![Baie avant migration](images/baie-avant.jpg)`

---

## Étape 2 — Repérage des VLANs existants

Repérage des VLANs actifs sur l'ancien switch à l'aide de **LibreNMS**, en complément du tableau de correspondance établi à l'étape 1.

---

## Étape 3 — Configuration du nouveau switch (en SSH)

Le switch Aruba 2530 étant déjà présent sur site mais non opérationnel, la configuration a été réalisée en SSH :

- Création des VLANs
- Taggage des VLANs sur les différents ports
- Attribution d'une IP au switch (afin de pouvoir le joindre à distance une fois installé sur site)
- Attribution d'un nom au switch (LVS_197)

> [!NOTE]
> Le détail des commandes utilisées est disponible dans **[PROCEDURE.md](./PROCEDURE.md)**.

---

## Étape 4 — Raccordement de la liaison fibre

Connexion de la fibre au switch, avec création du **VLAN 208** et taggage de ce dernier sur le port dédié à la liaison.

---

## Étape 5 — Mise à jour du plan de VLANs

| VLAN avant | Usage | VLAN après | Statut |
|---|---|---|---|
| 44 | ToIP | **2** (VoIP) | Migré |
| 207 | WiFi LVS | **210** (bornes WiFi) | Migré |
| — | — | 4, 150, 151 | Nouveaux — non taggués pour l'instant |
| 208 | — | 208 | Inchangé |
| 2 | — | 2 | Inchangé |

> [!IMPORTANT]
> Les VLANs 4, 150 et 151 sont bien créés sur le nouveau switch mais **ne sont pour l'instant taggués sur aucun port**, car ils n'existaient pas sur l'ancien équipement. Ils sont prévus pour des besoins à venir.

**📷 Schéma récapitulatif du plan de VLANs**

`![Schéma VLANs](images/schema-vlans.png)`

---

## Étape 6 — Mise en service du nouveau switch

Bascule du câblage de l'ancien switch vers le nouveau switch Aruba, port par port, en suivant le tableau de correspondance établi à l'étape 1.

> [!IMPORTANT]
> La bascule a été réalisée de manière à **garantir une continuité de service pendant les horaires d'ouverture du cinéma au public**.

**📷 Baie informatique — APRÈS intervention**

`![Baie après migration](images/baie-apres.jpg)`

---

## Étape 7 — Mise à jour de la supervision et de la documentation

Mise à jour de **LibreNMS** et de la documentation technique associée pour refléter le nouvel équipement et le nouveau plan de VLANs.

---

## Étape 8 — Réaménagement de la banque d'accueil

Gestion du débranchement puis du rebranchement des équipements de la banque d'accueil du LVS, dans le cadre du réaménagement de cet espace.

**📷 Banque d'accueil — AVANT**

`![Banque d'accueil avant](images/accueil-avant.jpg)`

**📷 Banque d'accueil — APRÈS**

`![Banque d'accueil après](images/accueil-apres.jpg)`

---

## Résultats

- Mise en production réussie, sans coupure de service pendant les horaires d'ouverture
- Baie clarifiée, câblage réorganisé et étiqueté
- Plan de VLANs harmonisé
- Supervision et documentation à jour
- Réaménagement mené sans incident
