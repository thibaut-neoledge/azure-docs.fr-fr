---
title: "Prise en main de l’authentification Azure AD à l’aide du portail Azure | Microsoft Docs"
description: "Découvrez comment utiliser le portail Azure pour accéder à l’authentification Azure Active Directory (Azure AD) en vue d’utiliser l’API Azure Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 0a32c758693c1889d7acfa6510bffba46b7eabc2
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017


---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Prise en main de l’authentification Azure AD à l’aide du portail Azure

Découvrez comment utiliser le portail Azure pour accéder à l’authentification Azure Active Directory (Azure AD) en vue d’accéder à l’API Azure Media Services.

## <a name="prerequisites"></a>Composants requis

- Un compte Azure. Si vous n’avez pas de compte, commencez avec un [essai gratuit Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Un compte Media Services. Pour plus d’informations, consultez [Création d’un compte Azure Media Services à l’aide du portail Azure](media-services-portal-create-account.md).
- Assurez-vous de consulter [Accessing Azure Media Services API with Azure AD authentication overview](media-services-use-aad-auth-to-access-ams-api.md) (Vue d’ensemble de l’accès à l’API Azure Media Services avec l’authentification Azure AD). 

Lorsque vous utilisez l’authentification Azure AD avec Azure Media Services, vous disposez de deux options d’authentification :

- **Authentification utilisateur**. Authentifiez une personne qui utilise l’application pour interagir avec les ressources Media Services. L’application interactive invite tout d’abord l’utilisateur à entrer ses informations d’identification. Par exemple, une application de console de gestion peut être utilisée par les utilisateurs autorisés pour contrôler les travaux d’encodage ou de streaming en direct. 
- **Authentification d’un principal de service**. Authentifiez un service. Les applications qui utilisent généralement cette méthode d’authentification sont des applications qui exécutent des services démon, des services de niveau intermédiaire ou des travaux planifiés : applications web, applications de fonction, applications logiques, API ou microservices.

> [!IMPORTANT]
> À l’heure actuelle, Media Services prend en charge le modèle d’authentification du service Azure Access Control. Toutefois, l’autorisation Access Control sera déconseillée à compter du 1er juin 2018. Nous vous recommandons de migrer vers le modèle d’authentification Azure AD dès que possible.

## <a name="select-the-authentication-method"></a>Sélectionner la méthode d’authentification

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Media Services.
2. Choisissez comment vous connecter à l’API Media Services.

    ![Sélectionner la page de méthode de connexion](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Authentification utilisateur

Pour vous connecter à l’API Media Services à l’aide de l’option d’authentification utilisateur, l’application cliente doit demander un jeton Azure AD qui possède les paramètres suivants :  

* Point de terminaison de locataire Azure AD
* URI de ressource Media Services
* ID client d’application Media Services (natif) 
* URI de redirection d’application Media Services (natif) 
* URI de ressource pour REST Media Services

Vous pouvez obtenir les valeurs de ces paramètres sur la page **Se connecter à l’API Media Services avec l’authentification utilisateur**. 

![Se connecter à la page d’authentification utilisateur](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Si vous vous connectez à l’API Media Services à l’aide du Kit de développement logiciel (SDK) Media Services Microsoft .NET, les valeurs requises sont disponibles dans le cadre du Kit de développement. Pour plus d’informations, consultez [Use Azure AD authentication to access the Azure Media Services API with .NET](media-services-dotnet-get-started-with-aad.md) (Utiliser l’authentification Azure AD pour accéder à l’API Azure Media Services avec .NET).

Si vous n’utilisez pas le Kit de développement logiciel (SDK) du client Media Services .NET, vous devez créer manuellement une demande de jeton Azure AD en utilisant les paramètres décrits précédemment. Pour plus d’informations, consultez [Bibliothèques d’authentification d’Azure Active Directory](../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Authentification d’un principal du service

Pour vous connecter à l’API Media Services à l’aide de l’option de principal de service, votre application de niveau intermédiaire (API web ou application API) doit demander un jeton Azure AD qui possède les paramètres suivants :  

* Point de terminaison de locataire Azure AD
* URI de ressource Media Services 
* URI de ressource pour REST Media Services
* Valeurs de l’application Azure AD : **ID client** et **clé secrète client**

Vous pouvez obtenir les valeurs de ces paramètres sur la page **Se connecter à l’API Media Services avec le principal de service**. Utilisez cette page pour créer une application Azure AD ou sélectionnez-en une existante. Après avoir sélectionné l’application Azure AD, vous pouvez obtenir l’ID client (ID d’application) et générer les valeurs de clé secrète client (clé). 

![Se connecter à la page du principal de service](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Lorsque le panneau **Principal de service** s’ouvre, la première application Azure AD qui répond aux critères suivants est sélectionnée :

- Il s’agit d’une application Azure AD inscrite.
- Elle dispose d’autorisations Access Control de type Collaborateur ou Propriétaire sur le compte.

Une fois que vous avez créé ou sélectionné une application Azure AD, vous pouvez créer et copier une clé secrète client (clé) ainsi que l’ID client (ID d’application). La clé secrète client et l’ID client sont requis pour l’obtention du jeton d’accès dans ce scénario.

Si vous n’êtes pas autorisé à créer des applications Azure AD dans votre domaine, les contrôles d’application Azure AD du panneau ne sont pas affichés, et un message d’avertissement s’affiche.

Si vous vous connectez à l’API Media Services à l’aide du Kit logiciel de développement (SDK) Media Services .NET, consultez [Use Azure AD authentication to access the Azure Media Services API with .NET](media-services-dotnet-get-started-with-aad.md) (Utiliser l’authentification Azure AD pour accéder à l’API Azure Media Services avec .NET).

Si vous n’utilisez pas le Kit de développement logiciel (SDK) du client Media Services .NET, vous devez créer manuellement une demande de jeton Azure AD en utilisant les paramètres décrits précédemment. Pour plus d’informations, consultez [Bibliothèques d’authentification d’Azure Active Directory](../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Obtenir l’ID client et la clé secrète client

Après avoir sélectionné une application Azure AD existante ou choisi l’option permettant d’en créer une, les boutons suivants s’affichent :

![Bouton Gérer les autorisations et bouton Gérer l’application](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Pour ouvrir le panneau de l’application Azure AD, cliquez sur **Gérer l’application**. Dans le panneau **Gérer l’application**, vous pouvez obtenir l’ID client de l’application (ID d’application). Pour générer une clé secrète client (clé), sélectionnez **Clés**.

![Option Clés du panneau Gérer l’application](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Gérer les autorisations et l’application

Après avoir sélectionné l’application Azure AD, vous pouvez gérer l’application et les autorisations. Pour configurer votre application Azure AD afin d’accéder à d’autres applications, cliquez sur **Gérer les autorisations**. Pour les tâches de gestion, telles que la modification des clés et des URL de réponse, ou pour modifier le manifeste de l’application, cliquez sur **Gérer l’application**.

### <a name="edit-the-apps-settings-or-manifest"></a>Modifier les paramètres ou le manifeste de l’application

Pour modifier les paramètres ou le manifeste de l’application, cliquez sur **Gérer l’application**.

![Page Gérer l’application](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Étapes suivantes

Commencez le [chargement de fichiers sur votre compte](media-services-portal-upload-files.md).

