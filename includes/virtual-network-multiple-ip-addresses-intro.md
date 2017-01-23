> [!div class="op_single_selector"]
> * [Portail](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [INTERFACE DE LIGNE DE COMMANDE](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
> * [Modèle](../articles/virtual-network/virtual-network-multiple-ip-addresses-template.md)
>

Une ou plusieurs cartes réseau sont attachées à une machine virtuelle Azure. Une ou plusieurs adresses IP publiques ou privées, statiques ou dynamiques, peuvent être affectées à chaque carte réseau. L’affectation de plusieurs adresses IP à une machine virtuelle permet :

* D’héberger plusieurs sites web ou services avec des adresses IP différentes et des certificats SSL sur un serveur unique.
* de jouer le rôle d’une appliance virtuelle réseau, telle qu’un pare-feu ou un équilibreur de charge.
* La possibilité d’ajouter l’une des adresses IP privées pour toutes les cartes réseau à un pool principal Azure Load Balancer. Auparavant, seule l’adresse IP principale de la carte réseau principale pouvait être ajoutée à un pool principal. Pour plus d’informations sur la façon d’équilibrer la charge entre plusieurs configurations IP, consultez l’article [Équilibrage de la charge entre plusieurs configurations IP](../articles/load-balancer/load-balancer-multiple-ip.md).

Une ou plusieurs configurations IP sont associées à chaque carte réseau attachée à une machine virtuelle. Une adresse IP privée statique ou dynamique est affectée à chaque configuration. Une ressource d’adresse IP publique peut également être associée à chaque configuration. Une adresse IP publique statique ou dynamique est affectée à une ressource d’adresse IP publique. Pour plus d’informations sur les adresses IP dans Azure, consultez l’article [Adresses IP dans Azure](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md).


<!--HONumber=Dec16_HO2-->


