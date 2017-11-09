---
title: Prise en main de Power BI Workspace Collections
description: "Power BI Workspace Collections est un service Azure qui permet aux développeurs d’applications d’ajouter des rapports Power BI interactifs dans leurs propres applications."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: asaxton
ms.openlocfilehash: 4ee113ed25142507f381d8c9d49b25ee6553c525
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-microsoft-power-bi-workspace-collections"></a>Prise en main de Power BI Workspace Collections

**Power BI Workspace Collections** est un service Azure qui permet aux développeurs d’applications d’ajouter des rapports Power BI interactifs dans leurs propres applications. **Power BI Workspace Collections** fonctionne avec les applications existantes sans avoir besoin de repenser ou de modifier le mode de connexion des utilisateurs.

> [!IMPORTANT]
> Power BI Workspace Collections est déconseillée et disponible jusqu’en juin 2018, ou jusqu’à la date indiquée sur votre contrat. Nous vous conseillons de planifier votre migration vers Power BI Embedded pour éviter toute interruption dans votre application. Pour en savoir plus sur la migration de vos données vers Power BI Embedded, voir [How to migrate Power BI Workspace Collections content to Power BI Embedded (Guide pratique pour migrer le contenu de Power BI Workspace Collections vers Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Les ressources pour **Power BI Workspace Collections** sont approvisionnées via les [API Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). Dans ce cas, la ressource approvisionnée constitue une **collection d’espaces de travail Power BI**.

![Flux général de Power BI Workspace Collections](media/get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Création d’une collection d’espaces de travail

Une **collection d’espaces de travail** est la ressource Azure de premier niveau, qui contient les données intégrées à votre application. Pour créer une **collection d’espaces de travail** , deux possibilités s’offrent à vous :

* Création manuelle à l’aide du portail Azure
* Création programmatique à l’aide des API Azure Resource Manager

Nous allons voir comment créer une **collection d’espaces de travail** à l’aide du portail Azure.

1. Ouvrez le **portail Azure** à l’adresse [http://portal.azure.com](http://portal.azure.com)et connectez-vous.
2. Sélectionnez **+ Nouveau** dans le panneau supérieur.
   
   ![+ Nouveau dans le portail Azure](media/get-started/create-workspace-1.png)
3. Sous **Data + Analytics** (Données + Analyses), sélectionnez **Power BI Workspace Collections**.
4. Si vous disposez déjà d’un abonnement Power BI Workspace Collections, sélectionnez **Créer une collection d’espaces de travail** au bas du message de démarrage.

5. Dans **Collection d’espaces de travail**, entrez les informations requises.
   
   ![Création d’une collection d’espaces de travail](media/get-started/create-workspace-2.png)
1. Sélectionnez **Créer**.

L’approvisionnement de la **collection d’espaces de travail** prend quelques instants. Une fois qu’il est terminé, la page **Collection d’espaces de travail**s’affiche.

   ![Espace de travail dans le portail Azure](media/get-started/create-workspace-3.png)

Les résultats de la **Création** contiennent les informations nécessaires pour appeler les API qui créent des espaces de travail et y déploient des contenus.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Affichage des touches d’accès rapide aux API de Power BI

Les **touches d’accès rapide**sont l’un des éléments d’informations les plus importants pour appeler les API REST de Power BI. Elles sont utilisées pour générer les **jetons d’application** qui servent à authentifier les demandes d’API. Pour afficher vos **touches d’accès rapide**, cliquez sur **Touches d’accès rapide** dans **Paramètres**. Pour en savoir plus sur les **jetons d’application**, voir [Authentification et autorisation avec Power BI Workspace Collections](app-token-flow.md).

   ![Touches d’accès rapide dans les paramètres d’une collection d’espace de travail dans le portail Azure](media/get-started/access-keys.png)

Vous allez constater que vous disposez de deux touches.

   ![Deux touches dans Touches d’accès rapide](media/get-started/access-keys-2.png)

Copiez-les et stockez-les de manière sécurisée dans votre application. Il est important de considérer ces touches d’accès rapide comme un mot de passe, car elles permettent d’accéder à l’ensemble du contenu de votre **collection d’espaces de travail**.

Même si deux touches sont répertoriées, une seule est nécessaire à un moment donné. La seconde vous est fournie pour regénérer régulièrement les touches sans interrompre l'accès au service.

Maintenant que vous disposez d’une instance de Power BI pour votre application, ainsi que des **touches d’accès rapide**, vous pouvez importer un rapport dans votre propre application. Avant que vous appreniez à importer un rapport, la section suivante décrit comment créer des rapports et jeux de données Power BI à intégrer dans une application.

## <a name="working-with-workspaces"></a>Utilisation des espaces de travail

Une fois votre collection d’espaces de travail créée, vous devrez créer un espace de travail qui hébergera vos rapports et vos jeux de données. Pour créer un espace de travail, vous devez utiliser [l’API REST Post Workspace](https://msdn.microsoft.com/library/azure/mt711503.aspx).

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Création de rapports et de jeux de données Power BI à intégrer dans une application avec Power BI Desktop

Maintenant que vous avez créé une instance de Power BI pour votre application et que vous avez des **touches d’accès rapide**, vous devez créer les rapports et les jeux de données Power BI à intégrer. Vous pouvez créer des rapports et des jeux de données à l’aide de **Power BI Desktop**. Vous pouvez télécharger [Power BI Desktop gratuitement](https://go.microsoft.com/fwlink/?LinkId=521662). Ou, pour une prise en main rapide, vous pouvez télécharger [l’exemple PBIX Analyse des données de vente](http://go.microsoft.com/fwlink/?LinkID=780547).

> [!NOTE]
> Pour en savoir plus sur l’utilisation de **Power BI Desktop**, consultez la section [Prise en main de Power BI Desktop](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Avec **Power BI Desktop**, vous vous connectez à votre source de données en important une copie des données dans **Power BI Desktop** ou directement à l’aide de **DirectQuery**.

Voici les différences entre l’**importation** et l’utilisation de **DirectQuery**.

| Importer | DirectQuery |
| --- | --- |
| Les tables, colonnes *et données* sont importées ou copiées dans **Power BI Desktop**. Comme pour les visualisations, **Power BI Desktop** interroge une copie des données. Pour voir les modifications apportées aux données sous-jacentes, vous devez actualiser ou réimporter un jeu de données à jour et complet. |Seules les *tables et colonnes* sont importées ou copiées dans **Power BI Desktop**. Comme pour les visualisations, **Power BI Desktop** interroge la source de données sous-jacente, ce qui signifie que vous visualisez toujours des données à jour. |

Pour plus d’informations sur la connexion à une source de données, consultez l’article [Se connecter à une source de données](connect-datasource.md).

Lorsque vous enregistrez votre travail dans **Power BI Desktop**, un fichier PBIX est créé. Ce fichier contient votre rapport. En outre, si vous importez des données, le PBIX contient le jeu de données complet. Si vous utilisez **DirectQuery**, le PBIX ne contient qu’un schéma du jeu de données. Par le biais d’un programme, vous déployez le PBIX dans votre espace de travail à l’aide de [l’API Import de Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [!NOTE]
> **Power BI Workspace Collections** contient d’autres API pour modifier le serveur et la base de données vers lesquels pointe votre jeu de données, et définir les informations d’identification du compte de service utilisé par le jeu de données pour se connecter à votre base de données. Consultez les pages [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) et [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>Création de rapports et de jeux de données Power BI avec des API

### <a name="datasets"></a>JEUX DE DONNÉES

Vous pouvez créer des jeux de données dans Power BI Workspace Collections avec l’API REST. Vous pouvez ensuite transmettre les données dans votre jeu de données. Cela vous permet d’utiliser des données sans avoir besoin de Power BI Desktop. Pour plus d’informations, consultez [Post Datasets](https://msdn.microsoft.com/library/azure/mt778875.aspx) (Opération Post Datasets).

### <a name="reports"></a>Rapports

Vous pouvez créer un rapport directement à partir d’un jeu de données de votre application avec l’API JavaScript. Pour plus d’informations, consultez [Create a new report from a dataset in Power BI Workspace Collections](create-report-from-dataset.md) (Créer un rapport à partir d’un jeu de données dans Power BI Workspace Collections).

## <a name="see-also"></a>Voir aussi

[Prise en main de l’exemple](get-started-sample.md)  
[Authentification et autorisation dans Power BI Workspace Collections](app-token-flow.md)  
[Incorporer un rapport](embed-report.md)  
[Créer un rapport à partir d’un jeu de données dans Power BI Workspace Collections](create-report-from-dataset.md)
[Enregistrer les rapports](save-reports.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemple d’incorporation JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Des questions ? [Essayer la communauté Power BI](http://community.powerbi.com/)

