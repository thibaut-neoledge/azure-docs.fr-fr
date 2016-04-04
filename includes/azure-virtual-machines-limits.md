Ressource|Limite par défaut|Limite maximale
---|---|---
[Machines virtuelles](../articles/virtual-machines/virtual-machines-linux-about.md) par service cloud<sup>1</sup>|50|50
Points de terminaison d’entrée par service cloud<sup>2</sup>|150|150

<sup>1</sup>Les machines virtuelles créées dans Service Management (plutôt que dans Resource Manager) sont automatiquement stockées dans un service cloud. Vous pouvez ajouter plus de machines virtuelles à ce service cloud pour l’équilibrage de charge et la disponibilité. Consultez [Connexion de machines virtuelles à un réseau virtuel ou un service cloud](../articles/virtual-machines/virtual-machines-linux-classic-connect-vms.md).

<sup>2</sup>Les points de terminaison d’entrée autorisent les communications avec une machine virtuelle depuis l’extérieur du service cloud de la machine virtuelle. Les machines virtuelles d’un même service cloud ou réseau virtuel peuvent automatiquement communiquer entre elles. Voir [Configuration des points de terminaison sur une machine virtuelle](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

<!---HONumber=AcomDC_0323_2016-->