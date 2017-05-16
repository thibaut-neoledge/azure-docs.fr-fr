## <a name="virtual-network"></a>Réseau virtuel
Les ressources de réseaux virtuels et de sous-réseaux permettent de définir une limite de sécurité pour les charges de travail s’exécutant dans Azure. Un réseau virtuel est caractérisé par une collection d’espaces d’adressage, appelés blocs CIDR. 

> [!NOTE]
> Les administrateurs réseau sont familiarisés avec la notation CIDR. Si vous n’êtes pas familiarisé avec CIDR, [obtenez davantage d’informations](http://whatismyipaddress.com/cidr).
> 
> 

![Réseau virtuel avec plusieurs sous-réseaux](./media/resource-groups-networking/Figure4.png)

Les réseaux virtuels contiennent les propriétés suivantes.

| Propriété | Description | Exemples de valeurs |
| --- | --- | --- |
| **addressSpace** |Collection de préfixes d’adresses qui composent le réseau virtuel dans la notation CIDR |192.168.0.0/16 |
| **Sous-réseaux** |Collection des sous-réseaux qui composent le réseau virtuel |voir [sous-réseaux](#Subnets) ci-dessous. |
| **ipAddress** |Adresse IP assignée à l’objet. Il s’agit d’une propriété en lecture seule. |104.42.233.77 |

### <a name="subnets"></a>Sous-réseaux
Un sous-réseau est une ressource enfant d’un réseau virtuel, et permet de définir des segments d’espaces d’adressage dans un bloc CIDR, à l’aide de préfixes d’adresses IP. Les cartes d’interface réseau (NIC) peuvent être ajoutées aux sous-réseaux et connectées aux machines virtuelles, ce qui fournit une connectivité pour différentes charges de travail.

Les sous-réseaux contiennent les propriétés suivantes. 

| Propriété | Description | Exemples de valeurs |
| --- | --- | --- |
| **addressPrefix** |Préfixe d’adresse unique qui constitue le sous-réseau dans la notation CIDR |192.168.1.0/24 |
| **networkSecurityGroup** |NSG appliquée au sous-réseau |voir [Groupes de sécurité réseau](#Network-Security-Group) |
| **routeTable** |Table de routage appliquée au sous-réseau |voir [itinéraires définis par l’utilisateur](#Route-table) |
| **ipConfigurations** |Collection d’objets de configuration IP utilisée par la carte réseau connectée au sous-réseau |voir [itinéraires définis par l’utilisateur](#Route-table) |

Exemple de réseau virtuel au format JSON :

    {
        "name": "TestVNet",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "westus",
        "tags": {
            "displayName": "VNet"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "addressSpace": {
                "addressPrefixes": [
                    "192.168.0.0/16"
                ]
            },
            "subnets": [
                {
                    "name": "FrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "networkSecurityGroup": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "routeTable": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                        },
                        "ipConfigurations": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                            },
                            ...]
                    }
                },
                ...]
        }
    }

### <a name="additional-resources"></a>Ressources supplémentaires
* Obtenez davantage d’informations sur les [réseaux virtuels](../articles/virtual-network/virtual-networks-overview.md).
* Consultez la [documentation de référence d’API REST](https://msdn.microsoft.com/library/azure/mt163650.aspx) pour les réseaux virtuels.
* Consultez la [documentation de référence d’API REST](https://msdn.microsoft.com/library/azure/mt163618.aspx) pour les sous-réseaux.

