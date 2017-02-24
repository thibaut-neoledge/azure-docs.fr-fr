---
title: "Azure DocumentDB comme magasin de valeurs de clés – Synthèse des coûts | Microsoft Docs"
description: "Découvrez le faible coût qu’implique l’utilisation d’Azure DocumentDB comme magasin de valeurs de clés."
keywords: "magasin de valeurs de clés"
services: documentdb
author: ArnoMicrosoft
manager: jhubbard
editor: 
tags: 
documentationcenter: 
ms.assetid: 7f765c17-8549-4509-9475-46394fc3a218
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: acomet
translationtype: Human Translation
ms.sourcegitcommit: faf363eb5848752b27faacd971867391b6393337
ms.openlocfilehash: 1a693477a51a05fb28e7c4772aeee77fd0c4e1dd

---

# <a name="documentdb-as-a-key-value-store--cost-overview"></a>DocumentDB comme magasin de valeurs de clés – Synthèse des coûts

Azure DocumentDB est un service de base de données NoSQL globalement distribué et entièrement géré qui permet de créer facilement des applications à haute disponibilité, à grande échelle et [globalement distribuées](documentdb-distribute-data-globally.md). Par défaut, DocumentDB indexe automatiquement toutes les données qu’il reçoit de manière efficace. Vous pouvez ainsi créer des requêtes [SQL](documentdb-sql-query.md) (et [JavaScript](documentdb-programming.md)) rapides et cohérentes sur n’importe quel type de données. 

Cet article décrit le coût qu’implique l’utilisation de DocumentDB pour de simples opérations de lecture et d’écriture lorsqu’il est utilisé comme magasin de valeurs de clés. Les opérations d’écriture incluent des insertions, des remplacements, des suppressions et des upserts de documents. En plus de garantir une haute disponibilité de 99,99 %, DocumentDB garantit une latence inférieure à 10 ms pour les opérations de lecture, et une latence inférieure à 15 ms pour les opérations d’écritures (indexées), au 99e centile. 

## <a name="why-we-use-request-units-rus"></a>Pourquoi utiliser des unités de requête (RU) ?

Les performances de DocumentDB sont basées sur la quantité [d’unités de requête](documentdb-programming.md) approvisionnées pour la partition. L’approvisionnement se fait avec une granularité d’une seconde et s’achète par unité de requête par seconde ([à ne pas confondre avec la tarification horaire](https://azure.microsoft.com/pricing/details/documentdb/)). Les unités de requête doivent être considérées comme une devise qui simplifie l’approvisionnement du débit dont a besoin l’application. Les utilisateurs n’ont donc plus à différencier les unités de capacité des opérations de lecture et d’écriture. Le modèle de devise unique des unités de requête permet de partager efficacement la capacité approvisionnée entre les opérations de lecture et d’écriture. Ce modèle de capacité approvisionnée permet au service de fournir un débit prévisible et constant, une faible latence garantie et une haute disponibilité. Enfin, les unités de requête sont utilisées pour modéliser le débit. Toutefois, chaque unité de requête approvisionnée a également une quantité définie de ressources (mémoire, cœurs). Les unités de requête par seconde ne correspondent pas uniquement à des opérations d’E/S par seconde.

En tant que système de base de données distribué globalement, DocumentDB est le seul service Azure à fournir un contrat SLA sur la latence, le débit et la cohérence, en plus de la haute disponibilité. Le débit que vous approvisionnez est appliqué à chacune des régions associées à votre compte de base de données DocumentDB. Pour les opérations de lecture, DocumentDB propose plusieurs [niveaux de cohérence](documentdb-consistency-levels.md). 

Le tableau suivant indique le nombre d’unités de requête nécessaire pour lire et écrire des transactions en fonction de la taille des documents de 1 ko et 100 ko.

|Taille du document|1 lecture|1 écriture|
|-------------|------|-------|
|1 Ko|1 unité de requête|5 unités de requête|
|100 Ko|10 unités de requête|50 unités de requête|

## <a name="cost-of-reads-and-writes"></a>Coût des lectures et écritures

Si vous approvisionnez 1 000 unités de requête par seconde, vous obtenez un total de 3,6 millions d’unités de requête par heure, à un coût de 0,08 dollar l’heure (aux États-Unis et en Europe). Pour un document d’une taille de 1 Ko, vous pouvez donc consommer 3,6 millions de lectures ou 0,72 million d’écritures (3,6 millions d’unités de requête/5) en utilisant le débit approvisionné. Normalisé au million de lectures et d’écritures, le coût serait de 0,022 $/million de lectures (0,08 $/3,6) et de 0,111 $/million d’écritures (0,08 $/0,72). Le coût par million devient minime, comme le montre le tableau ci-dessous.

|Taille du document|1 million de lectures|1 million d’écritures|
|-------------|-------|--------|
|1 Ko|0,022 $|0,111 $|
|100 Ko|0,222 $|1,111 $|

La plupart des magasins d’objets et d’objets Blob de base facturent&0;,40 $ le million de transactions de lecture et&5; $ le million de transactions d’écriture. S’il est utilisé de manière optimale, DocumentDB peut être jusqu’à 98 % moins cher que les autres solutions (pour les transactions de 1 Ko).

## <a name="next-steps"></a>Étapes suivantes

Consultez régulièrement cette rubrique pour obtenir de nouveaux articles sur l’optimisation de l’approvisionnement des ressources dans DocumentDB. En attendant, n’hésitez pas à utiliser notre [calculatrice d’unités de requête](https://www.documentdb.com/capacityplanner).



<!--HONumber=Feb17_HO1-->


