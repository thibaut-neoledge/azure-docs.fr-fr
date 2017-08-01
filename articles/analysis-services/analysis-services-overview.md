---
title: "Qu’est-ce qu’Azure Analysis Services ? | Microsoft Docs"
description: "Obtenez une vue d’ensemble d’Analysis Services dans Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/03/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: bf56aa4bafd929010bd916d09f7025ad76823d1f
ms.contentlocale: fr-fr
ms.lasthandoff: 07/21/2017

---
# <a name="what-is-azure-analysis-services"></a>Qu’est-ce qu’Azure Analysis Services ?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services fournit sur le cloud des données de modélisation de qualité professionnelle. Il s’agit d’une plateforme en tant que service (PaaS) entièrement gérée et étroitement intégrée aux services de plate-forme de données Azure. Avec son moteur d’analyse de données OLAP hautement optimisé, Analysis Services fournit un calque de modèle sémantique riche pour des sources de données pouvant être de grande taille, complexes ou souvent disparates. En effectuant l’extraction de toutes les données, vous pouvez définir le modèle de données sémantique, créant ainsi des analyses hautement personnalisées qui serviront de moteur aux expériences d’analyse riches et interactives d’outils clients modernes.

![Sources de données](./media/analysis-services-overview/aas-overview-data-sources.png)


Regardez [cette vidéo](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4) pour découvrir comment Azure Analysis Services s’intègre aux fonctionnalités de décisionnel globales de Microsoft et comment vous pouvez tirer parti du passage au cloud de vos modèles de données.

<!--
>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/Azure-Analysis-Services-overview/player]
>
>
-->

## <a name="built-on-sql-server-analysis-services"></a>Basé sur SQL Server Analysis Services
Azure Analysis Services est compatible avec SQL Server Analysis Services Enterprise Edition, que vous connaissez déjà. Azure Analysis Services prend en charge les modèles tabulaires aux niveaux de compatibilité 1200 et 1400. Les partitions, la sécurité au niveau des lignes, les relations bidirectionnelles et les traductions sont toutes prises en charge. Des modes en mémoire et des modes DirectQuery signifient des requêtes ultra-rapides sur des jeux de données massifs et complexes.

Les modèles tabulaires offrent un développement rapide et sont hautement personnalisables. Pour les développeurs, les modèles tabulaires incluent le modèle d’objet tabulaire (TOM) pour décrire les modèles d’objet. Le modèle d’objet tabulaire est affiché au format JSON via le langage [TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) et le langage de définition de données AMO via l’espace de noms [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx).

De nouvelles fonctionnalités dans les modèles tabulaires 1400 prennent en charge les lignes de détails, la sécurité au niveau objet, la hiérarchie déséquilibrée, une expérience moderne d’obtention des données dans SSDT permettant la connectivité des données, ainsi que de nombreuses autres améliorations. Et comme les métadonnées du modèle sous-jacent sont les mêmes, les solutions pour les modèles tabulaires locaux peuvent facilement être envoyées sur le cloud.


## <a name="better-with-azure"></a>Meilleur avec Azure
Azure Analysis Services s’intègre à de nombreux services de données Azure qui vous permettent de créer des solutions pour les analyses sophistiquées.

Azure Analysis Services peut utiliser des données à partir de Azure SQL Database, Azure SQL Data Warehouse et le stockage de Azure Blob. Vous pouvez créer des solutions pour les entrepôts de données d’entreprise dans Azure à l’aide d’un modèle hub-and-spoke. L’entrepôt de données SQL se trouve au centre et plusieurs modèles décisionnels autour ciblent différents groupes professionnels ou zones de sujet.

Avec Azure Data Factory, vous pouvez orchestrer le déplacement et la transformation de données, une fonctionnalité de base de n’importe quelle solution pour les décisionnels/analyses d’entreprise. Azure Analysis Services peut être intégré dans n’importe quel pipeline Azure Data Factory en incluant une activité qui charge des données dans le modèle. Azure Automation et Azure Functions peuvent également servir pour effectuer une orchestration légère de modèles à l’aide d’un code personnalisé.

Azure Analysis Services est également étroitement intégré à Azure Active Directory, fournissant un accès en fonction du rôle sécurisé à vos données critiques.

## <a name="pricing"></a>Tarification
Azure Analysis Services est disponible pour les niveaux Développeur, De base et Standard. Dans chaque niveau, les coûts de plan varient en fonction de la puissance de traitement, des QPUs et de la taille de la mémoire. Lorsque vous créez un serveur, vous sélectionnez un plan au sein d’un niveau. Vous pouvez modifier les plans vers le haut ou vers le bas au sein du même niveau, ou le passer à un niveau supérieur, mais vous ne pouvez pas le rétrograder d’un niveau supérieur à un niveau inférieur.

![Passer à un niveau supérieur](./media/analysis-services-overview/aas-overview-tier-up.png)

Modifiez des niveaux à la volée dans le portail Azure ou avec l’applet de commande Set-AzureRmAnalysisServicesServer PowerShell. Pour plus d’informations sur les différents plans et niveaux, et pour utiliser le calculateur de prix permet afin de déterminer le plan adapté pour vous, consultez la [Tarification Analysis Services Azure](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="scale-resources"></a>Mettre les ressources à l’échelle
Montez en puissance, descendez en puissance ou suspendez votre serveur. Utilisez le portail Azure ou obtenez le contrôle total à la volée à l’aide de PowerShell. Vous paierez uniquement pour ce que vous utiliserez.

Lorsque vous créez de nouveaux serveurs, utilisez l’applet de commande [New-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) pour définir votre plan. Pour des serveurs existants, utilisez l’applet de commande [Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver) pour définir votre plan. Vous n’utilisez pas tout le temps votre service ? Vous pouvez le suspendre à l’aide du portail ou de l’applet de commande [Suspend-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver). Redémarrez-le avec l’applet de commande [Resume-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver). Vous paierez uniquement lorsque votre serveur est actif.


## <a name="regions"></a>Régions
Les serveurs Analysis Services Azure peuvent être créés dans les [régions Azure](https://azure.microsoft.com/regions/) suivantes :

| Amérique | Europe | Asie-Pacifique |
|----------|--------|--------------|
|  Sud du Brésil<br> Centre du Canada<br> Est des États-Unis 2<br> États-Unis - partie centrale septentrionale<br> Centre-Sud des États-Unis<br> Ouest-Centre des États-Unis<br> Ouest des États-Unis | Europe du Nord<br> Sud du Royaume-Uni<br> Europe de l’Ouest |   Sud-Ouest de l’Australie<br> Est du Japon<br> Asie du Sud-Est<br> Inde occidentale  |

De nouvelles régions continuent d’être ajoutées, alors la liste peut être incomplète. Vous choisissez un emplacement lorsque vous créez votre serveur dans le portail Azure ou à l’aide des modèles Azure Resource Manager. Pour optimiser au mieux vos performances, choisissez l’emplacement le plus proche de votre plus grande base d’utilisateurs. Garantissez-vous une [haute disponibilité](analysis-services-bcdr.md) en déployant vos modèles sur des serveurs redondants dans plusieurs régions.

## <a name="get-up-and-running-quickly"></a>Opérationnel rapidement
Avec le portail Azure, vous pouvez [créer un serveur](analysis-services-create-server.md) en quelques minutes. Et, avec les modèles Azure Resource Manager et PowerShell, vous pouvez mettre en service des serveurs à l’aide d’un modèle déclaratif. Avec un modèle unique, vous pouvez déployer plusieurs services, ainsi que d’autres composants Azure tels que les comptes de stockage.  Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

Une fois que vous avez créé un serveur, vous pouvez créer un modèle tabulaire directement dans le portail Azure. Avec la nouvelle (version préliminaire) fonctionnalité web designer, vous pouvez vous connecter à une base de données Azure SQL, une source de données Azure SQL Data Warehouse, ou importer un fichier Power BI Desktop.pbix. Les relations entre les tables sont créées automatiquement et vous pouvez créer les mesures ou éditer le fichier model.bim au format json directement depuis votre navigateur.

## <a name="migrate-existing-tabular-models"></a>Migrer les modèles tabulaires existants
Si vous disposez déjà de solutions locales existantes pour les modèles SQL Server Analysis Services, vous pouvez migrer vers Azure Analysis Services sans réaliser de modification importante. Pour effectuer la migration, vous pouvez utiliser SSDT pour déployer votre modèle sur votre serveur. Ou, dans SSMS, vous pouvez utiliser la fonction de sauvegarde et restauration ou TMSL.

Si vous avez des sources de données locales, vous devez installer et configurer une [Passerelle de données locale](analysis-services-gateway.md). Si vos rôles et vos membres de rôle sont déjà configurés, vos rôles migreront mais vous devrez rajouter les membres de rôle à l’aide de SSMS ou de PowerShell.


## <a name="data-sources"></a>Sources de données
Azure Analysis Services prend en charge la connexion aux données sources locales dans votre entreprise et dans le cloud. Combinez des données provenant de sources de données locales et d’un cloud pour une solution hybride. 

Les nouveaux modèles tabulaires 1400 utilisent la fonctionnalité moderne d’obtention des données dans SSDT, basée sur le langage de requête de formule M. Avec Get Data, vous avez plus de transformation des données et de fonctionnalités de mashup, et vous avez la possibilité de créer et modifier vos propres requêtes avancées en langage de formule M . Par exemple, avec les modèles tabulaires 1400, vous pouvez modéliser sur les fichiers de données dans Azure Blob Storage.

Azure Analysis Services prend en charge à l’aide de [DirectQuery](https://docs.microsoft.com/sql/analysis-services/tabular-models/directquery-mode-ssas-tabular) afin de se connecter directement à Azure SQL Database, Azure SQL Data Warehouse, SQL Server, SQL Server Data Warehouse, Oracle, et aux bases de données relationnelles Teradata .

Pour plus d’informations, consultez les [Sources de données prises en charge par Azure Analysis Services](analysis-services-datasource.md).

## <a name="use-the-tools-you-already-know"></a>Utiliser les outils que vous connaissez déjà

![Outils de développement BI](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>SQL Server Data Tools pour Visual Studio (SSDT)
Développez et déployez gratuitement des modèles avec [SQL Server Data Tools (SSDT) pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). SSDT inclut des modèles de projet Analysis Services opérationnels rapidement. SSDT inclut maintenant la requête de source de données et les fonctionnalités de mashup modernes Get Data pour les modèles de tabulaire 1400. Si connaissez déjà Get Data dans Power BI Desktop et Excel 2016, vous devriez déjà savoir à quel point il est aisé de créer des requêtes de source de données hautement personnalisables.

Avec la nouvelle version de SSDT et les modèles tabulaires 1400, il n’est plus nécessaire d’installer une instance locale d’Analysis Services pour héberger une base de données de l’espace de travail. SSDT inclut désormais son propre moteur Analysis Services intégré et sa propre base de données. Lorsque vous êtes prêt, déployer sur vos serveurs dans Azure directement à partir de SSDT. De plus, SSDT est mis à jour tous les mois, et vous pouvez donc commencer rapidement à utiliser des fonctionnalités les plus récentes.

#### <a name="sql-server-management-studio"></a>SQL Server Management Studio
Gérez vos serveurs et bases de données model à l’aide de [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Connectez-vous à vos serveurs dans le cloud. Exécutez les scripts TMSL directement à partir de la fenêtre de requête XMLA et automatisez des tâches à l’aide des scripts TMSL. De nouvelles fonctionnalités sont régulièrement implémentées : SSMS est mis à jour tous les mois.

#### <a name="powershell"></a>PowerShell
Les tâches de gestion des ressources du serveur telles que la création d’un serveur, l’interruption ou la reprise des opérations du serveur ou la modification du niveau de service utilisent les applets de commande Azure Resource Manager (AzureRM). D’autres tâches de gestion des bases de données telles que l’ajout ou la suppression de membres de rôle, le traitement, ou l’exécution de scripts TMSL utilisent l’applet de commande dans le module Sqlserver. Les modules AzureRM et SQLServer sont tous les deux disponibles dans la [Galerie PowerShell](https://www.powershellgallery.com/).


## <a name="secure"></a>Sécuriser
![Visualisations de données](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>Authentification
L’authentification utilisateur pour Azure Analysis Services est gérée par [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). Lors d’une tentative de connexion à une base de données Azure Analysis Services, les utilisateurs utilisent une identité de compte d’entreprise ayant accès à la base de données à laquelle ils tentent d’accéder. Ces identités utilisateur doivent être des membres de l’Azure Active Directory par défaut pour l’abonnement dans lequel se trouve Azure Analysis Services. Pour en savoir plus, consultez [Authentification et autorisations utilisateur](analysis-services-manage-users.md).

#### <a name="data-security"></a>Sécurité des données
Azure Analysis Services utilise le stockage Blob Azure pour conserver le stockage et les métadonnées des bases de données Analysis Services. Les fichiers de données Blob sont chiffrés à l’aide du chiffrement côté serveur (SSE) Azure Blob. Si vous utilisez le mode Requête directe, seules les métadonnées sont stockées. Les données réelles sont accessibles à partir de la source de données au moment de la requête.

#### <a name="on-premises-data-sources"></a>Sources de données locales
Vous sécurisez l’accès aux données locales dans votre entreprise en installant et en configurant une [Passerelle de données locale](analysis-services-gateway.md). Les passerelles fournissent un accès aux données pour les requêtes directes et les modes en mémoire. Lorsqu’un modèle Azure Analysis Services se connecte à une source de données locale, une requête est créée, ainsi que les informations d’identification chiffrées pour la source de données locale. Le service cloud de la passerelle analyse la requête et envoie la requête vers Azure Service Bus. La passerelle locale interroge Azure Service Bus pour connaître les requêtes en attente. La passerelle reçoit ensuite la requête, déchiffre les informations d’identification et se connecte à la source de données pour l’exécution. Les résultats sont ensuite renvoyés de la source de données vers la passerelle, puis vers la base de données Azure Analysis Services.

Azure Analysis Services est régi par les [Termes du contrat Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) et la [Déclaration de confidentialité Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Pour plus d’informations sur la sécurité Azure, consultez [Microsoft Trust Center](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="client-connections"></a>Connexions clientes
![Visualisations de données](./media/analysis-services-overview/aas-overview-clients.png)

Les outils modernes d’exploration et de visualisation de données comme Power BI, Excel, et d’autres outils tiers fournissent à l’utilisateur final des informations hautement interactive et visuellement riche sur vos données de modèles.

Les clients utilisent les [Bibliothèques clientes](analysis-services-data-providers.md) MSOLAP, AMO ou ADOMD pour se connecter aux serveurs Analysis Services. Les applications clientes Microsoft telles que Power BI Desktop et Excel installent les trois bibliothèques clientes. Mais gardez à l’esprit que selon la version ou la fréquence des mises à jour, les bibliothèques clientes peuvent ne pas être les dernières versions requises par Azure Analysis Services. Il en va de même pour les applications personnalisées ou d’autres interfaces telles que AsCmd, TOM, ADOMD.NET. Ces applications nécessitent généralement l’installation manuelle des bibliothèques en même temps que le reste du package.


## <a name="get-help"></a>Obtenir de l'aide

#### <a name="documentation"></a>Documentation
Azure Analysis Services est simple à configurer et à gérer. Vous trouverez ici toutes les informations dont vous avez besoin pour créer et gérer vos services de serveur. Lorsque vous créez un modèle de données à déployer sur votre serveur, ceci est très similaire à la création d’un modèle de données que vous déployez sur un serveur local. Une bibliothèque importante de concepts, de procédures, de didacticiels et d’articles de référence est disponible dans [Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services).

#### <a name="videos"></a>Vidéos
Visionnez différentes vidéos utiles dans [Azure Analysis Services sur Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

#### <a name="blogs"></a>Blogs
Les choses évoluent rapidement. Vous pouvez toujours obtenir les dernières informations en consultant le [Blog de l’équipe Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/) et le [Blog Azure](https://azure.microsoft.com/blog/).

#### <a name="community"></a>Communauté
Analysis Services a une communauté active d’utilisateurs. Rejoignez la conversation sur le [forum Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Commentaires
Vous avez des suggestions ou des demandes de fonctionnalités ? Veillez à laisser vos commentaires sur [Azure Analysis Services Feedback](https://aka.ms/azureanalysisservicesfeedback).

Vous avez des suggestions concernant la documentation ? Vous pouvez ajouter des commentaires à l’aide de Livefyre en bas de chaque article.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous en savez plus sur Azure Analysis Services, il est temps de commencer. Découvrez comment [créer un serveur](analysis-services-create-server.md) dans Azure. Lorsque votre serveur est prêt, parcourez le [Didacticiel Adventure Works](tutorials/aas-adventure-works-tutorial.md) pour apprendre à créer un modèle tabulaire entièrement fonctionnel et le déployer sur votre serveur.

