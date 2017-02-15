---
title: "Définition et gestion de l’état | Microsoft Docs"
description: "Comment définir et gérer l&quot;état de service dans Service Fabric"
services: service-fabric
documentationcenter: .net
author: appi101
manager: timlt
editor: 
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/10/2016
ms.author: aprameyr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bb27c2f2224791a6a30819da5ee0f09ad7bfa95c


---
# <a name="service-state"></a>État du service
**État du service** correspond aux données nécessaires au bon fonctionnement du service. Il comprend des variables et des structures de données que le service lit et écrit afin d’effectuer des tâches.

Prenons l’exemple d’un service de calculatrice simple. Ce service accepte deux nombres et retourne leur somme. Il s'agit d'un service purement sans état auquel aucune donnée n'est associée.

À présent, considérons cette même calculatrice, mais qui en plus de calculer la somme possède également une méthode permettant de retourner la dernière somme calculée. Ce service est désormais avec état. Il contient un certain état dans lequel il écrit (quand il calcule une nouvelle somme) et qu’il lit (quand il renvoie la dernière somme calculée).

Dans Service Fabric Azure, le premier service porte le nom de « service sans état ». Le second service est appelé « service avec état ».

## <a name="storing-service-state"></a>Stockage de l’état du service
L’état peut être externalisé ou colocalisé avec le code qui le manipule. L'externalisation de l'état est généralement effectuée à l'aide d'un magasin ou d'une base de données externe. Dans notre exemple de calculatrice, il peut s’agir d’une base de données SQL et le résultat actuel y est stocké dans une table. Chaque demande de calcul d'une somme effectue une mise à jour sur cette ligne.

L'état peut également être colocalisé avec le code qui manipule ce code. Les services avec état dans Service Fabric sont générés à l'aide de ce modèle. Service Fabric fournit l’infrastructure garantissant que cet état est hautement disponible et tolère les pannes en cas de défaillance.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les concepts propres à Service Fabric, consultez les articles suivants :

* [Disponibilité des services Service Fabric](service-fabric-availability-services.md)
* [Extensibilité des services Service Fabric](service-fabric-concepts-scalability.md)
* [Partitionnement des services Service Fabric](service-fabric-concepts-partitioning.md)




<!--HONumber=Nov16_HO3-->


