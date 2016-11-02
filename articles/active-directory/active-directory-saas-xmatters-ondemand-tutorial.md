<properties 
    pageTitle="Didacticiel : Intégration d’Azure AD à XMatters OnDemand | Microsoft Azure"
    description="Découvrez comment utiliser XMatters OnDemand avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore." 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure AD à xMatters OnDemand
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et xMatters OnDemand. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un client xMatters OnDemand
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à xMatters OnDemand pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise xMatters OnDemand (connexion initiée par le fournisseur de services) ou à l’aide de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour xMatters OnDemand
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Scénario")

##Activation de l’intégration d’applications pour xMatters OnDemand
  
Cette section décrit l’activation de l’intégration d’applications pour xMatters OnDemand.

###Pour activer l’intégration d’applications pour xMatters OnDemand, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **xMatters OnDemand**.

    ![Galerie d’applications](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **xMatters OnDemand**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters OnDemand")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur XMatters OnDemand avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure Classic, dans la page d’intégration d’applications **xMatters OnDemand**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à xMatters OnDemand**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, procédez comme suit :

    ![Configurer l’URL de l’application](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configurer l’URL de l’application")

	a. Dans la zone de texte **URL de connexion de xMatters OnDemand**, tapez votre URL au format suivant : `https://<tenant-name>.XMattersOnDemandapp.com`

	b. Cliquez sur **Suivant**.


4.  Dans la page **Configurer l’authentification unique sur XMatters OnDemand**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sous le nom **c:\\xMatters OnDemand.cer**.

    >[AZURE.IMPORTANT] Vous devez transférer le certificat à l’équipe du support technique xMatters. L’équipe du support technique xMatters doit charger le certificat avant que vous ne puissiez finaliser la configuration de l’authentification unique.

    ![Configurer l’authentification unique](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise XMatters OnDemand en tant qu’administrateur.

6.  Dans la barre d’outils de la partie supérieure, cliquez sur **Admin**, puis sur **Company Details** dans la barre de navigation située à gauche.

    ![Administrateur](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Administrateur")

7.  Dans la page **SAML Configuration**, procédez comme suit :

    ![Configuration de SAML](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "Configuration de SAML")

    1.  Sélectionnez **Enable SAML**.
    2.  Dans la page **Configurer l’authentification unique sur xMatters OnDemand** du portail Azure Classic, copiez la valeur **ID du fournisseur d’identité**, puis collez-la dans la zone de texte **ID de fournisseur d’identité**.
    3.  Dans la page **Configurer l’authentification unique sur xMatters OnDemand** du portail Azure Classic, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **URL d’authentification unique**.
    4.  Dans la page **Configurer l’authentification unique sur xMatters OnDemand** du portail Azure Classic, copiez la valeur **URL du service de déconnexion unique**, puis collez-la dans la zone de texte **URL de déconnexion unique**.
    5.  Dans la partie supérieure de la page Company Details, cliquez sur **Save Changes**.
![Détails sur l’entreprise](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Détails sur l’entreprise")

8.  Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Configurer l’authentification unique")

##Configuration de l'approvisionnement des utilisateurs
  
Pour pouvoir se connecter à XMatters OnDemand, les utilisateurs d’Azure AD doivent être approvisionnés dans XMatters OnDemand. Dans le cas de XMatters OnDemand, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **xMatters OnDemand**.

2.  Cliquez sur l’onglet **Users**.

3.  Cliquez sur **Add User**.

    ![Utilisateurs](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Utilisateurs")

4.  Sélectionnez **Active**.

5.  Dans la section **Add a User**, procédez comme suit :

    ![Ajouter un utilisateur](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Ajouter un utilisateur")

    1.  Entrez les attributs **UserID**, **First name**, **Last name** et **Site** d’un compte Azure AD valide que vous voulez approvisionner.
    2.  Cliquez sur **Save**.

>[AZURE.NOTE] Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par XMatters OnDemand pour approvisionner des comptes d’utilisateurs Azure AD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à XMatters OnDemand, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’applications **xMatters OnDemand**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0914_2016-->