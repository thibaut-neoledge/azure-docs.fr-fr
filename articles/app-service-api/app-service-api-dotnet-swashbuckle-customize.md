
<properties 
	pageTitle="Personnaliser des définitions d’API générées par Swashbuckle" 
	description="Découvrez comment personnaliser les définitions d’API Swagger générées par Swashbuckle pour une application API dans Azure App Service." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2015" 
	ms.author="bradyg"/>

# Personnaliser des définitions d’API générées par Swashbuckle 

## Vue d'ensemble

Cet article explique comment personnaliser Swashbuckle pour gérer les scénarios courants de modification d’un comportement par défaut :

* Swashbuckle génère des identificateurs d'opération en double pour les surcharges des méthodes de contrôleur.
* Swashbuckle suppose que la seule réponse valide d'une méthode est HTTP 200 (OK). 
 
## Personnaliser la génération d'identificateurs d'opération

Swashbuckle génère des identificateurs d’opération Swagger en concaténant le nom du contrôleur et le nom de la méthode. Ce modèle crée un problème lorsque vous avez plusieurs surcharges pour une méthode : Swashbuckle génère les ID d’opération en double, ce qui est incompatible avec le format Swagger JSON.

Le code du contrôleur suivant, par exemple, force Swashbuckle à générer trois ID d’opération Contact\_Get.

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

Vous pouvez résoudre ce problème manuellement en donnant aux méthodes des noms uniques, tels que ceux-ci pour cet exemple :

* Obtenir
* GetById
* GetPage

L’alternative consiste à étendre Swashbuckle afin de pouvoir générer automatiquement des ID d’opération uniques.

Les étapes suivantes montrent comment personnaliser Swashbuckle en utilisant le fichier *SwaggerConfig.cs* inclus dans le projet avec le modèle de projet d’applications API de Visual Studio en version préliminaire. Vous pouvez également personnaliser Swashbuckle dans un projet d’API Web que vous configurerez pour être déployé sous la forme d’une application API.

1. Créer une implémentation `IOperationFilter` personnalisée 

	L’interface de `IOperationFilter` fournit un point d’extensibilité pour les utilisateurs de Swashbuckle qui souhaitent personnaliser divers aspects du processus des métadonnées Swagger. Le code suivant illustre une méthode de modification du comportement de génération de l’ID d’opération. Le code ajoute les noms de paramètre au nom de l’ID d’opération.

		using Swashbuckle.Swagger;
		using System.Web.Http.Description;
		
		namespace ContactsList
		{
		    public class MultipleOperationsWithSameVerbFilter : IOperationFilter
		    {
		        public void Apply(
		            Operation operation,
		            SchemaRegistry schemaRegistry,
		            ApiDescription apiDescription)
		        {
		            if (operation.parameters != null)
		            {
		                operation.operationId += "By";
		                foreach (var parm in operation.parameters)
		                {
		                    operation.operationId += string.Format("{0}",parm.name);
		                }
		            }
		        }
		    }
		}

2. Dans le fichier *App\_Start\\SwaggerConfig.cs*, appelez la méthode `OperationFilter` afin que Swashbuckle utilise la nouvelle implémentation `IOperationFilter`.

		c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

	![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

	Le fichier *SwaggerConfig.cs* qui est inséré par le package NuGet Swashbuckle contient de nombreux exemples commentés de points d’extensibilité. Les commentaires supplémentaires ne sont pas affichés ici.

	Après avoir apporté cette modification, votre implémentation `IOperationFilter` est utilisée et entraîne la génération d’ID d’opération uniques.
 
	![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

<a id="multiple-response-codes" name="multiple-response-codes"></a>
	
## Autoriser les codes de réponse autres que 200

Par défaut, Swashbuckle suppose que la réponse HTTP 200 (OK) est la *seule* réponse légitime d'une méthode d’API Web. Dans certains cas, vous voudrez peut-être utiliser d’autres codes de réponse sans que le client ne lève une exception. Par exemple, le code d'API Web suivant illustre un scénario dans lequel vous aimeriez que le client considère les réponses 200 ou 404 comme valides.

	[ResponseType(typeof(Contact))]
    public HttpResponseMessage Get(int id)
    {
        var contacts = GetContacts();

        var requestedContact = contacts.FirstOrDefault(x => x.Id == id);

        if (requestedContact == null)
        {
            return Request.CreateResponse(HttpStatusCode.NotFound);
        }
        else
        {
            return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
        }
    }

Dans ce scénario, le Swagger que Swashbuckle génère par défaut ne spécifie qu'un seul code d’état HTTP légitime, le code HTTP 200.

![](./media/app-service-api-dotnet-swashbuckle-customize/http-200-output-only.png)

Étant donné que Visual Studio utilise la définition d'API Swagger pour générer le code pour le client, il crée un code client qui déclenche une exception pour toute réponse autre que HTTP 200. Le code ci-dessous provient d’un client C# généré pour cet exemple de méthode d’API Web.

	if (statusCode != HttpStatusCode.OK)
    {
        HttpOperationException<object> ex = new HttpOperationException<object>();
        ex.Request = httpRequest;
        ex.Response = httpResponse;
        ex.Body = null;
        if (shouldTrace)
        {
            ServiceClientTracing.Error(invocationId, ex);
        }
        throw ex;
    } 

Swashbuckle fournit deux méthodes de personnalisation de la liste des codes de réponse HTTP attendus qu'il génère, à l'aide de commentaires XML ou de l’attribut `SwaggerResponse`. L'attribut est la méthode la plus simple, mais il est uniquement disponible dans Swashbuckle 5.1.5 ou version ultérieure. Le modèle d’aperçu de nouveau projet d’API Apps de Visual Studio 2013 comprend Swashbuckle 5.0.0, donc si vous avez utilisé le modèle et ne souhaitez pas mettre à jour Swashbuckle, vous n’avez pas d’autre choix que d’utiliser les commentaires XML.

### Personnaliser les codes de réponse attendue à l'aide de commentaires XML

Utilisez cette méthode pour spécifier les codes de réponse si votre version de Swashbuckle est antérieure à 5.1.5.

1. Tout d'abord, ajoutez des commentaires de documentation XML aux méthodes pour lesquelles vous souhaitez spécifier des codes de réponse HTTP. Si vous suivez l’exemple d’action d’API Web proposé ci-dessus et appliquez la documentation XML, vous obtiendrez un code semblable à celui ci-après 

		/// <summary>
		/// Returns the specified contact.
		/// </summary>
		/// <param name="id">The ID of the contact.</param>
		/// <returns>A contact record with an HTTP 200, or null with an HTTP 404.</returns>
		/// <response code="200">OK</response>
		/// <response code="404">Not Found</response>
		[ResponseType(typeof(Contact))]
		public HttpResponseMessage Get(int id)
		{
		    var contacts = GetContacts();
		
		    var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
		
		    if (requestedContact == null)
		    {
		        return Request.CreateResponse(HttpStatusCode.NotFound);
		    }
		    else
		    {
		        return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
		    }
		}

1. Ajoutez des instructions au fichier *SwaggerConfig.cs* pour que Swashbuckle utilise le fichier de documentation XML.

	* Ouvrez le fichier *SwaggerConfig.cs* et créez une méthode dans la classe *SwaggerConfig* pour spécifier le chemin d'accès au fichier de documentation XML. 

			private static string GetXmlCommentsPath()
			{
			    return string.Format(@"{0}\XmlComments.xml", 
			        System.AppDomain.CurrentDomain.BaseDirectory);
			}

	* Faites défiler le *SwaggerConfig.cs* jusqu'à l’apparition de la ligne de code commentée ressemblant à la capture d'écran ci-dessous.

		![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-commented-out.png)
	
	* Supprimez les commentaires de la ligne pour permettre le traitement des commentaires XML pendant la génération de Swagger.
	
		![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-uncommented.png)
	
1. Pour générer le fichier de documentation XML, accédez aux propriétés du projet et activez le fichier de documentation XML, comme illustré dans la capture d'écran ci-dessous.

	![](./media/app-service-api-dotnet-swashbuckle-customize/enable-xml-documentation-file.png)

Une fois ces étapes effectuées, le JSON Swagger généré par Swashbuckle reflète les codes de réponse HTTP que vous avez spécifié dans les commentaires XML. La capture d'écran ci-dessous illustre cette nouvelle charge JSON.

![](./media/app-service-api-dotnet-swashbuckle-customize/swagger-multiple-responses.png)

Lorsque vous utilisez Visual Studio pour régénérer le code client pour votre API REST, le code C# accepte les codes d'état HTTP OK et Not Found sans déclencher d’exception, permettant ainsi à votre code de consommation de prendre des décisions sur la façon de gérer le renvoi d'un enregistrement de Contact nul.

		if (statusCode != HttpStatusCode.OK && statusCode != HttpStatusCode.NotFound)
		{
		    HttpOperationException<object> ex = new HttpOperationException<object>();
		    ex.Request = httpRequest;
		    ex.Response = httpResponse;
		    ex.Body = null;
		    if (shouldTrace)
		    {
		        ServiceClientTracing.Error(invocationId, ex);
		    }
        	    throw ex;
		}

Vous trouverez le code pour cette démonstration dans [ce référentiel GitHub](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes). Le projet d'API Web balisé de commentaires de documentation XML est assorti d’un projet d'application de console qui contient un client généré pour cette API.

### Personnaliser les codes de réponse attendue à l'aide de l'attribut SwaggerResponse

L’attribut [SwaggerResponse](https://github.com/domaindrivendev/Swashbuckle/blob/master/Swashbuckle.Core/Swagger/Annotations/SwaggerResponseAttribute.cs) est disponible dans Swashbuckle 5.1.5 et versions ultérieures. Si votre projet fait appel à une version antérieure, la présente section commence par expliquer comment mettre à jour le package Swashbuckle NuGet afin que vous puissiez utiliser cet attribut.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet d’API Web, puis sélectionnez **Gérer les packages NuGet**. 

	![](./media/app-service-api-dotnet-swashbuckle-customize/manage-nuget-packages.png)

1. Cliquez sur le bouton *Mettre à jour* situé en regard du package *Swashbuckle* NuGet.

	![](./media/app-service-api-dotnet-swashbuckle-customize/update-nuget-dialog.png)

1. Ajouter les attributs *SwaggerResponse* aux méthodes d'action d’API Web pour lesquelles vous souhaitez spécifier des codes de réponse HTTP valides.

		[SwaggerResponse(HttpStatusCode.OK)]
		[SwaggerResponse(HttpStatusCode.NotFound)]
		[ResponseType(typeof(Contact))]
		public HttpResponseMessage Get(int id)
		{
		    var contacts = GetContacts();

		    var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
		    if (requestedContact == null)
		    {
		        return Request.CreateResponse(HttpStatusCode.NotFound);
		    }
		    else
		    {
		        return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
		    }
		}

2. Ajouter un énoncé `using` pour l'espace de noms de l'attribut :

		using Swashbuckle.Swagger.Annotations;
		
1. Accédez à la */swagger/docs/v1* URL de votre projet et les différents codes de réponse HTTP sera visible dans le JSON Swagger.

	![](./media/app-service-api-dotnet-swashbuckle-customize/multiple-responses-post-attributes.png)

Vous trouverez le code pour cette démonstration dans [ce référentiel GitHub](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes-With-Attributes). Le projet d'API Web doté de l’attribut *SwaggerResponse* est assorti d’un projet d'application de console qui contient un client généré pour cette API.

## Étapes suivantes

Cet article vous a expliqué comment personnaliser la façon dont Swashbuckle génère des identificateurs d'opération et des codes de réponse valides. Pour plus d’informations, consultez [Swashbuckle sur GitHub](https://github.com/domaindrivendev/Swashbuckle).
 

<!---HONumber=AcomDC_1203_2015-->