---
title: "Niveaux de performances dans DocumentDB | Microsoft Docs"
description: "Découvrez comment les niveaux de performances dans DocumentDB permettent de réserver le débit pour chaque collection."
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: ed44ca2076860128b175888748cdaa8794c2310d
ms.openlocfilehash: 237a92713ee8dca72a09550c47519189f2fd23cc


---
# <a name="performance-levels-and-pricing-tiers-in-documentdb"></a>Niveaux de performances et niveaux tarifaires dans DocumentDB
Cet article fournit une vue d’ensemble des niveaux de performances dans [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :  

* Qu'est-ce qu'un niveau de performances ?
* Comment le débit est-il réservé pour un compte de base de données ?
* Comment puis-je utiliser les niveaux de performances ?
* Quel est le mode de facturation des niveaux de performances ?

## <a name="introduction-to-performance-levels"></a>Introduction aux niveaux de performances
Chaque collection DocumentDB créée dans un compte DocumentDB standard est configurée avec un niveau de performances associé. Chaque collection dans une base de données peut avoir un niveau de performances différent, ce qui vous permet de désigner davantage de débit pour les collections fréquemment sollicitées et moins de débit pour les collections rarement sollicitées. 

Comme présenté dans le tableau suivant, DocumentDB prend en charge les niveaux de performances **définis par l’utilisateur** et les niveaux de performances **prédéfinis**.  Les performances définies par l’utilisateur vous permettent de réserver un débit par unités de 100 UR/s et de disposer d’un stockage illimité, tandis que les trois niveaux de performances prédéfinis présentent des options de débit spécifiques et un quota de stockage de 10 Go. Le tableau suivant compare les performances **définies par l’utilisateur** et les performances **prédéfinies** .

|Type de performance|Détails|Débit|Storage|Version|API|
|----------------|-------|----------|-------|-------|----|
|Performances définies par l’utilisateur|L’utilisateur définit le débit en unités de 100 UR/s|Illimité|Illimité|V2|API 2015-12-16 et versions ultérieures|
|Performances prédéfinies|10 Go de stockage réservé.<br><br>S1 = 250 UR/s<br>S2 = 1 000 UR/s<br>S3 = 2 500 UR/s|2 500 unités de demande/s|10 Go|V1|Quelconque|

Le débit est réservé par collection, et son utilisation est disponible exclusivement pour cette collection. Le débit est mesuré en [UR (unités de requête)](documentdb-request-units.md), identifiant la quantité de ressources requises pour effectuer diverses opérations de base de données DocumentDB.

> [!NOTE]
> Le niveau de performances d’une collection peut être ajusté à l’aide des [Kits de développement logiciel (SDK)](documentdb-sdk-dotnet.md) ou du [Portail Azure](https://portal.azure.com/). Les modifications au niveau des performances sont censées se terminer en 3 minutes.
> 
> 

## <a name="setting-performance-levels-for-collections"></a>Définition des niveaux de performances pour les collections
Lorsqu'une collection est créée, l'affectation complète des unités de demande est réservée pour la collection, en fonction du niveau de performances désigné.

Notez qu’avec les niveaux de performances prédéfinis et définis par l’utilisateur, DocumentDB fonctionne selon la réservation de débit. En créant une collection, une application a réservé et est facturée pour le débit réservé, quelle que soit la quantité de débit utilisée activement. Avec les niveaux de performances définis par l’utilisateur, le stockage est mesuré en fonction de la consommation, mais avec les niveaux de performances prédéfinis, 10 Go de stockage est réservé au moment de la création de la collection.  

Après la création de collections, vous pouvez modifier le niveau de performances et/ou le débit à l’aide des [Kits de développement logiciel (SDK)](documentdb-sdk-dotnet.md) ou du [Portail Azure](https://portal.azure.com/).

> [!IMPORTANT]
> Les collections DocumentDB standard sont facturées au tarif horaire et chaque collection que vous créez sera facturée pour une heure d'utilisation au minimum.
> 
> 

Si vous ajustez le niveau de performances d'une collection pendant une période d'une heure, vous serez facturé pour le plus haut niveau de performances défini pendant cette période. Par exemple, si vous augmentez votre niveau de performances pour une collection à 8 h 53, vous serez facturé pour le nouveau niveau à partir de 8 h 00. De même, si vous diminuez le niveau de performances à 8 h 53, le nouveau taux s'appliquera à partir de 9 h 00.

Les unités de demande sont réservées pour chaque collection sur la base du niveau de performances défini. La consommation d'unités de demande est évaluée sous la forme d'un taux par seconde. Les applications qui dépassent le taux d'unités de demande (ou le niveau de performances) configuré sur une collection sont limitées jusqu'à ce que le taux tombe sous le niveau réservé pour cette collection. Si votre application requiert un niveau de débit plus élevé, vous pouvez augmenter le niveau de performances pour chaque collection.

> [!NOTE]
> Lorsque votre application dépasse les niveaux de performances d'une ou plusieurs collections, les demandes sont limitées en fonction de chaque collection. Cela signifie que certaines demandes d'application peuvent réussir tandis que d'autres peuvent être limitées. Il est recommandé d’ajouter un petit nombre de nouvelles tentatives en cas de limitation, pour gérer les hausses du trafic des demandes.
> 
> 

## <a name="working-with-performance-levels"></a>Utilisation des niveaux de performances
Les collections DocumentDB vous permettent de regrouper vos données selon les modèles de requête et les besoins de performances de votre application. Avec l'indexation automatique et la prise en charge des requêtes dans DocumentDB, il est assez courant colocaliser des documents hétérogènes au sein de la même collection. Les points clés de la décision quant à l’utilisation de collections distinctes sont les suivants :

* Requêtes - Une collection est l'étendue pour l'exécution des requêtes. Si vous avez besoin d'interroger un ensemble de documents, la colocalisation des documents dans une collection unique donne les modèles de lecture les plus efficaces.
* Transactions : toutes les transactions sont limitées à une collection unique. Si vous avez des documents qui doivent être mis à jour au sein d’une seule procédure stockée ou d’un déclencheur, ils doivent être stockés dans la même collection. Plus précisément, une clé de partition dans une collection constitue la limite de transaction. Consultez [Partitionnement dans DocumentDB](documentdb-partition-data.md) pour plus de détails.
* Isolation des performances : à une collection est associé un niveau de performances. Cela garantit que chaque collection a des performances prévisibles via des unités de demande réservées. Les données peuvent être affectées à différentes collections, avec des niveaux de performances différents, selon la fréquence des accès.

> [!IMPORTANT]
> Il est important de comprendre que vous serez facturé au tarif standard en fonction du nombre de collections créées par votre application.
> 
> 

Il est recommandé que votre application utilise un petit nombre de collections à moins que vos besoins en termes de stockage ou de débit ne soient importants. Vérifiez que vous avez bien compris les modèles d’application pour la création de collections. Vous pouvez choisir de réserver la création de la collection comme une action de gestion gérée en dehors de votre application. De même, l'ajustement du niveau de performances pour une collection modifiera le taux horaire de facturation de la collection. Vous devez surveiller les niveaux de performances d'une collection si votre application les ajuste de manière dynamique.

## <a name="a-idchanging-performance-levels-using-the-azure-portalachange-from-s1-s2-s3-to-user-defined-performance"></a><a id="changing-performance-levels-using-the-azure-portal"></a>Remplacer S1, S2, S3 par les performances définies par l’utilisateur
Procédez comme suit pour passer de niveaux de débit prédéfinis à des niveaux de débit définis par l’utilisateur dans le portail Azure. Les niveaux de débit définis par l’utilisateur vous permettent d’adapter le débit à vos besoins. Et si vous utilisez encore un compte S1, vous pouvez augmenter le débit par défaut de 250 à 400 unités de requête/s en seulement quelques clics. Notez qu’après avoir déplacé une collection du niveau S1, S2 ou S3 vers le niveau Standard (défini par l’utilisateur), vous ne pourrez plus revenir à un niveau S1, S2 ou S3. Vous pouvez toutefois modifier le débit d’une collection Standard à tout moment.

Pour plus d’informations sur la modification de la tarification pour les débits définis par l’utilisateur ou prédéfinis, consultez l’article de blog [DocumentDB : tout ce que vous devez savoir sur l’utilisation des nouvelles options de tarification](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/).

> [!VIDEO https://channel9.msdn.com/Blogs/AzureDocumentDB/ChangeDocumentDBCollectionPerformance/player]
> 
> 

1. Dans le [**Portail Azure**](https://portal.azure.com), cliquez sur **NoSQL (DocumentDB)**, puis sélectionnez le compte DocumentDB à modifier. 
 
    Si **NoSQL (DocumentDB)** ne figure pas dans la barre de lancement, cliquez sur >, accédez à **Bases de données**, sélectionnez **NoSQL (DocumentDB)**, puis sélectionnez le compte DocumentDB.  

2. Dans le menu de la ressource, sous **Collections**, cliquez sur **Mettre à l’échelle**, sélectionnez la collection à modifier dans la liste déroulante, puis cliquez sur **Niveau tarifaire**. Les comptes utilisant un débit prédéfini ont un niveau tarifaire de S1, S2 ou S3.  Dans le panneau **Choisir votre niveau tarifaire**, cliquez sur **Standard** pour basculer vers le débit défini par l’utilisateur, puis cliquez sur **Sélectionner** pour enregistrer vos modifications.

    ![Capture d’écran du panneau Paramètres montrant où modifier la valeur de débit](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

3. De retour dans le panneau **Mettre à l’échelle**, le **Niveau tarifaire** est défini sur **Standard** et la zone **Débit (UR/s)** est affichée avec une valeur par défaut de 400. Définissez le débit entre 400 et 10 000 [unités de requête](documentdb-request-units.md)/seconde (unités de requête/s). **L’Estimation de la facture mensuelle** au bas de la page se met à jour automatiquement pour donner une estimation du coût mensuel. Cliquez sur **Enregistrer** pour enregistrer vos modifications.

    Si vous déterminez que vous avez besoin d’un débit supérieur (plus de 10 000 unités de requête/s) ou d’une plus grande capacité de stockage (plus de 10 Go), vous pouvez créer une collection partitionnée. Pour créer une collection partitionnée, consultez [Créer une collection](documentdb-create-collection.md).

> [!NOTE]
> La modification des niveaux de performances d’une collection peut prendre jusqu’à 2 minutes.
> 
> 

## <a name="changing-performance-levels-using-the-net-sdk"></a>Modification des niveaux de performances à l’aide du Kit SDK .NET
Vous pouvez également modifier les niveaux de performances de vos collections via nos Kits SDK. Cette section couvre uniquement la modification du niveau de performances d’une collection à l’aide de notre [Kit de développement logiciel (SDK) .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx), mais le processus est similaire pour nos autres [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Si vous ne connaissez pas notre Kit de développement logiciel (SDK) .NET, visitez notre [didacticiel de mise en route](documentdb-get-started.md).

Voici un extrait de code pour modifier le débit de l’offre sur 50 000 unités de demande par seconde :

    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);

    // Set the throughput to S2
    offer = new Offer(offer);
    offer.OfferType = "S2";

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);



> [!NOTE]
> Les collections dotées de moins de 10 000 unités de demande par seconde peuvent être migrées entre les offres avec un débit défini par l’utilisateur et un débit prédéfini (S1, S2, S3) à tout moment. Les collections dotées de plus de 10 000 unités de demande par seconde ne peuvent pas être converties en niveaux de débit prédéfinis.
> 
> 

Visitez [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) pour afficher des exemples supplémentaires et en savoir plus sur nos méthodes d’offre :

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

## <a name="a-idchange-throughputachanging-the-throughput-of-a-collection"></a><a id="change-throughput"></a>Modification du débit d’une collection
Si vous utilisez déjà les performances définies par l’utilisateur, vous pouvez modifier le débit de votre collection en procédant comme suit. Si vous avez besoin de passer d’un niveau de performance S1, S2 ou S3 (performances prédéfinies) aux performances définies par l’utilisateur, consultez [Remplacer S1, S2, S3 par les performances définies par l’utilisateur](#changing-performance-levels-using-the-azure-portal).

1. Dans le [**Portail Azure**](https://portal.azure.com), cliquez sur **NoSQL (DocumentDB)**, puis sélectionnez le compte DocumentDB à modifier.    
2. Dans le menu de la ressource, sous **Collections**, cliquez sur **Mettre à l’échelle**, puis sélectionnez la collection à modifier dans la liste déroulante.
3. Dans la zone **Débit (UR/s)**, tapez le nouveau niveau de débit. 
   
    **L’Estimation de la facture mensuelle** au bas de la page se met à jour automatiquement pour donner une estimation du coût mensuel. Cliquez sur **Enregistrer** pour enregistrer vos modifications.

    Si vous n’êtes pas sûr de l’augmentation à appliquer au débit, consultez [Estimation des besoins de débit](documentdb-request-units.md#estimating-throughput-needs) et [Calculatrice d’unités de requête](https://www.documentdb.com/capacityplanner).

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous ne voyez pas l’option permettant de modifier le niveau de performances et de choisir le niveau S1, S2 ou S3 dans le panneau **Choisir votre niveau tarifaire**, cliquez sur **Afficher tout** pour afficher les niveaux de performances Standard, S1, S2 et S3. Si vous utilisez le niveau tarifaire Standard, vous ne pouvez pas modifier le niveau entre S1, S2 et S3.

![Capture d’écran du panneau Choisir votre niveau tarifaire avec l’option Afficher tout en surbrillance](./media/documentdb-performance-levels/azure-documentdb-database-view-all-performance-levels.png)

Après avoir transféré une collection du niveau S1, S2 ou S3 vers le niveau Standard, vous ne pouvez plus revenir au niveau S1, S2 ou S3.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la tarification et la gestion des données avec Azure DocumentDB, explorez les ressources suivantes :

* [Tarification de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
* [Modélisation des données dans DocumentDB](documentdb-modeling-data.md)
* [Partitionnement des données dans DocumentDB](documentdb-partition-data.md)
* [Unités de demande](http://go.microsoft.com/fwlink/?LinkId=735027)

Pour en savoir plus sur DocumentDB, consultez la [documentation](https://azure.microsoft.com/documentation/services/documentdb/)Azure DocumentDB.

Pour commencer avec le test des performances et de la mise à l’échelle avec DocumentDB, consultez [Test des performances et de la mise à l’échelle avec Azure DocumentDB](documentdb-performance-testing.md).

[1]: ./media/documentdb-performance-levels/documentdb-change-collection-performance7-9.png
[2]: ./media/documentdb-performance-levels/documentdb-change-collection-performance10-11.png



<!--HONumber=Jan17_HO2-->


