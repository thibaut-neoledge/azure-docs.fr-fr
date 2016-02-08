<properties
   pageTitle="Modèles de programmation Service Fabric | Microsoft Azure"
   description="Service Fabric propose deux infrastructures pour la création de services : l’infrastructure d’acteurs et l’infrastructure de services. Elles offrent des compromis distincts en termes de simplicité et de contrôle."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/26/2016"
   ms.author="seanmck"/>

# Choix d’une infrastructure pour votre service

Azure Service Fabric propose deux infrastructures générales pour la création de services : l’API Reliable Services et l’API Reliable Actors. Si les deux infrastructures reposent sur le même noyau Service Fabric, ils offrent différents compromis entre simplicité et flexibilité en termes de concurrence, partitionnement et communication. Vous devez comprendre les deux modèles pour choisir l'infrastructure appropriée pour un service particulier dans votre application.

## Comparaison des API Reliable Actors et Reliable Services

|**Quand choisir l’API Reliable Actors**|**Quand choisir l’API Reliable Services**|
|-----------------------|--------------------------|
|Votre espace de problème implique un nombre élevé de petites unités indépendantes d’état et de logique (au moins 1000).|Vous devez conserver la logique entre plusieurs composants.|
|Vous souhaitez utiliser des objets monothread ne nécessitant pas une interaction externe significative.|Vous souhaitez utiliser Reliable Collections (par exemple, .NET Reliable Dictionary et Reliable Queue) pour stocker et gérer votre état.|
|Vous souhaitez que la plateforme gère les communications pour vous.|Vous souhaitez gérer les communications et contrôler le schéma de partitionnement de votre service.|

N'oubliez pas qu'il est parfaitement raisonnable d'utiliser des infrastructures différentes pour des services différents au sein de votre application. Par exemple, vous pouvez avoir un service avec état qui regroupe les données générées par plusieurs acteurs.

## Étapes suivantes

- [En savoir plus sur l’API Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [En savoir plus sur l’API Reliable Services](../Service-Fabric/service-fabric-reliable-services-introduction.md)

<!---HONumber=AcomDC_0128_2016-->