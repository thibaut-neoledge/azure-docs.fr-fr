<properties 
    pageTitle="Didacticiel : Intégration d’Azure AD à TeamSeer | Microsoft Azure" 
    description="Découvrez comment utiliser TeamSeer avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure AD à TeamSeer
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et TeamSeer. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire TeamSeer
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à TeamSeer pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise TeamSeer (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour TeamSeer
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-teamseer-tutorial/IC789618.png "Scénario")

##Activation de l’intégration d’applications pour TeamSeer
  
Cette section décrit l’activation de l’intégration d’applications pour TeamSeer.

###Pour activer l’intégration d’applications pour TeamSeer, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-teamseer-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-teamseer-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-teamseer-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **TeamSeer**.

    ![Galerie d’applications](./media/active-directory-saas-teamseer-tutorial/IC789619.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **TeamSeer**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![TeamSeer](./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur TeamSeer avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Sur la page d’intégration d’applications **TeamSeer** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à TeamSeer**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configurer l’authentification unique")

3.  Dans la zone de texte **URL de connexion à TeamSeer** de la page **Configurer l’URL de l’application**, tapez votre URL au format « *http://www.teamseer.com/companyid*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-teamseer-tutorial/IC789629.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur TeamSeer**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise TeamSeer en tant qu’administrateur.

6.  Accédez à **HR Admin**.

    ![HR Admin](./media/active-directory-saas-teamseer-tutorial/IC789634.png "HR Admin")

7.  Cliquez sur **Setup**.

    ![Paramétrage](./media/active-directory-saas-teamseer-tutorial/IC789635.png "Paramétrage")

8.  Cliquez sur **Set up SAML provider details**.

    ![SAML Settings](./media/active-directory-saas-teamseer-tutorial/IC789636.png "SAML Settings")

9.  Dans la section des détails sur le fournisseur SAML, procédez comme suit :

    ![SAML Settings](./media/active-directory-saas-teamseer-tutorial/IC789637.png "SAML Settings")

    1.  Dans la page **Configurer l’authentification unique sur TeamSeer** du portail Azure Classic, copiez la valeur de **URL du service d’authentification unique**, puis collez-la dans la zone de texte **URL**.
    2.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.

        >[AZURE.TIP] Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    3.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **IdP Public Certificate**.

10. Pour configurer le fournisseur SAML, procédez comme suit :

    ![SAML Settings](./media/active-directory-saas-teamseer-tutorial/IC789638.png "SAML Settings")

    1.  Dans la zone de test **Tester l’adresse de messagerie**, entrez l’adresse de messagerie de l’utilisateur de test.
    2.  Dans la zone de texte **Émetteur**, entrez l’URL de l’émetteur du fournisseur du service.
    3.  Cliquez sur **Save**.

11. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configurer l’authentification unique")

##Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à TeamSeer, les utilisateurs d’Azure AD doivent être approvisionnés dans TeamSeer. Dans le cas de TeamSeer, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous au site d’entreprise **TeamSeer** en tant qu’administrateur.

2.  Procédez comme suit :

    ![HR Admin](./media/active-directory-saas-teamseer-tutorial/IC789640.png "HR Admin")

    1.  Accédez à **HR Admin > Users**.
    2.  Cliquez sur **Run the New User wizard**.

3.  Dans la section **User Details**, procédez comme suit :

    ![User Details](./media/active-directory-saas-teamseer-tutorial/IC789641.png "Détails de l’utilisateur")

    1.  Indiquez le prénom, le nom et le nom d’utilisateur (adresse de messagerie) du compte AAD valide que vous souhaitez approvisionner, dans les zones de texte **First Name**, **Surname** et **User name (Email address)**.
    2.  Cliquez sur **Suivant**.

4.  Suivez les instructions à l’écran pour ajouter un nouvel utilisateur, puis cliquez sur **Finish**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par TeamSeer, pour approvisionner des comptes utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à TeamSeer, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’applications **TeamSeer**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-teamseer-tutorial/IC789642.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-teamseer-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0914_2016-->