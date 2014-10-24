<properties linkid="mobile-services-dotnet-backend-how-to-use" urlDisplayName="Use the Mobile Services .NET Backend" pageTitle="Use the Mobile Services .NET Backend - Azure Mobile Services" metaKeywords="" description="Learn the details of the .NET Backend programming model for Azure Mobile Services, including how to work with table data, APIs, authentication, and scheduled jobs" metaCanonical="" services="" documentationCenter="Mobile" title="Use the Mobile Services .NET Backend" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>

# Utilisation du service principal .NET de Mobile Services

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET backend" class="current">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript backend">JavaScript backend</a></div>

Cet article fournit des informations détaillées et des exemples sur l'utilisation du service principal .NET dans Azure Mobile Services. Cette rubrique se compose des sections suivantes :

-   [Introduction][Introduction]
-   [Opérations de table][Opérations de table]

## <a name="intro"></a>Introduction

Le service principal .NET Mobile Services vous permet d'écrire une logique métier de service principal riche avec l'[API web ASP.NET][API web ASP.NET] et votre langage .NET favori. Mobile Services expose une petite surface du modèle de programmation en tant que [packages NuGet][packages NuGet], ce qui facilite l'accès transparent à votre service depuis les Kits de développement logiciel (SDK) clients Mobile Services interplateformes pour Windows, Android, iOS, etc. Ces API garantissent également une simplicité maximale pour la prise en charge de l'authentification et des notifications Push. Toutefois, pour l'essentiel, le modèle de programmation est basé sur l'API web, et les développeurs qui sont familiarisés avec cette dernière se sentiront à l'aise.

## <a name="table-scripts"></a>Opérations de table

Le service principal .NET de Mobile Services fournit une abstraction de « table » universelle, qui représente une API HTTP basée sur CRUD pour le stockage de base de données. Cette abstraction sépare les questions liées au stockage de données de votre logique métier et permet à votre service mobile d'exposer différents magasins de données dans un format câble cohérent basé sur JSON, qui est facilement compris par les Kits de développement logiciel (SDK) clients Mobile Services interplateformes.

À la racine de ce modèle de programmation se trouve la classe [**TableController<t>**][**TableController<t>**], qui est simplement une classe [**ApiController**][**ApiController**] d'API web ordinaire personnalisée pour un modèle d'accès aux données CRUD. La classe **TableController** peut utiliser différents magasins de données, y compris SQL (via [Entity Framework][Entity Framework]), le [stockage de tables Azure][stockage de tables Azure], [MongoDB][MongoDB] ou votre propre magasin personnalisé.

  [.NET backend]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-use/ ".NET backend"
  [JavaScript backend]: /fr-fr/documentation/articles/mobile-services-how-to-use-server-scripts/ "JavaScript backend"
  [Introduction]: #intro
  [Opérations de table]: #table-scripts
  [API web ASP.NET]: http://www.asp.net/web-api
  [packages NuGet]: http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22
  [**TableController<t>**]: http://msdn.microsoft.com/library/dn643359.aspx
  [**ApiController**]: http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx
  [Entity Framework]: http://msdn.microsoft.com/data/ef.aspx
  [stockage de tables Azure]: http://azure.microsoft.com/documentation/services/storage/
  [MongoDB]: http://www.mongodb.org
