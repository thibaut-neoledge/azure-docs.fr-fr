---
title: "Migration entre les régions Azure Data Lake Store | Microsoft Docs"
description: "Découvrez la migration entre les régions pour Azure Data Lake Store."
services: data-lake-store
documentationcenter: 
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/27/2017
ms.author: stewu
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7292e1954418186d8820529c54d4d781c79e1ae4
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="migrate-data-lake-store-across-regions"></a>Migration Data Lake Store entre les régions

La disponibilité d’Azure Data Lake Store s’étendant à davantage de régions, vous pouvez décider d’effectuer une migration à usage unique pour tirer parti d’une nouvelle région. Découvrez ce que vous devez prendre en compte lorsque vous planifiez et effectuez la migration.

## <a name="prerequisites"></a>Composants requis

* **Un abonnement Azure**. Pour plus d’informations, consultez [Créer votre compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/).
* **Un compte Data Lake Store dans deux régions différentes**. Pour plus d’informations, consultez [Prise en main d’Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Pour plus d’informations, consultez [Présentation d’Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Considérations relatives à la migration

Tout d’abord, identifiez la stratégie de migration appropriée pour votre application qui écrit, lit ou traite des données dans Data Lake Store. Lorsque vous choisissez une stratégie, tenez compte des exigences de disponibilité de votre application et du temps d’arrêt qui se produit lors d’une migration. Par exemple, votre approche la plus simple peut être d’utiliser le modèle de migration dans le cloud de type « lift-and-shift ». Dans cette approche, vous suspendez l’application dans votre région existante et toutes vos données sont copiées dans la nouvelle région. Une fois le processus de copie terminé, vous reprenez votre application dans la nouvelle région, puis supprimez l’ancien compte Data Lake Store. La migration nécessite un temps d’arrêt.

Pour réduire ce temps d’arrêt, vous pouvez immédiatement commencer à ingérer les nouvelles données dans la nouvelle région. Une fois que vous avez les données minimales requises, exécutez votre application dans la nouvelle région. En arrière-plan, continuez à copier les données plus anciennes du compte Data Lake Store existant vers le nouveau compte Data Lake Store dans la nouvelle région. Cette approche vous permet de passer à la nouvelle région avec un temps d’arrêt minimal. Une fois que toutes les anciennes données ont été copiées, supprimez l’ancien compte Data Lake Store.

D’autres informations importantes à prendre en compte lorsque vous planifiez votre migration sont :

* **Volume de données**. Le volume de données (nombre de gigaoctets, nombre de fichiers et dossiers, etc.) a un impact sur le temps et les ressources nécessaires à la migration.

* **Nom du compte Data Lake Store**. Le nouveau nom de compte dans la nouvelle région doit être globalement unique. Par exemple, le nom de votre ancien compte Data Lake Store dans la région États-Unis de l’Est 2 peut être contosoeastus2.azuredatalakestore.net. Vous pouvez nommer votre nouveau compte Data Lake Store dans la région Europe du Nord contosonortheu.azuredatalakestore.net.

* **Outils**. Nous vous recommandons d’utiliser l’[activité de copie Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) pour copier les fichiers Data Lake Store. Data Factory prend en charge le déplacement de données avec une fiabilité optimale et de hautes performances. N’oubliez pas que Data Factory copie uniquement l’arborescence de dossiers et le contenu des fichiers. Vous devez appliquer manuellement les listes de contrôle d’accès que vous avez utilisées dans l’ancien compte vers le nouveau compte. Pour plus d’informations, y compris sur les objectifs de performance pour les scénarios optimistes, consultez le [Guide sur les performances et le réglage de l’activité de copie](../data-factory/copy-activity-performance.md). Si vous souhaitez copier les données plus rapidement, vous devrez peut-être utiliser des unités de déplacement des données dans le cloud supplémentaires. D’autres outils, tels que AdlCopy, ne permettent pas de copier de données entre différentes régions.  

* **Frais liés à la bande passante**. Des [frais liés à la bande passante](https://azure.microsoft.com/en-us/pricing/details/bandwidth/) s’appliquent, car les données sont transférées en dehors d’une région Azure.

* **Listes de contrôle d’accès sur vos données**. Sécurisez vos données dans la nouvelle région en appliquant des listes de contrôle d’accès aux fichiers et dossiers. Pour plus d’informations, consultez [Sécurisation des données stockées dans Azure Data Lake Store](data-lake-store-secure-data.md). Nous vous recommandons de profiter de la migration pour mettre à jour et ajuster vos listes de contrôle d’accès. Vous pouvez utiliser des paramètres similaires à vos paramètres actuels. Vous pouvez afficher les listes de contrôle d’accès dans n’importe quel fichier à l’aide du portail Azure, des [applets de commande PowerShell](/powershell/module/azurerm.datalakestore/get-azurermdatalakestoreitempermission) ou des Kits de développement logiciel (SDK).  

* **Emplacement des services d’analyse**. Pour des performances optimales, vos services d’analyse, tels qu’Azure Data Lake Analytics ou Azure HDInsight, doivent se situer dans la même région que vos données.  

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Data Lake Store](data-lake-store-overview.md)

