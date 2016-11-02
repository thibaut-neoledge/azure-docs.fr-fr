<properties 
    pageTitle="Didacticiel : Intégration d’Azure AD à SCC LifeCycle | Microsoft Azure" 
    description="Découvrez comment utiliser SCC LifeCycle avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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


#<a name="tutorial:-azure-active-directory-integration-with-scc-lifecycle"></a>Didacticiel : Intégration d’Azure AD à SCC LifeCycle
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et SCC LifeCycle.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement SCC LifeCycle pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à SCC LifeCycle pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise SCC LifeCycle (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour SCC LifeCycle
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Scenario")
##<a name="enabling-the-application-integration-for-scc-lifecycle"></a>Activation de l’intégration d’applications pour SCC LifeCycle
  
Cette section décrit comment activer l’intégration d’applications pour SCC LifeCycle.

###<a name="to-enable-the-application-integration-for-scc-lifecycle,-perform-the-following-steps:"></a>Pour activer l’intégration d’applications pour SCC LifeCycle, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter une application](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Add application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Add an application from gallerry")

6.  Dans la **zone de recherche**, entrez **SCC LifeCycle**.

    ![Galerie d’applications](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Application Gallery")

7.  Dans le volet des résultats, sélectionnez **SCC LifeCycle**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![SCC LifeCycle](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC LifeCycle")
##<a name="configuring-single-sign-on"></a>Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur SCC LifeCycle avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’application **SCC LifeCycle** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Configure Single Sign-On")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à SCC LifeCycle**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configure Single Sign-On")

3.  Dans la zone de texte **URL de connexion** de la page **Configurer l’URL de l’application**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application SCC LifeCycle à l’aide du modèle suivant « *https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx* », puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Configure App URL")

4.  Dans la page **Configurer l’authentification unique sur SCC LifeCycle**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Configure Single Sign-On")

5.  Transférez ce fichier de métadonnées à l’équipe de support de SCC LifeCycle.

    >[AZURE.NOTE]L’authentification unique doit être activée par l’équipe de support de SCC LifeCycle.

6.  Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configure Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à SCC LifeCycle, les utilisateurs d’Azure AD doivent être approvisionnés dans SCC LifeCycle.
  
Vous n’avez rien à faire pour configurer l’approvisionnement des utilisateurs dans SCC LifeCycle.  
Lorsqu’un utilisateur tente de se connecter à SCC LifeCycle, un compte SCC LifeCycle est, au besoin, automatiquement créé.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur, fourni par SCC LifeCycle, pour approvisionner des comptes d’utilisateur AAD.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###<a name="to-assign-users-to-scc-lifecycle,-perform-the-following-steps:"></a>Pour affecter des utilisateurs à SCC LifeCycle, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’applications **SCC LifeCycle**, cliquez sur **Affecter les utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Assign Users")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Yes")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).


<!--HONumber=Oct16_HO2-->


