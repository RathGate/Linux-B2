**But** :
- mise en place d'une VM fonctionnelle et configurable de PFSense ;
- configuration basique des deux réseaux où sera connectée la machine (WAN et LAN).

**Configuration matérielle minimale** :
- CPU : 64-bit compatible amd64 ;
- RAM : 1GB ou plus ;
- Disque : 8GB ou plus (SSD, HDD, etc.) ;
- Réseau : 1 NIC ou plus (2 ici)

A noter que le réseau WAN est celui utilisé par la machine hôte pour avoir accès à internet (celui de la box, par exemple).
Le LAN, quant à lui, sera le sous-réseau géré par PFSense.

# Configuration matérielle

## VMWare
### Configuration des NIC

Dans `Edit > Network Preferences...`, cliquer sur `Change Settings` pour afficher la totalité des informations éditables.

![[Pasted image 20240206205110.png]]

#### Réseau WAN (relié à Internet)

Dans l'édition de `VMNet0` :
- Configurer la connexion comme étant `Bridged`
- Dans le menu déroulant `bridged to:`, sélectionner la carte réseau de la machine hôte utilisée pour l'accès à Internet 
**Note** : Attention à ne pas sélectionner la carte Wi-Fi si l'on est connecté en Ethernet, et inversement...

![[Pasted image 20240206205920.png]]

#### Réseau LAN

Après avoir crée un nouveau réseau avec `Add Network...` et sélectionné la nouvelle interface :
- Configurer la connexion comme étant `Host Only` ;
- Cocher `Connect a host virtual adapter to this network` et décocher si besoin `Use local DHCP service to distribute IP address to VMs` ;
- Définir le réseau qui sera utilisé sur la nouvelle interface (ici `10.10.0.0/24` mais c'est au choix).

![[Pasted image 20240206210140.png]]

**Note** :
- Avoir coché `Connect a host virtual adapter to this network` permet de partager le réseau avec l'hôte.
- En tapant `ipconfig` dans un terminal, on y trouve la nouvelle interface virtuelle avec une adresse IP, souvent la première, du réseau qui a été défini (ici 10.10.0.1) :
![[Pasted image 20240206210711.png]]
- Cette pratique a l'avantage de directement inclure la machine hôte dans le nouveau LAN sans l'en isoler.

### Création de la VM

##### Paramètres globaux

- Lancer la création d'une nouvelle VM en configuration `typical (recommended)` et cliquer sur `Next` ;
- Choisir l'iso de PFSense avec `Browse` et cliquer sur `Next` ;
	![[Pasted image 20240206211020.png]]
- Donner un nom et un emplacement à la VM et cliquer sur `Next` ;
- Allouer la mémoire de disque voulue et cliquer sur `Next` ;
- Avant de valider la création, cliquer sur `Customize Hardware` :
	![[Pasted image 20240206211226.png]]

##### Assignation des interfaces réseau

- Dans `Network Adapter`, cliquer sur `Custom` et choisir `VMnet0` (l'interface configurée en `bridged` un peu plus tôt) :	![[Pasted image 20240206211346.png]]

- Ensuite, cliquer sur `Add...` en bas à gauche et ajouter un nouveau `Network Adapter` ;
- Configurer celui-ci en `Custom`, avec l'interface Host-only créée plus tôt :	![[Pasted image 20240206211543.png]]

- Fermer la fenêtre et cliquer sur `Finish`.


## Virtual Box
### Configuration des NIC

Il n'y a pas besoin de configurer d'interface pour le WAN avec Virtual Box. Cependant il faut tout de même configurer celle qui servira pour le LAN.

#### Réseau LAN

Dans `Files > Tools > Network Manager > Host-Only Networks` :
![[Pasted image 20240206212308.png]]

- Dans l'onglet `Adapter` de `VirtualBox Host-Only Ethernet Adapter`, sélectionner `Configure Adapter Manually` ;
- Entrer l'adresse IP et le masque de la NIC dans le réseau choisi (ex. ici le réseau choisi est `10.10.1.0/24`, donc l'IP pourra être `10.10.1.1`) ;
	**Note** : Attention ! VMWare demande l'adresse du réseau et attribue une IP à la NIC lui-même, tandis qu'ici on lui donne l'IP de la NIC et il déduit le réseau lui-même !
- Dans l'onglet `DHCP Server`, décocher `Enable Server` ;
- Cliquer sur `Apply`.

**Note** : 
- Contrairement à VMWare pour qui il faut spécifier que la carte NIC est partagée avec la machine hôte, elle l'est automatiquement dans Virtual Box.
- Un `ipconfig` dans la machine hôte devrait afficher directement la présence de la carte réseau :	![[Pasted image 20240206214226.png]]

### Création de la VM 

#### Paramètres globaux 

Cliquer sur `New` pour créer une nouvelle VM.

Dans l'onglet `Name And Operating System` :
- Choisir un nom et une location pour la VM ; 
- Dans `ISO Image`, rechercher l'ISO de PFSense dans les fichiers ;
- Dans `Type`, choisir `BSD` ;
- Dans `Version`, choisir `FreeBSD (64-bit)`.
	![[Pasted image 20240206213123.png]]

Dans les onglets `Hardware` et `Hard Disk`, choisir les spécificités CPU/RAM/Disque voulues et valider les paramètres de la VM avec `Finish`.

#### Assignation des interfaces réseau

- Sur le menu principal, après avoir sélectionné la nouvelle VM, cliquer sur `Settings` :
	![[Pasted image 20240206213424.png]]

Dans l'onglet `Network > Adapter 1` (WAN) :
- Cocher `Enable Network Adapter` ;
- Sélectionner `Bridged Adapter` dans `Attached to:` ;
- Sélectionner la carte NIC utilisée par la machine hôte pour accéder à internet dans `Name` ;
	![[Pasted image 20240206213730.png]]

Dans l'onglet `Network > Adapter 2` (LAN) :
- Cocher `Enable Network Adapter` ;
- Sélectionner `Host-only Adapter` dans `Attached to:` ;
- Sélectionner l'adaptateur que l'on a précédemment configuré (ici `VirtualBox Host-Only Ethernet Adapter).
	![[Pasted image 20240206213829.png]]

Valider les paramètres avec `OK`.

# Mise en place 

TODO