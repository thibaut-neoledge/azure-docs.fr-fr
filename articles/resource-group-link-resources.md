<properties 
	pageTitle="Liaison des ressources dans Azure Resource Manager" 
	description="Liez différentes ressources dans différents groupes de ressources dans Azure Resource Manager." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="tomfitz"/>

# Liaison des ressources dans Azure Resource Manager

Après le déploiement, vous pouvez avoir besoin d’interroger les relations ou les liens entre les ressources. Les dépendances informent le processus de déploiement, mais ce cycle se termine lors du déploiement. Une fois le déploiement terminé, il n’existe aucune relation identifiée entre les ressources dépendantes.

Au lieu de cela, Azure Resource Manager fournit une nouvelle fonctionnalité appelée « liaison des ressources », qui permet d’établir des relations entre les ressources et d’interroger les relations existantes. Vous pouvez déterminer quelles ressources sont liées à une ressource ou quelles ressources sont liées à partir d’une ressource.

L’étendue d’une liaison de ressource peut correspondre à un abonnement, un groupe de ressources ou une ressource spécifique. Cela signifie que les liaisons vers les ressources peuvent renseigner les relations qui s’étendent sur des groupes de ressources. Lorsque vous commencez à décomposer votre solution en plusieurs groupes de ressources et plusieurs modèles, le fait d’avoir un mécanisme pour identifier ces liaisons de ressources s’avère utile. Par exemple, il est courant qu’une base de données avec son propre cycle de vie réside dans un groupe de ressources, tandis qu’une application avec un autre cycle de vie réside dans un autre groupe de ressources. L’application se connecte à la base de données, il y a donc une liaison entre les ressources dans différents groupes de ressources.

Toutes les ressources liées doivent appartenir au même abonnement. Chaque ressource peut être liée à 50 autres ressources. Si l’une des ressources liées est supprimée ou déplacée, le propriétaire de la liaison doit nettoyer la liaison restante.

## Modèles de liaisons

L’exemple ci-dessous montre un modèle créant une ressource de type « Microsoft.AppService/apiapps » avec un ensemble de relations unidirectionnelles vers un site web, un concentrateur de notification et des bases de données SQL.

    {
        "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "$system": {
                "type": "Object"
            }
        },
        "resources": [
            {
                "apiVersion": "2014-11-01",
                "type": "Microsoft.Web/sites",
                "name": "[parameters('$system').siteName]",
                "location": "[parameters('$system').location]",
                "resources": [
                    {
                        "apiVersion": "2014-11-01",
                        "name": "appsettings",
                        "type": "config",
                        "dependsOn": [
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "MS_MobileServiceName": "[parameters('$system').apiAppName]",
                            "MS_NotificationHubName": "[variables('notificationHubName')]",
                            "MS_NotificationHubConnectionString": "[listkeys(resourceId('Microsoft.NotificationHubs/namespaces/notificationHubs/authorizationRules', variables('notificationHubNamespace'), variables('notificationHubName'), 'DefaultFullSharedAccessSignature'), '2014-09-01').primaryConnectionString]"
                        }
                    }
                ]
            },
            {
                "apiVersion": "[parameters('$system').apiVersion]",
                "type": "Microsoft.AppService/apiapps",
                "name": "[parameters('$system').apiAppName]",
                "properties": {
                    "accessLevel": "PublicAnonymous"
                },
                "resources": [
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-codesite",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.Web/Sites', variables('userSiteName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.Web/sites', variables('userSiteName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-notificationhub",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqlserver",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName)]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqldb",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName, '/databases/', parameters('userDatabase').databaseName)]"
                        }
                    }
                ]
            }
        ]
    }

## Liaison avec une API REST

Pour définir une liaison entre des ressources déployées, exécutez :

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Remplacez {subscription-id} par l’identificateur de votre abonnement. Remplacez {resource-group}, {provider-namespace}, {resource-type} et {resource-name} par les valeurs identifiant la première ressource de la liaison. Remplacez {link-name} par le nom du lien à créer. Utilisez 2015-01-01 pour la version d’API.

Dans la demande, intégrez un objet définissant la deuxième ressource de la liaison :

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

L’élément « properties » contient l’identificateur de la deuxième ressource.

Pour consulter d’autres exemples traitant notamment de la récupération d’informations à propos des liaisons, consultez la section [Ressources liées](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## Étapes suivantes

- Vous pouvez également organiser vos ressources en utilisant des balises. Pour en savoir plus sur le balisage de ressources, consultez la page [Organisation des ressources Azure à l’aide de balises](resource-group-using-tags.md).
- Pour consulter une description de la création de modèles et définir les ressources à déployer, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

<!---HONumber=July15_HO3-->