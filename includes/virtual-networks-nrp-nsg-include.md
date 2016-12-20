## <a name="network-security-group"></a>Groupe de sécurité réseau
Une ressource de groupe de sécurité réseau permet de créer des limites de sécurité pour les charges de travail, en implémentant des règles d'autorisation et de refus. Ces règles peuvent être appliquées à une machine virtuelle, une carte réseau ou un sous-réseau.

| Propriété | Description | Exemples de valeurs |
| --- | --- | --- |
| **Sous-réseaux** |Liste des ID de sous-réseau auxquels le groupe de sécurité réseau s'applique. |/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd |
| **securityRules** |Liste des règles de sécurité qui composent le groupe de sécurité réseau |Voir [Règle de sécurité](#Security-rule) ci-dessous |
| **defaultSecurityRules** |Liste des règles de sécurité par défaut présentes dans chaque groupe de sécurité réseau |Voir [Règles de sécurité par défaut](#Default-security-rules) ci-dessous |

* **Règle de sécurité** : plusieurs règles de sécurité peuvent être définies pour un groupe de sécurité réseau. Chaque règle peut autoriser ou refuser différents types de trafic.

### <a name="security-rule"></a>Règle de sécurité
Une règle de sécurité est une ressource enfant d'un groupe de sécurité réseau qui contient les propriétés ci-dessous.

| Propriété | Description | Exemples de valeurs |
| --- | --- | --- |
| **description** |Description de la règle |Autoriser le trafic entrant pour toutes les machines virtuelles dans un sous-réseau X |
| **protocol** |Protocole à faire correspondre pour la règle |TCP, UDP ou * |
| **sourcePortRange** |Plage de ports source à faire correspondre pour la règle |80, 100-200, * |
| **destinationPortRange** |Plage de ports de destination à faire correspondre pour la règle |80, 100-200, * |
| **sourceAddressPrefix** |Préfixe d'adresse source à faire correspondre pour la règle |10.10.10.1, 10.10.10.0/24, VirtualNetwork |
| **destinationAddressPrefix** |Préfixe d'adresse de destination à faire correspondre pour la règle |10.10.10.1, 10.10.10.0/24, VirtualNetwork |
| **direction** |Direction du trafic à faire correspondre pour la règle |entrant ou sortant |
| **priority** |Priorité de la règle. Les règles sont vérifiées dans l'ordre de priorité ; une fois qu'une règle s'applique, plus aucune règle n'est testée pour la correspondance. |10, 100, 65000 |
| **access** |Type d'accès à appliquer si la règle correspond |autoriser ou refuser |

Exemple de groupe de sécurité réseau au format JSON :

    {
        "name": "NSG-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkSecurityGroups",
        "location": "westus",
        "tags": {
            "displayName": "NSG - Front End"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "securityRules": [
                {
                    "name": "rdp-rule",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/rdp-rule",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "description": "Allow RDP",
                        "protocol": "Tcp",
                        "sourcePortRange": "*",
                        "destinationPortRange": "3389",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "access": "Allow",
                        "priority": 100,
                        "direction": "Inbound"
                    }
                }
            ],
            "defaultSecurityRules": [
                { [...],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                }
            ]
        }
    }

### <a name="default-security-rules"></a>Règles de sécurité par défaut

Les règles de sécurité par défaut ont les mêmes propriétés que les règles de sécurité. Elles existent pour fournir une connectivité de base entre les ressources qui ont un groupe de sécurité réseau appliqué. Vérifiez les [règles de sécurité par défaut](../articles/virtual-network/virtual-networks-nsg.md#default-rules) existantes.

### <a name="additional-resources"></a>Ressources supplémentaires
* Obtenez davantage d'informations sur les [groupes de sécurité réseau](../articles/virtual-network/virtual-networks-nsg.md).
* Consultez la [documentation de référence d'API REST](https://msdn.microsoft.com/library/azure/mt163615.aspx) pour les groupes de sécurité réseau.
* Consultez la [documentation de référence d'API REST](https://msdn.microsoft.com/library/azure/mt163580.aspx) pour les règles de sécurité.


<!--HONumber=Nov16_HO3-->


