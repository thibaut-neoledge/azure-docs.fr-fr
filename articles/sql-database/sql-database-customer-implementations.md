---
title: "Implémentations client Azure SQL Database (technique) | Microsoft Docs"
description: "Découvrez les détails techniques des implémentations client d’Azure SQL Database pour résoudre des problèmes métier."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 00c8a713-f20c-4d6b-b8b7-0c1b9ba5f05b
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/03/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: b05b24bf9a14570b07c2c1ab3076835c7167b647
ms.contentlocale: fr-fr
ms.lasthandoff: 03/10/2017


---
# <a name="azure-sql-database-customer-implementation-technical-studies"></a>Études techniques de l’implémentation client Azure SQL Database

- [Daxko](sql-database-implementation-daxko.md) : Daxko/CSI Software était confronté à un défi : sa clientèle des centres de fitness et de loisirs augmentait rapidement, grâce au succès de sa solution complète de logiciels d’entreprise, mais le personnel informatique de l’entreprise rencontrait des difficultés pour faire face aux besoins d’infrastructure informatique liés au développement de la clientèle. La société était de plus en plus gênée par l’augmentation de la charge opérationnelle, en particulier pour la gestion de ses bases de données. Pire encore, cette surcharge rognait les ressources de développement de nouvelles initiatives, notamment les nouvelles fonctionnalités de mobilité des logiciels de la société.

- [GEP](sql-database-implementation-gep.md) : GEP propose des logiciels et des services qui permettent aux leaders de l’approvisionnement dans le monde entier d’optimiser leur impact sur les opérations, les stratégies et les performances financières de leur entreprise. En complément des services de conseils et des services gérés, la société propose SMART by GEP®, une plateforme cloud de logiciels d’approvisionnement complète. Toutefois, elle s’est heurtée à des limites pour prendre en charge des solutions comme SMART by GEP avec ses propres centres de données locaux : les investissements requis étaient exorbitants, et les exigences réglementaires dans d’autres pays les auraient rendus plus colossaux encore. En passant au cloud, GEP a libéré des ressources informatiques, ce qui lui a permis de moins se soucier des opérations informatiques et de se concentrer davantage sur le développement de nouvelles sources de valeur ajoutée pour ses clients dans le monde entier.

- [SnelStart](sql-database-implementation-snelstart.md) : SnelStart crée des logiciels populaires de gestion financière et commerciale pour les petites et moyennes entreprises (PME) aux Pays-Bas. Une équipe de 110 employés, dont 35 dans le service informatique, est au service de ses 55 000 clients. En passant des logiciels de bureau à une offre SaaS (logiciel en tant que service) basée sur Azure, SnelStart a tiré le meilleur parti des services intégrés, en automatisant la gestion grâce à un environnement familier en C# et en optimisant les performances et l’évolutivité grâce à un approvisionnement parfaitement calibré à l’aide de pools élastiques. L’utilisation d’Azure fournit à SnelStart la fluidité nécessaire pour déplacer les clients mobiles en local et sur le cloud.

- [Umbraco](sql-database-implementation-umbraco.md) : Pour simplifier les déploiements clients, Umbraco a ajouté Umbraco-as-a-Service (UaaS) : une offre SaaS (Software-as-a-Service) qui élimine la nécessité de déploiements locaux, fournit une évolutivité intégrée et supprime la surcharge de gestion en permettant aux développeurs de se concentrer sur l’innovation produits plutôt que sur la gestion des solutions. Umbraco est en mesure d’offrir tous ces avantages en s’appuyant sur le modèle PaaS (Platform-as-a-Service) flexible proposé par Microsoft Azure.
