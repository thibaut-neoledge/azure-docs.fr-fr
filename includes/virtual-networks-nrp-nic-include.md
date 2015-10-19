## Carte d'interface réseau
 
Une ressource de carte d’interface réseau fournit la connectivité à un sous-réseau existant dans une ressource de réseau virtuel. Bien que vous puissiez créer une carte d’interface réseau en tant qu’objet autonome, vous devez l’associer à un autre objet pour fournir réellement la connectivité. Une carte d’interface réseau permet de connecter une machine virtuelle à un sous-réseau, à une adresse IP publique ou à un équilibreur de charge.

|Propriété|Description|Exemples de valeurs|
|---|---|---|
|**virtualMachine**|Machine virtuelle à laquelle est associée la carte d’interface réseau.|/subscriptions/{guid}/../Microsoft.Compute/virtualMachines/vm1|
|**macAddress**|Adresse MAC de la carte d’interface réseau|Toute valeur aléatoire comprise entre 4 et 30.|
|**networkSecurityGroup**|Groupe de sécurité réseau associé à la carte d’interface réseau|/subscriptions/{guid}/../Microsoft.Network/networkSecurityGroups/myNSG1|
|**dnsSettings**|Paramètres DNS pour la carte d’interface réseau.|Consultez [Adresse IP publique](#Public-IP-address).|

La carte d'interface réseau, ou NIC, représente une interface réseau qui peut être associée à une machine virtuelle. Une machine virtuelle peut comporter une ou plusieurs cartes d'interface réseau.

![Cartes d'interface réseau sur une seule machine virtuelle](./media/resource-groups-networking/Figure3.png)

### Configurations IP
Les cartes d’interface réseau ont un objet enfant nommé **ipConfigurations** qui contient les propriétés suivantes :

|Propriété|Description|Exemples de valeurs|
|---|---|---|
|**subnet**|Sous-réseau auquel est connectée la carte d’interface réseau.|/subscriptions/{guid}/../Microsoft.Network/virtualNetworks/myvnet1/subnets/mysub1|
|**privateIPAddress**|Adresse IP de la carte d’interface réseau dans le sous-réseau|10\.0.0.8|
|**privateIPAllocationMethod**|Méthode d’allocation des adresses IP|Dynamic ou Static|
|**enableIPForwarding**|Détermine si la carte d’interface réseau peut être utilisée pour le routage|true ou false|
|**primary**|Détermine si la carte d’interface réseau est la carte d’interface réseau principale de la machine virtuelle|true ou false|
|**publicIPAddress**|Adresse IP publique associée à la carte d’interface réseau|Consultez [Paramètres DNS](#DNS-settings).|
|**loadBalancerBackendAddressPools**|Pools d’adresses principaux auxquels la carte d’interface réseau est associée||
|**loadBalancerInboundNatRules**|Règles NAT de trafic entrant de l’équilibreur de charge auxquelles est associée la carte d’interface réseau||

Exemple d’adresse IP publique au format JSON :

	{
	    "name": "lb-nic1-be",
	    "id": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be",
	    "etag": "W/"0027f1a2-3ac8-49de-b5d5-fd46550500b1"",
	    "type": "Microsoft.Network/networkInterfaces",
	    "location": "eastus",
	    "properties": {
	        "provisioningState": "Succeeded",
	        "resourceGuid": "e80fdad0-f0da-44ab-816a-828c9ac3c20e",
	        "ipConfigurations": [
	            {
	                "name": "NIC-config",
	                "id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/NIC-config",
	                "etag": "W/"0027f1a2-3ac8-49de-b5d5-fd46550500b1"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "privateIPAddress": "10.0.0.4",
	                    "privateIPAllocationMethod": "Dynamic",
	                    "subnet": {
	                        "id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet"
	                    },
	                    "loadBalancerBackendAddressPools": [
	                        {
	                            "id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool"
	                        }
	                    ],
	                    "loadBalancerInboundNatRules": [
	                        {
	                            "id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1"
	                        }
	                    ]
	                }
	            }
	        ],
	        "dnsSettings": {
	            "dnsServers": [],
	            "appliedDnsServers": []
	        },
	        "macAddress": "00-0D-3A-10-F1-29",
	        "enableIPForwarding": false,
	        "primary": true,
	        "virtualMachine": {
	            "id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/nrprg/providers/Microsoft.Compute/virtualMachines/web1"
	        }
	    }
	}

<!---HONumber=Oct15_HO2-->