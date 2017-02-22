---
title: "Exportation d’une API hébergée sur Azure vers PowerApps et Microsoft Flow | Microsoft Docs"
description: "Présentation de l’exposition d’une API hébergée dans App Service vers PowerApps et Microsoft Flow"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/06/2017
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: 060fbb885f254c0de02fe422460e8e8d659ac848
ms.openlocfilehash: 503f17a629527e8c9a3bfe6cde1da31dcf1f18ef


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportation d’une API hébergée sur Azure vers PowerApps et Microsoft Flow

## <a name="creating-custom-apis-for-powerapps-and-microsoft-flow"></a>Création d’API personnalisées pour PowerApps et Microsoft Flow

La [plateforme d’application métier Microsoft](https://businessplatform.microsoft.com/) inclut divers produits permettant aux utilisateurs d’en faire davantage. [PowerApps](https://powerapps.com) est un service destiné à la création et à l’utilisation d’applications métier personnalisées qui se connectent à vos données et fonctionnent sur plusieurs plateformes. [Microsoft Flow](https://flow.microsoft.com) facilite l’automatisation des workflows et des processus métier entre vos services et applications préférés. Divers connecteurs intégrés aux sources de données tels que Office 365, Dynamics 365, Salesforce, etc. sont fournis avec PowerApps et Microsoft Flow. Toutefois, les utilisateurs doivent également être en mesure d’utiliser les API et les sources de données conçues par leur organisation.

De même, les développeurs qui souhaitent exposer leurs API plus largement au sein de l’organisation veulent peut-être rendre leurs API disponibles pour les utilisateurs PowerApps et Microsoft Flow. Cette rubrique vous explique comment exposer une API conçue avec Azure App Service ou Azure Functions dans PowerApps et Microsoft Flow. [Azure App Service](https://azure.microsoft.com/services/app-service/) est une offre de type Platform-as-a-Service qui permet aux développeurs de créer rapidement et facilement des applications d’API, mobiles et Web d’entreprise. [Azure Functions](https://azure.microsoft.com/services/functions/) est une solution de calcul sans serveur basée sur des événements qui vous permet de créer rapidement du code pour réagir à d’autres parties de votre système et d’effectuer une mise à l’échelle en fonction de la demande.

Pour en savoir plus sur ces services, consultez les rubriques suivantes :
- [PowerApps Guided Learning](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) (Formation guidée sur PowerApps) 
- [Formation guidée sur Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [Présentation d’App Service](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [Vue d’ensemble d’Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>Partage d’une définition d’API

Les API sont souvent décrites à l’aide un [document Open API](https://www.openapis.org/) (parfois appelé un document « Swagger »). Il contient toutes les informations sur les opérations disponibles et la façon dont les données doivent être structurées. PowerApps et Microsoft Flow peuvent créer des API personnalisées pour tout document Open API 2.0. Une fois l’API personnalisée créée, elle peut être utilisée exactement de la même façon que l’un des connecteurs intégrés et peut rapidement être ajoutée à une application.

Azure App Service et Azure Functions [prennent en charge](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata) la création, l’hébergement et la gestion d’un document Open API. Deux étapes sont nécessaires pour créer un connecteur personnalisé pour une Function App ou une API Web ou mobile :

1. [Récupération de la définition de l’API à partir d’App Service ou d’Azure Functions](#export)
2. [Importation de la définition de l’API dans PowerApps](#import)

Ces deux étapes peuvent être réalisées par deux personnes différentes d’une organisation, car un utilisateur peut ne pas avoir les autorisations nécessaires pour effectuer les deux actions. Dans ce cas, un développeur disposant du rôle Collaborateur pour accéder à l’application App Service ou Azure Functions devra obtenir la définition de l’API (un seul fichier JSON) ou un lien vers celle-ci. Il devra ensuite fournir cette définition à un propriétaire d’application PowerApps ou Microsoft Flow. Ce propriétaire peut utiliser les métadonnées pour créer l’API personnalisée.

> [!NOTE]
> Une copie de la définition de l’API étant utilisée, PowerApps et Microsoft Flow ne seront pas immédiatement informés des mises à jour ou modifications apportées à l’application. Si une nouvelle version de l’API est disponible, ces étapes doivent être répétées pour la nouvelle version. 

<a name="export"></a>
## <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>Récupération de la définition de l’API à partir d’App Service ou d’Azure Functions

Dans cette section, vous allez exporter la définition de l’API pour votre API App Service, qui sera utilisée ultérieurement dans PowerApps.

1. Ouvrez le [portail Azure](https://portal.azure.com) et accédez à votre application App Service ou Azure Functions.

    Si vous utilisez Azure App Service, sélectionnez **Définition de l’API** dans la liste des paramètres. 
    
    Si vous utilisez Azure Functions, sélectionnez **Paramètres Function App**, puis **Configurer les métadonnées de l’API**.

2. Si une définition de l’API a été fournie, un bouton **Export to PowerApps + Microsoft Flow** (Exporter vers PowerApps + Microsoft Flow) sera affiché. Cliquez sur ce bouton pour lancer le processus d’exportation.

3. Vous pouvez choisir de **télécharger la définition de l’API** ou **d’obtenir un lien**. Quel que soit votre choix, le résultat sera fourni à PowerApps dans la section suivante. Sélectionnez l’une de ces options et suivez les instructions.
 
4. Si votre définition de l’API comprend des définitions de sécurité, elles seront exigées à l’étape n° 2. Lors de l’importation, PowerApps et Microsoft Flow détecteront ces définitions et demanderont les informations de sécurité. Les services utilisent cela pour connecter les utilisateurs, de façon qu’ils puissent accéder à l’API. Si votre API nécessite une authentification, assurez-vous qu’elle est capturée en tant que _définition de sécurité_ dans votre document Open API.

    Collectez les informations d’identification associées à chaque définition pour les utiliser dans la section suivante. Pour obtenir une liste des fournisseurs d’identité pris en charge en mode natif par PowerApps et des informations d’identification nécessaires pour chacun, consultez [Register custom APIs in PowerApps] (Inscrire des API personnalisées dans PowerApps) et [Inscrire des API personnalisées dans Microsoft Flow].
 
> [!NOTE]
> Si vous utilisez l’authentification Azure Active Directory, une nouvelle inscription d’application AAD sera nécessaire. Elle comportera un accès délégué à votre API et une URL de réponse de _https://msmanaged-na.consent.azure-apim.net/redirect_. Consultez [cet exemple](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) pour plus de détails, en remplaçant votre API par Azure Resource Manager.
>
> Si une autre personne importe la définition de l’API dans PowerApps, vous fournirez l’ID et le mot de passe du client associés **à la nouvelle inscription**, ainsi que l’URL de ressource de votre API, en plus du fichier de définition de l’API. Assurez-vous que ces informations secrètes sont gérées de façon sécurisée. **Ne partagez pas les informations d’identification de sécurité de l’API.**

<a name="import"></a>
## <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>Importation de la définition de l’API dans PowerApps et Microsoft Flow

Dans cette section, vous allez créer une API personnalisée dans PowerApps et Microsoft Flow à l’aide de la définition de l’API obtenue précédemment. Les API personnalisées étant partagées entre les deux services, vous devez uniquement importer la définition une seule fois. Pour plus d’informations sur les API personnalisées, consultez [Register custom APIs in PowerApps] (Inscrire des API personnalisées dans PowerApps) et [Inscrire des API personnalisées dans Microsoft Flow].

**Pour importer dans PowerApps :**

1. Ouvrez le [portail Web PowerApps](https://web.powerapps.com), connectez-vous, puis sélectionnez **Connexions**. Cliquez sur **Nouvelle connexion**.

2. Sélectionnez **Personnalisée**, puis cliquez sur **New custom API** (Nouvelle API personnalisée).

3. Fournissez un nom pour votre API, puis chargez la définition Swagger ou collez l’URL des métadonnées. Cliquez sur **Suivant**.

4. Si vous êtes invité à fournir des informations d’authentification, entrez les valeurs obtenues dans la section précédente. Dans le cas contraire, passez à l’étape suivante.

5. Cliquez sur **Create**.

**Pour importer dans Microsoft Flow :**

1. Ouvrez le [portail Web Microsoft Flow](https://flow.microsoft.com/) et connectez-vous. 

2. Cliquez sur le bouton **Paramètres** en haut à droite de la page (il ressemble à un engrenage) et sélectionnez **API personnalisées**. Cliquez sur **Créer une API personnalisée**.

3. Chargez la définition Swagger et cliquez sur **Continuer**.

4. Si vous êtes invité à fournir des informations d’authentification, entrez les valeurs obtenues dans la section précédente. Dans le cas contraire, passez à l’étape suivante.

5. Cliquez sur la case à cocher en haut de l’écran.



[Register custom APIs in PowerApps]: https://powerapps.microsoft.com/tutorials/register-custom-api/ (Inscrire des API personnalisées dans PowerApps)
[Inscrire des API personnalisées dans Microsoft Flow]: https://flow.microsoft.com/documentation/register-custom-api/



<!--HONumber=Feb17_HO2-->


