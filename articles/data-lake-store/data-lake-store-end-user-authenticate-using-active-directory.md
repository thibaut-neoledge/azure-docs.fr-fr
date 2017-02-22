---
title: "Authentification d’utilisateur final : Data Lake Store à l’aide d’Azure Active Directory | Microsoft Docs"
description: "Découvrez comment authentifier l’utilisateur final auprès de Data Lake Store à l’aide d’Azure Active Directory"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ec586ecd-1b42-459e-b600-fadbb7b80a9b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9019a4115e81a7d8f1960098b1138cd437a0460b
ms.openlocfilehash: a50fc687a1738a55c3d22eb3e12060397c162e06


---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Authentification d’utilisateur final auprès de Data Lake Store à l’aide d’Azure Active Directory
> [!div class="op_single_selector"]
> * [Authentification de service à service](data-lake-store-authenticate-using-active-directory.md)
> * [Authentification de l’utilisateur final](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store utilise Azure Active Directory pour l’authentification. Avant de créer une application qui fonctionne avec Azure Data Lake Store ou Azure Data Lake Analytics, vous devez commencer par déterminer comment vous voulez authentifier votre application avec Azure Active Directory (Azure AD). Les deux options principales disponibles sont :

* Authentification de l’utilisateur final (cet article)
* Authentification de service à service

En raison de ces deux options, votre application est fournie avec un jeton OAuth 2.0, qui est attaché à chaque demande adressée à Azure Data Lake Store ou Azure Data Lake Analytics.

Cet article traite de la création d’une application web Azure AD pour l’authentification de l’utilisateur final. Pour obtenir des instructions sur la configuration de l’application Azure AD pour l’authentification de service à service, voir [Authentification de service à service avec Data Lake Store à l’aide d’Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Composants requis
* Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* Votre ID d’abonnement. Vous pouvez le récupérer à partir du portail Azure. Par exemple, il est disponible à partir du panneau de compte Data Lake Store.
  
    ![Obtenir l’ID d’abonnement](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)
* Votre nom de domaine Azure AD. Vous pouvez le récupérer en pointant la souris dans le coin supérieur droit du Portail Azure. Dans la capture d’écran ci-dessous, le nom de domaine est **contoso.microsoft.com**, et le GUID entre crochets est l’ID de client. 
  
    ![Obtenir le domaine AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>Authentification de l’utilisateur final
Il s’agit de l’approche recommandée si vous souhaitez qu’un utilisateur final se connecte à votre application via Azure AD. Votre application sera en mesure d’accéder aux ressources Azure avec le même niveau d’accès que l’utilisateur final qui s’est connecté. Votre utilisateur final devra fournir ses informations d’identification régulièrement pour que votre application conserve l’accès.

Conséquence de la connexion de l’utilisateur final : votre application reçoit un jeton d’accès et un jeton d’actualisation. Le jeton d’accès est lié à chaque demande adressée au Data Lake Store ou à Data Lake Analytics et, par défaut, il est valide pendant une heure. Le jeton d’actualisation peut être utilisé pour obtenir un nouveau jeton d’accès et, par défaut, il est valide pendant deux semaines au maximum, s’il est régulièrement utilisé. Vous pouvez utiliser deux approches différentes pour la connexion de l’utilisateur final.

### <a name="using-the-oauth-20-pop-up"></a>Utilisation de la fenêtre contextuelle OAuth 2.0
Votre application peut déclencher une fenêtre contextuelle d’autorisation OAuth 2.0 dans laquelle l’utilisateur final peut entrer ses informations d’identification. Cette fenêtre contextuelle fonctionne également avec le processus d’authentification à&2; facteurs Azure AD (TFA), si nécessaire. 

> [!NOTE]
> Cette méthode n’est pas encore prise en charge dans la bibliothèque d’authentification Azure AD (ADAL) pour Python ou Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Transmission directe des informations d’identification de l’utilisateur
Votre application peut fournir directement des informations d’identification de l’utilisateur à Azure AD. Cette méthode fonctionne uniquement avec les comptes d’utilisateur dotés d’ID d’organisation. Elle n’est pas compatible avec les comptes d’utilisateur personnels ou « live ID », notamment ceux se terminant par @outlook.com ou @live.com. En outre, cette méthode n’est pas compatible avec les comptes d’utilisateur qui nécessitent l’authentification à&2; facteurs Azure AD (TFA).

### <a name="what-do-i-need-to-use-this-approach"></a>De quoi ai-je besoin pour utiliser cette approche ?
* Votre nom de domaine Azure AD. Celui-ci est déjà répertorié dans les conditions préalables de cet article.
* **Application web** Azure AD
* ID client pour l’application web Azure AD
* URI de réponse pour l’application web Azure AD
* Définir des autorisations déléguées

Pour obtenir des instructions sur la manière de créer une application web Azure AD et de la configurer conformément aux exigences ci-dessus, voir la section [Créer une application Active Directory](#create-an-active-directory-application) ci-dessous. 

## <a name="create-an-active-directory-application"></a>Créer une application Active Directory
Cette section décrit comment créer et configurer une application web Azure AD pour l’authentification de l’utilisateur final auprès d’Azure Data Lake Store à l’aide d’Azure Active Directory.

### <a name="step-1-create-an-azure-active-directory-application"></a>Étape 1 : créez une application Azure Active Directory
> [!NOTE]
> Les étapes ci-dessous utilisent le portail Azure. Vous pouvez également créer une application Azure AD en utilisant [Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md) ou [Azure CLI](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md).
> 
> 

1. Connectez-vous à votre compte Azure via le [portail classique](https://manage.windowsazure.com/).
2. Sélectionnez **Active Directory** dans le volet gauche.
   
     ![sélectionner Active Directory](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory.png)
3. Sélectionnez l’annuaire Active Directory que vous voulez utiliser pour la création de l’application. Si vous avez plusieurs Active Directory, il est généralement recommandé de créer l’application dans le répertoire où se trouve votre abonnement. Vous pouvez accorder l’accès aux ressources de votre abonnement uniquement à des applications résidant dans le même répertoire que celui-ci.  
   
     ![choisir l’annuaire](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory-details.png)
4. Pour afficher les applications dans votre annuaire, cliquez sur **Applications**.
   
     ![afficher les applications](./media/data-lake-store-end-user-authenticate-using-active-directory/view-applications.png)
5. Si vous n’avez pas préalablement créé d’application dans cet annuaire, vous devez voir quelque chose de similaire à l’image suivante. Cliquez sur **AJOUTER UNE APPLICATION**.
   
     ![Ajouter une application](./media/data-lake-store-end-user-authenticate-using-active-directory/create-application.png)
   
     Ou, cliquez sur **Ajouter** dans le volet inférieur.
   
     ![ajouter](./media/data-lake-store-end-user-authenticate-using-active-directory/add-icon.png)
6. Renseignez le nom de l’application et sélectionnez le type d’application que vous voulez créer. Pour ce didacticiel, créez une **APPLICATION WEB ET/OU API WEB** , puis cliquez sur le bouton Suivant.
   
     ![nommer l’application](./media/data-lake-store-end-user-authenticate-using-active-directory/tell-us-about-your-application.png)
7. Renseignez les propriétés de votre application. Pour **URL DE CONNEXION**, indiquez l’URI d’un site web qui décrit votre application. L’existence du site web n’est pas validée. 
   Pour **URI ID d’application**, indiquez l’URI qui identifie votre application.
   
     ![propriétés de l’application](./media/data-lake-store-end-user-authenticate-using-active-directory/app-properties.png)
   
    Cliquez sur la case à cocher pour fermer l’Assistant et créer l’application.

### <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>Étape 2 : obtenir un ID client et un URI de réponse, et définir des autorisations déléguées
1. Cliquez sur l’onglet **Configurer** pour configurer le mot de passe de votre application.
   
     ![configurer l’application](./media/data-lake-store-end-user-authenticate-using-active-directory/application-configure.png)
2. Copiez l’ **ID CLIENT**.
   
     ![ID CLIENT](./media/data-lake-store-end-user-authenticate-using-active-directory/client-id.png)
3. Dans la section **Authentification unique**, copiez l’**URI de réponse**.
   
    ![ID CLIENT](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-get-reply-uri.png)
4. Dans **Autorisations pour d’autres applications**, cliquez sur **Ajouter une application**.
   
    ![ID CLIENT](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)
5. Dans l’Assistant **Autorisations pour d’autres applications**, sélectionnez **Azure Data Lake** et **API** **de gestion des services Windows Azure**, puis cliquez sur la coche.
6. Par défaut l’option **Autorisations déléguées** pour les services nouvellement ajoutés est définie sur zéro. Cliquez sur la liste déroulante **Autorisations déléguées** pour Azure Data Lake et Service de gestion Windows Azure, puis sélectionnez les cases à cocher disponibles pour définir les valeurs sur 1. Le résultat doit avoir l’aspect suivant.
   
     ![ID CLIENT](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
7. Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article vous a créé une application web Azure AD et regroupé les informations nécessaires dans les applications clientes que vous créez à l’aide du Kit de développement logiciel (SDK) .NET, du Kit de développement Logiciel Java, etc. Vous pouvez à présent passer aux articles suivants qui traitent de l’utilisation de l’application web Azure AD, d’abord pour vous authentifier auprès de Data Lake Store, et ensuite pour effectuer d’autres opérations sur le magasin.

* [Prise en main d'Azure Data Lake Store avec le Kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)
* [Prise en main d’Azure Data Lake Store à l’aide du Kit de développement logiciel (SDK) Java](data-lake-store-get-started-java-sdk.md)




<!--HONumber=Jan17_HO4-->


