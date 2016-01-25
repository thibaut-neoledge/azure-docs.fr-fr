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
   ms.date="10/15/2015"
   ms.author="seanmck"/>

# Choix d’une infrastructure pour votre service

Azure Service Fabric propose deux infrastructures générales pour la création de services : l’API Reliable Actors et l’API Reliable Services. Si les deux infrastructures reposent sur le même noyau Service Fabric, ils offrent différents compromis entre simplicité et flexibilité en termes de concurrence, partitionnement et communication. Vous devez comprendre les deux modèles pour choisir l'infrastructure appropriée pour un service particulier dans votre application.

## Comparaison des API Reliable Actors et Reliable Services

|**Quand choisir l’API Reliable Actors**|**Quand choisir l’API Reliable Services**|
|-----------------------|--------------------------|
|Votre espace de problème implique de nombreuses petites unités indépendantes d’état et de logique.|Vous devez conserver la logique entre plusieurs composants.|
|Vous souhaitez travailler avec des objets monothread tout en ayant la possibilité d’effectuer des mises à l’échelle et de maintenir la cohérence.|Vous souhaitez utiliser Reliable Collections (par exemple, .NET Reliable Dictionary et Reliable Queue) pour stocker et gérer votre état.|
|Vous souhaitez que l’infrastructure gère la concurrence et la granularité de l’état.|Vous souhaitez contrôler la granularité et la concurrence de votre état.|
|Vous souhaitez que la plateforme gère les communications pour vous.|Vous souhaitez gérer les communications et contrôler le schéma de partitionnement de votre service.|

N'oubliez pas qu'il est parfaitement raisonnable d'utiliser des infrastructures différentes pour des services différents au sein de votre application. Par exemple, vous pouvez avoir un service avec état qui regroupe les données générées par plusieurs acteurs.

## Étapes suivantes

- [En savoir plus sur l’API Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [En savoir plus sur l’API Reliable Services](../Service-Fabric/service-fabric-reliable-services-introduction.md)

<!---HONumber=AcomDC_0114_2016-->