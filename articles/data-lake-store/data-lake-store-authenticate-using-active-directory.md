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
ms.date: 01/10/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9019a4115e81a7d8f1960098b1138cd437a0460b
ms.openlocfilehash: dac6c9f3be7b4535f8cb30a9ec0c1e398ca5ff28


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

Cet article traite de la création d’une application web Azure AD pour l’authentification de service à service. Pour obtenir des instructions sur la configuration de l’application Azure AD pour l’authentification de l’utilisateur final, consultez [Authentification d’utilisateur final auprès de Data Lake Store à l’aide d’Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Conditions préalables
* Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-active-directory-application"></a>Créer une application Active Directory
Cette section décrit comment créer et configurer une application web Azure AD pour l’authentification de service à service auprès d’Azure Data Lake Store à l’aide d’Azure Active Directory. Notez que la création d’une « application Active Directory » crée un principal du service pour vous, il ne crée pas réellement une application ou du code.

### <a name="step-1-create-an-azure-active-directory-application"></a>Étape 1 : Créer une application Azure Active Directory
1. Connectez-vous à votre compte Azure via le [portail classique](https://manage.windowsazure.com/).
2. Sélectionnez **Active Directory** dans le volet gauche.
   
     ![sélectionner Active Directory](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
3. Sélectionnez l’annuaire Active Directory que vous voulez utiliser pour la création de l’application. Si vous avez plusieurs Active Directory, il est généralement recommandé de créer l’application dans le répertoire où se trouve votre abonnement. Vous pouvez accorder l’accès aux ressources de votre abonnement uniquement à des applications résidant dans le même répertoire que celui-ci.  
   
     ![choisir l’annuaire](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
4. Pour afficher les applications dans votre annuaire, cliquez sur **Applications**.
   
     ![afficher les applications](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)
5. Si vous n’avez pas préalablement créé d’application dans cet annuaire, vous devez voir quelque chose de similaire à l’image suivante. Cliquez sur **AJOUTER UNE APPLICATION**.
   
     ![Ajouter une application](./media/data-lake-store-authenticate-using-active-directory/create-application.png)
   
     Ou, cliquez sur **Ajouter** dans le volet inférieur.
   
     ![ajouter](./media/data-lake-store-authenticate-using-active-directory/add-icon.png)
6. Renseignez le nom de l’application et sélectionnez le type d’application que vous voulez créer. Pour ce didacticiel, créez une **APPLICATION WEB ET/OU API WEB** , puis cliquez sur le bouton Suivant.
   
     ![nommer l’application](./media/data-lake-store-authenticate-using-active-directory/tell-us-about-your-application.png)

    > [!TIP]
    > Fournissez un nom pour l’application, qui facilite la recherche de celle-ci. Plus loin dans ce didacticiel, vous allez rechercher cette application et l’assigner à un compte Data Lake Store.
    > 
    > 

7. Renseignez les propriétés de votre application. Pour **URL DE CONNEXION**, indiquez l’URI d’un site web qui décrit votre application. L’existence du site web n’est pas validée. 
   Pour **URI ID d’application**, indiquez l’URI qui identifie votre application.
   
     ![propriétés de l’application](./media/data-lake-store-authenticate-using-active-directory/app-properties.png)
   
    Cliquez sur la case à cocher pour fermer l’Assistant et créer l’application.

### <a name="step-2-get-client-id-client-secret-and-token-endpoint"></a>Étape 2 : Obtenir l’ID client, la clé secrète client et le point de terminaison de jeton
Lors d’une connexion par programmation, vous aurez besoin de l’ID de votre application. Si l’application s’exécute sous ses propres informations d’identification, vous aurez également besoin d’une clé d’authentification.

1. Cliquez sur l’onglet **Configurer** pour configurer le mot de passe de votre application.
   
     ![configurer l’application](./media/data-lake-store-authenticate-using-active-directory/application-configure.png)
2. Copiez l’ **ID CLIENT**.
   
     ![ID CLIENT](./media/data-lake-store-authenticate-using-active-directory/client-id.png)
3. Si l’application s’exécute sous ses propres informations d’identification, faites défiler jusqu’à la section **Clés** et sélectionnez la durée de validité de votre mot de passe.
   
     ![clés](./media/data-lake-store-authenticate-using-active-directory/create-key.png)
4. Sélectionnez **Enregistrer** pour créer votre clé.
   
    ![Enregistrer](./media/data-lake-store-authenticate-using-active-directory/save-icon.png)
   
    La clé enregistrée s’affiche. Vous pouvez la copier. Copiez la clé dès à présent, car par la suite, vous ne pourrez plus la récupérer.
   
    ![clé enregistrée](./media/data-lake-store-authenticate-using-active-directory/save-key.png)
5. Récupérez le point de terminaison de jeton en sélectionnant **Afficher les points de terminaison** au bas de l’écran et en récupérant la valeur du champ **Point de terminaison de jeton OAuth 2.0** comme indiqué ci-dessous.  
   
    ![ID client](./media/data-lake-store-authenticate-using-active-directory/save-tenant.png)

#### <a name="note-down-the-following-properties-that-you-will-need-for-the-next-steps"></a>Notez les propriétés suivantes dont vous aurez besoin pour les étapes suivantes :
1. Nom de l’ID de l’application web créée à l’étape 1.6 ci-dessus
2. ID de client récupéré à l’étape 2.2 ci-dessus
3. Clé créée à l’étape 2.4 ci-dessus
4. ID de client récupéré à l’étape 2.5 ci-dessus

### <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>Étape 3 : Affecter l’application Azure AD au dossier ou fichier de compte Azure Data Lake Store (uniquement pour l’authentification de service à service)
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

## <a name="next-steps"></a>Étapes suivantes
Dans cet article vous a créé une application web Azure AD et regroupé les informations nécessaires dans les applications clientes que vous créez à l’aide du Kit de développement logiciel (SDK) .NET, du Kit de développement Logiciel Java, etc. Vous pouvez à présent passer aux articles suivants qui traitent de l’utilisation de l’application web Azure AD, d’abord pour vous authentifier auprès de Data Lake Store, et ensuite pour effectuer d’autres opérations sur le magasin.

* [Prise en main d'Azure Data Lake Store avec le Kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)
* [Prise en main d’Azure Data Lake Store à l’aide du Kit de développement logiciel (SDK) Java](data-lake-store-get-started-java-sdk.md)

Cet article vous a présenté les étapes de base nécessaires au déploiement d’un principal d’utilisateur pour votre application. Vous pouvez consulter les articles suivants pour des informations complémentaires :
* [Utiliser PowerShell pour créer un principal du service](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Utiliser l’authentification de certificat pour l’authentification du principal du service](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal#create-service-principal-with-certificate)
* [Autres méthodes d’authentification sur Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-authentication-scenarios)





<!--HONumber=Jan17_HO4-->


