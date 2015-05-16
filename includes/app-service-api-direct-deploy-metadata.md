La plupart des propriétés du fichier *apiapp.json*, ainsi que les fichiers du dossier *Metadata*, affectent la façon dont le package d'application API est présenté dans Azure Marketplace. Les sections suivantes expliquent quelles propriétés et quels fichiers affectent les applications API quand vous déployez votre code directement au lieu d'installer une application API depuis le Marketplace.

### ID d'application API 

La propriété `id` détermine le nom de l'application API. Par exemple :

		"id": "ContactsList",

![](./media/app-service-api-direct-deploy-metadata/apiappname.png)

### Espace de noms

Définissez la propriété `namespace` avec le domaine de votre client Azure Active Directory. Pour rechercher votre domaine, ouvrez votre navigateur sur le [portail classique Azure](https://manage.windowsazure.com/), accédez à **Active Directory**, puis sélectionnez l'onglet **Domaines**. Par exemple :

		"namespace": "contoso.onmicrosoft.com",

### Définition d'API Swagger dynamique

Pour fournir un point de terminaison d'URL pour une définition d'API [Swagger](http://swagger.io/) dynamique, stockez dans la propriété `endpoints.apiDefinition` l'URL relative d'une API exposée par l'application API, qui retourne une définition d'API Swagger 2.0 sur une demande GET. Par exemple :

		"endpoints": {
		    "apiDefinition": "/swagger/docs/v1"
		}

### Définition d'une API Swagger statique

Pour fournir un fichier de définition d'API [Swagger](http://swagger.io/) 2.0 statique, stockez le fichier dans le dossier *Metadata* et nommez le fichier *apiDefinition.swagger.json*

![](./media/app-service-api-direct-deploy-metadata/apidefinmetadata.png)

Laissez `endpoints.apiDefinition` en dehors du fichier *apiapp.json* ou définissez sa valeur à null. Si vous incluez à la fois une URL `endpoints.apiDefinition` et un fichier *apiDefinition.swagger.json*, l'URL sera prioritaire et le fichier sera ignoré.
 
### Autres métadonnées d'une application API

Pour plus d'informations sur le fichier *apiapp.json* et sur le dossier *Metadata*, consultez [Créer un package d'application API](app-service-api-create-package.md).


<!--HONumber=52-->
