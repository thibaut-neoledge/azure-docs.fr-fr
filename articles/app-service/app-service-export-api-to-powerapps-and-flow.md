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
ms.date: 06/20/2017
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 0d166a2e5b60c3b9f911f9fc3ad49ad7f252abb4
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportation d’une API hébergée sur Azure vers PowerApps et Microsoft Flow

## <a name="creating-custom-connectors-for-powerapps-and-microsoft-flow"></a>Création de connecteurs personnalisés pour PowerApps et Microsoft Flow

[PowerApps](https://powerapps.com) est un service destiné à la création et à l’utilisation d’applications métier personnalisées qui se connectent à vos données et fonctionnent sur plusieurs plateformes. [Microsoft Flow](https://flow.microsoft.com) facilite l’automatisation des workflows et des processus métier entre vos services et applications préférés. Divers connecteurs intégrés aux sources de données tels que Office 365, Dynamics 365, Salesforce, etc. sont fournis avec PowerApps et Microsoft Flow. Toutefois, les utilisateurs doivent également être en mesure d’utiliser les API et les sources de données conçues par leur organisation.

De même, les développeurs qui souhaitent exposer leurs API plus largement au sein de l’organisation veulent peut-être rendre leurs API disponibles pour les utilisateurs PowerApps et Microsoft Flow. Cette rubrique vous explique comment exposer une API conçue avec Azure App Service ou Azure Functions dans PowerApps et Microsoft Flow. [Azure App Service](https://azure.microsoft.com/services/app-service/) est une offre de type Platform-as-a-Service qui permet aux développeurs de créer rapidement et facilement des applications d’API, mobiles et Web d’entreprise. [Azure Functions](https://azure.microsoft.com/services/functions/) est une solution de calcul sans serveur basée sur des événements qui vous permet de créer rapidement du code pour réagir à d’autres parties de votre système et d’effectuer une mise à l’échelle en fonction de la demande.

Pour en savoir plus sur ces services, consultez les rubriques suivantes :
- [PowerApps Guided Learning](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) (Formation guidée sur PowerApps) 
- [Formation guidée sur Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [Présentation d’App Service](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [Vue d’ensemble d’Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>Partage d’une définition d’API

Les API sont souvent décrites à l’aide d’un [document Open API](https://www.openapis.org/) (parfois appelé un document « Swagger »). Il contient toutes les informations sur les opérations disponibles et la façon dont les données doivent être structurées. PowerApps et Microsoft Flow peuvent créer des connecteurs personnalisés pour tout document Open API 2.0. Une fois qu’un connecteur personnalisé est créé, il peut être utilisé exactement de la même façon que l’un des connecteurs intégrés et peut rapidement être ajouté à une application.

Azure App Service et Azure Functions [prennent en charge](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata) la création, l’hébergement et la gestion d’un document Open API. Pour créer un connecteur personnalisé pour une application web, mobile, API ou de fonction, PowerApps et Flow doivent recevoir une copie de la définition.

> [!NOTE]
> Une copie de la définition de l’API étant utilisée, PowerApps et Microsoft Flow ne seront pas immédiatement informés des mises à jour ou modifications apportées à l’application. Si une nouvelle version de l’API est disponible, ces étapes doivent être répétées pour la nouvelle version. 

Pour fournir à PowerApps et Microsoft Flow la définition de l’API hébergée pour votre application, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com) et accédez à votre application App Service ou Azure Functions.

    Si vous utilisez Azure App Service, sélectionnez **Définition de l’API** dans la liste des paramètres. 
    
    Si vous utilisez Azure Functions, sélectionnez votre application de fonction, choisissez **Fonctionnalités de la plateforme**, puis **Définition de l’API**. Vous pouvez également ouvrir l’onglet **Définition d’API (préversion)** à la place.

2. Si une définition de l’API a été fournie, un bouton **Export to PowerApps + Microsoft Flow** (Exporter vers PowerApps + Microsoft Flow) sera affiché. Cliquez sur ce bouton pour lancer le processus d’exportation.

3. Sélectionnez un mode sous **Mode d’exportation**. Ce mode détermine les étapes à suivre pour créer un connecteur. App Service offre deux options pour fournir votre définition d’API à PowerApps et Microsoft Flow :

    **Express** : vous pouvez créer le connecteur personnalisé à partir du Portail Azure. Ce mode nécessite que l’utilisateur actuellement connecté soit autorisé à créer des connecteurs dans l’environnement cible. Il s’agit de l’approche recommandée si cette condition peut être respectée. Si vous utilisez ce mode, suivez les instructions de la section [Mode d’exportation express](#express) plus bas.

    **Manuel** : vous pouvez exporter une copie de la définition d’API, puis l’importer à l’aide du portail PowerApps ou Microsoft Flow. Il s’agit de l’approche recommandée si l’utilisateur Azure et celui autorisé à créer des connecteurs sont des personnes différentes ou si le connecteur doit être créé dans une autre locataire. Si vous utilisez ce mode, suivez les instructions de la section [Mode d’exportation et d’importation manuel](#manual) plus bas.

<a name="express"></a>
## <a name="express-export"></a>Mode d’exportation express

Dans cette section, vous allez créer un connecteur personnalisé à partir du Portail Azure. Vous devez être connecté au locataire vers lequel vous souhaitez effectuer l’exportation, et vous devez être autorisé à créer un connecteur personnalisé dans l’environnement cible.

1. Sélectionnez l’environnement dans lequel vous souhaitez créer le connecteur. Puis, indiquez un nom pour votre connecteur personnalisé.

2. Si votre définition de l’API comprend des définitions de sécurité, elles seront exigées à l’étape n° 2. Si nécessaire, fournissez les informations de configuration de sécurité nécessaires pour accorder aux utilisateurs l’accès à votre API. Pour en savoir plus, consultez la section [Authentification](#auth) plus bas. 

3. Cliquez sur **OK** pour créer votre connecteur personnalisé.


<a name="manual"></a>
## <a name="manual-export-and-import"></a>Mode d’exportation et d’importation manuel

Deux étapes sont nécessaires pour créer un connecteur personnalisé pour une Function App ou une API Web ou mobile :

1. [Récupération de la définition de l’API à partir d’App Service ou d’Azure Functions](#export)
2. [Importation de la définition de l’API dans PowerApps et Microsoft Flow](#import)

Ces deux étapes peuvent être réalisées par deux personnes différentes d’une organisation, car un utilisateur peut ne pas avoir les autorisations nécessaires pour effectuer les deux actions. Dans ce cas, un développeur disposant du rôle Collaborateur pour accéder à l’application App Service ou Azure Functions devra obtenir la définition de l’API (un seul fichier JSON) ou un lien vers celle-ci. Il devra ensuite fournir cette définition à un propriétaire d’application PowerApps ou Microsoft Flow. Ce propriétaire peut utiliser les métadonnées pour créer le connecteur personnalisé.

<a name="export"></a>
### <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>Récupération de la définition de l’API à partir d’App Service ou d’Azure Functions

Dans cette section, vous allez exporter la définition de l’API pour votre API App Service, qui sera utilisée ultérieurement dans le portail PowerApps ou Microsoft Flow.

1. Vous pouvez choisir de **télécharger la définition de l’API** ou **d’obtenir un lien**. Quel que soit votre choix, le résultat sera fourni dans la section suivante. Sélectionnez l’une de ces options et suivez les instructions.
 
2. Si votre définition de l’API comprend des définitions de sécurité, elles seront exigées à l’étape n° 2. Lors de l’importation, PowerApps et Microsoft Flow détecteront ces définitions et demanderont les informations de sécurité. Collectez les informations d’identification associées à chaque définition pour les utiliser dans la section suivante. Pour en savoir plus, consultez la section [Authentification](#auth) plus bas. 

<a name="import"></a>
### <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>Importation de la définition de l’API dans PowerApps et Microsoft Flow

Dans cette section, vous allez créer un connecteur personnalisé dans PowerApps et Microsoft Flow à l’aide de la définition de l’API obtenue précédemment. Les connecteurs personnalisés étant partagés entre les deux services, vous devez uniquement importer la définition une seule fois. Pour plus d’informations sur les connecteurs personnalisés, consultez [S’inscrire et utiliser des connecteurs personnalisés dans PowerApps] et [S’inscrire et utiliser des connecteurs personnalisés dans Microsoft Flow].

1. Ouvrez le [portail web PowerApps](https://web.powerapps.com) ou le [portail web Microsoft Flow](https://flow.microsoft.com/) et connectez-vous. 

2. Cliquez sur le bouton **Paramètres** (l’icône d’engrenage) en haut à droite de la page et sélectionnez **Connecteurs personnalisés**. 

3. Cliquez sur **Créer un connecteur personnalisé**.

4. Dans l’onglet **Général**, fournissez un nom pour votre API, puis chargez la définition OpenAPI ou collez l’URL des métadonnées. Cliquez sur **Continuer**.

4. Dans l’onglet **Sécurité**, si vous êtes invité à fournir des informations d’authentification, entrez les valeurs obtenues dans la section précédente. Dans le cas contraire, passez à l’étape suivante.

5. Dans l’onglet **Définitions**, toutes les opérations définies dans votre fichier OpenAPI sont remplies automatiquement. Si toutes vos opérations requises sont définies, vous pouvez accéder à l’étape suivante. Si ce n’est pas le cas, vous pouvez ajouter et modifier les opérations ici.

6. Cliquez sur **Créer un connecteur**. Si vous souhaitez tester des appels d’API, passez à l’étape suivante.

7. Sur l’onglet **Test**, créez une connexion, sélectionnez une opération à tester et saisissez les données requises par l’opération.

8. Cliquez sur **Test de fonctionnement**.


<a name="auth"></a>
## <a name="authentication"></a>Authentification

PowerApps et Microsoft Flow prennent en charge de manière native un ensemble de fournisseurs d’identité, qui peuvent être utilisés pour connecter les utilisateurs de votre connecteur personnalisé. Si votre API nécessite une authentification, assurez-vous qu’elle est capturée en tant que _définition de sécurité_ dans votre document Open API. Pendant l’exportation, vous devrez fournir des valeurs de configuration permettant à PowerApps et Microsoft Flow d’effectuer les actions de connexion.

Cette section décrit les types d’authentification qui sont pris en charge par le flux express : clé API, Azure Active Directory et Generic OAuth 2.0. Pour obtenir la liste complète des fournisseurs et des informations d’identification requise par chacun d’eux, consultez [S’inscrire et utiliser des connecteurs personnalisés dans PowerApps] et [S’inscrire et utiliser des connecteurs personnalisés dans Microsoft Flow].

### <a name="api-key"></a>Clé API
Lorsque ce schéma de sécurité est utilisé, les utilisateurs de votre connecteur sont invités à fournir la clé lorsqu’ils créent une connexion. Vous pouvez fournir un nom de clé API pour les aider à déterminer la clé requise. Pour Azure Functions, il s’agit généralement de l’une des clés d’hôte, couvrant plusieurs fonctions au sein de l’application de fonction.

### <a name="azure-active-directory"></a>Azure Active Directory
Lors de la configuration d’un connecteur personnalisé nécessitant une connexion AAD, deux inscriptions d’application AAD sont nécessaires : une pour modéliser l’API de service principal et l’autre pour modéliser le connecteur dans PowerApps et Flow.

Votre API doit être configurée de façon à fonctionner avec la première inscription, ce qui sera déjà fait si vous avez utilisé la fonctionnalité [Configurer l’authentification et les autorisations App Service](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication).

Vous devrez créer manuellement la deuxième inscription pour le connecteur en suivant les étapes décrites dans [Ajout d’une application AAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application). L’inscription doit disposer d’un accès délégué à votre API et de l’URL de réponse `https://msmanaged-na.consent.azure-apim.net/redirect`. Consultez [cet exemple](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) pour plus de détails, en remplaçant votre API par Azure Resource Manager.

Les valeurs de configuration suivantes sont requises :
- **ID client** : l’ID client de l’inscription AAD de votre connecteur
- **Clé secrète client** : la clé secrète client de l’inscription AAD de votre connecteur
- **URL de connexion** : l’URL de base pour AAD. Dans Azure public, il s’agit généralement de `https://login.windows.net`.
- **ID de locataire** : l’ID du locataire à utiliser pour la connexion. Il doit s’agir d’un ID « commun » ou de l’ID du locataire dans lequel le connecteur est créé.
- **URL de la ressource** : l’URL de ressource de l’inscription AAD de service principal de votre API

> [!IMPORTANT]
> Si une autre personne importe la définition de l’API dans PowerApps et Microsoft Flow dans le cadre du flux manuel, vous devrez lui fournir l’ID client et la clé secrète client de **l’inscription du connecteur**, ainsi que l’URL de ressource de votre API. Assurez-vous que ces informations secrètes sont gérées de façon sécurisée. **Ne partagez pas les informations d’identification de sécurité de l’API.**

### <a name="generic-oauth-20"></a>Generic OAuth 2.0
La prise en charge de Generic OAuth 2.0 permet une intégration à n’importe quel fournisseur OAuth 2.0. Vous pouvez ainsi utiliser n’importe quel fournisseur personnalisé non pris en charge de manière native.

Les valeurs de configuration suivantes sont requises :
- **ID client** : l’ID client OAuth 2.0
- **Clé secrète client** : la clé secrète client OAuth 2.0
- **URL d’autorisation** : l’URL d’autorisation OAuth 2.0
- **URL du jeton** : l’URL du jeton OAuth 2.0
- **Actualiser l’URL** : l’URL d’actualisation OAuth 2.0



[S’inscrire et utiliser des connecteurs personnalisés dans PowerApps]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[S’inscrire et utiliser des connecteurs personnalisés dans Microsoft Flow]: https://flow.microsoft.com/documentation/register-custom-api/

