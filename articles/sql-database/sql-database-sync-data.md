---
title: "Synchroniser des données (aperçu) | Microsoft Docs"
description: "Cette vue d’ensemble présente Azure SQL Data Sync (aperçu)."
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: douglasl
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 94c8160464cd7355ac0e0733801d0b06fcdfab7c
ms.contentlocale: fr-fr
ms.lasthandoff: 07/12/2017

---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Synchroniser des données sur plusieurs bases de données cloud et locales avec SQL Data Sync

SQL Data Sync est un service conçu sur la base de données SQL Azure qui vous permet de synchroniser les données choisies de manière bidirectionnelle sur plusieurs bases de données SQL et instances SQL Server.

Data Sync est basé sur le concept d’un groupe de synchronisation. Un groupe de synchronisation est un groupe de bases de données que vous souhaitez synchroniser.

Un groupe de synchronisation dispose des propriétés suivantes :

-   Le **schéma de synchronisation** décrit quelles sont les données en cours de synchronisation.

-   Le **sens de synchronisation** peut être bidirectionnel ou peut circuler dans une seule direction. Autrement dit, le sens de synchronisation peut être *Hub vers membre*, *Membre vers hub*, ou les deux.

-   L’**intervalle de synchronisation** correspond à la fréquence à laquelle la synchronisation se produit.

-   La **stratégie de résolution de conflit** est une stratégie au niveau groupe, qui peut être *Priorité au hub* ou *Priorité au membre*.

Data Sync utilise une topologie hub and spoke pour synchroniser les données. Vous définissez l’une des bases de données du groupe en tant que base de données Hub. Le reste des bases de données sont des bases de données membres. La synchronisation se produit uniquement entre le hub et des membres individuels.
-   Le **base de données Hub** doit être une base de données Azure SQL Database.
-   Les **bases de données membres** peuvent être des bases de données SQL, des bases de données locales SQL Server, ou des instances SQL Server sur des machines virtuelles Azure.
-   La **base de données de synchronisation** contient les métadonnées et le journal de Data Sync. La base de données de synchronisation doit être une base de données Azure SQL Database située dans la même région que la base de données Hub. La base de données de synchronisation est créée par le client et lui appartient.

> [!NOTE]
> Si vous utilisez une base de données locale, vous devez [configurer un agent local.](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-sql-data-sync)

![Synchroniser des données entre des bases de données](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>Quand utiliser Data Sync

Data Sync est utile dans les cas où les données doivent être mises à jour entre plusieurs bases de données Azure SQL Database ou bases de données SQL Server. Voici les principaux cas d’usage pour Data Sync :

-   **Synchronisation de données hybride :** avec Data Sync, vous pouvez maintenir la synchronisation de données entre vos bases de données locales et les bases de données SQL Azure pour activer des applications hybrides avec leur couche Données dans SQL. Cette fonctionnalité peut intéresser les clients qui envisagent de passer au cloud et souhaiteraient placer une partie de leur application dans Azure.

-   **Applications distribuées :** dans de nombreux cas, il est recommandé de séparer les différentes charges de travail entre plusieurs bases de données. Par exemple, si vous possédez une base de données de production de grande taille, mais que vous devez également exécuter un rapport ou une charge de travail analytique de ces données, il est utile de disposer d’une seconde base de données pour cette charge de travail supplémentaire. Cette approche réduit l’impact sur les performances de votre charge de travail de production. Vous pouvez utiliser Data Sync afin de maintenir la synchronisation de ces deux bases de données.

-   **Applications distribuées globalement :** De nombreuses entreprises sont présentes dans plusieurs régions et même dans plusieurs pays. Afin de réduire la latence du réseau, il est préférable de conserver vos données dans une région proche. Avec Data Sync, vous pouvez facilement synchroniser des bases de données dans différentes régions partout dans le monde.

Nous déconseillons Data Sync pour les scénarios suivants :

-   Récupération d’urgence

-   Mise à l’échelle en lecture

-   ETL (OLTP vers OLAP)

-   Migration du serveur local SQL vers la base de données Azure SQL Database

## <a name="how-does-data-sync-work"></a>Comment fonctionne Data Sync ? 

-   **Suivi des modifications de données :** Data Sync effectue le suivi des modifications en utilisant des déclencheurs d’insertion, de mise à jour et de suppression. Les modifications sont enregistrées dans une table latérale dans la base de données utilisateur.

-   **Synchronisation des données :** Data Sync est conçu dans un modèle de Hub and Spoke. Le hub se synchronise avec chaque membre individuellement. Les modifications depuis le hub sont téléchargées vers le membre, puis les modifications à partir du membre sont téléchargées vers le hub.

-   **Résolution des conflits :** Data Sync fournit deux options pour la résolution de conflit, *Priorité au hub* ou *Priorité au membre*.
    -   Si vous sélectionnez *Priorité au hub*, les modifications dans le hub remplacent toujours les modifications dans le membre.
    -   Si vous sélectionnez *Priorité au membre*, les modifications dans le membre remplacent toujours les modifications dans le hub. S’il existe plusieurs membres, la valeur finale dépend du membre qui se synchronise en premier.

## <a name="limitations-and-considerations"></a>Limitations et considérations

### <a name="performance-impact"></a>Impact sur les performances
Data Sync utilise des déclencheurs d’insertion, de mise à jour et de suppression pour effectuer le suivi des modifications. Cela crée des tables latérales dans la base de données utilisateur pour le suivi des modifications. Ces activités de suivi des modifications ont un impact sur votre charge de travail de base de données. Évaluez votre niveau de service et effectuez une mise à niveau si nécessaire.

### <a name="eventual-consistency"></a>Cohérence éventuelle
Étant donné que Data Sync est basé sur le déclencheur, la cohérence transactionnelle n’est pas garantie. Microsoft garantit que toutes les modifications sont effectuées par la suite et que Data Sync n’entraîne pas de perte de données.

### <a name="unsupported-data-types"></a>Types de données non pris en charge

-   FileStream

-   SQL/CLR UDT

-   XMLSchemaCollection (prise en charge de XML)

-   Curseur, horodateur, hierarchyid

### <a name="requirements"></a>Configuration requise

-   Chaque table doit avoir une clé primaire.

-   Une table ne peut pas avoir une colonne d’identité qui n’est pas la clé primaire.

-   Les noms des objets (bases de données, tables et colonnes) ne peuvent pas contenir les caractères imprimables suivants : point (.), crochet gauche ou crochet droit (]).

### <a name="limitations-on-service-and-database-dimensions"></a>Limitations des dimensions de la base de données et du service

|                                                                 |                        |                             |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| **Dimensions**                                                      | **Limite**              | **Solution de contournement**              |
| Nombre maximal de groupes de synchronisation auquel peut appartenir une base de données.       | 5                      |                             |
| Nombre maximal de points de terminaison dans un seul groupe de synchronisation              | 30                     | Créer plusieurs groupes de synchronisation |
| Nombre maximal de points de terminaison locaux dans un seul groupe de synchronisation. | 5                      | Créer plusieurs groupes de synchronisation |
| Noms de la base de données, de la table, du schéma et des colonnes                       | 50 caractères par nom |                             |
| Tables dans un groupe de synchronisation                                          | 500                    | Créer plusieurs groupes de synchronisation |
| Colonnes d’une table dans un groupe de synchronisation                              | 1 000                   |                             |
| Taille de ligne de données sur une table                                        | 24 Mo                  |                             |
| Intervalle de synchronisation minimale                                           | 5 minutes              |                             |

## <a name="common-questions"></a>Questions courantes

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>À quelle fréquence Data Sync synchronise-t-il mes données ? 
Il effectue une synchronisation au minimum toutes les cinq minutes.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Puis-je utiliser Data Sync pour effectuer une synchronisation entre les bases de données SQL Server locales uniquement ? 
Pas directement. Vous pouvez effectuer une synchronisation entre les bases de données SQL Server locales de façon indirecte, en créant une base de données Hub dans Azure, puis en ajoutant les bases de données locales au groupe de synchronisation.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>Puis-je utiliser Data Sync pour envoyer des données de ma base de données de production vers une base de données vide, et garder mes données synchronisées ? 
Oui. Créez manuellement le schéma dans la nouvelle base de données en créant le script à partir de la base de données d’origine. Après avoir créé le schéma, ajoutez les tables à un groupe de synchronisation pour copier les données et les garder synchronisées.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Pourquoi puis-je voir des tables que je n’ai pas créées ?  
Data Sync crée des tables latérales dans votre base de données utilisateur pour le suivi des modifications. Ne les supprimez pas, sinon Data Sync cessera de fonctionner.
   
### <a name="i-got-an-error-message-that-said-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-the-error"></a>J’ai reçu un message d’erreur indiquant : « Impossible d’insérer la valeur NULL dans la colonne \<colonne\>. Cette colonne n’accepte pas les valeurs NULL. » Que signifie cette erreur et comment puis-je la corriger ? 
Ce message d’erreur indique l’un des deux problèmes suivants :
1.  Une table sans clé primaire existe peut-être. Pour résoudre ce problème, ajoutez une clé primaire à toutes les tables que vous synchronisez.
2.  Une clause WHERE existe peut-être dans votre instruction CREATE INDEX. La synchronisation ne gère pas cette condition. Pour résoudre ce problème, supprimez la clause WHERE ou apportez manuellement les modifications à toutes les bases de données. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>Comment Data Sync traite-t-il les références circulaires ? Autrement dit, lorsque les mêmes données sont synchronisées dans plusieurs groupes de synchronisation et changent constamment en conséquence ?
Data Sync ne traite pas les références circulaires. Veillez à les éviter. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur SQL Database et SQL Data Sync, consultez :

-   [Prise en main de SQL Data Sync](sql-database-get-started-sql-data-sync.md)

-   [Télécharger la documentation technique complète sur la synchronisation des données SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)

-   [Télécharger la documentation de l’API REST de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

-   [Vue d’ensemble des bases de données SQL](sql-database-technical-overview.md)

-   [Gestion du cycle de vie des bases de données](https://msdn.microsoft.com/library/jj907294.aspx)

