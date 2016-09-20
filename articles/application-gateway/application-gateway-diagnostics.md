<properties 
   pageTitle="Surveiller les journaux d’accès et des performances pour Application Gateway | Microsoft Azure"
   description="Découvrez comment activer et gérer les journaux d’accès et des performances pour Application Gateway"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2016"
   ms.author="amitsriva" />

# Journalisation des diagnostics pour Application Gateway

Vous pouvez utiliser différents types de journaux dans Azure pour gérer les passerelles Application Gateway et résoudre les problèmes associés. Certains de ces journaux sont accessibles par le biais du portail et tous les journaux peuvent être extraits à partir d’un stockage blob Azure et affichés dans différents outils, notamment [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel et PowerBI. Pour en savoir plus sur les différents types de journaux, consultez la liste suivante.

- **Journaux d’audit :** vous pouvez utiliser les [journaux d’audit Azure](../azure-portal/insights-debugging-with-events.md) (anciennement journaux des opérations) pour afficher toutes les opérations soumises à votre abonnement Azure, ainsi que leur état. Les journaux d’audit sont activés par défaut et peuvent être affichés dans le portail Azure en version préliminaire.
- **Journaux d’accès :** vous pouvez utiliser ce journal pour afficher le modèle d’accès Application Gateway et analyser des informations importantes, notamment l’adresse IP de l’appelant, l’URL demandée, la latence de réponse, le code de retour, les octets d’entrée et de sortie. Le journal d’accès est collecté toutes les 300 secondes. Ce journal contient un enregistrement par instance Application Gateway. L’instance Application Gateway peut être identifié par la propriété « instanceId ».
- **Journaux de performances :** vous pouvez utiliser ce journal pour afficher les performances des instances d’Application Gateway. Ce journal capture des informations sur les performances par instance, notamment le nombre total de demandes traitées, le débit en octets, le nombre total de demandes présentées, le nombre de demandes ayant échoué, le nombre d’instances du serveur principal correctes et incorrectes. Le journal des performances est collecté toutes les 60 secondes.

>[AZURE.WARNING] Les journaux ne sont disponibles que pour les ressources déployées avec le modèle de déploiement de Resource Manager. Vous ne pouvez pas les utiliser pour les ressources utilisant le modèle de déploiement classique. Pour mieux comprendre ces deux modèles, reportez-vous à l’article [Présentation du déploiement de Resource Manager et du déploiement classique](../resource-manager-deployment-model.md).

## Activation de la journalisation
La journalisation d’audit est automatiquement activée pour chaque ressource Resource Manager. Vous devez activer la journalisation de l’accès et des performances pour commencer à collecter les données disponibles dans ces journaux. Pour activer la journalisation, consultez les étapes suivantes.

1. Notez l’ID de ressource de votre compte de stockage, où les données de journalisation sont stockées. Il peut avoir la forme suivante : /subscriptions/<ID de l’abonnement>/resourceGroups/<nom du groupe de ressources>/providers/Microsoft.Storage/storageAccounts/<nom du compte de stockage>. Vous pouvez utiliser n’importe quel compte de stockage dans votre abonnement. Vous pouvez utiliser la version préliminaire du portail pour rechercher ces informations.

	![Portail en version préliminaire - Diagnostics Application Gateway](./media/application-gateway-diagnostics/diagnostics1.png)

2. Notez l’ID de ressource de votre Application Gateway pour laquelle la journalisation doit être activée. Il peut avoir la forme suivante : /subscriptions/<ID de l’abonnement>/resourceGroups/<nom du groupe de ressources>/providers/Microsoft.Network/applicationGateways/<nom de la passerelle Application Gateway>. Vous pouvez utiliser la version préliminaire du portail pour rechercher ces informations.

	![Portail en version préliminaire - Diagnostics Application Gateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Activez la journalisation des diagnostics à l’aide de l’applet de commande PowerShell suivante.

		Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true 	

>[AZURE.INFORMATION] Les journaux d’audit ne nécessitent pas de compte de stockage distinct. L’utilisation du stockage pour la journalisation de l’accès et des performances occasionne des frais de service.

## Journal d’audit

Ce journal (anciennement appelé « journal des opérations ») est généré par Azure par défaut. Les journaux sont conservés pendant 90 jours dans la banque de journalisation des événements d’Azure. Pour en savoir plus sur ces journaux, lisez l’article [Affichage des événements et des journaux d’audit](../azure-portal/insights-debugging-with-events.md).

## Journal d’accès

Ce journal n’est généré que si vous l’avez activé au niveau de chaque passerelle Application Gateway, comme détaillé dans les étapes précédentes. Les données sont stockées dans le compte de stockage spécifié lors de l’activation de la journalisation. Chaque accès à la passerelle Application Gateway est journalisé au format JSON, comme indiqué dans l’exemple suivant.

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
		"operationName": "ApplicationGatewayAccess",
		"time": "2016-04-11T04:24:37Z",
		"category": "ApplicationGatewayAccessLog",
		"properties": {
			"instanceId":"ApplicationGatewayRole_IN_0",
			"clientIP":"37.186.113.170",
			"clientPort":"12345",
			"httpMethod":"HEAD",
			"requestUri":"/xyz/portal",
			"requestQuery":"",
			"userAgent":"-",
			"httpStatus":"200",
			"httpVersion":"HTTP/1.0",
			"receivedBytes":"27",
			"sentBytes":"202",
			"timeTaken":"359",
			"sslEnabled":"off"
		}
	}

## Journal des performances

Ce journal n’est généré que si vous l’avez activé au niveau de chaque passerelle Application Gateway, comme détaillé dans les étapes précédentes. Les données sont stockées dans le compte de stockage spécifié lors de l’activation de la journalisation. Les données suivantes sont enregistrées :

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
		"operationName": "ApplicationGatewayPerformance",
		"time": "2016-04-09T00:00:00Z",
		"category": "ApplicationGatewayPerformanceLog",
		"properties": 
		{
			"instanceId":"ApplicationGatewayRole_IN_1",
			"healthyHostCount":"4",
			"unHealthyHostCount":"0",
			"requestCount":"185",
			"latency":"0",
			"failedRequestCount":"0",
			"throughput":"119427"
		}
	}

## Afficher et analyser le journal d’audit

Vous pouvez afficher et analyser les données du journal d’audit en utilisant l’une des méthodes suivantes :

- **Outils Azure :** récupérez les informations à partir des journaux via Azure PowerShell, l’interface de ligne de commande Azure, l’API REST Azure ou le portail Azure en version préliminaire. Des instructions détaillées pour chaque méthode sont détaillées dans l’article [Opérations d’audit avec Resource Manager](../resource-group-audit.md).
- **Power BI :** si vous n’avez pas encore de compte [Power BI](https://powerbi.microsoft.com/pricing), vous pouvez l’essayer gratuitement. À l’aide du [pack de contenus des journaux d’audit Azure pour Power BI](https://powerbi.microsoft.com/fr-FR/documentation/powerbi-content-pack-azure-audit-logs/), vous pouvez analyser vos données avec des tableaux de bord préconfigurés à utiliser en l’état ou à personnaliser.

## Affichage et analyse du journal des performances et d’accès

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) peut collecter le compteur et les fichiers de journal des événements à partir de votre compte de stockage d’objets Blob et inclut des visualisations et de puissantes fonctionnalités de recherche pour analyser vos journaux.

Vous pouvez également vous connecter à votre compte de stockage et récupérer les entrées de journal JSON pour les journaux d’accès et des performances. Une fois que vous avez téléchargé les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, PowerBI ou tout autre outil de visualisation de données.

>[AZURE.TIP] Si vous savez utiliser Visual Studio et les concepts de base de la modification des valeurs de constantes et variables en C#, vous pouvez utiliser les [outils de convertisseur de journaux](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles dans Github.

## Étapes suivantes

- Visualisation du compteur et des journaux des événements avec [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)
- Billet de blog [Visualiser vos journaux d’audit Azure avec Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
- Billet de blog [Afficher et analyser les journaux d’audit Azure dans Power BI et bien plus encore](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

<!---HONumber=AcomDC_0907_2016-->