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
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: cc85ae1c6642e763e57327c151ec68845f7673f7
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


---
# <a name="what-is-azure-analysis-services"></a>Qu’est-ce qu’Azure Analysis Services ?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Basé sur le moteur d’analyse reconnu de Microsoft SQL Server Analysis Services, Azure Analysis Services fournit une modélisation des données professionnelle dans le cloud. 

Regardez cette vidéo pour découvrir comment Azure Analysis Services s’intègre aux fonctionnalités de décisionnel globales de Microsoft et comment vous pouvez tirer parti du passage au cloud de vos modèles de données.


>
>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/Azure-Analysis-Services-overview/player]
>
>


## <a name="built-on-sql-server-analysis-services"></a>Basé sur SQL Server Analysis Services
Azure Analysis Services est compatible avec SQL Server Analysis Services Enterprise Edition, que vous connaissez déjà. Azure Analysis Services prend en charge les modèles tabulaires aux niveaux de compatibilité 1200 ou ultérieurs. Les requêtes directes (DirectQuery), les partitions, la sécurité au niveau des lignes, les relations bidirectionnelles et les traductions sont toutes prises en charge.

## <a name="use-the-tools-you-already-know"></a>Utiliser les outils que vous connaissez déjà
![Outils de développement BI](./media/analysis-services-overview/aas-overview-dev-tools.png)

Lors de la création de modèles de données pour Azure Analysis Services, vous utilisez les mêmes outils que pour SQL Server Analysis Services. Créez et déployez des modèles à l’aide de [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) ou à l’aide des modèles [Azure PowerShell](/powershell/azureps-cmdlets-docs) et [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) de [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="supports-the-latest-features"></a>Prend en charge les dernières fonctionnalités
Azure Analysis Services prend en charge les modèles tabulaires aux niveaux de compatibilité 1200 et 1400 (préversion).

**Tabular 1200** : inclus la première fois dans SQL Server 2016 Analysis Services, 1200 a introduit le modèle d’objet tabulaire (TOM) pour décrire des objets de modèle tels que des tables, des colonnes et des relations. Le modèle d’objet tabulaire est affiché au format JSON via le langage [TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) et le langage de définition de données AMO via l’espace de noms [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx).

**Tabular 1400 Preview** : introduit la prise en charge des lignes de détails, la sécurité au niveau objet, la prise en charge de la hiérarchie déséquilibrée, une expérience moderne d’obtention des données ainsi que de nombreuses autres améliorations. Pour tirer parti des toutes dernières fonctionnalités, vous devez utiliser le dernier ensemble d’outils [SSDT (SQL Server Data Tools)](https://msdn.microsoft.com/library/mt204009.aspx). Comme Tabular 1400 est toujours en préversion, les choses évoluent rapidement. Pour obtenir les dernières informations, consultez notre [billet de blog](https://azure.microsoft.com/blog/1400-models-in-azure-as/).

## <a name="data-sources"></a>Sources de données
Les modèles de données déployés sur des serveurs dans Azure prennent en charge la connexion aux données sources locales dans votre entreprise ou dans le cloud. Combinez des données de sources de données locales et de cloud pour une solution BI hybride.

Votre serveur se trouvant dans le cloud, la connexion aux sources de données de cloud est transparente. Lors de la connexion à des sources de données locales, la [passerelle de données locale](analysis-services-gateway.md) garantit des connexions rapides et sécurisées avec votre serveur dans le cloud.

Pour en savoir plus sur les sources de données locales prises en charge, consultez [Connexions de source de données](analysis-services-datasource.md).


## <a name="explore-your-data-from-anywhere"></a>Explorer vos données où que vous soyez
Connectez-vous et obtenez des données de vos serveurs où que vous soyez. Azure Analysis Services prend en charge la connexion à partir de Power BI Desktop, d’Excel, d’applications personnalisées et d’outils web.

![Visualisations de données](./media/analysis-services-overview/aas-overview-visualization.png)


## <a name="secure"></a>Sécuriser
#### <a name="user-authentication"></a>Authentification utilisateur
L’authentification utilisateur pour Azure Analysis Services est gérée par [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). Lors d’une tentative de connexion à une base de données Azure Analysis Services, les utilisateurs utilisent une identité de compte d’entreprise ayant accès à la base de données à laquelle ils tentent d’accéder. Ces identités utilisateur doivent être des membres de l’Azure Active Directory par défaut pour l’abonnement dans lequel se trouve Azure Analysis Services. [L’intégration d’annuaire](https://technet.microsoft.com/library/jj573653.aspx) entre AAD et le service Active Directory local est un excellent moyen d’obtenir vos accès utilisateur à une base de données Azure Analysis Services, mais n’est pas nécessaire dans tous les scénarios.

Les utilisateurs se connectent avec le nom d’utilisateur principal (UPN) de leur compte et leur mot de passe. Lors de la synchronisation avec un annuaire Active Directory local, l’UPN de l’utilisateur est généralement l’adresse de messagerie professionnelle.

Les autorisations nécessaires pour gérer la ressource serveur Azure Analysis Services sont gérées en associant des utilisateurs à des rôles dans votre abonnement Azure. Par défaut, les administrateurs des abonnements disposent d’autorisations de propriétaire sur la ressource serveur dans Azure. Des utilisateurs supplémentaires peuvent être ajoutés à l’aide d’Azure Resource Manager.

#### <a name="data-security"></a>Sécurité des données
Azure Analysis Services utilise le stockage Blob Azure pour conserver le stockage et les métadonnées des bases de données Analysis Services. Les fichiers de données Blob sont chiffrés à l’aide du chiffrement côté serveur (SSE) Azure Blob. Si vous utilisez le mode Requête directe, seules les métadonnées sont stockées. Les données réelles sont accessibles à partir de la source de données au moment de la requête.

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

Vous avez des suggestions concernant la documentation ? Vous pouvez ajouter des commentaires à l’aide de Livefyre en bas de chaque article.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous en savez plus sur Azure Analysis Services, il est temps de commencer. Découvrez comment [créer un serveur](analysis-services-create-server.md) dans Azure et [déployer un modèle tabulaire](analysis-services-deploy.md) sur celui-ci.


