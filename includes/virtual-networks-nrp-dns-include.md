## DNS Azure

Azure DNS est un service d'hébergement pour les domaines DNS et qui offre une résolution de noms à l'aide de l'infrastructure Microsoft Azure.


| Propriété | Description | Exemple de valeur |
|---|---|---|
| **DNSzones** | Informations relatives à la zone du domaine pour l'hébergement des enregistrements DNS d'un domaine particulier | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com"| 


### Jeux d'enregistrements DNS

Les zones DNS ont un objet enfant appelé jeu d'enregistrements. Les jeux d'enregistrements sont un ensemble d'enregistrements hôtes par type pour une zone DNS. Les types d'enregistrements sont A, AAAA, CNAME, MX, NS, SOA,SRV et TXT.

| Propriété | Description | Exemple de valeur |
|---|---|---|
| Une | Type d'enregistrement IPv4 | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/A/www |
| AAAA | Type d'enregistrement IPv6| /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/AAAA/hostrecord |
| CNAME | type d'enregistrement nom canonique <sup>1</sup> | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/CNAME/www |
| MX | type d'enregistrement messagerie | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/MX/mail |
| NS | type d'enregistrement serveur nom | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/NS/ |
| SOA | Type d'enregistrement « SOA » (Start of Authority) <sup>2</sup> | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SOA |
| SRV | type d'enregistrement service | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SRV |

<sup>1</sup> autorise uniquement une valeur par jeu d'enregistrements.

<sup>2</sup> autorise uniquement un type d'enregistrement SOA par zone DNS.

Exemple de zone DNS au format Json :

	{
	  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "newZoneName": {
	      "type": "String",
	      "metadata": {
	          "description": "The name of the DNS zone to be created."
	      }
	    },
	    "newRecordName": {
	      "type": "String",
	      "defaultValue": "www",
	      "metadata": {
	          "description": "The name of the DNS record to be created.  The name is relative to the zone, not the FQDN."
	      }
	    }
	  },
	  "resources": 
	  [
	    {
	      "type": "microsoft.network/dnszones",
	      "name": "[parameters('newZoneName')]",
	      "apiVersion": "2015-05-04-preview",
	      "location": "global",
	      "properties": {
	      }
	    },
	    {
	      "type": "microsoft.network/dnszones/a",
		  "name": "[concat(parameters('newZoneName'), concat('/', parameters('newRecordName')))]",
      	"apiVersion": "2015-05-04-preview",
      	"location": "global",
	  	"properties": 
	  	{
        	"TTL": 3600,
			"ARecords": 
			[
			    {
				    "ipv4Address": "1.2.3.4"
				},
				{
				    "ipv4Address": "1.2.3.5"
				}
			]
	  	},
	  	"dependsOn": [
        	"[concat('Microsoft.Network/dnszones/', parameters('newZoneName'))]"
      	]
    	}
	  	]
	}

## Ressources supplémentaires

Lisez la [documentation API REST pour les zones DNS](https://msdn.microsoft.com/library/azure/mt130626.aspx) pour plus d'informations.

Lisez la [documentation API REST pour les jeux d'enregistrements DNS](https://msdn.microsoft.com/library/azure/mt130627.aspx) pour plus d'informations.

<!---HONumber=AcomDC_0128_2016-->