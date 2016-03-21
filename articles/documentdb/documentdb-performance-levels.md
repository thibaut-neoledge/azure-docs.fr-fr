<properties 
	pageTitle="Niveaux de performances dans DocumentDB | Microsoft Azure" 
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
	ms.date="03/08/2016" 
	ms.author="johnmac"/>

#Niveaux de performances dans DocumentDB

Cet article fournit une vue d’ensemble des niveaux de performances dans [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

-	Qu'est-ce qu'un niveau de performances ?
-	Comment le débit est-il réservé pour un compte de base de données ?
-	Comment puis-je utiliser les niveaux de performances ?
-	Quel est le mode de facturation des niveaux de performances ?

##Introduction aux niveaux de performances

Chaque collection DocumentDB créée sous un compte d'utilisateur Standard est configurée avec un niveau de performances associé. Les niveaux de performances sont désignés comme suit : S1, S2 ou S3 (du niveau le plus faible au plus élevé). Le niveau de performances de la collection détermine la quantité de ressources de traitement de requête réservée pour votre application. Chaque collection dans une base de données peut avoir un niveau de performances différent, ce qui vous permet de désigner davantage de débit pour les collections fréquemment sollicitées et moins de débit pour les collections rarement sollicitées. Le niveau de performances minimal pour toute collection est S1.

À chaque niveau de performances est associé un taux limite d'unités de demande. Il s'agit du débit qui sera réservé pour une collection en fonction de son niveau de performances et qui sera disponible exclusivement pour cette collection. Vous pouvez créer des collections via le [portail Microsoft Azure](https://portal.azure.com) ou l’un des [Kits de développement logiciel (SDK) DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Les API DocumentDB vous permettent de spécifier le niveau de performances d'une collection.

Niveau de performances de collection|Débit réservé
---|---
S1|250 unités de demande/seconde
S2|1 000 unités de demande/seconde
S3|2 500 unités de demande/seconde

DocumentDB permet un ensemble complet d'opérations de base de données, notamment des requêtes, des requêtes avec des fonctions définies par l'utilisateur, des procédures stockées et des déclencheurs. Le coût de traitement associé à chacune de ces opérations varie selon le processeur, les E/S et la mémoire nécessaires à l'exécution de l'opération. Plutôt que de vous soucier de la gestion des ressources matérielles, vous pouvez considérer une unité de demande comme une mesure unique des ressources nécessaires à l'exécution des opérations de base de données et à la réponse à une demande de l'application.

> [AZURE.NOTE] Les niveaux de performances sont mesurés en unités de demande. À chaque niveau de performances est associé un taux maximum d'unités de demande par seconde. Le niveau de performances d’une collection peut être ajusté via les API ou le [portail Microsoft Azure](https://portal.azure.com/). Les modifications au niveau des performances sont censées se terminer en 3 minutes.

##Définition des niveaux de performances pour les collections
Lorsqu'une collection est créée, l'affectation complète des unités de demande est réservée pour la collection, en fonction du niveau de performances désigné. Par exemple, si une collection est définie en tant que S3, elle est en mesure de traiter 2 500 unités de demande/seconde. Chaque collection réserve son débit affecté et 10 Go de stockage de base de données. Le prix de la collection varie selon le niveau de performances choisi (S1, S2, S3). Notez que DocumentDB fonctionne sur la base d'une réservation de capacité ; en créant une collection, une application réserve un débit et un stockage de base de données, et elle est facturée en fonction de ces derniers, quelle que soit la quantité de stockage et de débit réellement utilisée.

Après la création de collections, vous pouvez modifier le niveau de performances via les Kits de développement logiciel (SDK) DocumentDB ou via le portail Azure Classic.

> [AZURE.IMPORTANT] Les collections DocumentDB standard sont facturées au tarif horaire et chaque collection que vous créez sera facturée pour une heure d'utilisation au minimum.

Si vous ajustez le niveau de performances d'une collection pendant une période d'une heure, vous serez facturé pour le plus haut niveau de performances défini pendant cette période. Par exemple, si vous augmentez votre niveau de performances pour une collection à 8 h 53, vous serez facturé pour le nouveau niveau à partir de 8 h 00. De même, si vous diminuez le niveau de performances à 8 h 53, le nouveau taux s'appliquera à partir de 9 h 00.

Les unités de demande sont réservées pour chaque collection sur la base du niveau de performances défini. La consommation d'unités de demande est évaluée sous la forme d'un taux par seconde. Les applications qui dépassent le taux d'unités de demande (ou le niveau de performances) configuré sur une collection sont limitées jusqu'à ce que le taux tombe sous le niveau réservé pour cette collection. Si votre application requiert un niveau de débit plus élevé, vous pouvez augmenter le niveau de performances pour chaque collection.

> [AZURE.NOTE] Lorsque votre application dépasse les niveaux de performances d'une ou plusieurs collections, les demandes sont limitées en fonction de chaque collection. Cela signifie que certaines demandes d'application peuvent réussir tandis que d'autres peuvent être limitées.

##Utilisation des niveaux de performances
Les collections DocumentDB vous permettent de partitionner vos données selon les modèles de requête et les besoins de performances de votre application. Pour plus d’informations sur le partitionnement des données avec DocumentDB, consultez la [documentation sur le partitionnement des données](documentdb-partition-data.md). Avec l'indexation automatique et la prise en charge des requêtes dans DocumentDB, il est assez courant colocaliser des documents hétérogènes au sein de la même collection. Les points clés de la décision quant à l’utilisation de collections distinctes sont les suivants :

- Requêtes - Une collection est l'étendue pour l'exécution des requêtes. Si vous avez besoin d'interroger un ensemble de documents, la colocalisation des documents dans une collection unique donne les modèles de lecture les plus efficaces.
- Transactions - Une collection est le domaine de transaction des procédures stockées et des déclencheurs. Toutes les transactions sont limitées à une collection unique. 
- Performances – À une collection est associé un niveau de performances. Cela garantit que chaque collection a des performances prévisibles via des unités de demande réservées. Les données peuvent être affectées à différentes collections, avec des niveaux de performances différents, selon la fréquence des accès.

> [AZURE.IMPORTANT] Il est important de comprendre que vous serez facturé au tarif standard en fonction du nombre de collections créées par votre application.

Il est recommandé que votre application utilise un petit nombre de collections à moins que vos besoins en termes de stockage ou de débit ne soient importants. Vérifiez que vous avez bien compris les modèles d’application pour la création de collections. Vous pouvez choisir de réserver la création de la collection comme une action de gestion gérée en dehors de votre application. De même, l'ajustement du niveau de performances pour une collection modifiera le taux horaire de facturation de la collection. Vous devez surveiller les niveaux de performances d'une collection si votre application les ajuste de manière dynamique.

##Modification des niveaux de performances à l’aide du portail Azure

Le portail Azure est une option qui est mise à votre disposition lorsque vous gérez les niveaux de performances de vos collections. Pour modifier le niveau de performances d’une collection à partir du portail Azure Classic, procédez comme suit.

1. Accédez au [**portail Microsoft Azure**](https://portal.azure.com) depuis votre navigateur.
2. Dans la barre d’index de gauche, cliquez sur **Parcourir**.
3. Dans le hub **Parcourir**, cliquez sur **Comptes DocumentDB** sous l’étiquette **Filtrer par**.
4. Dans le panneau **Comptes DocumentDB**, cliquez sur le compte DocumentDB qui contient la collection souhaitée.
5. Faites défiler le panneau **Compte DocumentDB** vers le bas jusqu’au filtre **Bases de données**, puis cliquez sur la base de données qui contient la collection souhaitée. 
6. Faites défiler le panneau **Base de données** récemment ouvert vers le bas jusqu’au filtre **Collections**, puis sélectionnez la collection souhaitée.
7. Pour finir, dans le panneau **Collection**, recherchez la vignette **Niveau de tarification** dans le filtre **Utilisation**, puis cliquez dessus.
8. Dans le panneau **Choisir votre niveau de tarification**, cliquez sur le niveau de performances souhaité, puis sur **Sélectionner** en bas du panneau. 

>[AZURE.NOTE] La modification des niveaux de performances d’une collection peut prendre jusqu’à 2 minutes.

![Modification du niveau de tarification][1]

##Modification des niveaux de performances à l’aide du Kit de développement logiciel (SDK) .NET

Vous pouvez également modifier les niveaux de performances de vos collections via nos Kits de développement logiciel (SDK). Cette section couvre uniquement la modification du niveau de performances d’une collection à l’aide de notre [Kit de développement logiciel (SDK) .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx), mais le processus est similaire pour nos autres [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Si vous ne connaissez pas notre Kit de développement logiciel (SDK) .NET, visitez notre [didacticiel de mise en route](documentdb-get-started.md).

Voici un extrait de code pour modifier le type de l’offre :

	//Fetch the resource to be updated
	Offer offer = client.CreateOfferQuery()
	                          .Where(r => r.ResourceLink == "collection selfLink")    
	                          .AsEnumerable()
	                          .SingleOrDefault();
	                          
	//Change the user mode to All
	offer.OfferType = "S3";
	                    
	//Now persist these changes to the database by replacing the original resource
	Offer updated = await client.ReplaceOfferAsync(offer);

Visitez [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) pour afficher des exemples supplémentaires et en savoir plus sur nos méthodes d’offre :

- [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
- [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
- [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
- [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx) 

##Étapes suivantes

Pour en savoir plus sur la tarification et la gestion des données avec Azure DocumentDB, explorez les ressources suivantes :
 
- [Tarification de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
- [Gestion de la capacité de DocumentDB](documentdb-manage.md) 
- [Modélisation des données dans DocumentDB](documentdb-modeling-data.md)
- [Partitionnement des données dans DocumentDB](documentdb-partition-data.md)

Pour en savoir plus sur DocumentDB, consultez la [documentation](https://azure.microsoft.com/documentation/services/documentdb/) Azure DocumentDB.

[1]: ./media/documentdb-performance-levels/img1.png

<!------HONumber=AcomDC_0309_2016-->