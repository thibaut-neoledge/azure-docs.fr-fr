---
title: "Approvisionner le débit pour Azure DocumentDB | Microsoft Docs"
description: "Découvrez comment définir un débit approvisionné pour votre collection DocumentDB."
services: documentdb
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: abdf0af8a85db19c68a0d74c0477d798c0fd03fc
ms.openlocfilehash: 8ff2fc6106438e35b93112a6dc97814ba79b06fc
ms.lasthandoff: 02/22/2017


---

# <a name="set-throughput-for-azure-documentdb-collections"></a>Définir le débit des collections Azure DocumentDB

Vous pouvez définir le débit de vos collections DocumentDB dans le portail Azure ou à l’aide des Kits de développement logiciel (SDK) clients. 

Le tableau suivant répertorie les débits disponibles pour les collections :

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Collection à partition unique</strong></p></td>
            <td valign="top"><p><strong>Collection partitionnée</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débit minimal</p></td>
            <td valign="top"><p>400 unités de demande par seconde</p></td>
            <td valign="top"><p>2&500; unités de requête par seconde</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débit maximal</p></td>
            <td valign="top"><p>10&000; unités de demande par seconde</p></td>
            <td valign="top"><p>Illimité</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE] 
> Pour définir des collections partitionnées sur une valeur de débit comprise entre 2 500 unités de requête par seconde et 10 000 unités de requête par seconde, vous devez utiliser temporairement le portail Azure. Cette fonctionnalité n’est pas encore disponible dans les Kits de développement logiciel (SDK).

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>Pour définir le débit à l’aide du portail Azure

1. Dans une nouvelle fenêtre, ouvrez le [portail Azure](https://portal.azure.com).
2. Dans la barre de gauche, cliquez sur **NoSQL (DocumentDB)** ou sur **Plus de services** en bas, accédez à **Bases de données**, puis sélectionnez **NoSQL (DocumentDB)**.
3. Sélectionnez votre compte DocumentDB.
4. Dans la nouvelle fenêtre, sous **Collections**, cliquez sur **Mettre à l’échelle** comme indiqué dans la capture d’écran suivante.
5. Dans la nouvelle fenêtre, sélectionnez votre collection dans la liste déroulante, modifiez la valeur **Débit**, puis cliquez sur **Enregistrer**.

    ![Capture d’écran montrant comment modifier le débit d’une collection dans le portail Azure en accédant à votre compte et en cliquant sur Mettre à l’échelle](./media/documentdb-set-throughput/azure-documentdb-change-throughput-value.png)

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-net-sdk"></a>Pour définir le débit à l’aide du Kit de développement logiciel (SDK) .NET

```C#
//Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

//Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

## <a name="throughput-faq"></a>Forum Aux Questions sur le débit

**Puis-je définir mon débit sur une valeur inférieure à 400 unités de demande/s ?**

400 unités de demande/s est le débit minimal disponible sur les collections à partition DocumentDB unique (2500 unités de demande/s est la valeur minimale pour les collections partitionnées). Les unités de demande sont définies par intervalles de 100 unités de demande/s, mais le débit ne peut pas avoir la valeur 100 unités de demande/s ou toute valeur inférieure à 400 unités de demande/s. Si vous recherchez une méthode économique pour développer et tester DocumentDB, vous pouvez utiliser gratuitement [l’Émulateur DocumentDB](documentdb-nosql-local-emulator.md), que vous pouvez déployer localement sans frais. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’approvisionnement et la mise à l’échelle avec DocumentDB, voir [Partitionnement et mise à l’échelle dans Azure DocumentDB](documentdb-partition-data.md).

