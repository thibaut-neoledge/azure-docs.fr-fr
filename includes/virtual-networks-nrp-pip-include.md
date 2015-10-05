## Adresse IP publique
Une ressource d'adresse IP publique fournit une adresse IP publique réservée ou une adresse IP publique dynamique. Bien que vous puissiez créer une adresse IP publique en tant qu’objet autonome, vous devez l’associer à un autre objet pour utiliser réellement l’adresse. Vous pouvez associer une adresse IP publique à un équilibrage de charge, à une passerelle d’application ou à une carte réseau pour fournir un accès Internet à ces ressources.

|Propriété|Description|Exemples de valeurs|
|---|---|---|
|**publicIPAllocationMethod**|Définit si l’adresse IP est *statique* ou *dynamique*.|static, dynamic|
|**idleTimeoutInMinutes**|Définit le délai d’inactivité.|Toute valeur aléatoire comprise entre 4 et 30.|
|**ipAddress**|Adresse IP assignée à l’objet. Il s’agit d’une propriété en lecture seule.|104\.42.233.77|

### Paramètres DNS
Les adresses IP publiques ont un objet enfant nommé **dnsSettings** qui contient les propriétés suivantes :

|Propriété|Description|Exemples de valeurs|
|---|---|---|
|**domainNameLabel**|Ordinateur hôte nommé utilisé pour la résolution de nom.|www, ftp, vm1|
|**reverseFqdn**|Nom de domaine complet (FQDN) qui résout l’adresse IP et est inscrit dans DNS en tant qu’enregistrement PTR.|www.contoso.com.|

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
	         "reverseFqdn": "contoso.com."
	      }
	   }
	} 

<!---HONumber=Sept15_HO4-->