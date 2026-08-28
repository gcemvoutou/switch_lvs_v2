# Procédures techniques — Switch Aruba 2530

Ce document rassemble les procédures techniques réutilisables issues du projet de migration du switch LVS. Elles peuvent être réappliquées pour toute future migration ou configuration de switch Aruba (série 2530, syntaxe ArubaOS-Switch).

---

## Procédure 1 — Se connecter au switch en SSH

> [!NOTE]
> Le switch doit déjà avoir une IP de management configurée, ou être accessible via le port console pour la première configuration.

```bash
ssh manager@<IP_DU_SWITCH>
```

Passer en mode configuration :

```
switch> enable
switch# configure terminal
switch(config)#
```

---

## Procédure 2 — Créer un VLAN

```
switch(config)# vlan <ID_VLAN>
switch(vlan-<ID_VLAN>)# name "<NOM_DU_VLAN>"
switch(vlan-<ID_VLAN>)# exit
```

**Exemple :**

```
switch(config)# vlan 210
switch(vlan-210)# name "WIFI_BORNES"
switch(vlan-210)# exit
```

> [!TIP]
> Toujours donner un nom explicite au VLAN (usage, service) pour faciliter la maintenance future et éviter de dépendre uniquement du numéro d'ID.

---

## Procédure 3 — Tagger un VLAN sur un port (mode trunk / tagged)

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

## Procédure 4 — Attribuer une IP de management au switch

```
switch(config)# vlan <ID_VLAN_MANAGEMENT>
switch(vlan-<ID_VLAN_MANAGEMENT>)# ip address <IP> <MASQUE>
switch(vlan-<ID_VLAN_MANAGEMENT>)# exit
switch(config)# ip default-gateway <PASSERELLE>
```

**Exemple :**

```
switch(config)# vlan 1
switch(vlan-1)# ip address 192.168.198.197 255.255.255.0
switch(vlan-1)# exit
switch(config)# ip default-gateway 192.168.198.1
```

---

## Procédure 5 — Nommer le switch

```
switch(config)# hostname "<NOM_DU_SWITCH>"
```

**Exemple :**

```
switch(config)# hostname "LVS_197"
```

---

## Procédure 6 — Sécuriser l'accès au switch

- Définir un mot de passe manager :

```
switch(config)# password manager
```

- Isoler l'administration sur un VLAN de management dédié, distinct des VLANs "utilisateurs" (bonne pratique appliquée sur ce projet).

---

## Procédure 7 — Sauvegarder la configuration

```
switch(config)# write memory
```

> [!IMPORTANT]
> Toujours sauvegarder la configuration après chaque modification. Une config non sauvegardée est perdue au redémarrage du switch.

---

## Procédure 8 — Méthodologie de migration de switch (câblage)

1. **Repérer** chaque port de l'ancien switch : prise murale associée, VLAN actif, équipement branché.
2. **Consigner** ces informations dans un tableau de correspondance (port ancien → prise murale → VLAN → port prévu sur le nouveau switch).
3. **Configurer** le nouveau switch à l'avance (VLANs, IP, nom) avant toute bascule physique.
4. **Basculer** le câblage port par port en suivant le tableau, de préférence en dehors des horaires d'affluence / d'ouverture au public.
5. **Vérifier** chaque liaison après bascule (connectivité, VLAN correct) avant de démonter l'ancien équipement.
6. **Mettre à jour** la supervision (LibreNMS ou équivalent) et la documentation technique.

> [!TIP]
> Cette méthodologie en 6 points est réutilisable telle quelle pour toute future migration de switch, quel que soit le site.
