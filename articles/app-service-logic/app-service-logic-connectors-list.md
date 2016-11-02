<properties
    pageTitle="Liste des connecteurs et applications API disponibles | Microsoft Azure App Service"
    description="En savoir plus sur les connecteurs et les applications API dans Azure App Service"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="mandia"/>



# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>Liste des connecteurs et des applications API à utiliser dans vos applications logiques
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques. Pour la version de disponibilité générale de Logic Apps, consultez l’article [Liste de connecteurs](../connectors/apis-list.md).

Découvrez tous les connecteurs et les applications API disponibles créés par Microsoft et à utiliser dans votre application logique.

Pour plus d’informations sur la tarification et une liste de ce qui est inclus dans chaque niveau de service, consultez la page [Tarification Azure App Service](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Pour commencer à utiliser Logic Apps avant de vous inscrire pour ouvrir un compte Azure, accédez à [Try Logic App (Essayer Logic Apps)](https://tryappservice.azure.com/?appservice=logic). Vous pouvez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## <a name="core-connectors"></a>Connecteurs principaux
Le tableau suivant répertorie tous les connecteurs et toutes les applications API créés par Microsoft et disponibles dans les connecteurs principaux :

Nom | Description
--- | ---
[Bing Traduction](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) | Utilisez Bing pour traduire un texte dans une autre langue.
[HTTP](app-service-logic-connector-http.md) | L'écouteur HTTP ouvre un point de terminaison qui agit comme serveur HTTP et écoute les requêtes HTTP ou HTTPS entrantes. L'action HTTP ne nécessite aucune application API et est prise en charge en mode natif au sein des applications logiques.
[Slack](app-service-logic-connector-slack.md) | Se connecte à Slack et publie des messages dans les canaux Slack.


## <a name="enterprise-integration-connectors"></a>Connecteurs d'intégration d’entreprise
Le tableau suivant répertorie les connecteurs et les applications API créés par Microsoft et disponibles dans les connecteurs d’intégration d’entreprise :

Nom  | Description
------------- | -------------
[Règles BizTalk](app-service-logic-use-biztalk-rules.md) | Utilisez les règles de BizTalk pour définir et contrôler la logique métier d'une organisation. Les stratégies d’entreprise peuvent être mises à jour sans recompilation ou redéploiement des applications associées.
[Extracteur XPath BizTalk](app-service-logic-xpath-extract.md) | Permet de rechercher et d’extraire des données du contenu XML en fonction d’une expression XPath que vous avez choisie.
[Connecteur DB2](app-service-logic-connector-db2.md) | Se connecte à une base de données IBM DB2, localement et sur une machine virtuelle Azure exécutant un système d’exploitation Windows. Il peut mapper les opérations API web et API OData avec les commandes SQL Informix. <br/><br/>Aucun déclencheur. Les actions comprennent les instructions table select, insert, update, delete et les instructions personnalisées<br/><br/>Ce connecteur comprend également le client Microsoft pour DRDA pour se connecter à un serveur Informix via un réseau TCP/IP.
[File](app-service-logic-connector-file.md) | À l'aide de ce connecteur, vous pouvez vous connecter au réseau ou système de fichiers local, et notamment télécharger, supprimer et répertorier des fichiers.
[Informix](app-service-logic-connector-informix.md) | Le connecteur DB2 se connecte à une base de données IBM Informix, localement et sur une machine virtuelle Azure exécutant un système d'exploitation Windows. Il peut mapper les opérations API web et API OData avec les commandes SQL Informix.<br/><br/>Aucun déclencheur. Les actions comprennent les instructions table select, insert, update, delete et les instructions personnalisées.<br/><br/>En mode local, VPN ou Azure ExpressRoute peut être utilisé. Ce connecteur comprend également un client Microsoft pour DRDA pour se connecter à un serveur Informix via un réseau TCP/IP.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Se connecte au serveur SQL Server local ou à une base de données SQL Azure. Vous pouvez créer, mettre à jour, obtenir et supprimer des entrées dans une table de base de données SQL.
MQ | Se connecte à une base de données IBM WebSphere MQ Server version 8, sur site et sur une machine virtuelle Azure exécutant un système d'exploitation Windows. En mode local, VPN ou Azure ExpressRoute peut être utilisé. Ce connecteur comprend également le client Microsoft pour MQ.<br/><br/>Aucun déclencheur. Aucune action.<br/><br/>**Remarque** N’est pas utilisable avec Logic Apps pour l’instant.

## <a name="connectors-as-triggers"></a>Connecteurs en tant que déclencheurs
Plusieurs connecteurs fournissent des déclencheurs pour les applications logiques. Ces déclencheurs sont de deux types :

1. Déclencheurs d'interrogation : ces déclencheurs interrogent votre service selon une fréquence spécifiée pour vérifier l'existence de nouvelles données. Quand de nouvelles données sont disponibles, une nouvelle instance de votre application logique s'exécute avec les données comme entrée. Pour empêcher que les mêmes données soient consommées plusieurs fois, le déclencheur peut nettoyer les données qui ont été lues et transmises à l'application logique. File, SQL et Azure Storage sont des exemples de ce type de connecteur.
2. Déclencheurs d'interrogation : ces déclencheurs écoutent les données sur un point de terminaison ou attendent qu'un événement se produise. Ensuite, ils déclenchent une nouvelle instance d'une application logique. L'Écouteur HTTP et Twitter sont des exemples de ce type de connecteur.

## <a name="connectors-as-actions"></a>Connecteurs en tant qu'actions
Vous pouvez aussi utiliser des connecteurs comme actions au sein de votre application logique. Les actions sont utiles pour rechercher des données dans l'application logique qui peuvent ensuite être utilisées lors de l'exécution. Par exemple, vous devrez peut-être rechercher des données à partir d'une base de données SQL afin d'obtenir des informations complémentaires sur un client lors du traitement d'une commande. Ou bien, vous devrez peut-être écrire, mettre à jour ou supprimer des données d'une destination. Pour cela, vous pouvez utiliser les actions fournies par les connecteurs. Les actions mappent aux opérations des applications API (comme défini par leurs métadonnées Swagger).

## <a name="create-your-own-connectors-and-api-apps"></a>Créer vos propres connecteurs et applications API
[Référence de connecteurs et d’applications API](http://aka.ms/appservicesconnectorreference)  
[Déclencheurs des applications API Azure App Service](../app-service-api/app-service-api-dotnet-triggers.md)  
[Référence de l'application logique](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>En savoir plus sur les connecteurs et les applications API
[Qu’est-ce qu’un connecteur et une application API BizTalk](app-service-logic-what-are-biztalk-api-apps.md)  
[Utilisation du Gestionnaire de connexion hybride dans Azure App Service](app-service-logic-hybrid-connection-manager.md)  
[Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md)



<!--HONumber=Oct16_HO2-->


