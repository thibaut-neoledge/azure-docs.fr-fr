<properties 
	pageTitle="Niveaux de performances dans DocumentDB | Azure" 
	description="Découvrez comment les niveaux de performances dans DocumentDB permettent de réserver le débit pour chaque collection." 
	services="documentdb" 
	authors="johnfmacintyre" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2015" 
	ms.author="johnmac"/>

# Niveaux de performances dans DocumentDB #

Cet article fournit une vue d'ensemble des niveaux de performances dans [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/). 

> [AZURE.IMPORTANT] Les niveaux de performances seront fournis après la mise à disposition générale d'Azure DocumentDB. Cette version est prévue pour avril 2015.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :  

-	Qu'est-ce qu'un niveau de performances ?
-	Comment le débit est-il réservé pour un compte de base de données ?
-	Comment puis-je utiliser les niveaux de performances ?
-	Quel est le mode de facturation des niveaux de performances ?

##<a id="Sub1"></a>Introduction aux niveaux de performances##

Chaque collection DocumentDB créée sous un compte d'utilisateur Standard est configurée avec un niveau de performances associé. Les niveaux de performances sont désignés comme suit : S1, S2 ou S3 (du niveau le plus faible au plus élevé). Le niveau de performances de la collection détermine la quantité de ressources de service réservée pour votre application. Chaque collection dans une base de données peut avoir un niveau de performances différent, ce qui vous permet de désigner davantage de débit pour les collections fréquemment sollicitées et moins de débit pour les collections rarement sollicitées. 

À chaque niveau de performances est associé un taux limite d'unités de demande. Il s'agit du débit qui sera réservé pour une collection en fonction de son niveau de performances et qui sera disponible exclusivement pour cette collection. Des collections peuvent être créées via le [portail Azure](http://portal.azure.com) ou l'un des [Kits de développement logiciel (SDK) DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Les API DocumentDB vous permettent de spécifier le niveau de performances d'une collection. 

<table> 
<tbody>
<tr>
<td valign="top" ><p><b>Niveau de performances de collection</b></p></td>
<td valign="top" ><p><b>Débit réservé</b></p></td>
</tr>

<tr>
<td valign="top" ><p>S1</p></td>
<td valign="top" ><p>250 unités de demande/seconde</p></td>
</tr>

<tr>
<td valign="top" ><p>S2</p></td>
<td valign="top" ><p>1000 unités de demande/seconde</p></td>
</tr>

<tr>
<td valign="top" ><p>S3</p></td>
<td valign="top" ><p>2500 unités de demande/seconde</p></td>
</tr>

</tbody>
</table>

DocumentDB permet un ensemble complet d'opérations de base de données, notamment des requêtes, des requêtes avec des fonctions définies par l'utilisateur, des procédures stockées et des déclencheurs. Le coût de traitement associé à chacune de ces opérations varie selon le processeur, les E/S et la mémoire nécessaires à l'exécution de l'opération. Plutôt que de vous soucier de la gestion des ressources matérielles, vous pouvez considérer une unité de demande comme une mesure unique des ressources nécessaires à l'exécution des opérations de base de données et à la réponse à une demande de l'application.

> [AZURE.NOTE] Les niveaux de performances sont mesurés en unités de demande. À chaque niveau de performances est associé un taux maximum d'unités de demande par seconde. Le niveau de performances d'une collection peut être ajusté via les API ou le [portail Azure](https://portal.azure.com/).

##<a id="Sub2"></a>Définition des niveaux de performances pour les collections##
Lorsqu'une collection est créée, l'affectation complète des unités de demande est réservée pour la collection, en fonction du niveau de performances désigné. Par exemple, si une collection est définie en tant que S3, elle est capable de traiter 2 500 unités de demande/seconde. Chaque collection réserve son débit désigné et 10 Go de stockage de base de données. Le prix de la collection varie selon le niveau de performances choisi (S1, S2, S3). Notez que DocumentDB fonctionne sur la base d'une réservation de capacité ; en créant une collection, une application réserve un débit et un stockage de base de données, et elle est facturée en fonction de ces derniers, quelle que soit la quantité de stockage et de débit réellement utilisée.

Après la création de collections, vous pouvez modifier le niveau de performances via les Kits de développement logiciel (SDK) DocumentDB ou via le portail de gestion Azure. 

> [AZURE.IMPORTANT] Les collections DocumentDB standard sont facturées au tarif horaire et chaque collection que vous créez sera facturée pour une heure d'utilisation au minimum. 

Si vous ajustez le niveau de performances d'une collection pendant une période d'une heure, vous serez facturé pour le plus haut niveau de performances défini pendant cette période. Par exemple, si vous augmentez votre niveau de performances pour une collection à 8 h 53, vous serez facturé pour le nouveau niveau à partir de 8 h 00. De même, si vous diminuez le niveau de performances à 8 h 53, le nouveau taux s'appliquera à partir de 9 h 00.

Les unités de demande sont réservées pour chaque collection sur la base du niveau de performances défini. La consommation d'unités de demande est évaluée sous la forme d'un taux par seconde. Les applications qui dépassent le taux d'unités de demande (ou le niveau de performances) configuré sur une collection sont limitées jusqu'à ce que le taux tombe sous le niveau réservé pour cette collection. Si votre application requiert un niveau de débit plus élevé, vous pouvez augmenter le niveau de performances pour chaque collection.

> [AZURE.NOTE] Lorsque votre application dépasse les niveaux de performances d'une ou plusieurs collections, les demandes sont limitées en fonction de chaque collection. Cela signifie que certaines demandes d'application peuvent réussir tandis que d'autres peuvent être limitées.

##<a id="Sub3"></a>Utilisation des niveaux de performances##
Les collections DocumentDB vous permettent de partitionner vos données selon les modèles de requête et les besoins de performances de votre application. Pour plus d'informations sur le partitionnement des données avec DocumentDB, consultez la [documentation sur le partitionnement des données](documentdb-partition-data.md). Avec l'indexation automatique et la prise en charge des requêtes dans DocumentDB, il est assez courant colocaliser des documents hétérogènes au sein de la même collection. Les points clés de la décision quant à l'utilisation de collections distinctes pour les données sont les suivants :

- Requêtes - Une collection est l'étendue pour l'exécution des requêtes. Si vous avez besoin d'interroger un ensemble de documents, la colocalisation des documents dans une collection unique donne les modèles de lecture les plus efficaces.
- Transactions - Une collection est le domaine de transaction des procédures stockées et des déclencheurs. Toutes les transactions sont limitées à une collection unique. 
- Performances - À une collection est associé un niveau de performances. Cela garantit que chaque collection a des performances prévisibles via des unités de demande réservées. Les données peuvent être affectées à différentes collections, avec des niveaux de performances différents, selon la fréquence des accès.

> [AZURE.IMPORTANT] Il est important de comprendre que vous serez facturé au tarif standard en fonction du nombre de collections créées dans votre application.

Il est recommandé de faire en sorte que votre application utilise un petit nombre de collections et de maîtriser les modèles d'application pour la création de nouvelles collections. Vous pouvez choisir de réserver la création de la collection comme une action de gestion gérée en dehors de votre application. De même, l'ajustement du niveau de performances pour une collection modifiera le taux horaire de facturation de la collection. Vous devez surveiller les niveaux de performances d'une collection si votre application les ajuste de manière dynamique.

##<a name="NextSteps"></a>Étapes suivantes

Pour en savoir plus sur la tarification et la gestion des données avec Azure DocumentDB, explorez les ressources suivantes :
 
- [Tarification de DocumentDB](http://azure.microsoft.com/pricing/details/documentdb/)
- [Gestion de la capacité de DocumentDB](documentdb-manage.md) 
- [Modélisation des données dans DocumentDB](documentdb-modeling-data.md)
- [Partitionnement des données dans DocumentDB](documentdb-partition-data.md)

Pour en savoir plus sur DocumentDB, consultez la [documentation](http://azure.microsoft.com/documentation/services/documentdb/) Azure DocumentDB. 



<!--HONumber=49--> 