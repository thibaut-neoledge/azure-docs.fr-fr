## Réseau virtuel
Les réseaux virtuels et sous-réseaux permettent de définir une limite de sécurité pour les charges de travail s'exécutant dans Azure. Un réseau virtuel est caractérisée par une collection d’espaces d'adressage, également appelé « bloc CIDR ».

Un sous-réseau est une ressource enfant d'un réseau virtuel, et permet de définir des segments d'espaces d'adressage dans un bloc CIDR, à l'aide de préfixes d'adresses IP. Les cartes d’interface réseau (NIC) peuvent être ajoutées aux sous-réseaux et connectées aux machines virtuelles, ce qui fournit une connectivité pour différentes charges de travail.

![Cartes d'interface réseau sur une seule machine virtuelle](./media/resource-groups-networking/Figure4.png)

Les propriétés clés d'une ressource de réseau virtuel sont les suivantes :

- Espace d'adressage IP (bloc CIDR) 
- Nom du réseau virtuel
- Sous-réseaux
- Serveurs DNS

Un réseau virtuel peut également être associé aux ressources réseau suivantes :

- Passerelle VPN

### Sous-réseau

Les propriétés clés d'un sous-réseau sont les suivantes :

- Préfixe d'adresse IP
- Nom du sous-réseau

Un sous-réseau peut également être associé aux ressources réseau suivantes :

- Groupe de sécurité réseau

<!---HONumber=Sept15_HO4-->