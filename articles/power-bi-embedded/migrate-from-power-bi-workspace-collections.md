---
title: "Guide pratique pour migrer du contenu Collections d’espaces de travail Power BI vers Power BI Embedded | Microsoft Docs"
description: "Découvrez comment migrer à partir de Collections d’espaces de travail Power BI vers Power BI Embedded et tirer parti des fonctionnalités d’incorporation dans les applications."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 069f31c8213bd0d8586f7ca50e543acfdad8a2b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>Guide pratique pour migrer du contenu Collections d’espaces de travail Power BI vers Power BI Embedded

Découvrez comment migrer du service Collections d’espaces de travail Power BI vers Power BI Embedded. Cet article fournit des conseils pour migrer du service Collections d’espaces de travail Azure Power BI vers Power BI Embedded. Nous allons également voir à quoi vous devez vous attendre par rapport aux changements d’application.

La ressource Collections d’espaces de travail Power BI reste disponible pendant une période limitée après la disponibilité générale de Power BI Premium. Les clients sous Contrat Entreprise ont accès à leurs collections d’espaces de travail existantes jusqu’à l’expiration de leurs contrats existants. Les clients ayant fait l’acquisition du service Collections d’espaces de travail Power BI par le biais de canaux directs ou CSP disposent d’un accès pendant un an à partir de la disponibilité générale de Power BI Premium.

> [!IMPORTANT]
> Bien que la migration dépende du service Power BI, il n’existe aucune dépendance envers Power BI pour les utilisateurs de votre application quand vous utilisez un **jeton d’incorporation**. Ils n’ont pas besoin de s’inscrire à Power BI pour afficher le contenu incorporé dans votre application. Vous pouvez utiliser cette approche d’incorporation pour incorporer Power BI pour vos clients.

![Flux Power BI Embedded](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>Préparer la migration

Vous devez effectuer quelques tâches pour préparer la migration du service Collections d’espaces de travail Power BI vers Power BI Embedded. Vous avez besoin d’un locataire disponible, ainsi que d’un utilisateur ayant une licence Power BI Pro.

1. Vérifiez que vous avez accès à un locataire Azure Active Directory (Azure AD).

    Quel locataire utiliser ?

    * Utiliser votre locataire Power BI d’entreprise existant ?
    * Utiliser un locataire distinct pour votre application ?
    * Utiliser un locataire distinct pour chaque client ?

    Si vous décidez de créer un locataire pour votre application, ou pour chaque client, consultez l’un des articles suivants :

    * [Créer un locataire Azure Active Directory](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Guide pratique pour obtenir un locataire Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant)

2. Créez un utilisateur dans ce nouveau locataire qui assume la fonction de compte d’application « maître ». Ce compte doit s’inscrire auprès de Power BI et doit avoir une licence Power BI Pro.

## <a name="accounts-within-azure-ad"></a>Comptes dans Azure AD

Les comptes suivants doivent exister dans votre locataire.

> [!NOTE]
> Ces comptes doivent disposer de licences Power BI Pro pour pouvoir utiliser des espaces de travail d’application.

1. Un utilisateur administrateur de locataire.

    Il est recommandé de lister l’administrateur de locataire en tant que membre pour l’espace de travail d’application d’incorporation.

2. Comptes pour les analystes qui créent du contenu.

    Ces utilisateurs doivent être assignés aux espaces de travail d’application en fonction des besoins.

3. Un compte d’utilisateur *maître* d’application, ou compte de service.

    Le backend d’application stocke les informations d’identification pour ce compte. Utilisez le compte *maître* pour l’acquisition d’un jeton Azure AD utilisable avec l’API REST Power BI. Ce compte sert à générer le jeton d’incorporation pour l’application. Le compte *maître* doit être un administrateur des espaces de travail d’application créés pour l’incorporation.

    **Ce compte est simplement un compte d’utilisateur normal de votre organisation, utilisé à des fins d’incorporation.**

## <a name="app-registration-and-permissions"></a>Autorisations et inscription d’application

Pour effectuer des appels d’API REST, inscrivez une application auprès d’Azure AD. Une configuration supplémentaire est appliquée dans le portail Microsoft Azure en plus de la page d’inscription d’application Power BI. Pour plus d’informations, consultez [Inscrire une application Azure AD pour incorporer du contenu Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

Nous vous recommandons d’inscrire l’application à l’aide du compte **maître** d’application.

## <a name="create-app-workspaces-required"></a>Créer des espaces de travail d’application (obligatoire)

Si votre application est utilisée par plusieurs clients, vous pouvez tirer parti des espaces de travail d’application pour fournir une meilleure isolation. Les tableaux de bord et rapports seraient isolés entre vos clients. Vous pourriez ensuite utiliser un compte Power BI par espace de travail d’application afin d’isoler encore davantage les expériences d’application entre vos clients, mais pour simplifier vous pouvez juste utiliser un seul compte.

> [!IMPORTANT]
> Vous ne pouvez pas utiliser un espace de travail personnel (« Mon espace de travail ») pour tirer parti de l’incorporation pour vos clients.

Vous avez besoin d’un utilisateur qui dispose d’une licence Pro pour créer un espace de travail d’application dans Power BI. L’utilisateur de Power BI qui crée l’espace de travail d’application est un administrateur de cet espace de travail par défaut. **Le compte *maître* d’application doit être un administrateur de l’espace de travail.**

## <a name="content-migration"></a>Migration de contenu

La migration du contenu de vos collections d’espaces de travail vers Power BI Embedded peut s’effectuer en parallèle avec votre solution actuelle et ne nécessite aucun temps d’arrêt.

Un **outil de migration** est disponible pour vous aider à copier le contenu du service Collections d’espace de travail Power BI vers Power BI Embedded, en particulier si vous avez de nombreux rapports. Pour plus d’informations, consultez [Outil de migration Power BI Embedded](migrate-tool.md).

La migration du contenu s’appuie principalement sur deux API.

1. Télécharger PBIX : cette API peut télécharger des fichiers PBIX qui ont été chargés dans Power BI après octobre 2016.
2. Importer PBIX : cette API charge tout fichier PBIX dans Power BI.

Pour obtenir certains extraits de code connexes, consultez [Extraits de code pour migrer du contenu à partir de Power BI Embedded](migrate-code-snippets.md).

### <a name="report-types"></a>Types de rapports

Il existe plusieurs types de rapports, chacun nécessitant un flux de migration différent.

#### <a name="cached-dataset-and-report"></a>Rapports et jeux de données mis en cache

Les jeux de données mis en cache font référence à des fichiers PBIX qui avaient importé des données, par opposition à une connexion active ou DirectQuery.

**Flux**

1. Appelez l’API Télécharger PBIX à partir de votre espace de travail Collections d’espaces de travail Power BI.
2. Enregistrez le fichier PBIX.
3. Appelez l’API Importer PBIX pour votre espace de travail Power BI Embedded.

#### <a name="directquery-dataset-and-report"></a>Rapports et jeux de données DirectQuery

**Flux**

1. Appelez https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources et enregistrez la chaîne de connexion reçue.
2. Appelez l’API Télécharger PBIX à partir de votre espace de travail Collections d’espaces de travail Power BI.
3. Enregistrez le fichier PBIX.
4. Appelez l’API Importer PBIX pour votre espace de travail Power BI Embedded.
5. Mettez à jour la chaîne de connexion en appelant POST  https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections.
6. Obtenez l’ID GW et l’ID de source de données en appelant GET https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources.
7. Mettez à jour les informations d’identification de l’utilisateur en appelant PATCH https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id}.

#### <a name="old-dataset-and-reports"></a>Anciens rapports et jeux de données

Les rapports chargés avant octobre 2016 ne prennent pas en charge la fonctionnalité Télécharger PBIX.

**Flux**

1. Obtenez le fichier PBIX à partir de votre environnement de développement (votre contrôle de code source interne).
2. Appelez l’API Importer PBIX pour votre espace de travail Power BI Embedded.

#### <a name="push-dataset-and-report"></a>Rapports et jeux de données de type Push

Télécharger PBIX ne prend pas en charge les jeux de données *d’API Push*. Les données des jeux de données API Push ne peuvent pas être déplacées du service Collections d’espaces de travail Power BI vers Power BI Embedded.

**Flux**

1. Appelez l’API « Créer un jeu de données » avec le jeu de données Json afin de créer le jeu de données de votre espace de travail Power BI Embedded.
2. Regénérez le rapport pour le jeu de données créé*.

En vous aidant de solutions de contournement, vous pouvez migrer le rapport d’API Push du service Collections d’espaces de travail Power BI vers Power BI Embedded en essayant ce qui suit :

1. Chargez un fichier PBIX factice sur votre espace de travail Collections d’espaces de travail Power BI.
2. Clonez le rapport d’API Push et liez-le au fichier PBIX factice de l’étape 1.
3. Téléchargez le rapport d’API Push avec le fichier PBIX factice.
4. Chargez le fichier PBIX factice sur votre espace de travail Power BI Embedded.
5. Créez le jeu de données Push dans votre espace de travail Power BI Embedded.
6. Reliez le rapport au jeu de données d’API Push.

## <a name="create-and-upload-new-reports"></a>Créez et chargez les nouveaux rapports.

En plus du contenu que vous avez migré de Collections d’espaces de travail Power BI, vous pouvez créer vos rapports et jeux de données à l’aide de Power BI Desktop, puis publier ces rapports dans un espace de travail d’application. L’utilisateur final qui publie les rapports doit avoir une licence Power BI Pro lui permettant de publier sur un espace de travail d’application.

## <a name="rebuild-your-application"></a>Regénérer votre application

1. Modifiez votre application pour qu’elle utilise les API REST Power BI et l’emplacement des rapports dans powerbi.com.

2. Regénérez votre authentification AuthN/AuthZ à l’aide du compte *maître* de votre application. Vous pouvez tirer parti de l’utilisation d’un [jeton d’incorporation](https://msdn.microsoft.com/library/mt784614.aspx) pour permettre à cet utilisateur d’agir au nom d’autres utilisateurs.

3. Incorporez vos rapports à partir de Power BI Embedded dans votre application. Pour plus d’informations, consultez [Guide pratique pour incorporer vos tableaux de bord, rapports et vignettes Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

## <a name="map-your-users-to-a-power-bi-user"></a>Mapper les utilisateurs à un utilisateur de Power BI

Dans votre application, vous mappez les utilisateurs que vous gérez dans l’application à des informations d’identification Power BI *maître* pour les besoins de votre application. Les informations d’identification pour ce compte *maître* Power BI sont stockées dans votre application et utilisées pour créer des jetons d’incorporation.

## <a name="what-to-do-when-you-are-ready-for-production"></a>Que faire quand vous êtes prêt pour la production ?

Quand vous êtes prêt à passer en production, vous devez effectuer les opérations suivantes :

- Si vous utilisez un locataire distinct pour le développement, vous devez vérifier que vos espaces de travail d’application, ainsi que les tableaux de bord et les rapports, sont disponibles dans votre environnement de production. Vérifiez que vous avez créé l’application dans Azure AD pour votre locataire de production, et attribué les autorisations d’application appropriées comme indiqué à l’étape 1.

- Achetez une capacité adaptée à vos besoins. Vous pouvez utiliser le [livre blanc sur la planification de la capacité analytique Embedded](https://aka.ms/pbiewhitepaper) pour aider à comprendre ce dont vous pourriez avoir besoin. Une fois prêt, vous pouvez acheter une ressource Power BI Embedded dans le portail Azure.

- Modifiez l’espace de travail d’application et affectez-le à une capacité sous Avancé.

    ![Affecter l’espace de travail d’application à une capacité dans powerbi.com](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- Déployez votre application mise à jour en production et commencez l’incorporation des rapports à partir de Power BI Embedded.

## <a name="after-migration"></a>Après la migration

Un nettoyage est nécessaire dans le service Collections d’espaces de travail Power BI.

- Supprimez tous les espaces de travail de la solution déployée dans le service Azure Collections d’espaces de travail Power BI.
- Supprimez toutes les collections d’espaces de travail qui existent dans Azure.

## <a name="next-steps"></a>Étapes suivantes

Félicitations ! Votre application a maintenant été migrée vers Power BI Embedded. Pour plus d’informations sur la façon d’incorporer vos tableaux de bord, rapports et jeux de données Power BI, consultez [Guide pratique pour incorporer vos tableaux de bord, rapports et vignettes Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Des questions ? [Essayer d’interroger la communauté Power BI](http://community.powerbi.com/)