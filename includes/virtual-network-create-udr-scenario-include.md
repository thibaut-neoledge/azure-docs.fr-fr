## Scénario

Pour mieux illustrer la création d’itinéraires définis par l’utilisateur, ce document utilise le scénario ci-dessous.

![IMAGE DESCRIPTIVE](./media/virtual-network-create-udr-scenario-include/figure1.png)

Dans ce scénario, vous allez créer un itinéraire défini par l’utilisateur pour le *sous-réseau frontal* et un autre pour le *sous-réseau principal*, comme décrit ci-dessous :

- **UDR-FrontEnd**. L’itinéraire frontal défini par l’utilisateur est appliqué au sous-réseau *FrontEnd*, et contient un itinéraire :	
	- **RouteToBackend**. Cet itinéraire envoie tout le trafic sur le sous-réseau d’extrémité principal à la machine virtuelle **FW1**.
- **UDR-BackEnd**. L’itinéraire frontal défini par l’utilisateur est appliqué au sous-réseau *FrontEnd*, et contient un itinéraire :	
	- **RouteToBackend**. Cet itinéraire envoie tout le trafic sur le sous-réseau d’extrémité principal à la machine virtuelle **FW1**.

La combinaison de ces itinéraires garantit que tout le trafic qui transite d’un sous-réseau à un autre est routé vers la machine virtuelle **FW1**, qui fait office d’équipement virtuel. Vous devez également activer le transfert IP pour cette machine virtuelle, afin qu’elle puisse recevoir le trafic destiné aux autres machines virtuelles.

<!---HONumber=Oct15_HO2-->