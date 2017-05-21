---
title: "Découvrir Data Lake Analytics et U-SQL à l’aide des didacticiels interactifs du Portail Azure | Microsoft Docs"
description: "Démarrage rapide avec Data Lake Analytics et U-SQL. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 31399d47-e937-4c43-ab0a-6aea8875378d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: ca88b355416b4ae4785bc69244185a67756ca916
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---
# <a name="use-azure-data-lake-analytics-interactive-tutorials"></a>Utiliser les didacticiels interactifs Azure Data Lake Analytics
Le Portail Azure fournit un didacticiel interactif pour vous permettre de vous familiariser avec Data Lake Analytics. Cet article vous indique comment parcourir le didacticiel pour l’analyse des journaux de sites web.

Pour les autres didacticiels, consultez :

* [Prise en main de Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
* [Prise en main de Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Prise en main de Data Lake Analytics à l'aide du Kit de développement logiciel (SDK) .NET](data-lake-analytics-get-started-net-sdk.md)
* [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) 

**Configuration requise**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un compte Analytique Data Lake**.  Consultez l’article [Prise en main d’Azure Data Lake Analytics à l’aide du Portail Azure](data-lake-analytics-get-started-portal.md).

## <a name="create-data-lake-analytics-account"></a>Créer un compte Analytique Data Lake
Vous devez disposer d’un compte Analytique Data Lake avant de pouvoir exécuter un travail quelconque.

Chaque compte Data Lake Analytics possède une dépendance de compte [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) : le compte Data Lake Store par défaut.  Dans ce didacticiel, vous allez créer le compte Data Lake Store par défaut avec le compte Analytics, mais vous pouvez également le créer au préalable.

**Pour créer un compte Analytique Data Lake**

1. Connectez-vous au [portail Azure](https://portal.azure.com/signin/index/?Microsoft_Azure_Kona=true&Microsoft_Azure_DataLake=true&hubsExtension_ItemHideKey=AzureDataLake_BigStorage%2cAzureKona_BigCompute).
2. Cliquez sur **Microsoft Azure** dans le coin supérieur gauche pour ouvrir le Tableau d'accueil.
3. Cliquez sur la vignette **Marketplace** .  
4. Tapez **Azure Data Lake Analytics** dans la zone de recherche du volet **Tout**, puis appuyez sur **ENTRÉE**. Vous devez voir **Azure Data Lake Analytics** dans la liste.
5. Cliquez sur **Azure Data Lake Analytics** dans la liste.
6. Cliquez sur **Créer** figurant en bas du volet.
7. Tapez ou sélectionnez :
   
    ![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)
   
   * **Nom**: nom du compte Analytics.
   * **Data Lake Store**: chaque compte Data Lake Analytics possède un compte Data Lake Store dépendant. Le compte Data Lake Analytics et le compte Data Lake Store dépendant doivent se trouver dans le même centre de données Azure. Suivez les instructions pour créer un compte Data Lake Store, ou sélectionnez un compte existant.
   * **Abonnement**: choisissez l’abonnement Azure utilisé pour le compte Analytics.
   * **Groupe de ressources**. Sélectionnez un groupe de ressources Azure existant ou créez-en un. Les applications sont généralement constituées de nombreux composants, par exemple une application web, une base de données, un serveur de base de données, un stockage et des services tiers. Azure Resource Manager (ARM) vous permet de manipuler les ressources de votre application sous la forme d’un groupe, nommé groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer les ressources de votre application dans le cadre d’une seule opération coordonnée. Vous utilisez un modèle de déploiement pouvant fonctionner dans différents environnements (environnements de test, intermédiaire et de production). Vous pouvez clarifier la facturation pour votre organisation en visualisant les coûts cumulés pour l’ensemble du groupe. Pour plus d'informations, consultez [Présentation d'Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 
   * **Emplacement**. Sélectionnez un centre de données Azure pour le compte Data Lake Analytics. 
8. Sélectionnez **Épingler au Tableau d'accueil**. Cela est nécessaire pour ce didacticiel.
9. Cliquez sur **Create**. Le Tableau d'accueil du portail s'affiche. Une nouvelle vignette, intitulée « Déploiement d’Azure Data Lake Analytics », est ajoutée à la page d’accueil. La création d'un compte Data Lake Analytics prend plusieurs minutes. Lorsque le compte est créé, le portail ouvre le compte dans un nouveau volet.
   
    ![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

## <a name="run-website-log-analysis-interactive-tutorial"></a>Exécuter le didacticiel interactif d'analyse des journaux des sites web
**Pour ouvrir le didacticiel interactif d'analyse des journaux des sites web**

1. Dans le portail, cliquez sur **Microsoft Azure** dans le menu de gauche pour ouvrir le Tableau d'accueil.
2. Cliquez sur la vignette liée à votre compte Data Lake Analytics.
3. Cliquez sur **Explorer les didacticiels interactifs** à partir de la barre **Essentials**.
   
    ![Didacticiels interactifs Data Lake Analytics](./media/data-lake-analytics-use-interactive-tutorials/data-lake-analytics-explore-interactive-tutorials.png)
4. Si vous voyez un avertissement orange indiquant « Exemples non configurés, cliquez sur... », cliquez sur **Copier les exemples de données** pour copier les exemples de données vers le compte Data Lake Store par défaut. Le didacticiel interactif a besoin des données pour s'exécuter.
5. À partir du volet **Didacticiels interactifs**, cliquez sur **Analyse des journaux des sites web**. Le portail ouvre le didacticiel dans un nouveau volet de portail.
6. Cliquez sur **Introduction**, puis suivez les instructions.

## <a name="see-also"></a>Voir aussi
* [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Prise en main de Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
* [Prise en main de Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Analyser les journaux des sites web à l'aide d'Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)


