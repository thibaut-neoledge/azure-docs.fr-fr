## Profil Traffic Manager

Traffic Manager et ses ressources de point de terminaison enfant activent le routage DNS vers les points de terminaison dans Azure et en dehors d’Azure. Cette répartition du trafic est régie par des stratégies de routage. Traffic Manager permet également de surveiller l'intégrité des points de terminaison, ainsi que le trafic dévié de manière appropriée en fonction de l'intégrité d'un point de terminaison.

| Propriété | Description |
|---|---|
|**trafficRoutingMethod**| Les valeurs possibles sont *Performance*, *Weighted* et *Priority*. | 
| **dnsConfig** | Nom de domaine complet du profil | 
| **Protocole** | Protocole de surveillance. Les valeurs possibles sont *HTTP* et *HTTPS*.|
| **Port** | Port de surveillance |  
| **Chemin d’accès** | Chemin d’accès de surveillance |
| **Points de terminaison** | Conteneur pour les ressources des points de terminaison | 

### Point de terminaison 

Un point de terminaison est une ressource enfant d'un profil Traffic Manager. Il représente un service ou un point de terminaison web vers lequel le trafic est réparti en fonction de la stratégie configurée dans la ressource de profil Traffic Manager.

| Propriété | Description | 
|---|---| 
| **Type** | Type du point de terminaison. Les valeurs possibles sont *Point de terminaison Azure*, *Point de terminaison externe* et *Point de terminaison imbriqué*. | 
| **targetResourceId** | Adresse IP publique d’un service ou d’un point de terminaison web. Ce peut être un point de terminaison Azure ou un point de terminaison externe. | 
| **Poids** | Poids du point de terminaison utilisé dans la gestion du trafic. | 
| **Priorité** | Priorité du point de terminaison, utilisée pour définir une action de basculement. |

Exemple Traffic Manager au format Json :


        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }

 
## Ressources supplémentaires

Lisez la [documentation sur l’API REST pour Traffic Manager](https://msdn.microsoft.com/library/azure/mt163664.aspx) pour plus d’informations.

<!---HONumber=AcomDC_1223_2015-->