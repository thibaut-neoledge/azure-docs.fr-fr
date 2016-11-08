---
title: FAQ de base de données SQL Azure
description: Réponses fréquemment posées sur les bases de données du cloud et Azure SQL Database, le système de gestion de base de données relationnelle Microsoft (SGBDR) et la base de données en tant que service dans cloud.
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 08/16/2016
ms.author: sashan;carlrab

---
# Forum Aux Questions de base de données SQL
## Comment l’utilisation de base de données SQL apparaît-elle sur ma facture ?
La base de données SQL effectue sa facturation en utilisant un taux horaire prédéterminé basé sur le niveau de service ainsi qu’un niveau de performance pour des bases de données uniques ou des eDTU par pool élastique de base de données. L’utilisation réelle est calculée au pro-rata horaire, et il se peut que votre facture affiche des fractions d’heure. Par exemple, si une base de données existe pendant 12 heures au cours d’un mois, votre facture affiche une demi-journée d’utilisation. En outre, les niveaux de service et le niveau de performances et le nombre d’eDTU par pool sont séparés dans la facture afin que vous puissiez connaître aisément le nombre de jours pendant lequel vous avez utilisé la base de données chaque mois.

## Que se passe-t-il si une base de données est active pendant moins d’une heure ou utilise un niveau de service plus élevé pendant moins d’une heure ?
Vous êtes facturé pour chaque heure d’existence de la base de données avec le niveau de service le plus élevé, le niveau de performance appliqué pendant cette heure quel que soit l’usage, ou si la base de données a été active pendant moins d’une heure. Par exemple, si vous avez créé une base de données unique et que vous l’avez supprimée cinq minutes après, votre facture mentionne le coût d’une heure de base de données.

Exemples

* Si vous créez une base de données de base, puis que vous la mettez à niveau immédiatement vers le niveau Standard S1, vous serez facturé au tarif Standard S1 pour la première heure.
* Si vous mettez à niveau une base de données De base vers Premium à 22 h 00 et si la mise à niveau se termine à 01 h 35 le jour suivant, vous êtes facturé au tarif Premium à partir d’1 h 00.
* Si vous passez une base de données Premium à une version antérieure De base à 11 h 00 et terminez à 14 h 15, la base de données est facturée au tarif Premium jusqu’à 15 h 00, après quoi, elle est facturée aux tarifs De base.

## Comment l’utilisation du pool élastique de bases de données s’affiche-il sur ma facture et que se passe-t-il lorsque je change le nombre d’eDTU par pool ?
Les frais de pool élastique de bases de données s’affichent sur votre facture en tant que DTU élastiques (eDTU) dans les incréments indiqués sous eDTU par pool sur la [page de facturation](https://azure.microsoft.com/pricing/details/sql-database/). Il n’existe pas de facturation par base de données pour les pools élastiques de bases de données. Vous êtes facturé pour chaque heure d’existence d’un pool selon l’eDTU la plus élevée indépendamment de l’utilisation ou si le pool a été actif de moins d’une heure.

Exemples

* Si vous créez un pool de base de données élastique standard avec 200 eDTU à 11 h 18, en ajoutant cinq bases de données au pool, vous êtes facturé pour 200 eDTU pendant une heure complète, à partir de 11 h 00 pendant le reste de la journée.
* Le 2e jour, à 5 h 05, la base de données 1 commence à consommer 50 eDTU et reste stable toute la journée. Les bases de données 2-5 fluctuent entre 0 et 80 eDTU. Pendant la journée, vous ajoutez cinq autres bases de données qui utilisent des eDTU pendant la journée. Le jour 2 est un jour complet facturé à 200 eDTU.
* Le 3e jour, à 5 h du matin vous ajoutez 15 autres bases de données. L’utilisation de la base de données augmente pendant la journée jusqu’au point où vous décidez d’augmenter le nombre d’eDTU du pool de 200 à 400 à 20 h 05. Les frais au niveau de 200 eDTU étaient en vigueur jusqu’à 8 h 00 et augmentent jusqu’à 400 eDTU pour les quatre heures restantes.

## Comment l’utilisation de la géo-réplication active dans un pool de base de données élastique figurera-t-elle sur ma facture ?
Contrairement aux bases de données uniques, la [géo-réplication active](sql-database-geo-replication-overview.md) avec les bases de données élastiques n’a pas un impact direct sur la facturation. Vous êtes facturé pour les eDTU configurées pour chaque pool (pool principal et pool secondaire)

## Quel sera l’impact de l’utilisation de la fonctionnalité d’audit sur ma facture ?
La fonctionnalité d’audit est intégrée au service Base de données SQL sans frais supplémentaires et est disponible pour les bases de données De base, Standard et Premium. Toutefois, pour stocker les journaux d’audit, la fonction d’audit utilise un compte Azure Storage et les tarifs des tables et files d’attente dans Azure Storage s’appliquent en fonction de la taille de votre journal d’audit.

## Comment trouver le niveau de service adéquat et le niveau de performance adapté pour les pools de bases de données uniques et élastiques ?
Il existe quelques outils à votre disposition.

* Pour les bases de données locales, utilisez le [conseil de dimensionnement DTU](http://dtucalculator.azurewebsites.net/), qui recommande les bases de données et les DTU requis, et évalue plusieurs bases de données pour les pools de base de données élastiques.
* Si une base de données unique tire avantage d’être au sein d’un pool, le moteur intelligent d’Azure recommande un pool de base de données élastique s’il voit un schéma d’utilisation historique qui le garantit. Consultez [Surveiller et gérer un pool de base de données élastique avec le portail Azure](sql-database-elastic-pool-manage-portal.md). Pour plus d’informations sur la façon d’effectuer des calculs vous-même, consultez [Considérations relatives au prix et aux performances d’un pool de bases de données élastiques](sql-database-elastic-pool-guidance.md)
* Pour voir si vous devez activer/désactiver une base de données unique, consultez [Guide des performances pour les bases de données uniques](sql-database-performance-guidance.md).

## À quelle fréquence puis-je changer le niveau de service ou le niveau de performances d’une base de données unique ?
Avec les bases de données V12, vous pouvez modifier le niveau de service (entre De base, Standard et Premium) ou le niveau de performance au sein d’un niveau de service (par exemple, S1 à S2) aussi souvent que vous le souhaitez. Pour les versions de bases de données plus anciennes, vous pouvez modifier le niveau de service ou de performance jusqu’à quatre fois pendant une période de 24 heures.

## À quelle fréquence puis-je ajuster le nombre d’eDTU par pool ?
Aussi souvent que vous le souhaitez.

## Combien de temps faut-il pour modifier le niveau de service ou le niveau de performances d’une base de données ou pour déplacer une base de données dans ou hors du pool élastique de base de données ?
Le changement de niveau de service d’une base de données et le déplacement dans ou vers un pool exige que la base de données soit copiée sur la plateforme en tant qu’opération d’arrière-plan. Le changement de niveau de service peut prendre de quelques minutes à plusieurs heures en fonction de la taille de la base de données. Dans les deux cas, les bases de données restent en ligne et disponibles pendant le déplacement. Pour plus d’informations sur la modification de bases de données uniques, consultez [Modifier le niveau de service d’une base de données](sql-database-scale-up.md).

## Quand dois-je choisir une base de données unique plutôt que des bases de données élastiques ?
En général, les pools de base de données élastiques sont conçus pour un modèle d’[application en tant que service (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md) standard, où il existe une base de données par client ou par locataire. L’achat de bases de données individuelles et le sur-approvisionnement destiné à répondre aux pics de demandes variables de chaque base de données ne sont généralement pas rentables. Avec les pools, vous gérez les performances collectives du pool, et les bases de données se mettent à l’échelle automatiquement.

Le moteur intelligent d’Azure recommande un pool pour les bases de données quand un modèle d’utilisation l’exige. Pour plus d’informations, consultez [Recommandations relatives aux niveaux tarifaires des bases de données SQL](sql-database-service-tier-advisor.md). Pour obtenir des instructions détaillées sur le choix entre les bases de données uniques et élastiques, consultez [Considérations relatives aux prix et aux performances pour les pools élastiques de bases de données](sql-database-elastic-pool-guidance.md).

## Qu’est-ce que cela signifie si je possède jusqu’à 200 % du stockage de base de données configuré maximal pour le stockage de sauvegarde ?
Le stockage de sauvegarde est le stockage associé à vos sauvegardes de bases de données automatisées qui sont utilisées pour la [restauration dans le temps](sql-database-recovery-using-backups.md#-point-in-time-restore) et la [géo-restauration](sql-database-recovery-using-backups.md#geo-restore). Microsoft Azure SQL Database fournit jusqu’à 200 % du stockage de base de données maximal configuré pour le stockage de sauvegarde sans coût supplémentaire. Par exemple, si vous avez une instance de base de données Standard configurée à une taille de 250 Go, vous bénéficiez de 500 Go d’espace de stockage de sauvegarde sans coût supplémentaire. Si votre base de données dépasse l’espace de stockage de sauvegarde fourni, vous pouvez choisir de réduire la période de rétention en contactant le Support technique Azure ou en achetant l’espace de stockage supplémentaire facturé au tarif RA-GRS (Read-Access Geo Redundant Microsoft Azure) standard. Pour plus d’informations sur la facturation RA-GRS, consultez la page Détails de tarification de stockage.

## Je passe de services Web/Business à de nouveaux niveaux de service, que dois-je savoir ?
Les bases de données Web et Business SQL Azure sont désormais supprimées. Les niveaux De base, Standard, Premium et Élastique remplacent les bases de données Web et Business. Nous disposons d’un Forum aux questions supplémentaires qui devrait vous aider dans cette période de transition. [Forum aux questions sur la disparition des éditions Web et Business](sql-database-web-business-sunset-faq.md)

## Quel décalage de réplication est attendu lors de la réplication géographique d’une base de données entre deux régions au sein de la même zone géographique Azure ?
Nous prenons actuellement en charge un RPO de cinq secondes et le décalage de réplication est inférieur à cette mesure quand la zone géographique secondaire est hébergée dans la région Azure associée recommandée et au même niveau de service.

## Quel est le décalage réplication attendu lorsque la zone géographique secondaire est créée dans la même région que la base de données primaire ?
En se basant sur des données empiriques, il y a peu de différence entre la réplication intra-région et inter-région si la région associée recommandée par Azure est utilisée.

## Si une défaillance du réseau entre deux régions se produit, comment la logique de nouvelle tentative fonctionne-t-elle lorsque la géoréplication est définie ?
En cas de déconnexion, une nouvelle tentative de rétablissement des connexions est effectuée toutes les 10 secondes.

## Que puis-je faire pour garantir qu’une modification critique de la base de données primaire est répliquée ?
La zone géographique secondaire est un réplica asynchrone et nous n’essayons pas de conserver une synchronisation complète avec la zone principale. Mais nous fournissons une méthode pour forcer la synchronisation afin de garantir la réplication des modifications critiques (par exemple, les mises à jour du mot de passe). La synchronisation forcée a un impact sur les performances, car elle bloque le thread appelant jusqu’à ce que toutes les transactions validées soient répliquées. Pour plus d’informations, consultez [sp\_wait\_for\_database\_copy\_sync](https://msdn.microsoft.com/library/dn467644.aspx).

## Quels outils sont disponibles pour surveiller le décalage de réplication entre la base de données primaire et la zone géographique secondaire ?
Nous exposons le décalage de réplication en temps réel entre la base de données primaire et la zone géographique secondaire par le biais d’une vue de gestion dynamique (DMV). Pour plus d’informations, consultez [sys.dm\_geo\_replication\_link\_status](https://msdn.microsoft.com/library/mt575504.aspx).

<!---HONumber=AcomDC_0817_2016-->