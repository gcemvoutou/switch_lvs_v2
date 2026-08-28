# Procédures techniques — Switch Aruba 2530

![Aruba](https://img.shields.io/badge/Aruba-Networking-FF8300?logo=aruba&logoColor=white)
![Projet](https://img.shields.io/badge/Contexte-Entreprise-success)

Ce document rassemble les procédures techniques réutilisables issues d'un projet de migration de switch. Elles peuvent être réappliquées pour toute future migration ou configuration de switch Aruba (série 2530, syntaxe ArubaOS-Switch) : la logique de taggage VLAN (Procédure 2) et la méthodologie de migration (Procédure 3) sont directement transposables à d'autres sites ou d'autres modèles.

---

## Procédure 1 — De la première connexion (console) à la configuration de base

Un switch neuf n'a ni IP, ni nom, ni accès distant activé : la toute première connexion se fait obligatoirement en local, via le port console.

**1. Connexion initiale en série (câble console)**

```bash
# Connexion via câble console (RJ45/USB ou RS232) + logiciel terminal (PuTTY, TeraTerm...)
# Paramètres typiques : 9600 bauds, 8N1
```

**2. Définir une IP de management** (afin de pouvoir joindre le switch à distance une fois installé sur site) :

```
switch> enable
switch# configure terminal
switch(config)# vlan <ID_VLAN_MANAGEMENT>
switch(vlan-<ID_VLAN_MANAGEMENT>)# ip address <IP> <MASQUE>
switch(vlan-<ID_VLAN_MANAGEMENT>)# exit
switch(config)# ip default-gateway <PASSERELLE>
```

**Exemple :**

```
switch(config)# vlan 1
switch(vlan-1)# ip address 192.0.2.10 255.255.255.0
switch(vlan-1)# exit
switch(config)# ip default-gateway 192.0.2.1
```

> [!NOTE]
> Adresses IP données à titre d'exemple (plage documentaire RFC 5737), anonymisées par rapport à la configuration réelle.

**3. Donner un nom au switch** :

```
switch(config)# hostname "<NOM_DU_SWITCH>"
```

> [!NOTE]
> Une fois l'IP de management active, le switch devient joignable à distance. Le reste de la configuration (VLANs, taggage des ports, sécurisation) peut alors être mené sans repasser par le câble console.

**4. Suite de la configuration à distance**

```
switch(config)# vlan <ID_VLAN>
switch(vlan-<ID_VLAN>)# name "<NOM_DU_VLAN>"
switch(vlan-<ID_VLAN>)# exit
switch(config)# password manager
switch(config)# write memory
```

> [!TIP]
> Toujours donner un nom explicite au VLAN (usage, service) plutôt que de dépendre uniquement du numéro d'ID — ça évite de devoir rouvrir la documentation à chaque fois.

> [!IMPORTANT]
> `write memory` doit être exécuté après **chaque** modification. Une configuration non sauvegardée est perdue au redémarrage du switch.

Bonne pratique appliquée sur ce projet : isoler l'administration sur un VLAN de management dédié, distinct des VLANs « utilisateurs ».

---

## Procédure 2 — Tagger un VLAN sur un port (mode trunk / tagged)

Un port peut porter plusieurs VLANs "tagués" (trunk) ou un seul VLAN "untagué" (access).

```
switch(config)# vlan <ID_VLAN>
switch(vlan-<ID_VLAN>)# tagged <PORT>
```

**Exemple — tagger le VLAN 208 sur le port A5 (liaison fibre) :**

```
switch(config)# vlan 208
switch(vlan-208)# tagged A5
```

Pour un port en mode access (VLAN natif, non tagué) :

```
switch(config)# vlan <ID_VLAN>
switch(vlan-<ID_VLAN>)# untagged <PORT>
```

> [!IMPORTANT]
> Ne jamais tagger un VLAN sur un port sans avoir vérifié au préalable, via le tableau de correspondance, quel équipement est branché sur ce port et quel VLAN il doit réellement porter.

---

## Procédure 3 — Méthodologie de migration de switch (câblage)

1. **Repérer** chaque port de l'ancien switch : prise murale associée, VLAN actif, équipement branché.
2. **Consigner** ces informations dans un tableau de correspondance (port ancien → prise murale → VLAN → port prévu sur le nouveau switch).
3. **Configurer** le nouveau switch à l'avance (VLANs, IP, nom) avant toute bascule physique.
4. **Basculer** le câblage port par port en suivant le tableau, de préférence en dehors des horaires d'affluence / d'ouverture au public.
5. **Vérifier** chaque liaison après bascule (connectivité, VLAN correct) avant de démonter l'ancien équipement.
6. **Mettre à jour** la supervision (LibreNMS ou équivalent) et la documentation technique.

> [!TIP]
> Cette méthodologie en 6 points est réutilisable telle quelle pour toute future migration de switch, quel que soit le site.
