<properties
   pageTitle="Distribution mondiale des données avec DocumentDB | Microsoft Azure"
   description="Apprenez-en plus sur la géoréplication à l’échelle de la planète, le basculement et la récupération des données à l’aide de bases de données mondiales à partir d’Azure DocumentDB, un service de base de données NoSQL entièrement géré."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/14/2016"
   ms.author="kipandya"/>
   
   
# Distribution mondiale des données avec DocumentDB

Azure DocumentDB est conçu pour satisfaire les exigences des applications IoT, notamment les millions d’appareils et applications Internet distribués mondialement qui permettent aux utilisateurs de vivre des expériences hautement réactives dans le monde entier. Ces systèmes de base de données doivent relever le défi d’un accès à faible latence aux données d’application à partir de plusieurs zones géographiques avec des garanties de cohérence et de disponibilité des données bien définies. DocumentDB est un système de base de données distribué mondialement qui simplifie la distribution globale de données en gérant complètement les comptes de bases de données dans plusieurs régions qui fournissent des compromis clairs entre cohérence, disponibilité et performance, avec les garanties correspondantes. Les comptes de bases de données DocumentDB sont fournis avec des capacités de haute disponibilité, des latences en millisecondes à un seul chiffre, plusieurs [niveaux de cohérence bien définis][consistency], un basculement régional transparent avec des API d’hébergement multiple et la possibilité de mettre à l’échelle le débit et le stockage dans le monde entier de manière flexible.

  
## Configuration de comptes dans plusieurs régions

La configuration de votre compte DocumentDB pour une mise à l’échelle mondiale peut être effectuée en moins d’une minute via le Portail Azure. Il vous suffit de sélectionner le niveau de cohérence adéquat parmi plusieurs niveaux de cohérence bien définis pris en charge et d’associer le nombre de régions Azure de votre choix à votre compte de base de données. Les niveaux de cohérence de DocumentDB fournissent des compromis clairs entre les performances et la garantie de cohérence spécifique.

![DocumentDB offre le choix entre plusieurs modèles de cohérence bien définis (souples)][1]

DocumentDB offre plusieurs modèles de cohérence (souples) bien définis parmi lesquels choisir.

La sélection du niveau de cohérence adéquat dépend de la garantie de cohérence des données dont votre application a besoin. DocumentDB réplique vos données dans toutes les régions spécifiées automatiquement et garantit la cohérence que vous avez sélectionnée pour votre compte de base de données.


## Utilisation du basculement entre plusieurs régions 

Azure DocumentDB est en mesure de basculer en toute transparence d’un compte de base de données à l’autre entre plusieurs régions Azure. Les nouvelles [API multihébergement][developingwithmultipleregions] garantissent que votre application peut continuer à utiliser un point de terminaison logique et n’est pas interrompue par le basculement. Vous contrôlez le basculement, ce qui vous laisse la possibilité de modifier l’hébergement de votre compte de base de base de données dans l’éventualité où des conditions de défaillance potentielle se produiraient, notamment la défaillance (réelle ou simulée) d’une application, d’une infrastructure, d’un service ou d’une région. En cas de défaillance d’une région DocumentDB, le service bascule en toute transparence votre compte de base de données et votre application continue à accéder aux données sans aucune perte de disponibilité. Alors que DocumentDB offre un [SLA de disponibilité à 99,99 %][sla], vous pouvez tester les propriétés de disponibilité de l’application de bout en bout en simulant une défaillance de région [par programme][arm] et via le Portail Azure.


## Mise à l’échelle mondiale
DocumentDB vous permet d’approvisionner le débit indépendamment et d’utiliser du stockage pour chaque collection DocumentDB à n’importe quelle échelle, dans toutes les régions associées à votre compte de base de données à l’échelle mondiale. Une collection DocumentDB est automatiquement distribuée mondialement et gérée dans toutes les régions associées à votre compte de base de données. Au sein de votre compte de base de données, vous pouvez distribuer les collections dans n’importe quelle région Azure où le [service DocumentDB est disponible][serviceregions].

Le débit acheté et le stockage utilisé pour chaque collection DocumentDB sont automatiquement approvisionnés à parts égales dans toutes les régions. Vous pouvez ainsi mettre votre application à l’échelle en toute transparence dans le monde entier [en ne payant que le débit et le stockage utilisés par heure][pricing]. Par exemple, si vous avez configuré 2 millions de requêtes d’unités pour une collection DocumentDB, chacune des régions associées à votre compte de base de données obtient 2 millions d’unités de requêtes pour cette collection. En voici une illustration :

![Mise à l’échelle du débit pour une collection DocumentDB dans quatre régions][2]

DocumentDB garantit des latences de lecture < 10 ms et d’écriture < 15 ms avec une disponibilité à 99,99 %. Les demandes de lecture ne franchissent jamais la limite du centre de données afin de garantir les [exigences de cohérence que vous avez sélectionnées][consistency]. Les écritures sont toujours validées localement dans le quorum avant d’être acceptées par les clients. Chaque compte de base de données est configuré avec la priorité de région d’écriture. La région désignée avec la priorité la plus élevée agira comme la région d’écriture en cours du compte. Tous les SDK achemineront en toute transparence les écritures de compte de base de données vers la région d’écriture en cours.

Enfin, dans la mesure où DocumentDB est complètement [indépendant du schéma][vldb], vous n’avez jamais à vous soucier de la gestion/mise à jour des schémas ou des index secondaires entre plusieurs centres de données. Vos [requêtes SQL][sqlqueries] continuent à travailler pendant que vos modèles d’application et de données poursuivent leur évolution.


## Activation de la distribution mondiale 

Vous pouvez décider de distribuer vos données localement ou mondialement en associant une ou plusieurs régions Azure à un compte de base de données DocumentDB. Vous pouvez décider de distribuer mondialement vos données ou de les limiter à une seule région en ajoutant ou supprimant des régions dans votre compte de base de données à tout moment. Les comptes de base de données DocumentDB qui prennent en charge l’affectation dans plusieurs régions peuvent être créés via Azure Marketplace en sélectionnant « DocumentDB – Multi-Region Database Account » (DocumentDB - Compte de base de données dans plusieurs régions).



## Étapes suivantes

Apprenez-en plus sur la distribution mondiale de données avec DocumentDB dans les articles suivants :

* [Approvisionnement du débit et du stockage pour une collection][throughputandstorage]
* [API multihébergement via les SDK REST, .NET, Java, Python et Node][developingwithmultipleregions]
* [Niveaux de cohérence dans DocumentDB][consistency]
* [SLA de disponibilité][sla]
* [Gestion du compte de base de données][manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: https://azure.microsoft.com/documentation/articles/documentdb-partition-data/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: https://azure.microsoft.com/documentation/articles/documentdb-developing-with-multiple-regions/
[createaccount]: https://azure.microsoft.com/documentation/articles/documentdb-create-account/
[manageaccount]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/
[manageaccount-consistency]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/#consistency
[manageaccount-addregion]: https://azure.microsoft.com/documentation/articles/documentdb-manage-account/#addregion
[throughputandstorage]: https://azure.microsoft.com/documentation/articles/documentdb-manage/
[arm]: https://azure.microsoft.com/fr-FR/documentation/articles/documentdb-automation-resource-manager-cli/
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/fr-FR/regions/#services
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: https://azure.microsoft.com/documentation/articles/documentdb-sql-query/

<!---HONumber=AcomDC_0615_2016-->