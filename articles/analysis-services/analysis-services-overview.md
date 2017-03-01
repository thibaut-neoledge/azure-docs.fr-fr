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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 653c9a04016c0157b2107710e170ba63b3a499f7
ms.openlocfilehash: 38f68d6df19d24492a234933e10d5d5cf34d1b23
ms.lasthandoff: 01/05/2017


---
# <a name="what-is-azure-analysis-services"></a>Qu’est-ce qu’Azure Analysis Services ?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Basé sur le moteur d’analyse reconnu de Microsoft SQL Server Analysis Services, Azure Analysis Services fournit une modélisation des données professionnelle dans le cloud. 

Regardez cette vidéo pour en savoir plus sur la façon dont Azure Analysis Services s’intègre aux fonctionnalités de décisionnel globales de Microsoft et sur les avantages que vous pourriez vous aussi tirer du passage au cloud de vos modèles sémantiques.

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesGettingStarted/player]
>
>

> [!IMPORTANT]
> Azure Analysis Services est en **version préliminaire**. Certaines choses ne fonctionnent pas encore. Consultez [Attentes concernant la version préliminaire](#preview-expectations) plus loin dans cet article. Veillez également à rester informé sur notre [blog Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920) pour disposer des dernières informations.
> 
> 

## <a name="built-on-sql-server-analysis-services"></a>Basé sur SQL Server Analysis Services
Azure Analysis Services est compatible avec SQL Server 2016 Analysis Services Enterprise Edition, que vous connaissez déjà. Azure Analysis Services prend en charge les modèles tabulaires au niveau de compatibilité 1200. Les requêtes directes (DirectQuery), les partitions, la sécurité au niveau des lignes, les relations bidirectionnelles et les traductions sont toutes prises en charge.

## <a name="use-the-tools-you-already-know"></a>Utiliser les outils que vous connaissez déjà
![Outils de développement BI](./media/analysis-services-overview/aas-overview-dev-tools.png)

Lors de la création de modèles de données pour Azure Analysis Services, vous utilisez les mêmes outils que pour SQL Server Analysis Services. Créez et déployez des modèles tabulaires à l’aide des dernières versions de [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) ou à l’aide des modèles [Azure Powershell](/powershell/azureps-cmdlets-docs) et [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) de [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connect-to-data-sources"></a>Se connecter aux sources de données
Les modèles de données déployés sur des serveurs dans Azure prennent en charge la connexion aux données sources locales dans votre entreprise ou dans le cloud. Combinez des données de sources de données locales et de cloud pour une solution BI hybride.

![Sources de données](./media/analysis-services-overview/aas-overview-data-sources.png)

Votre serveur se trouvant dans le cloud, la connexion aux sources de données de cloud est transparente. Lors de la connexion à des sources de données locales, la [Passerelle de données locale](analysis-services-gateway.md) garantit des connexions rapides et sécurisées avec votre serveur Analysis Services dans le cloud.  

 \* Certaines sources de données ne sont pas encore prises en charge dans la version préliminaire. Pour en savoir plus, consultez [Attentes concernant la version préliminaire](#preview-expectations) plus loin dans cet article.

## <a name="explore-your-data-from-anywhere"></a>Explorer vos données où que vous soyez
Connectez-vous et [obtenez des données](analysis-services-connect.md) de vos serveurs où que vous soyez. Azure Analysis Services prend en charge la connexion à partir de Power BI Desktop, d’Excel, d’applications personnalisées et d’outils web.

![Visualisations de données](./media/analysis-services-overview/aas-overview-visualization.png)

 \* Power BI Embedded n’est pas encore pris en charge dans la version préliminaire.

## <a name="secure"></a>Sécuriser
#### <a name="user-authentication"></a>Authentification utilisateur
L’authentification utilisateur pour Azure Analysis Services est gérée par [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). Lors d’une tentative de connexion à une base de données Azure Analysis Services, les utilisateurs utilisent une identité de compte d’entreprise ayant accès à la base de données à laquelle ils tentent d’accéder. Ces identités utilisateur doivent être des membres de l’Azure Active Directory par défaut pour l’abonnement dans lequel se trouve Azure Analysis Services. L’[intégration d’annuaire](https://technet.microsoft.com/library/jj573653.aspx) entre AAD et un Active Directory local est un excellent moyen pour obtenir vos accès utilisateurs locaux à une base de données Azure Analysis Services, mais n’est pas nécessaire dans tous les scénarios.

Les utilisateurs se connectent avec le nom d’utilisateur principal (UPN) de leur compte et leur mot de passe. Lors de la synchronisation avec un annuaire Active Directory local, l’UPN de l’utilisateur est généralement l’adresse de messagerie professionnelle.

Les autorisations nécessaires pour gérer la ressource serveur Azure Analysis Services sont gérées en associant des utilisateurs à des rôles dans votre abonnement Azure. Par défaut, les administrateurs des abonnements disposent d’autorisations de propriétaire sur la ressource serveur dans Azure. Des utilisateurs supplémentaires peuvent être ajoutés à l’aide d’Azure Resource Manager.

#### <a name="data-security"></a>Sécurité des données
Azure Analysis Services utilise le stockage Blob Azure pour conserver le stockage et les métadonnées des bases de données Analysis Services. Les fichiers de données Blob sont chiffrés à l’aide du chiffrement côté serveur (SSE) Azure Blob. Lorsque vous utilisez le mode de requête directe, seules les métadonnées sont stockées ; les données réelles sont accessibles à partir de la source de données au moment de la requête.

#### <a name="on-premises-data-sources"></a>Sources de données locales
La sécurisation de l’accès aux données locales dans votre entreprise est possible en installant et en configurant une [Passerelle de données locale](analysis-services-gateway.md). Les passerelles fournissent un accès aux données pour les requêtes directes et les modes en mémoire. Lorsqu’un modèle Azure Analysis Services se connecte à une source de données locale, une requête est créée, ainsi que les informations d’identification chiffrées pour la source de données locale. Le service cloud de la passerelle analyse la requête et envoie la requête vers Azure Service Bus. La passerelle locale interroge Azure Service Bus pour connaître les requêtes en attente. La passerelle reçoit ensuite la requête, déchiffre les informations d’identification et se connecte à la source de données pour l’exécution. Les résultats sont ensuite renvoyés de la source de données vers la passerelle, puis vers la base de données Azure Analysis Services.

Azure Analysis Services est régi par les [Termes du contrat Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) et la [Déclaration de confidentialité Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Pour plus d’informations sur la sécurité Azure, consultez [Microsoft Trust Center](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="get-help"></a>Obtenir de l'aide
Azure Analysis Services est simple à configurer et à gérer. Vous trouverez ici toutes les informations dont vous avez besoin pour créer et gérer un serveur. Lorsque vous créez un modèle de données à déployer sur votre serveur, ceci est très similaire à la création d’un modèle de données que vous déployez sur un serveur local. Une bibliothèque importante de concepts, de procédures, de didacticiels et d’articles de référence est disponible dans [Analysis Services sur MSDN](https://msdn.microsoft.com/library/bb522607.aspx).

Nous disposons également d’un nombre de vidéos utiles dans [Azure Analysis Services sur Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

Les choses évoluent rapidement. Vous pouvez toujours obtenir les dernières informations dans le [blog Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920).

## <a name="community"></a>Communauté
Analysis Services a une communauté active d’utilisateurs. Rejoignez la conversation sur le [forum Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Commentaires
Vous avez des suggestions ou des demandes de fonctionnalités ? Veillez à laisser vos commentaires sur [Azure Analysis Services Feedback](https://aka.ms/azureanalysisservicesfeedback).

Vous avez des suggestions concernant la documentation ? Vous pouvez ajouter des commentaires à l’aide de Disqus en bas de chaque article.

## <a name="preview-expectations"></a>Attentes concernant la version préliminaire
Azure Analysis Services est actuellement en version préliminaire. Il existe quelques points que vous devez connaître.

##### <a name="server-modes"></a>Modes serveur
Azure Analysis Services prend actuellement en charge le mode Tabulaire pour les modèles tabulaires au niveau de compatibilité 1200. Le mode multidimensionnel et d’exploration de données, et Power Pivot pour le mode SharePoint ne sont pas pris en charge.

##### <a name="data-sources"></a>Sources de données
Dans la version préliminaire, les sources de données suivantes sont prises en charge dans des modèles tabulaires 1200 déployés sur un serveur Azure Analysis Services.

| **Cloud** | **Local** |
| --- | --- |
| Base de données SQL |SQL Server |
| SQL Data Warehouse |APS |
| Oracle | Oracle |
| Teradata | |

### <a name="data-source-providers"></a>Fournisseurs de sources de données
Les modèles de données dans Azure Analysis Services peuvent nécessiter des différents fournisseurs de données pour se connecter aux sources de données autres que des modèles de données dans SQL Server Analysis Services. La configuration requise du fournisseur de données dépend de la source de données, dans le cloud ou locale, et le type de modèle de données ; en mémoire ou requête directe. Pour plus d’informations, consultez les rubriques suivantes : [Connexions de source de données](analysis-services-datasource.md).

### <a name="client-connections"></a>Connexions clientes
Power BI Embedded n’est pas encore pris en charge dans la version préliminaire.

Les classeurs Excel avec des connexions actives à un serveur Azure Analysis Services et enregistré sur OneDrive ou SharePoint Online ne sont pas pris en charge.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous en savez plus sur Azure Analysis Services, il est temps de commencer. Découvrez comment [créer un serveur](analysis-services-create-server.md) dans Azure et [déployer un modèle tabulaire](analysis-services-deploy.md) sur celui-ci.


