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
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 0f6af54b351235390afa88f1ce156abd839a723f
ms.lasthandoff: 03/03/2017


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

Cet article traite de la création d’une **application native Azure AD pour l’authentification de l’utilisateur final**. Pour obtenir des instructions sur la configuration de l’application Azure AD pour l’authentification de service à service, voir [Authentification de service à service avec Data Lake Store à l’aide d’Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Composants requis
* Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

* Votre ID d’abonnement. Vous pouvez le récupérer à partir du portail Azure. Par exemple, il est disponible à partir du panneau de compte Data Lake Store.
  
    ![Obtenir l’ID d’abonnement](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Votre nom de domaine Azure AD. Vous pouvez le récupérer en pointant la souris dans le coin supérieur droit du Portail Azure. Dans la capture d’écran ci-dessous, le nom de domaine est **contoso.onmicrosoft.com**, et le GUID entre crochets est l’ID client. 
  
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
* Application native **Azure AD**
* ID client de l’application native Azure AD
* URI de réponse de l’application native Azure AD
* Définir des autorisations déléguées


## <a name="step-1-create-an-active-directory-web-application"></a>Étape 1 : Créer une application web Active Directory

Créez et configurez une application native Azure AD pour l’authentification de l’utilisateur final auprès d’Azure Data Lake Store à l’aide d’Azure Active Directory. Pour obtenir des instructions, consultez la page [Créer une application Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Si vous suivez les instructions du lien ci-dessus, veillez à sélectionner le type d’application **Native**, comme l’illustre la capture d’écran ci-dessous.

![Créer une application web](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Créer une application native")

## <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>Étape 2 : obtenir un ID client et un URI de réponse, et définir des autorisations déléguées

Consultez la page [Obtenir l’ID client](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key) pour récupérer l’ID client (également appelé l’ID de l’application) de l’application native Azure AD.

Pour récupérer l’URI de redirection, suivez les étapes ci-dessous.

1. Sur le Portail Azure, sélectionnez **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis recherchez et cliquez sur l’application native Azure AD que vous venez de créer.

2. Dans le panneau **Paramètres** de l’application, cliquez sur **URI de redirection**.

    ![Obtenir un URI de redirection](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Copiez la valeur affichée.


## <a name="step-3-set-permissions"></a>Étape 3 : Définir des autorisations

1. Sur le Portail Azure, sélectionnez **Azure Active Directory**, cliquez sur **Inscriptions des applications**, puis recherchez et cliquez sur l’application native Azure AD que vous venez de créer.

2. Dans le panneau **Paramètres** de l’application, cliquez sur **Autorisations requises**, puis sur **Ajouter**.

    ![ID CLIENT](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. Dans le panneau **Ajouter l’accès des API**, cliquez sur **Sélectionner une API**, puis sur **Azure Data Lake** et enfin sur **Sélectionner**.

    ![ID CLIENT](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  Dans le panneau **Ajouter l’accès des API**, cliquez sur **Sélectionner des autorisations**, cochez la case pour accorder un **Accès complet à Data Lake Store**, puis cliquez sur **Sélectionner**.

    ![ID CLIENT](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Cliquez sur **Done**.

5. Répétez les deux dernières étapes pour accorder également des autorisations à **l’API Gestion des services Microsoft Azure**.
   
## <a name="next-steps"></a>Étapes suivantes
Dans cet article vous a créé une application web Azure AD et regroupé les informations nécessaires dans les applications clientes que vous créez à l’aide du Kit de développement logiciel (SDK) .NET, du Kit de développement Logiciel Java, etc. Vous pouvez à présent passer aux articles suivants qui traitent de l’utilisation de l’application web Azure AD, d’abord pour vous authentifier auprès de Data Lake Store, et ensuite pour effectuer d’autres opérations sur le magasin.

* [Prise en main d'Azure Data Lake Store avec le Kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)
* [Prise en main d’Azure Data Lake Store à l’aide du Kit de développement logiciel (SDK) Java](data-lake-store-get-started-java-sdk.md)


