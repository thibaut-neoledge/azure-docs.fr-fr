<properties 
    pageTitle="Didacticiel : Intégration d’Azure AD à ScreenSteps | Microsoft Azure" 
    description="Découvrez comment utiliser ScreenSteps avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/26/2016" 
    ms.author="jeedes" />


#<a name="tutorial:-azure-active-directory-integration-with-screensteps"></a>Didacticiel : Intégration d’Azure AD à ScreenSteps
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et ScreenSteps.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire ScreenSteps
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à ScreenSteps pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise ScreenSteps (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour ScreenSteps
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-screensteps-tutorial/IC778516.png "Scenario")
##<a name="enabling-the-application-integration-for-screensteps"></a>Activation de l’intégration d’applications pour ScreenSteps
  
Cette section décrit l’activation de l’intégration d’applications pour ScreenSteps.

###<a name="to-enable-the-application-integration-for-screensteps,-perform-the-following-steps:"></a>Pour activer l’intégration d’applications pour ScreenSteps, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-screensteps-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter une application](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Add application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-screensteps-tutorial/IC749322.png "Add an application from gallerry")

6.  Dans la **zone de recherche**, entrez **ScreenSteps**.

    ![Galerie d’applications](./media/active-directory-saas-screensteps-tutorial/IC778517.png "Application gallery")

7.  Dans le volet des résultats, sélectionnez **ScreenSteps**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##<a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur ScreenSteps avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **ScreenSteps** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configure single sign-on")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à ScreenSteps**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configure single sign-on")

3.  Dans la zone de texte **URL de connexion à ScreenSteps** de la page **Configurer l’URL de l’application**, tapez votre URL au format « *https://\<nom_locataire\>.ScreenSteps.com* », puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configure app URL")

4.  Dans la page **Configurer l’authentification unique sur ScreenSteps**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configure single sign-on")

5.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise ScreenSteps en tant qu’administrateur.

6.  Cliquez sur **Account Management**.

    ![Account Management](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Account management")

7.  Cliquez sur **Remote Authentication**.

    ![Remote Authentication](./media/active-directory-saas-screensteps-tutorial/IC778524.png "Remote authentication")

8.  Cliquez sur **Create authentication endpoint**.

    ![Remote Authentication](./media/active-directory-saas-screensteps-tutorial/IC778525.png "Remote authentication")

9.  Dans la section **Create an Authentication Endpoint** , procédez comme suit :

    ![Create an Authentication Endpoint](./media/active-directory-saas-screensteps-tutorial/IC778526.png "Create an authentication endpoint")

    1.  Dans la zone de texte **Title** , tapez un titre.
    2.  Dans la liste **Mode**, sélectionnez **SAML**.
    3.  Cliquez sur **Create**.

10. Dans la section **Remote Authentication Endpoint** , procédez comme suit :

    ![Remote Authentication Endpoint](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Remote authentication endpoint")

    1.  Dans la page **Configurer l’authentification unique sur ScreenSteps** du portail Azure Classic, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **Remote Login URL**.
    2.  Dans la page **Configurer l’authentification unique sur ScreenSteps** du portail Azure Classic, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **Log out URL**.
    3.  Cliquez sur **Choisir un fichier**, puis chargez le certificat téléchargé.
    4.  Cliquez sur **Update**.

11. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configure single sign-on")
##<a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à **ScreenSteps**, les utilisateurs d’Azure AD doivent être approvisionnés dans **ScreenSteps**.  
Dans le cas de **ScreenSteps**, l’approvisionnement est une tâche manuelle.

###<a name="to-provision-a-user-account-to-screensteps,-perform-the-following-steps:"></a>Pour approvisionner un compte d’utilisateur dans ScreenSteps, procédez comme suit :

1.  Connectez-vous à votre locataire **ScreenSteps** .

2.  Cliquez sur **Account Management**.

    ![Account Management](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Account management")

3.  Cliquez sur **Utilisateurs**.

    ![Utilisateurs](./media/active-directory-saas-screensteps-tutorial/IC778544.png "Users")

4.  Cliquez sur **Create a user**.

    ![Tous les utilisateurs](./media/active-directory-saas-screensteps-tutorial/IC778545.png "All Users")

5.  Dans la liste **User Role** , sélectionnez un rôle pour l’utilisateur.

6.  Dans la section User Role, renseignez les champs **First Name**, **Last Name**, **Email**, **Login**, **Password** et **Password Confirmation** du compte AAD valide que vous souhaitez approvisionner.

    ![Nouvel utilisateur](./media/active-directory-saas-screensteps-tutorial/IC778546.png "New user")

7.  Dans la section Groups, sélectionnez **Authentication Group (SAML)**, puis cliquez sur **Create user**.

    ![Groupes](./media/active-directory-saas-screensteps-tutorial/IC778547.png "Groups")

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur, fourni par ScreenSteps, pour approvisionner des comptes d’utilisateur AAD.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###<a name="to-assign-users-to-screensteps,-perform-the-following-steps:"></a>Pour affecter des utilisateurs à ScreenSteps, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’applications **ScreenSteps**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-screensteps-tutorial/IC773094.png "Assign users")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Affecter des utilisateurs](./media/active-directory-saas-screensteps-tutorial/IC778548.png "Assign users")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


<!--HONumber=Oct16_HO2-->


