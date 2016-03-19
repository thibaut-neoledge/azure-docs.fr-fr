
<properties
	pageTitle="Optimiser votre application API pour les applications logiques | Microsoft Azure"
	description="Comment décorer votre application API afin qu’elle fonctionne correctement avec les applications logiques"
	services="app-service\logic"
	documentationCenter=".net"
	authors="sameerch"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016" 
	ms.author="sameerch"/>

# Optimisez votre application API pour les applications logiques #

Dans cet article, vous allez apprendre à configurer la définition d’API de votre application API, afin qu’elle fonctionne correctement avec des applications logiques. Cela améliorera l’expérience de l’utilisateur final pour votre application API lorsqu’elle est utilisée dans le concepteur d’applications logiques.

## Composants requis

Si vous n’êtes pas familiarisé avec les [applications API](app-service-api-apps-why-best-platform.md) dans [Microsoft Azure App Service](../app-service/app-service-value-prop-what-is.md), nous vous recommandons de lire la formation en plusieurs parties portant sur la [création d’applications API](app-service-dotnet-create-api-app.md).


## Afficher les noms d’affichage ##
Le concepteur d’applications logiques affiche le nom des opérations, des champs et des paramètres, qui peuvent parfois être difficiles à lire qu’ils sont générés par programme. Pour améliorer la lisibilité, le concepteur d’applications logiques peut, le cas échéant, afficher une valeur de texte plus facile à lire, appelée *nom d’affichage*, plutôt que le nom par défaut des opérations, des champs et des paramètres. Pour ce faire, il s’efforce de détecter certaines propriétés dans les métadonnées swagger fournies par votre application API. Les propriétés suivantes sont utilisées en tant que noms d’affichage :

* Opérations (action et déclencheurs) Valeur de la propriété **summary**, si elle existe ; sinon, valeur de la propriété **operationId**. Remarque : la spécification Swagger 2.0 permet de saisir un maximum de 120 caractères pour la propriété **summary**.

* Paramètres (entrées) Valeur de la propriété d’extension **x-ms-summary**, si elle est présente ; sinon, valeur de la propriété **name**. La propriété d’extension **x-ms-summary** doit être définie de manière dynamique dans le code. Ce processus est décrit dans la section « Utilisation d’attributs personnalisés pour annoter des propriétés d’extension » de cette rubrique. Pour définir la propriété **name**, vous pouvez utiliser des commentaires (///). Ce processus est décrit dans la section « Utilisation de commentaires XML lors de la génération de définitions d’API » de cette rubrique.

* Champs de schéma (réponses en sortie) Valeur de la propriété d’extension **x-ms-summary**, si elle est présente ; sinon, valeur de la propriété **name**. La propriété d’extension **x-ms-summary** doit être définie de manière dynamique dans le code. Ce processus est décrit dans la section « Utilisation d’attributs personnalisés pour annoter des propriétés d’extension » de cette rubrique. Pour définir la propriété **name**, vous pouvez utiliser des commentaires (///). Ce processus est décrit dans la section « Utilisation de commentaires XML lors de la génération de définitions d’API » de cette rubrique.

**Remarque :** il est recommandé de limiter la longueur des noms d’affichage à 30 caractères maximum.

### Utilisation de commentaires XML lors de la génération de définitions d’API

Pour un développement via Visual Studio, il est recommandé d’annoter vos contrôleurs d’API en utilisant des [commentaires XML](https://msdn.microsoft.com/library/b2s063f7.aspx). Lors d’une compilation avec [/doc](https://msdn.microsoft.com/library/3260k4x7.aspx), le compilateur crée un fichier de documentation XML. L’ensemble d’outils Swashbuckle inclus avec le Kit de développement logiciel (SDK) de l’application API peut incorporer ces commentaires tout en générant les métadonnées de l’API ; vous pouvez configurer votre projet d’API pour effectuer cette opération, en procédant comme suit :

1. Ouvrez votre projet dans Visual Studio.

2. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le projet et sélectionnez **Propriétés**.

	![Propriétés du projet](./media/app-service-api-optimize-for-logic-apps/project-properties.png)

3. Lorsque les pages de propriétés du projet s’affichent, procédez comme suit :

	- Sélectionnez la **configuration** à laquelle s’appliquent les paramètres. En règle générale, vous sélectionnez l’option Toutes les configurations, afin que les paramètres que vous spécifiez s’appliquent aux builds Debug et Release.
	
	- Sélectionnez l’onglet **Build**, affiché sur la gauche.
	
	- Vérifiez que l’option **Fichier de documentation XML** est activée. Visual Studio fournit un nom de fichier par défaut basé sur le nom du projet. Vous pouvez définir sa valeur sur ce que préconise votre convention d’affectation de noms, ou la laisser en l’état.

	![Définir la propriété de document XML](./media/app-service-api-optimize-for-logic-apps/xml-documentation-file-property.png)

4. Ouvrez le fichier *SwaggerConfig.cs* (situé dans le dossier **App\_Start** du projet).

5. Ajoutez des directives **using**, sur la partie supérieure du fichier *SwaggerConfig.cs*, pour les espaces de noms **System** et **System.Globalization**.

		using System;
		using System.Globalization;
 
6. Recherchez le fichier *SwaggerConfig.cs* pour un appel à **GetXmlCommentsPath**, et supprimez les commentaires de la ligne afin qu’il s’exécute. Étant donné que vous n’avez pas encore implémenté cette méthode, Visual Studio souligne l’appel à **GetXmlCommentsPath** et indique qu’il n’est pas défini dans le contexte actuel. C’est normal. Vous allez implémenter cet élément à l’étape suivante.

7. Ajoutez l’implémentation suivante de la méthode **GetXmlCommentsPath** dans la classe **SwaggerConfig** (définie dans le fichier *SwaggerConfig.cs*). Cette méthode renvoie simplement le fichier de documentation XML que vous avez spécifié précédemment dans les paramètres du projet.

        public static string GetXmlCommentsPath()
        {
            return String.Format(CultureInfo.InvariantCulture, 
								 @"{0}\bin\ContactsList.xml", 
								 AppDomain.CurrentDomain.BaseDirectory);
        }

8. Enfin, spécifiez les commentaires XML associés à vos méthodes de contrôleur. Pour ce faire, ouvrez l’un des fichiers du contrôleur de l’application API et saisissez « /// » sur une ligne vide précédant une méthode de contrôleur que vous souhaitez documenter. Visual Studio insère automatiquement une section commentée, dans laquelle vous pouvez spécifier un résumé de méthode, ainsi que les informations sur les valeurs de retour et les paramètres.

Désormais, lorsque vous générerez et publierez votre application API, vous verrez que le fichier de documentation figure également dans la charge utile et est chargé avec le reste de votre application API.

## Classer les propriétés et opérations avancées

Le concepteur d’applications logiques dispose d’un écran d’affichage limité pour les opérations, les paramètres et les propriétés. En outre, une application API peut définir un ensemble complet de propriétés et d’opérations. L’affichage d’un volume d’informations important dans une petite zone peut compliquer l’exécution du concepteur pour l’utilisateur final.

Pour atténuer cet encombrement, le concepteur d’applications logiques vous permet de regrouper les opérations et propriétés de l’application API dans des catégories définies par l’utilisateur. En classant les opérations et les propriétés de manière adéquate, une application API peut améliorer l’expérience utilisateur en présentant d’abord les opérations et propriétés les plus basiques et les plus utiles.

Pour offrir cette possibilité, le concepteur d’applications logiques s’efforce de détecter la présence d’une propriété d’extension de fournisseur personnalisée spécifique dans la définition API Swagger de votre application API. Cette propriété est appelée **x-ms-visibility** et peut prendre l’une des valeurs suivantes :

* Valeur vide ou « none » Ces opérations et propriétés sont immédiatement visibles par l’utilisateur.

* Valeur « Advanced » Comme ces opérations et propriétés sont avancées, elles sont masquées par défaut. Toutefois, l’utilisateur peut facilement y accéder, si nécessaire.

* Valeur « Internal » Ces opérations et propriétés sont traitées en tant que propriétés internes ou système ; elles ne sont pas censées être directement exploitées par l’utilisateur. Par conséquent, elles sont masquées par le concepteur, et mises à disposition uniquement en mode Code. Pour ces propriétés, vous pouvez également spécifier la propriété d’extension **x-ms-scheduler-recommendation** pour définir leur valeur via le concepteur d’applications logiques. Pour obtenir un exemple, consultez l’article relatif à l’[ajout de déclencheurs à une application API](app-service-api-dotnet-triggers.md).


## Utilisation d’attributs personnalisés pour annoter des propriétés d’extension

Comme indiqué ci-dessus, les propriétés d’extension de fournisseur personnalisées sont utilisées pour annoter les métadonnées d’API, afin de fournir des informations plus détaillées, que le concepteur d’applications logiques peut utiliser. Si vous utilisez des métadonnées statiques pour décrire votre application API, vous pouvez modifier directement le fichier */metadata/apiDefinition.swagger.json* dans votre projet, afin d’ajouter manuellement les propriétés d’extension requises.

Pour les applications API qui utilisent des métadonnées dynamiques, vous pouvez utiliser des attributs personnalisés pour annoter votre code. Vous pouvez définir un filtre d’opération dans le fichier *SwaggerConfig.cs*, afin de rechercher les attributs personnalisés et d’ajouter l’extension de fournisseur nécessaire. Cette approche est décrite en détail ci-dessous. Elle porte sur la génération dynamique de la propriété d’extension **x-ms-summary**.

1. Définissez une classe d’attributs appelée **CustomSummaryAttribute**, qui sera utilisée pour annoter votre code.

	    [AttributeUsage(AttributeTargets.All)]
	    public class CustomSummaryAttribute : Attribute
	    {
	        public string SummaryText { get; internal set; }

	        public CustomSummaryAttribute(string summaryText)
	        {
	            this.SummaryText = summaryText;
	        }
	    }

2. Définissez un filtre d’opération appelé **AddCustomSummaryFilter**, qui recherche cet attribut personnalisé dans les paramètres d’opération.


	    using Swashbuckle.Swagger;

		...

		public class AddCustomSummaryFilter : IOperationFilter
	    {
	        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
	        {
	            if (operation.parameters == null)
	            {
	                // no parameter
	                return;
	            }

	            foreach (var param in operation.parameters)
	            {
	                var summaryAttributes = apiDescription.ParameterDescriptions.First(x => x.Name.Equals(param.name))
	                                        .ParameterDescriptor.GetCustomAttributes<CustomSummaryAttribute>();

	                if (summaryAttributes != null && summaryAttributes.Count > 0)
	                {
	                    // add x-ms-summary extension
	                    if (param.vendorExtensions == null)
	                    {
	                        param.vendorExtensions = new Dictionary<string, object>();
	                    }
	                    param.vendorExtensions.Add("x-ms-summary", summaryAttributes[0].SummaryText);
	                }
	            }
	        }
	    }

3. Modifiez le fichier *SwaggerConfig.cs* et ajoutez la classe de filtre définie ci-dessus.


            GlobalConfiguration.Configuration
                .EnableSwagger(c =>
                    {
                        ...
                        c.OperationFilter<AddCustomSummaryFilter>();
                        ...
                    }

4. Utilisez la classe **CustomSummaryAttribute** pour annoter votre code, comme illustré dans l’extrait de code suivant.

        /// <summary>
        /// Send Message
        /// </summary>
        /// <param name="queueName">The name of the Storage Queue</param>
        /// <param name="messageText">The message text to be sent</param>
        public void SendMessage(
            [CustomSummary("Queue Name")] string queueName,
            [CustomSummary("Message Text")] string messageText)
        {
             ...
        }

	Lorsque vous générez l’application API ci-dessus, elle crée les métadonnées d’API suivantes :


			...
            "post": {
                ...
                "parameters": [
                    {
                        "name": "queueName",
                        "in": "query",
                        "description": "The name of the Storage Queue",
                        "required": true,
                        "x-ms-summary": "Queue Name",
                        "type": "string"
                    },
                    {
                        "name": "messageText",
                        "in": "query",
                        "description": "The message text to be sent",
                        "required": true,
                        "x-ms-summary": "Message Text",
                        "type": "string"
                    }
                ],
                ...

5. De même, vous pouvez définir le filtre de schéma **AddCustomSummarySchemaFilter** afin qu’il annote automatiquement la propriété d’extension **x-ms-summary** pour vos modèles de schéma, comme dans l’exemple suivant.

	    public class AddCustomSummarySchemaFilter: ISchemaFilter
	    {
	        public void Apply(Schema schema, SchemaRegistry schemaRegistry, Type type)
	        {
	            SetCustomSummary(schema, type.GetCustomAttribute<CustomSummaryAttribute>());

	            if (schema.properties != null)
	            {
	                foreach (var property in schema.properties)
	                {
	                    var summaryAttribute = type.GetProperty(property.Key).GetCustomAttribute<CustomSummaryAttribute>();
	                    SetCustomSummary(property.Value, summaryAttribute);
	                }
	            }
	        }

	        private static void SetCustomSummary(Schema schema, CustomSummaryAttribute summaryAttribute)
	        {
	            if (summaryAttribute != null)
	            {
	                if (schema.vendorExtensions == null)
	                {
	                    schema.vendorExtensions = new Dictionary<string, object>();
	                }
	                schema.vendorExtensions.Add("x-ms-summary", summaryAttribute.SummaryText);
	            }
	        }
	    }

## Résumé

Dans cet article, vous avez vu comment améliorer l’expérience utilisateur de votre application API lorsqu’elle est utilisée dans le concepteur d’applications logiques. Nous vous recommandons, à titre de meilleure pratique, de donner des noms conviviaux corrects à toutes les opérations (actions et déclencheurs), paramètres et propriétés. Il est également recommandé de fournir un maximum de 5 opérations de base. Pour les paramètres d’entrée, la recommandation consiste à limiter le nombre de propriétés de base à 4 au maximum ; pour les propriétés, la valeur recommandée est inférieure ou égale à 5. Vos opérations et propriétés restantes doivent être signalées comme avancées.
 

<!---HONumber=AcomDC_0114_2016-->