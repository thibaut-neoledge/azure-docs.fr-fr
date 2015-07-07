## Métadonnées de l’application API

Les métadonnées qui permettent à un projet d’API web d’être déployé comme application API se trouvent dans un fichier *apiapp.json* et un dossier *métadonnées*.

![](./media/app-service-api-review-metadata/metadatainse.png)

Le contenu par défaut du fichier *apiapp.json* ressemble à l’exemple suivant :

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "ContactsList",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "ContactsList",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    }
		}

Notez le point de terminaison `apiDefinition` de `/swagger/docs/v1` : par défaut, les projets d’application API utilisent le package NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) pour assurer la génération automatique des métadonnées de [Swagger](http://swagger.io/).

Pour ce didacticiel, vous pouvez accepter les valeurs par défaut. La section [Métadonnées d’une application API](#api-app-metadata) plus loin dans ce didacticiel explique comment personnaliser ces métadonnées.

<!---HONumber=62-->