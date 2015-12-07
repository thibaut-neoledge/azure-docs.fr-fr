<properties 
   pageTitle="Forum aux questions sur la disparition des éditions Web et Business des bases de données SQL Azure | Microsoft Azure"
   description="Découvrez les fonctionnalités des nouveaux niveaux de service qui remplacent les bases de données Azure SQL Web et Business."
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jeffreyg"
   editor="monicar" />
<tags 
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2015"
   ms.author="sstein" />

# Forum aux questions sur la disparition des éditions Web et Business

Les bases de données Web et Business SQL Azure sont désormais supprimées. Les niveaux De base, Standard, Premium et Élastique remplacent les bases de données Web et Business.

Pour vous aider lors de la mise à niveau des bases de données Web et Business, le service SQL Database recommande un niveau de service et de performances approprié (niveau tarifaire) pour chaque base de données en fonction de son historique de charge de travail.

**Pour obtenir des recommandations sur les niveaux tarifaires :**

- [Mise à niveau vers SQL Database V12 à l’aide du portail Azure](sql-database-v12-upgrade.md)
- [Mise à niveau vers SQL Database V12 à l’aide de PowerShell](sql-database-upgrade-server.md)
- [Modifier le niveau tarifaire d’une base de données Web ou Business](sql-database-service-tier-advisor.md)
 


## Pourquoi le portail de gestion Azure indique-t-il que mes bases de données des éditions Web et Business sont supprimées ?

Étant donné que les bases de données des éditions Web et Business ne seront plus disponibles après le mois de septembre 2015, le portail de gestion marque les bases de données Web et Business comme étant supprimées. Vous pouvez toujours approvisionner et gérer les bases de données Web et Business comme d’habitude, mais l’étiquette « Supprimé » est un rappel signalant qu’il est préférable d’utiliser le niveau de service De base, Standard ou Premium pour les nouvelles bases de données. Cette étiquette rappelle également que les bases de données Web et Business doivent être mises à niveau vers le niveau Standard, De base ou Premium. Pour plus d’informations sur la mise à niveau des bases de données Web ou Business existantes vers les nouveaux niveaux de service, consultez [Mise à niveau des bases de données SQL des éditions Web et Business vers les nouveaux niveaux de service](sql-database-upgrade-new-service-tiers.md).

## Quel nouveau niveau de service constitue le meilleur choix pour mettre à niveau ma base de données Web ou Business existante ?

La sélection d’un nouveau niveau de service et niveau de performances pour votre base de données Web ou Business varie selon les exigences de fonctionnalités et de performances spécifiques à votre application.

Appliquez les recommandations sur le niveau tarifaire fournies ci-dessus ou, pour obtenir des informations sur la sélection d’un nouveau niveau de service approprié, consultez [Mise à niveau des bases de données SQL des éditions Web et Business vers les nouveaux niveaux de service](sql-database-upgrade-new-service-tiers.md).

## Pourquoi Microsoft introduit-il de nouveaux niveaux de service ?

Suite aux commentaires des clients, nous avons introduit de nouveaux niveaux de service pour Azure SQL Database afin de permettre aux clients de prendre en charge les charges de travail des bases de données relationnelles plus facilement. Les nouveaux niveaux sont conçus pour fournir des performances prévisibles sur une gamme de sept niveaux de demandes d’applications transactionnelles allant du plus léger au plus intense. En outre, les nouveaux niveaux proposent une gamme de fonctionnalités de continuité des activités, un contrat SLA de disponibilité renforcée, des tailles de bases de données plus élevées pour un prix inférieur et une expérience de facturation améliorée.

## Où puis-je obtenir plus d’informations sur les nouveaux niveaux de service ?

Pour obtenir des informations détaillées sur les nouveaux niveaux de service et sur le modèle de performance, consultez [Niveaux de service](sql-database-service-tiers.md). Pour obtenir des informations détaillées sur la tarification des nouveaux niveaux de service, consultez [Tarification de SQL Database](http://azure.microsoft.com/pricing/details/sql-database/).

## Quelles sont les fonctionnalités qui ne seront pas disponibles dans les niveaux De base, Standard et Premium ?

La fonctionnalité Fédérations sera supprimée en même temps que les éditions Web et Business. Nous encourageons les clients souhaitant effectuer une montée en charge de leurs bases de données à utiliser ou migrer vers les [outils de bases de données élastiques](sql-database-elastic-scale-get-started.md) pour [Azure SQL Database](sql-database-elastic-scale-get-started.md), qui simplifient la création et la gestion d’une application qui utilise le partitionnement. Une bibliothèque cliente .NET permet aux applications de définir comment les données sont mappées aux partitions et achemine les demandes OLTP vers les bases de données appropriées. Pour prendre en charge les opérations de gestion qui reconfigurent la façon dont les données sont réparties parmi les partitions, un modèle de service cloud Azure est inclus, que vous pouvez héberger dans votre propre abonnement Azure. En plus des [outils de bases de données élastiques](sql-database-elastic-scale-get-started.md), Microsoft continuera à créer et à publier des [recommandations en matière de pratiques et de modèles de partitionnement personnalisé](https://msdn.microsoft.com/library/azure/dn764977.aspx) basées sur les enseignements tirés des expériences approfondies de nos clients. Les nouveaux clients qui ont besoin d’une fonctionnalité de montée en charge doivent consulter les [outils de bases de données élastiques](sql-database-elastic-scale-get-started.md) et/ou contacter le support technique de Microsoft pour évaluer les possibilités qui leur sont offertes.

Microsoft modifie également l’expérience de copie de base de données avec les bases de données Premium. Avant, le quota de base de données Premium étant limité, CREATE DATABASE … AS A COPY OF en T-SQL créait une base de données Premium suspendue sans ressources réservées, qui était facturée au même taux qu’une base de données Business. Le quota Premium étant désormais plus libre, l’état Premium suspendue n’est plus pris en charge. Des copies de bases de données seront désormais créées avec la même édition et le même niveau de performances que la source et seront facturées en conséquence. Les clients peuvent choisir de rétrograder les bases de données copiées à un niveau de service ou de performances différent afin de réduire le coût si nécessaire. Les bases de données existantes à l’état Premium suspendue seront converties en édition Business dans le cadre de cette nouvelle version. Nous prévoyons que l’introduction de la limite de restauration dans le temps réduira le besoin d’effectuer des copies de sauvegarde des bases de données.

## En quoi les niveaux De base, Standard et Premium améliorent-ils mes opérations de facturation ?

Les bases de données SQL Azure De base, Standard et Premium sont facturées à l’heure et peuvent être augmentées ou réduites quatre fois par période de 24 heures. Vous êtes facturé à un taux fixe basé sur le niveau de service et le niveau de performances les plus élevés que vous choisissez pour chaque heure. En outre, les niveaux de performances (par exemple De base, S1 et P2) sont détaillés sur la facture. Ainsi, il est plus facile de voir le nombre de jours/heures de base de données que vous avez accumulés durant un mois pour chaque niveau de performances. Les bases de données Web et Business continuent à être facturées à l’aide d’Unités de bases de données, en fonction de la taille de la base de données. Pour en savoir plus sur la tarification et sur les différences entre les nouveaux niveaux de service, consultez la [page sur la tarification de SQL Database](http://azure.microsoft.com/pricing/details/sql-database/).


## Voir aussi

[Base de données SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)

[Tarification Web et Business](https://azure.microsoft.com/pricing/details/sql-database/web-business/)

[Niveaux de service](sql-database-service-tiers.md)

[Mise à niveau des bases de données SQL des éditions Web et Business vers les nouveaux niveaux de service](sql-database-upgrade-new-service-tiers.md)

<!---HONumber=AcomDC_1125_2015-->