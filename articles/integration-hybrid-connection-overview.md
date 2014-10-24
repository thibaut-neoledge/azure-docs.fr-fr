<properties linkid="manage-services-integration-hybrid-connection" urlDisplayName="Hybrid Connections Overview - BizTalk Services" pageTitle="Hybrid Connections Overview | Azure" metaKeywords="BizTalk Services, BizTalk, websites, web sites, hybrid connections, Azure" description="Learn about hybrid connections, including Security." metaCanonical="" services="integration-services" documentationCenter="" title="Hybrid Connections Overview" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="mandia"></tags>

# Aperçu des connexions hybrides

Cette rubrique présente les connexions hybrides et répertorie les configurations prises en charge ainsi que les ports TCP requis. Plus précisément :

-   [Présentation des connexions hybrides][Présentation des connexions hybrides]
-   [Configurations prises en charge][Configurations prises en charge]
-   [Sécurité][Sécurité]

## <a name="HCOverview"></a>Présentation des connexions hybrides

Les connexions hybrides offrent un moyen aisé et pratique de connecter Sites Web Azure et Azure Mobile Services à des ressources locales. Les connexions hybrides sont une fonctionnalité d'Azure BizTalk Services :

![Hybrid Connections][Hybrid Connections]

Les avantages des connexions hybrides sont les suivants :

-   Les sites web et les services mobiles peuvent accéder aux données et aux services locaux existants en toute sécurité.
-   Plusieurs sites web ou services mobiles peuvent partager une connexion hybride pour accéder à une ressource locale.
-   Un nombre minimum de ports TCP est requis pour accéder à votre réseau.
-   Les applications qui utilisent des connexions hybrides accèdent uniquement à la ressource locale spécifique qui est publiée par l'intermédiaire de la connexion hybride.
-   Elles peuvent se connecter à n'importe quelle ressource qui utilise un port TCP statique, par exemple, SQL Server, MySQL, les API web HTTP et la plupart des services web personnalisés.

> [WACOM.NOTE] Actuellement, les services TCP qui utilisent des ports dynamiques (tels le mode FTP passif ou le mode passif étendu) ne sont pas pris en charge.

-   Elles peuvent être utilisées avec toutes les infrastructures prises en charge par Sites Web Azure (.NET, PHP, Java, Python, Node.js) et Azure Mobile Services (Node.js, .NET).
-   Les sites web et les services mobiles peuvent accéder aux ressources locales exactement de la même manière que si le site web ou le service mobile était situé sur votre réseau local. Par exemple, la chaîne de connexion utilisée localement peut également être utilisée sur Azure.

Les connexions hybrides peuvent également fournir aux administrateurs d'entreprise des possibilités de contrôle et de visibilité sur les ressources d'entreprise auxquelles elles accèdent, notamment les possibilités suivantes :

-   À l'aide des paramètres de stratégie de groupe, les administrateurs peuvent autoriser les connexions hybrides sur le réseau et également désigner les ressources auxquelles elles peuvent accéder.
-   Les journaux d'événements et d'audit sur le réseau d'entreprise fournissent la visibilité sur les ressources auxquelles accèdent les connexions hybrides.

## <a name="KnownIssues"></a>Configurations prises en charge

Les connexions hybrides prennent en charge les combinaisons d'infrastructure et d'application suivantes :

-   Accès .NET Framework à SQL Server
-   Accès .NET Framework aux services HTTP/HTTPS avec WebClient
-   Accès PHP à SQL Server, MySQL
-   Accès Java à SQL Server, MySQL et Oracle
-   Accès Java aux services HTTP/HTTPS

Lorsque vous utilisez des connexions hybrides pour un accès local à SQL Server, tenez compte des éléments suivants :

-   Les instances nommées SQL Express doivent être configurées pour utiliser des ports statiques. Par défaut, les instances nommées SQL Express utilisent des ports dynamiques.
-   Les instances par défaut SQL Express utilisent un port statique, mais TCP doit être activé. Par défaut, TCP n'est pas activé.
-   À l'heure actuelle, le mode MultiSubnetFailover=true n'est pas pris en charge lorsque vous utilisez le clustering ou des groupes de disponibilité.
-   Actuellement, ApplicationIntent=ReadOnly n'est pas pris en charge.
-   L'authentification SQL peut être exigée en tant que méthode d'autorisation de bout en bout prise en charge par l'application Azure et le serveur SQL local.

## <a name="HCSecurity"></a>Sécurité

Les connexions hybrides utilisent l'autorisation avec signature d'accès partagé pour sécuriser les connexions des applications Azure/du Gestionnaire de connexions hybrides local à la connexion hybride. Des clés de connexion distinctes sont créées pour l'application et le Gestionnaire de connexions hybrides local. Ces clés de connexion peuvent être substituées et révoquées indépendamment.

Les connexions hybrides assurent une distribution transparente et sécurisée des clés aux applications et au Gestionnaire de connexions hybrides local.

Consultez la page [Création et gestion des connexions hybrides][Création et gestion des connexions hybrides].

**L'autorisation de l'application est séparée de la connexion hybride**. Toute méthode d'autorisation appropriée peut être utilisée. Elle dépend des méthodes d'autorisation de bout en bout prises en charge dans le cloud Azure et sur les composants locaux. Par exemple, votre application Azure accède à un serveur SQL local. Dans ce scénario, l'autorisation SQL peut être la méthode d'autorisation prise en charge de bout en bout.

#### Ports TCP

| **Port** | Pourquoi                                                                                                             |
|----------|----------------------------------------------------------------------------------------------------------------------|
| 80       | Port HTTP ; utilisé pour la validation du certificat.                                                                |
| 443      | Port HTTPS                                                                                                           |
| 5671     | Utilisé pour la connexion à Azure. Si le port TCP 5671 n'est pas disponible, le port TCP 443 est utilisé.            |
| 9352     | Utilisé pour envoyer et extraire des données. Si le port TCP 9352 n'est pas disponible, le port TCP 443 est utilisé. |

## Suivant

-   [Création et gestion des connexions hybrides][Création et gestion des connexions hybrides]
-   [Connexion d'un site web Azure à une ressource locale][Connexion d'un site web Azure à une ressource locale]
-   [Les connexions hybrides étape par étape : connexion à un serveur SQL local à partir d'un site web Azure][Les connexions hybrides étape par étape : connexion à un serveur SQL local à partir d'un site web Azure]
-   [Azure Mobile Services et connexions hybrides][Azure Mobile Services et connexions hybrides]

## Voir aussi

-   [API REST pour gérer BizTalk Services sur Microsoft Azure][API REST pour gérer BizTalk Services sur Microsoft Azure]
-   [BizTalk Services : tableau comparatif des éditions][BizTalk Services : tableau comparatif des éditions]
-   [Création d'un service BizTalk avec le portail de gestion Azure][Création d'un service BizTalk avec le portail de gestion Azure]
-   [BizTalk Services : Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services][BizTalk Services : Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services]

  [Présentation des connexions hybrides]: #HCOverview
  [Configurations prises en charge]: #KnownIssues
  [Sécurité]: #HCSecurity
  [Hybrid Connections]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
  [Création et gestion des connexions hybrides]: http://azure.microsoft.com/fr-fr/documentation/articles/integration-hybrid-connection-create-manage
  [Connexion d'un site web Azure à une ressource locale]: http://go.microsoft.com/fwlink/p/?LinkId=397538
  [Les connexions hybrides étape par étape : connexion à un serveur SQL local à partir d'un site web Azure]: http://go.microsoft.com/fwlink/?LinkID=397979
  [Azure Mobile Services et connexions hybrides]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started
  [API REST pour gérer BizTalk Services sur Microsoft Azure]: http://msdn.microsoft.com/library/azure/dn232347.aspx
  [BizTalk Services : tableau comparatif des éditions]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Création d'un service BizTalk avec le portail de gestion Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [BizTalk Services : Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services]: http://go.microsoft.com/fwlink/p/?LinkID=302281
