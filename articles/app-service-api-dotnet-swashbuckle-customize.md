
<properties 
	pageTitle="Personnalisation des identificateurs d’opération générés avec Swashbuckle" 
	description="Découvrez comment personnaliser les identificateurs d’opération Swagger générés par Swashbuckle pour une application API dans Azure App Service." 
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
	ms.date="05/16/2015" 
	ms.author="bradyg"/>

# Personnalisation des identificateurs d’opération générés avec Swashbuckle 

## Vue d'ensemble

Swashbuckle génère des identificateurs d’opération Swagger en concaténant le nom du contrôleur et le nom de la méthode. Ce modèle crée un problème lorsque vous avez plusieurs surcharges pour une méthode : Swashbuckle génère les ID d’opération en double, ce qui est incompatible avec le format Swagger JSON.

Le code du contrôleur suivant, par exemple, force Swashbuckle à générer trois ID d’opération Contact_Get.

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

Vous pouvez résoudre ce problème manuellement en donnant aux méthodes des noms uniques, tels que ceux-ci pour cet exemple :

* Obtenir
* GetById
* GetPage

L’alternative consiste à étendre Swashbuckle afin de pouvoir générer automatiquement des ID d’opération uniques. Cet article explique comment procéder.

## Étendre Swashbuckle 

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

2. Dans le fichier *App_Start\SwaggerConfig.cs*, appelez la méthode `OperationFilter` afin que Swashbuckle utilise la nouvelle implémentation `IOperationFilter`.

		c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

	![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

	Le fichier *SwaggerConfig.cs* qui est inséré par le package NuGet Swashbuckle contient de nombreux exemples commentés de points d’extensibilité. Les commentaires supplémentaires ne sont pas affichés ici.

	Après avoir apporté cette modification, votre implémentation `IOperationFilter` est utilisée et entraîne la génération d’ID d’opération uniques.
 
	![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

## Étapes suivantes

Cet article vous a montré comment personnaliser Swashbuckle afin de pouvoir générer des ID d’opération uniques. Pour plus d’informations, consultez [Swashbuckle sur GitHub](https://github.com/domaindrivendev/Swashbuckle).

<!---HONumber=58-->