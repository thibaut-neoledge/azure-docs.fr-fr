<properties pageTitle="Utilisation du service principal .NET de Mobile Services - Azure Mobile Services" description="Découvrez les détails du modèle de programmation de serveur principal .NET pour Azure Mobile Services, y compris comment utiliser les données de table, les API, l'authentification et les tâches planifiées" services="" documentationCenter="windows" authors="mattchenderson" manager="dwrede" editor="mollybos"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/11/2014" ms.author="mahender"/>
# Utilisation du service principal .NET de Mobile Services

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET backend" class="current">Service principal .NET</a> | <a href="/fr-fr/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript backend">Service principal JavaScript</a></div>

Cet article fournit des informations détaillées et des exemples sur l'utilisation du service principal .NET dans Azure Mobile Services. Cette rubrique se compose des sections suivantes :

+ [Introduction](#intro)
+ [Opérations de table](#table-scripts)

##<a name="intro"></a>Introduction

Le service principal .NET Mobile Services vous permet d'écrire une logique métier de service principal riche avec l'[API web ASP.NET](http://www.asp.net/web-api) et votre langage .NET favori. Mobile Services expose une petite surface du modèle de programmation en tant que [packages NuGet](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22), ce qui facilite l'accès transparent à votre service depuis les Kits de développement logiciel (SDK) clients Mobile Services interplateformes pour Windows, Android, iOS, etc. Ces API garantissent également une simplicité maximale pour la prise en charge de l'authentification et des notifications Push. Toutefois, pour l'essentiel, le modèle de programmation est basé sur l'API web, et les développeurs qui sont familiarisés avec cette dernière se sentiront à l'aise. 

##<a name="table-scripts"></a>Opérations de table

Le service principal .NET de Mobile Services fournit une abstraction de " table " universelle, qui représente une API HTTP basée sur CRUD pour le stockage de base de données. Cette abstraction sépare les questions liées au stockage de données de votre logique métier et permet à votre service mobile d'exposer différents magasins de données dans un format câble cohérent basé sur JSON, qui est facilement compris par les Kits de développement logiciel (SDK) clients Mobile Services interplateformes. 

À la racine de ce modèle de programmation se trouve la classe [**TableController<T>**](http://msdn.microsoft.com/library/dn643359.aspx), qui est simplement une classe [**ApiController**](http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx) d'API web ordinaire personnalisée pour un modèle d'accès aux données CRUD. Le **TableController** peut utiliser différents magasins de données, y compris SQL (via [Entity Framework](http://msdn.microsoft.com/data/ef.aspx)), [Azure Table Storage](http://azure.microsoft.com/documentation/services/storage/), [MongoDB](http://www.mongodb.org) ou votre propre magasin personnalisé.

<!--HONumber=42-->
