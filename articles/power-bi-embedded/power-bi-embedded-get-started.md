---
title: Prise en main de Microsoft Power BI Embedded
description: Power BI incorporée, ajoutez des rapports interactifs Power BI dans votre application business intelligence
services: power-bi-embedded
documentationcenter: ''
author: guyinacube
manager: erikre
editor: ''
tags: ''

ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton

---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Prise en main de Microsoft Power BI Embedded
**Power BI Embedded** est un service Azure qui permet aux développeurs d’applications d’ajouter des rapports Power BI interactifs dans leurs propres applications. **Power BI Embedded** fonctionne avec les applications existantes sans avoir besoin de repenser ou de modifier le mode de connexion des utilisateurs.

Les ressources de **Microsoft Power BI Embedded** sont approvisionnées via les [API Azure ARM](https://msdn.microsoft.com/library/mt712306.aspx). Dans ce cas, la ressource que vous approvisionnez constitue une **collection d’espaces de travail Power BI**.

![](media\\power-bi-embedded-get-started\\introduction.png)

## <a name="create-a-workspace-collection"></a>Création d’une collection d’espaces de travail
Une **collection d’espaces de travail** est la ressource Azure de premier niveau, qui contient les données intégrées à votre application. Pour créer une **collection d’espaces de travail** , deux possibilités s’offrent à vous :

* Création manuelle à l’aide du portail Azure
* Création programmatique à l’aide des API ARM (Azure Resource Manager)

Nous allons voir comment créer une **collection d’espaces de travail** à l’aide du portail Azure.

1. Ouvrez le **portail Azure**à l’adresse [http://portal.azure.com](http://portal.azure.com)et connectez-vous-y.
2. Cliquez sur **+ Nouveau** dans le panneau supérieur.
   
   ![](media\\power-bi-embedded-get-started\\create-workspace-1.png)
3. Sous **Données + analyse**, cliquez sur **Power BI Embedded**.
4. Dans le **panneau Création**, entrez les informations requises. Pour connaître la **tarification**, consultez la page [Tarification de Power BI Embedded](http://go.microsoft.com/fwlink/?LinkID=760527).
   
   ![](media\\power-bi-embedded-get-started\\create-workspace-2.png)
5. Cliquez sur **Create**.

L’approvisionnement de la **collection d’espaces de travail** prend quelques instants. Une fois qu’il est terminé, le **panneau Collection d’espaces de travail**s’affiche.

   ![](media\\power-bi-embedded-get-started\\create-workspace-3.png)

Le **panneau Création** contient les informations nécessaires pour appeler les API qui créent des espaces de travail et y déploient des contenus.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Affichage des touches d'accès rapide aux API de Power BI
Les **touches d’accès rapide**sont l’un des éléments d’informations les plus importants pour appeler les API REST de Power BI. Elles sont utilisées pour générer les **jetons d’application** qui servent à authentifier les demandes d’API. Pour afficher vos **touches d’accès rapide**, cliquez sur **Touches d’accès rapide** dans le **panneau Paramètres**. Pour en savoir plus sur les **jetons d’application**, voir [Authentification et autorisation avec Power BI Embedded](power-bi-embedded-app-token-flow.md).

   ![](media\\power-bi-embedded-get-started\\access-keys.png)

Vous allez constater que vous disposez de deux touches.

   ![](media\\power-bi-embedded-get-started\\access-keys-2.png)

Copiez-les et stockez-les de manière sécurisée dans votre application. Il est très important de considérer ces touches d’accès rapide comme un mot de passe, car elles permettent d’accéder à l’ensemble du contenu de votre **collection d’espaces de travail**.

Même si deux touches sont répertoriées, une seule est nécessaire à un moment donné. La seconde vous est fournie pour regénérer régulièrement les touches sans interrompre l'accès au service.

Maintenant que vous disposez d’une instance de Power BI pour votre application, ainsi que des **touches d’accès rapide**, vous pouvez importer un rapport dans votre propre application. Avant que vous appreniez à importer un rapport, la section suivante décrit comment créer des rapports et jeux de données Power BI à intégrer dans une application.

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app"></a>Création de rapports et de jeux de données à intégrer dans une application
Maintenant que vous avez créé une instance de Power BI pour votre application et que vous avez des **touches d’accès rapide**, vous devez créer les rapports et les jeux de données Power BI à intégrer. Vous pouvez créer des rapports et des jeux de données à l’aide de **Power BI Desktop**. Vous pouvez télécharger [Power BI Desktop gratuitement](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/). Ou, pour une prise en main rapide, vous pouvez télécharger [l’exemple PBIX Analyse des données de vente](http://go.microsoft.com/fwlink/?LinkID=780547). Pour en savoir plus sur l’utilisation de **Power BI Desktop**, consultez la section [Prise en main de Power BI Desktop](https://powerbi.microsoft.com/en-us/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Avec **Power BI Desktop**, vous vous connectez à votre source de données en important une copie des données dans **Power BI Desktop** ou directement à l’aide de **DirectQuery**.

Voici les différences entre l’**importation** et l’utilisation de **DirectQuery**.

| Importation | DirectQuery |
| --- | --- |
| Les tables, colonnes *et données* sont importées ou copiées dans **Power BI Desktop**. Comme pour les visualisations, **Power BI Desktop** interroge une copie des données. Pour voir les modifications apportées aux données sous-jacentes, vous devez actualiser ou réimporter un jeu de données à jour et complet. |Seules les *tables et colonnes* sont importées ou copiées dans **Power BI Desktop**. Comme pour les visualisations, **Power BI Desktop** interroge la source de données sous-jacente, ce qui signifie que vous visualisez toujours des données à jour. |

Pour plus d’informations sur la connexion à une source de données, consultez l’article [Se connecter à une source de données](power-bi-embedded-connect-datasource.md).

Lorsque vous enregistrez votre travail dans **Power BI Desktop**, un fichier PBIX est créé. Ce fichier contient votre rapport. En outre, si vous importez des données, le PBIX contient le jeu de données complet. Si vous utilisez **DirectQuery**, le PBIX ne contient qu’un schéma du jeu de données. Par le biais d’un programme, vous déployez le PBIX dans votre espace de travail à l’aide de [l’API Import de Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [!NOTE]
> **Power BI Embedded** contient d’autres API pour modifier le serveur et la base de données vers lesquels pointe votre jeu de données, et définir les informations d’identification du compte de service utilisé par le jeu de données pour se connecter à votre base de données. Consultez les pages [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) et [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx).
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Dans les étapes précédentes, vous avez créé une collection d’espaces de travail, votre premier rapport et votre premier jeu de données. Maintenant, vous allez découvrir comment écrire du code pour **Power BI Embedded**. Pour faciliter vos premiers pas, nous avons créé un exemple d’application web : [Prise en main de l’exemple Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md). Cet exemple vous montre comment :

* Approvisionner du contenu
  
  * Créer un espace de travail
  * Importer un fichier PBIX
  * Mettre à jour les chaînes de connexion et définir les informations d'identification de vos jeux de données.
* Intégrer un rapport en toute sécurité

## <a name="see-also"></a>Voir aussi
* [Prise en main de l’exemple](power-bi-embedded-get-started-sample.md)
* [Authentification et autorisation avec Power BI Embedded](power-bi-embedded-app-token-flow.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

<!--HONumber=Oct16_HO2-->


