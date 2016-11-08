---
title: Réplication de base de données mondiale dans DocumentDB | Microsoft Docs
description: Apprenez à gérer la réplication mondiale de votre compte DocumentDB via le portail Azure.
services: documentdb
keywords: base de données mondiale, réplication
documentationcenter: ''
author: mimig1
manager: jhubbard
editor: cgronlun

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: mimig

---
# Comment effectuer la réplication de base de données mondiale DocumentDB à l’aide du portail Azure
Découvrez comment utiliser le portail Azure afin de répliquer les données dans plusieurs régions pour une disponibilité mondiale des données dans Azure DocumentDB.

Pour plus d’informations sur le fonctionnement de la réplication de base de données mondiale dans DocumentDB, consultez la section [Distribution mondiale des données avec DocumentDB](documentdb-distribute-data-globally.md). Pour plus d’informations sur l’exécution de la réplication de base de données mondiale par programme, consultez la section [Développement avec des comptes DocumentDB à plusieurs régions](documentdb-developing-with-multiple-regions.md).

> [!NOTE]
> La distribution mondiale des bases de données DocumentDB est généralement disponible et activée automatiquement pour tous les comptes DocumentDB nouvellement créés. Nous travaillons à l’activation de la distribution mondiale sur tous les comptes existants, mais en attendant, si vous souhaitez que la distribution mondiale soit activée sur votre compte, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) et nous l’activerons pour vous.
> 
> 

## <a id="addregion"></a>Ajouter des régions de base de données mondiale
DocumentDB est disponible dans la plupart des [régions Azure][azureregions]. Après avoir sélectionné le niveau de cohérence par défaut pour votre compte de base de données, vous pouvez associer une ou plusieurs régions (en fonction de votre choix de niveau de cohérence par défaut et de vos besoins de distribution mondiale).

1. Dans la barre de lancement du [portail Azure](https://portal.azure.com/), cliquez sur **Comptes DocumentDB**.
2. Dans le panneau **Compte DocumentDB**, sélectionnez le compte de base de données à modifier.
3. Dans le panneau du compte, cliquez sur **Ajouter/Supprimer des régions** dans le menu.
4. Dans le panneau **Ajouter/Supprimer des régions**, sélectionnez les régions à ajouter ou supprimer, puis cliquez sur **OK**. L’ajout de régions est payant. Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/) ou l’article [Distribution mondiale des données avec DocumentDB](documentdb-distribute-data-globally.md) pour plus d’informations.
   
    ![Cliquez sur les régions dans la carte pour les ajouter ou les supprimer.][1]

### Sélection de régions de base de données mondiale
Lors de la configuration de deux régions ou plus, nous vous recommandons de sélectionner les régions en fonction des paires de régions décrites dans l’article [Continuité des activités et récupération d’urgence (BCDR) : régions jumelées d’Azure][bcdr].

En particulier, lors de la configuration de plusieurs régions, veillez à sélectionner le même nombre de régions (+/-1 pour pair/impair) à partir de chacune des colonnes de la région associée. Par exemple, si vous voulez déployer vers quatre régions des États-Unis, vous sélectionnez deux régions des États-Unis dans la colonne de gauche et deux autres dans la colonne de droite. La configuration suivante devrait donc être appropriée : États-Unis de l’Ouest, États-Unis de l’Est, Nord du centre des États-Unis et Sud du centre des États-Unis.

Il est important de suivre ces instructions lorsque deux régions seulement sont configurées pour les scénarios de récupération d’urgence. Lorsqu’il y a plus de deux régions, le suivi de ces instructions est une bonne pratique mais n’est pas essentiel dans la mesure où certaines des régions sélectionnées respectent cette association.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **DocumentDB Account** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a id="next"></a>Étapes suivantes
Découvrez comment gérer la cohérence de votre compte répliqué à l’échelle mondiale en lisant la section [Niveaux de cohérence dans DocumentDB](documentdb-consistency-levels.md).

Pour plus d’informations sur le fonctionnement de la réplication de base de données mondiale dans DocumentDB, consultez la section [Distribution mondiale des données avec DocumentDB](documentdb-distribute-data-globally.md). Pour plus d’informations sur la réplication des données par programme dans plusieurs régions, consultez la section [Développement avec des comptes DocumentDB à plusieurs régions](documentdb-developing-with-multiple-regions.md).

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/documentdb/

<!---HONumber=AcomDC_0831_2016-->