# Vue d'ensemble
## [Réseaux virtuels](virtual-networks-overview.md)
## [Itinéraires définis par l’utilisateur et transfert IP](virtual-networks-udr-overview.md)
## [Homologation de réseaux virtuels](virtual-network-peering-overview.md)
## [Continuité de l’activité](virtual-network-disaster-recovery-guidance.md)
## [FAQ](virtual-networks-faq.md)
## Adressage IP
### [Gestionnaire de ressources](virtual-network-ip-addresses-overview-arm.md)
### [Classique](virtual-network-ip-addresses-overview-classic.md)

# Prise en main
## [Créer votre premier réseau virtuel](virtual-network-get-started-vnet-subnet.md)

# Procédure
## Planifier et concevoir
### [Réseaux virtuels](virtual-network-vnet-plan-design-arm.md)
### [Groupes de sécurité réseau](virtual-networks-nsg.md)

## Déployer
### Réseaux virtuels (VNet)
#### [Créer, modifier ou supprimer des réseaux virtuels](virtual-network-manage-network.md)
#### [Créer, modifier ou supprimer des sous-réseaux](virtual-network-manage-subnet.md)
#### [Créer un réseau virtuel – Plusieurs sous-réseaux](virtual-networks-create-vnet-arm-pportal.md) 
##### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
##### [INTERFACE DE LIGNE DE COMMANDE](virtual-networks-create-vnet-arm-cli.md)
##### [Modèle](virtual-networks-create-vnet-arm-template-click.md)
#### Créer un réseau virtuel – Plusieurs sous-réseaux (classique)
##### [Portail](virtual-networks-create-vnet-classic-pportal.md)
##### [PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)
##### [INTERFACE DE LIGNE DE COMMANDE](virtual-networks-create-vnet-classic-cli.md)

### groupes de sécurité réseau ;
#### [Portail](virtual-networks-create-nsg-arm-pportal.md)
#### [PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [INTERFACE DE LIGNE DE COMMANDE](virtual-networks-create-nsg-arm-cli.md)
#### [Modèle](virtual-networks-create-nsg-arm-template.md)
#### [PowerShell (classique)](virtual-networks-create-nsg-classic-ps.md)
#### [Interface de ligne de commande (classique)](virtual-networks-create-nsg-classic-cli.md)

### Itinéraires définis par l’utilisateur
#### [PowerShell](virtual-network-create-udr-arm-ps.md)
#### [INTERFACE DE LIGNE DE COMMANDE](virtual-network-create-udr-arm-cli.md)
#### [Modèle](virtual-network-create-udr-arm-template.md)
#### [PowerShell (classique)](virtual-network-create-udr-classic-ps.md)
#### [Interface de ligne de commande (classique)](virtual-network-create-udr-classic-cli.md)

### Homologation de réseaux virtuels
#### [Portail](virtual-networks-create-vnetpeering-arm-portal.md)
#### [PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
#### [Modèle](virtual-networks-create-vnetpeering-arm-template-click.md)

### Interfaces réseau (cartes réseau)
#### [Créer, modifier ou supprimer des cartes réseau](virtual-network-network-interface.md)
#### [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md)

### [Adresses IP publiques](virtual-network-public-ip-address.md)

### Machines virtuelles
#### [Ajouter ou supprimer des interfaces réseau](virtual-network-network-interface-vm.md) 
#### Créer une machine virtuelle avec une adresse IP publique statique
##### [Portail](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [INTERFACE DE LIGNE DE COMMANDE](virtual-network-deploy-static-pip-arm-cli.md)
##### [Modèle](virtual-network-deploy-static-pip-arm-template.md)
##### [PowerShell (classique)](virtual-networks-reserved-public-ip.md)

#### Créer une machine virtuelle avec une adresse IP privée statique
##### [Portail](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [INTERFACE DE LIGNE DE COMMANDE](virtual-networks-static-private-ip-arm-cli.md)
##### [Portail (classique)](virtual-networks-static-private-ip-classic-pportal.md)
##### [PowerShell (classique)](virtual-networks-static-private-ip-classic-ps.md)
##### [Interface de ligne de commande (classique)](virtual-networks-static-private-ip-classic-cli.md)

#### Créer une machine virtuelle avec plusieurs interfaces réseau
##### [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [INTERFACE DE LIGNE DE COMMANDE](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [PowerShell (classique)](virtual-network-deploy-multinic-classic-ps.md)
##### [Interface de ligne de commande (classique)](virtual-network-deploy-multinic-classic-cli.md)

#### Créer une machine virtuelle avec plusieurs adresses IP
##### [Portail Azure](virtual-network-multiple-ip-addresses-portal.md)
##### [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [INTERFACE DE LIGNE DE COMMANDE](virtual-network-multiple-ip-addresses-cli.md)
##### [Modèle](virtual-network-multiple-ip-addresses-template.md)

#### [Créer une machine virtuelle avec mise en réseau accélérée](virtual-network-create-vm-accelerated-networking.md)

### Scénarios de connectivité
#### [Réseau virtuel à réseau virtuel](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Réseau virtuel (Resource Manager) à réseau virtuel (classique)](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Réseau virtuel à réseau local (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Réseau virtuel à réseau local (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Architecture réseau hybride hautement disponible](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### Scénarios de sécurité
#### [Sécuriser les réseaux avec des appliances virtuelles](virtual-network-scenario-udr-gw-nva.md)
#### [Zone DMZ entre Azure et Internet](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Service cloud et sécurité réseau](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Zone DMZ simple avec groupes de sécurité réseau](virtual-networks-dmz-nsg-asm.md)
##### [Zone DMZ avec pare-feu et groupes de sécurité réseau](virtual-networks-dmz-nsg-fw-asm.md)
##### [Zone DMZ avec pare-feu, itinéraires définis par l’utilisateur et groupes de sécurité réseau](virtual-networks-dmz-nsg-fw-udr-asm.md)
##### [Exemple d’application](virtual-networks-sample-app.md)

## Configuration
### [Optimiser le débit du réseau des machines virtuelles](virtual-network-optimize-network-bandwidth.md)
### Listes de contrôle d'accès
#### [Portail classique](virtual-networks-acl.md)
#### [PowerShell](virtual-networks-acl-powershell.md)
### [Résolution de noms pour les machines virtuelles et les services cloud](virtual-networks-name-resolution-for-vms-and-role-instances.md)

## Gérer
### groupes de sécurité réseau ;
#### [Portail](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [INTERFACE DE LIGNE DE COMMANDE](virtual-network-manage-nsg-arm-cli.md)
#### [Journaux](virtual-network-nsg-manage-log.md)
### Machines virtuelles
#### [Afficher et modifier les noms d’hôte](virtual-networks-viewing-and-modifying-hostnames.md)
#### [Déplacer une machine virtuelle vers un autre sous-réseau](virtual-networks-move-vm-role-to-subnet.md)

## Résolution des problèmes
### groupes de sécurité réseau ;
#### [Portail](virtual-network-nsg-troubleshoot-portal.md)
#### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Itinéraires
#### [Portail](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [Test de débit](virtual-network-bandwidth-testing.md)

# Référence
## [PowerShell (Resource Manager)](/powershell/module/azurerm.network)
## [PowerShell (classique)](/powershell/module/azure/?view=azuresmps-3.7.0)
## [Interface de ligne de commande Azure](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (classique)](https://msdn.microsoft.com/library/jj157182.aspx)


# Rubriques connexes
## [Machines virtuelles](/azure/virtual-machines/)
## [Application Gateway](/azure/application-gateway/)
## [DNS Azure](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Équilibreur de charge](/azure/load-balancer/)
## [Passerelle VPN](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# Ressources
## [Blog sur la mise en réseau](http://azure.microsoft.com/blog/topics/networking)
## [Forum sur la mise en réseau](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Tarification](https://azure.microsoft.com/pricing/details/virtual-network)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
