---
title: S’authentifier sur Data Lake Store à l’aide d’Azure Active Directory | Microsoft Docs
description: En savoir plus sur la façon de s’authentifier sur Data Lake Store à l’aide d’Azure Active Directory
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: nitinme

---
# <a name="authenticate-with-data-lake-store-using-azure-active-directory"></a>S’authentifier sur Data Lake Store à l’aide d’Azure Active Directory
Azure Data Lake Store utilise Azure Active Directory pour l’authentification. Avant de créer une application qui fonctionne avec Azure Data Lake Store ou Azure Data Lake Analytics, vous devez commencer par déterminer comment vous voulez authentifier votre application avec Azure Active Directory (Azure AD). Les deux options principales disponibles sont :

* Authentification de l’utilisateur final, et 
* Authentification de service à service. 

En raison de ces deux options, votre application est fournie avec un jeton OAuth 2.0, qui est attaché à chaque demande adressée à Azure Data Lake Store ou Azure Data Lake Analytics.

## <a name="prerequisites"></a>Composants requis
* Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* Votre ID d’abonnement. Vous pouvez le récupérer à partir du portail Azure. Par exemple, il est disponible à partir du panneau de compte Data Lake Store.
  
    ![Obtenir l’ID d’abonnement](./media/data-lake-store-authenticate-using-active-directory/get-subscription-id.png)
* Votre nom de domaine Azure AD. Vous pouvez le récupérer en pointant la souris dans le coin supérieur droit du Portail Azure. 
  
    ![Obtenir le domaine AAD](./media/data-lake-store-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>Authentification de l’utilisateur final
Il s’agit de l’approche recommandée si vous souhaitez qu’un utilisateur final se connecte à votre application via Azure AD. Votre application sera en mesure d’accéder aux ressources Azure avec le même niveau d’accès que l’utilisateur final qui s’est connecté. Votre utilisateur final devra fournir ses informations d’identification régulièrement pour que votre application conserve l’accès.

Conséquence de la connexion de l’utilisateur final : votre application reçoit un jeton d’accès et un jeton d’actualisation. Le jeton d’accès est lié à chaque demande adressée au Data Lake Store ou à Data Lake Analytics et, par défaut, il est valide pendant une heure. Le jeton d’actualisation peut être utilisé pour obtenir un nouveau jeton d’accès et, par défaut, il est valide pendant deux semaines au maximum, s’il est régulièrement utilisé. Vous pouvez utiliser deux approches différentes pour la connexion de l’utilisateur final.

### <a name="using-the-oauth-2.0-pop-up"></a>Utilisation de la fenêtre contextuelle OAuth 2.0
Votre application peut déclencher une fenêtre contextuelle d’autorisation OAuth 2.0 dans laquelle l’utilisateur final peut entrer ses informations d’identification. Cette fenêtre contextuelle fonctionne également avec le processus d’authentification à 2 facteurs Azure AD (TFA), si nécessaire. 

> [!NOTE]
> Cette méthode n’est pas encore prise en charge dans la bibliothèque d’authentification Azure AD (ADAL) pour Python ou Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Transmission directe des informations d’identification de l’utilisateur
Votre application peut fournir directement des informations d’identification de l’utilisateur à Azure AD. Cette méthode fonctionne uniquement avec les comptes d’utilisateur dotés d’ID d’organisation. Elle n’est pas compatible avec les comptes d’utilisateur personnels ou « live ID », notamment ceux se terminant par @outlook.com ou @live.com.. En outre, cette méthode n’est pas compatible avec les comptes d’utilisateur qui nécessitent l’authentification à deux facteurs Azure AD (TFA).

### <a name="what-do-i-need-to-use-this-approach?"></a>De quoi ai-je besoin pour utiliser cette approche ?
* Le nom de domaine Azure AD (déjà répertorié dans la configuration requise de cet article).
* **Application cliente native**Azure AD. 
* ID client pour l’application cliente native Azure AD.
* URI de redirection pour l’application cliente native Azure AD. 

Pour obtenir des instructions sur la création d’une application Azure AD et la récupération de l’ID client, consultez [Créer une application Active Directory](../resource-group-create-service-principal-portal.md#create-an-active-directory-application). 

> [!NOTE]
> Les instructions fournies dans les liens ci-dessus s’appliquent à une application web Azure AD. Cependant, la procédure est exactement la même si vous choisissez de créer une application cliente native à la place.
> 
> 

## <a name="service-to-service-authentication"></a>Authentification de service à service
Il s’agit de l’approche recommandée si vous souhaitez que votre application s’authentifie automatiquement avec Azure AD, sans que l’utilisateur final ait besoin de fournir ses informations d’identification. Votre application sera en mesure de s’authentifier tant que ses informations d’identification seront valides, paramètre qui peut être personnalisé pour être de plusieurs années.

### <a name="what-do-i-need-to-use-this-approach?"></a>De quoi ai-je besoin pour utiliser cette approche ?
* Le nom de domaine Azure AD (déjà répertorié dans la configuration requise de cet article).
* **Application web**Azure AD.
* ID client pour l’application web Azure AD.
  
  > [!NOTE]
  > Pour obtenir des instructions sur la création d’une application Azure AD et la récupération de l’ID client, consultez [Créer une application Active Directory](../resource-group-create-service-principal-portal.md#create-an-active-directory-application).
  > 
  > 
* Configurez l’application web Azure AD pour qu’elle utilise soit le secret du client soit un certificat. Pour créer une application web à l’aide d’un certificat, consultez [Créer un principal de service avec certificat](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).
* Activez l’accès pour l’application web Azure AD sur le fichier/dossier Data Lake Store ou le compte Data Lake Analytics que vous souhaitez utiliser. Pour obtenir des instructions sur la manière de fournir un accès à une application Azure AD à un fichier/dossier Data Lake Store, consultez [Affecter des utilisateurs ou un groupe de sécurité comme ACL au système de fichiers Azure Data Lake Store](data-lake-store-secure-data.md#filepermissions).

## <a name="next-steps"></a>Étapes suivantes
* [Prise en main d'Azure Data Lake Store avec le Kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)
* [Prise en main d’Azure Data Lake Store à l’aide du Kit de développement logiciel (SDK) Java](data-lake-store-get-started-java-sdk.md)

<!--HONumber=Oct16_HO2-->


