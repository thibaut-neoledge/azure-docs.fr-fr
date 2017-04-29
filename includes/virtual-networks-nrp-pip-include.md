## <a name="public-ip-address"></a>Adresse IP publique
Une ressource d’adresse IP publique fournit une adresse IP réservée ou une adresse IP dynamique accessible sur Internet. Bien que vous puissiez créer une adresse IP publique en tant qu’objet autonome, vous devez l’associer à un autre objet pour utiliser réellement l’adresse. Vous pouvez associer une adresse IP publique à un équilibrage de charge, à une passerelle d’application ou à une carte réseau pour fournir un accès Internet à ces ressources.  

| Propriété | Description | Exemples de valeurs |
| --- | --- | --- |
| **publicIPAllocationMethod** |Définit si l’adresse IP est *statique* ou *dynamique*. |static, dynamic |
| **idleTimeoutInMinutes** |Définit le délai d’inactivité, avec une valeur par défaut de 4 minutes. Si aucun autre paquet pour une session donnée n'est reçu dans ce délai, la session est interrompue. |Toute valeur aléatoire comprise entre 4 et 30. |
| **ipAddress** |Adresse IP assignée à l’objet. Il s’agit d’une propriété en lecture seule. |104.42.233.77 |

### <a name="dns-settings"></a>Paramètres DNS
Les adresses IP publiques ont un objet enfant nommé **dnsSettings** qui contient les propriétés suivantes :

| Propriété | Description | Exemples de valeurs |
| --- | --- | --- |
| **domainNameLabel** |Ordinateur hôte nommé utilisé pour la résolution de nom. |www, ftp, vm1 |
| **fqdn** |Nom complet de l'adresse IP publique. |www.westus.cloudapp.azure.com |
| **reverseFqdn** |Nom de domaine complet (FQDN) qui résout l’adresse IP et est inscrit dans DNS en tant qu’enregistrement PTR. |www.contoso.com. |

Exemple d’adresse IP publique au format JSON :

    {
       "name": "PIP01",
       "location": "North US",
       "tags": { "key": "value" },
       "properties": {
          "publicIPAllocationMethod": "Static",
          "idleTimeoutInMinutes": 4,
          "ipAddress": "104.42.233.77",
          "dnsSettings": {
             "domainNameLabel": "mylabel",
             "fqdn": "mylabel.westus.cloudapp.azure.com",
             "reverseFqdn": "contoso.com."
          }
       }
    } 

### <a name="additional-resources"></a>Ressources supplémentaires
* Obtenez davantage d’informations sur les [adresses IP publiques](../articles/virtual-network/virtual-networks-reserved-public-ip.md).
* En savoir plus sur les [adresses IP publiques de niveau d’instance](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).
* Consultez la [documentation de référence API REST](https://msdn.microsoft.com/library/azure/mt163638.aspx) pour les adresses IP publiques.

