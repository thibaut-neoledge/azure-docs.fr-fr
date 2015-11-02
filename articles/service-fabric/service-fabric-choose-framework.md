<properties
   pageTitle="Modèles de programmation Service Fabric | Microsoft Azure"
   description="Service Fabric propose deux infrastructures pour la création de services : l’infrastructure d’acteurs et l’infrastructure de services. Elles offrent des compromis distincts en termes de simplicité et de contrôle."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
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

Service Fabric propose deux infrastructures générales pour la création de services : les API avec acteurs fiables et les API avec services fiables. Si les deux infrastructures reposent sur le même noyau Service Fabric, ils offrent différents compromis entre simplicité et flexibilité en termes de concurrence, partitionnement et communication. Vous devez comprendre les deux modèles pour choisir l'infrastructure appropriée pour un service particulier dans votre application.

## Comparaison des API avec acteurs fiables et des API avec services fiables

|**API avec acteurs fiables**|**API avec services fiables**|
|-----------------------|--------------------------|
|Votre espace de problème implique de nombreuses petites unités indépendantes d'état et de logique.|Vous devez conserver la logique entre plusieurs composants.|
|Vous souhaitez travailler avec des objets monothread tout en restant en mesure de mettre à l'échelle et de maintenir la cohérence.|Vous souhaitez utiliser des collections fiables (telles que la file d'attente et le dictionnaire .NET) pour stocker et gérer l'état.|
|Vous voulez que l'infrastructure gère la concurrence et la granularité de l'état.|Vous souhaitez contrôler la granularité et la concurrence de l'état.|
|Vous souhaitez que la plateforme gère les communications pour vous.|Vous souhaitez gérer les communications et contrôler le schéma de partitionnement de votre service|

N'oubliez pas qu'il est parfaitement raisonnable d'utiliser des infrastructures différentes pour des services différents au sein de votre application. Par exemple, vous pouvez avoir un service avec état qui regroupe les données générées par plusieurs acteurs.

## Étapes suivantes

- [En savoir plus sur les API avec acteurs fiables](service-fabric-reliable-actors-introduction.md)
- [En savoir plus sur les API avec services fiables](../Service-Fabric/service-fabric-reliable-services-introduction.md)

<!---HONumber=Oct15_HO4-->