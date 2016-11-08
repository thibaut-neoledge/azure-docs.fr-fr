## Tables de routage
Les ressources de table de routage contiennent les itinéraires utilisés pour définir la circulation du trafic au sein de votre infrastructure Azure. Vous pouvez utiliser les itinéraires définis par l’utilisateur (UDR) pour envoyer tout le trafic à partir d’un sous-réseau donné à un équipement virtuel, tel qu’un système de détection de pare-feu ou d’intrusion (IDS). Vous pouvez associer une table de routage à des sous-réseaux.

Les tables de routage peuvent contenir les propriétés suivantes.

| Propriété | Description | Exemples de valeurs |
| --- | --- | --- |
| **itinéraires** |Collection d’itinéraires définis par l’utilisateur dans la table de routage |voir [itinéraires définis par l’utilisateur](#User-defined-routes) |
| **Sous-réseaux** |Collection de sous réseaux auxquels la table de routage est appliquée |voir [sous-réseaux](#Subnets) |

### Itinéraires définis par l’utilisateur
Vous pouvez créer des itinéraires définis par l’utilisateur pour indiquer où le trafic doit être envoyé, en fonction de son adresse de destination. Vous pouvez considérer un itinéraire comme la définition de passerelle par défaut en fonction de l’adresse de destination d’un paquet réseau.

Les itinéraires définis par l’utilisateur peuvent contenir les propriétés suivantes.

| Propriété | Description | Exemples de valeurs |
| --- | --- | --- |
| **addressPrefix** |Préfixe d’adresse ou adresse IP complète pour la destination |192\.168.1.0/24, 192.168.1.101 |
| **nextHopType** |Type d’appareil vers lequel le trafic sera envoyé |VirtualAppliance, passerelle VPN, Internet |
| **nextHopIpAddress** |Adresse IP de tronçon suivant |192\.168.1.4 |

Exemple de table de routage au format JSON :

    {
        "name": "UDR-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd",
        "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
        "type": "Microsoft.Network/routeTables",
        "location": "westus",
        "properties": {
            "provisioningState": "Succeeded",
            "routes": [
                {
                    "name": "RouteToFrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd/routes/RouteToFrontEnd",
                    "etag": "W/"v"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "nextHopType": "VirtualAppliance",
                        "nextHopIpAddress": "192.168.0.4"
                    }
                }
            ],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd"
                }
            ]
        }
    }

### Ressources supplémentaires
* Obtenez davantage d’informations sur les [itinéraires définis par l’utilisateur](../articles/virtual-network/virtual-networks-udr-overview.md).
* Consultez la [documentation de référence d’API REST](https://msdn.microsoft.com/library/azure/mt502549.aspx) pour les tables de routage.
* Consultez la [documentation de référence d’API REST](https://msdn.microsoft.com/library/azure/mt502539.aspx) pour les itinéraires définis par l’utilisateur (UDR).

<!---HONumber=AcomDC_0323_2016-->