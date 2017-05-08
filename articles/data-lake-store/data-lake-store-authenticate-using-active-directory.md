---
title: "Authentification de service à service : Data Lake Store à l’aide d’Azure Active Directory | Microsoft Docs"
description: "Découvrez comment authentifier le service à service auprès de Data Lake Store à l’aide d’Azure Active Directory"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 820b7c5d-4863-4225-9bd1-df4d8f515537
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 1d712ef6987a4af2014bedb54378f288bcf535a8
ms.lasthandoff: 04/22/2017


---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Authentification de service à service auprès de Data Lake Store à l’aide d’Azure Active Directory
> [!div class="op_single_selector"]
> * [Authentification de service à service](data-lake-store-authenticate-using-active-directory.md)
> * [Authentification de l’utilisateur final](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store utilise Azure Active Directory pour l’authentification. Avant de créer une application qui fonctionne avec Azure Data Lake Store ou Azure Data Lake Analytics, vous devez commencer par déterminer comment vous voulez authentifier votre application avec Azure Active Directory (Azure AD). Les deux options principales disponibles sont :

* Authentification de l’utilisateur final 
* Authentification de service à service (cet article) 

En raison de ces deux options, votre application est fournie avec un jeton OAuth 2.0, qui est attaché à chaque demande adressée à Azure Data Lake Store ou Azure Data Lake Analytics.

Cet article traite de la création d’une **application web Azure AD pour l’authentification de service à service**. Pour obtenir des instructions sur la configuration de l’application Azure AD pour l’authentification de l’utilisateur final, consultez [Authentification d’utilisateur final auprès de Data Lake Store à l’aide d’Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Conditions préalables
* Un abonnement Azure. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Étape 1 : Créer une application web Active Directory

Créez et configurez une application web Azure AD pour l’authentification de service à service auprès d’Azure Data Lake Store à l’aide d’Azure Active Directory. Pour obtenir des instructions, consultez la page [Créer une application Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

Si vous suivez les instructions du lien ci-dessus, veillez à sélectionner le type d’application **Application web / API**, comme l’illustre la capture d’écran ci-dessous.

![Créer une application web](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Créer une application web")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Étape 2 : obtenir un ID d’application, une clé d’authentification et un ID de locataire
Lors d’une connexion par programmation, vous aurez besoin de l’ID de votre application. Si l’application s’exécute sous ses propres informations d’identification, vous aurez également besoin d’une clé d’authentification.

* Pour obtenir des instructions afin de récupérer l’ID de l’application et la clé d’authentification (également appelée clé secrète client), consultez la page [Obtenir la clé d’authentification et l’ID d’une application](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Pour obtenir des instructions afin de récupérer l’ID du locataire, consultez [Obtenir l’ID du locataire](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>Étape 3 : Affecter l’application Azure AD au dossier ou fichier de compte Azure Data Lake Store (uniquement pour l’authentification de service à service)
1. Connectez-vous au nouveau [portail Azure](https://portal.azure.com) et ouvrez le compte Azure Data Lake Store que vous souhaitez associer à l’application Azure Active Directory que vous avez créée.
2. Dans le panneau de votre compte Data Lake Store, cliquez sur **Explorateur de données**.
   
    ![Créer des répertoires dans un compte Data Lake Store](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Créer des répertoires dans un compte Data Lake Store")
3. Dans le panneau **Explorateur de données**, cliquez sur le fichier ou dossier pour lequel vous souhaitez fournir un accès à l’application Azure AD, puis cliquez sur **Accès**. Pour configurer l’accès à un fichier, vous devez cliquer sur **Accès** dans le panneau **Aperçu du fichier**.
   
    ![Définir des ACL sur le système de fichiers Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Définir des ACL sur le système de fichiers Data Lake")
4. Le panneau **Accès** liste les accès standard et personnalisés déjà affectés à la racine. Cliquez sur l'icône **Ajouter** pour ajouter des ACL personnalisées.
   
    ![Lister les accès standard et personnalisés](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Lister les accès standard et personnalisés")
5. Cliquez sur l’icône **Ajouter** pour ouvrir le panneau **Ajouter un accès personnalisé**. Dans ce panneau, cliquez sur **Sélectionner un utilisateur ou un groupe**, puis, dans le panneau **Sélectionner un utilisateur ou un groupe**, recherchez le groupe de sécurité Azure Active Directory que vous avez créé précédemment. Si la liste de groupes est longue, utilisez la zone de texte en haut pour filtrer par nom de groupe. Cliquez sur le groupe que vous souhaitez ajouter, puis cliquez sur **Sélectionner**.
   
    ![Ajouter un groupe](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Ajouter un groupe")
6. Cliquez sur **Sélectionner des autorisations**, sélectionnez les autorisations et indiquez si vous souhaitez attribuer les autorisations en tant qu’ACL par défaut et/ou d’accès. Cliquez sur **OK**.
   
    ![Affecter des autorisations à un groupe](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Affecter des autorisations à un groupe")
   
    Pour plus d’informations sur les autorisations dans Data Lake Store et sur les ACL par défaut ou d’accès, consultez [Contrôle d’accès dans Azure Data Lake Store](data-lake-store-access-control.md).
7. Dans le panneau **Ajouter un accès personnalisé**, cliquez sur **OK**. Le groupe récemment créé, avec les autorisations associées, sera désormais répertorié dans le panneau **Accès** .
   
    ![Affecter des autorisations à un groupe](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Affecter des autorisations à un groupe")

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Étape 4 : Obtenir le point de terminaison de jeton OAuth 2.0 (uniquement pour les applications Java)

1. Connectez-vous au nouveau [portail Azure](https://portal.azure.com) et cliquez sur Active Directory dans le volet gauche.

2. Dans le volet gauche, cliquez sur **Inscriptions d’applications**.

3. En haut du panneau Inscriptions des applications, cliquez sur **Points de terminaison**.

    ![Point de terminaison de jeton OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Point de terminaison de jeton OAuth")

4. Dans la liste des points de terminaison, copiez le point de terminaison de jeton OAuth 2.0.

    ![Point de terminaison de jeton OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Point de terminaison de jeton OAuth")   

## <a name="next-steps"></a>Étapes suivantes
Dans cet article vous a créé une application web Azure AD et regroupé les informations nécessaires dans les applications clientes que vous créez à l’aide du Kit de développement logiciel (SDK) .NET, du Kit de développement Logiciel Java, etc. Vous pouvez à présent passer aux articles suivants qui traitent de l’utilisation de l’application web Azure AD, d’abord pour vous authentifier auprès de Data Lake Store, et ensuite pour effectuer d’autres opérations sur le magasin.

* [Prise en main d'Azure Data Lake Store avec le Kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)
* [Prise en main d’Azure Data Lake Store à l’aide du Kit de développement logiciel (SDK) Java](data-lake-store-get-started-java-sdk.md)

Cet article vous a présenté les étapes de base nécessaires au déploiement d’un principal d’utilisateur pour votre application. Vous pouvez consulter les articles suivants pour des informations complémentaires :
* [Utiliser PowerShell pour créer un principal du service](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Utiliser l’authentification de certificat pour l’authentification du principal du service](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal#create-service-principal-with-certificate)
* [Autres méthodes d’authentification sur Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-authentication-scenarios)



