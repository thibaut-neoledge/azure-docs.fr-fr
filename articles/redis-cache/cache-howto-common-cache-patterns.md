<properties 
   pageTitle="Modèles de cache courants du Cache Redis Azure" 
   description="Découvrez où et pourquoi utiliser le Cache Redis Azure" 
   services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd" 
   ms.date="02/23/2016"
   ms.author="riande"/>

# Modèles de cache courants du Cache Redis Azure

Cette page répertorie les principaux avantages de l'utilisation d'un cache.

## Optimisation de l'accès aux données avec un cache

L'utilisation d'un cache peut considérablement accélérer l'accès aux données grâce à une extraction depuis un magasin de données. Un cache permet un débit élevé et une faible latence. L'extraction des données à partir du cache permet non seulement de rendre votre application plus rapide, mais elle permet également de réduire la charge d'accès aux données et d'accroître sa réactivité pour les autres requêtes. Le stockage d'informations dans un cache permet d'économiser des ressources et d'accroître l'évolutivité à mesure qu'augmentent les demandes pour l'application. Votre application sera plus réactive aux charges en rafale si elle peut extraire efficacement des données à partir d'un cache.

## État de session distribuée
Même s’il est recommandé de ne pas utiliser l’état de session, certaines applications peuvent bénéficier de telles données (meilleures performances, réduction de la complexité). D’autres applications peuvent même avoir besoin de l’état de session. Par défaut, dans le fournisseur de mémoire, l'état de session n'autorise pas la montée en puissance (l'exécution de plusieurs instances d'un site web). Le fournisseur d'état de session ASP.NET SQL Server permet à plusieurs sites web d'utiliser l'état de session. Toutefois, cela implique un coût de latence élevé par rapport à celui d'un fournisseur de mémoire. Le fournisseur d'état de session du Cache Redis est une alternative à faible latence très facile à configurer. Si votre application n'utilise qu'une quantité limitée de l'état de session, vous pouvez utiliser une grande partie du cache pour la mise en cache des données et une petite quantité pour l'état de session.

## Surmonter une interruption de service (cache de secours)
 Grâce au stockage des données dans un cache, l'application peut faire face aux défaillances du système telles que la latence du réseau, les problèmes liés aux services web et les défaillances matérielles. Il est souvent préférable de distribuer les données mises en cache jusqu'à la récupération du service web ou de la base de données, plutôt que d'arriver à la défaillance complète de votre application.

## Étapes suivantes
Pour en savoir plus sur l'utilisation du Cache Redis Azure :
 
- [Documentation sur le cache Redis Azure](https://azure.microsoft.com/documentation/services/cache/) : cette page fournit de nombreux liens utiles concernant l’utilisation du cache Redis Azure.
- [Application de vidéo MVC avec le cache Redis Azure en 15 minutes](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/) : ce billet de blog constitue un bon point de départ pour utiliser le cache Redis Azure dans une application ASP.NET MVC.
- [Utilisation de l’état de session avec le cache Redis Azure dans Azure App Service](../app-service-web/web-sites-dotnet-session-state-caching.md) : cette rubrique explique comment utiliser le service de cache Redis Azure pour l’état de session.

<!---HONumber=AcomDC_0224_2016-->