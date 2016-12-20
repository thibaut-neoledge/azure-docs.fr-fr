---
title: "Configuration requise pour accéder à l’API de création de rapports Azure AD. | Microsoft Docs"
description: "En savoir plus sur la configuration requise pour accéder à l’API de création de rapports Azure AD"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b0f23cc578fb946247d492b9dee92cf7a9daafa1


---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Configuration requise pour accéder à l’API de création de rapports Azure AD
Les [API de création de rapports Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) vous fournissent un accès par programme aux données via un ensemble d’API REST. Vous pouvez appeler ces API à partir de divers outils et langages de programmation.

L’API de création de rapports utilise [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) pour autoriser l’accès aux API web. 

Pour préparer votre accès à l’API de création de rapports, vous devez :

1. Créer une application dans votre client Azure AD 
2. Accorder les autorisations appropriées d’application pour accéder aux données Azure AD
3. Collecter les paramètres de configuration de votre annuaire

Si vous avez des questions, des problèmes ou des commentaires, veuillez contacter [Aide à la création de rapports AAD](mailto:aadreportinghelp@microsoft.com).

## <a name="create-an-azure-ad-application"></a>Créer une application Azure AD
Pour configurer votre annuaire et lui permettre d’accéder à l’API de création de rapports Azure AD, vous devez vous connecter au portail Azure Classic avec un compte d’administrateur d’abonnements Azure, également membre du rôle d’annuaire Administrateur général dans votre client Azure AD.

> [!IMPORTANT]
> Les applications qui s’exécutent sous les informations d’identification pourvues de privilèges d’administrateur de ce type peuvent être très puissantes. Veillez donc à sécuriser les informations d’identification d’ID/du secret de l’application.
> 
> 

1. Dans le volet de navigation gauche du [portail Azure Classic](https://manage.windowsazure.com), cliquez sur **Active Directory**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Dans la liste **active directory** , sélectionnez votre annuaire.
3. Dans le menu supérieur, cliquez sur **Applications**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/02.png) 
4. Dans la barre inférieure, cliquez sur **Ajouter**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/03.png) 
5. Sur la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application développée par mon organisation**. 
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/04.png) 
6. Dans la boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit : 
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/05.png) 
   
    a. Dans la zone de texte **Nom** , entrez un nom (par exemple : Application API Création de rapports).
   
    b. Sélectionnez **Application Web et/ou API Web**.
   
    c. Cliquez sur **Suivant**.
7. Dans la boîte de dialogue **Propriétés de l’application** , effectuez les opérations suivantes : 
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/06.png) 
   
    a. Dans la zone de texte **URL d’authentification**, tapez `https://localhost`.
   
    b. Dans la zone de texte **URL ID d’application**, tapez ```https://localhost/ReportingApiApp```.
   
    c. Cliquez sur **Terminé**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Autorisation d'utilisation de l'API pour votre application
1. Dans le volet de navigation gauche du [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur **Active Directory**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Dans la liste **active directory** , sélectionnez votre annuaire.
3. Dans le menu supérieur, cliquez sur **Applications**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/02.png)
4. Dans la liste des applications, sélectionnez l’application nouvellement créée.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/07.png)
5. Dans le menu situé en haut, cliquez sur **Configurer**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/08.png)
6. Dans la section **Autorisations pour d’autres applications**, pour la ressource **Azure Active Directory**, cliquez sur la liste déroulante **Autorisations d’application**, puis sélectionnez **Lire les données du répertoire**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/09.png)
7. Dans la barre inférieure, cliquez sur **Enregistrer**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/10.png)

## <a name="gather-configuration-settings-from-your-directory"></a>Collecter les paramètres de configuration de votre annuaire
Cette section vous montre comment obtenir les paramètres suivants à partir de votre annuaire :

* Nom de domaine
* ID client
* Clé secrète client

Ces valeurs sont nécessaires lors de la configuration des appels à l’API de création de rapports. 

### <a name="get-your-domain-name"></a>Obtenir votre nom de domaine
1. Dans le volet de navigation gauche du [portail Azure Classic](https://manage.windowsazure.com), cliquez sur **Active Directory**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Dans la liste **active directory** , sélectionnez votre annuaire.
3. Dans le menu situé en haut de l’écran, cliquez sur **Domaines**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/11.png) 
4. Dans la colonne **Nom de domaine** , copiez le nom de votre domaine.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/12.png) 

### <a name="get-the-applications-client-id"></a>Obtenir l’ID client de l’application
1. Dans le volet de navigation gauche du [portail Azure Classic](https://manage.windowsazure.com), cliquez sur **Active Directory**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Dans la liste **active directory** , sélectionnez votre annuaire.
3. Dans le menu supérieur, cliquez sur **Applications**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/02.png) 
4. Dans la liste des applications, sélectionnez l’application nouvellement créée.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/07.png)
5. Dans le menu situé en haut, cliquez sur **Configurer**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/08.png)
6. Copiez votre **ID Client**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/13.png)

### <a name="get-the-applications-client-secret"></a>Obtenir la clé secrète client de l’application
Pour obtenir la clé secrète client de l’application, vous devez créer une nouvelle clé et enregistrer sa valeur lors de l’enregistrement de la nouvelle clé car il est impossible de récupérer cette valeur ultérieurement.

1. Dans le volet de navigation gauche du [portail Azure Classic](https://manage.windowsazure.com), cliquez sur **Active Directory**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 
2. Dans la liste **active directory** , sélectionnez votre annuaire.
3. Dans le menu supérieur, cliquez sur **Applications**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/02.png) 
4. Dans la liste des applications, sélectionnez l’application nouvellement créée.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/07.png)
5. Dans le menu situé en haut, cliquez sur **Configurer**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/08.png)
6. Dans la section **Clés** , procédez comme suit : 
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/14.png)
   
    a. Dans la liste de durée, sélectionnez une durée
   
    b. Dans la barre inférieure, cliquez sur **Enregistrer**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites/10.png)
   
    c. Copiez la valeur de la clé.

## <a name="next-steps"></a>Étapes suivantes
* Vous souhaitez accéder aux données de l’API de création de rapports Azure AD par programme ? Consultez [Prise en main de l’API de création de rapports Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Si vous souhaitez en savoir plus sur la création de rapports Azure Active Directory, consultez le [Guide Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).  




<!--HONumber=Nov16_HO3-->


