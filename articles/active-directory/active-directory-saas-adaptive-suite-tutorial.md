<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à Adaptative Suite | Microsoft Azure"
    description="Apprenez à utiliser Adaptive Suite avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore !" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Didacticiel : Intégration d’Azure Active Directory à Adaptative Suite

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Adaptive Suite. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Adaptive Suite

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Adaptive Suite pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Adaptive Suite (connexion initiée par le fournisseur du service) ou en s’appuyant sur la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Adaptive Suite
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Scénario")
##Activation de l’intégration d’application pour Adaptive Suite

Cette section décrit l’activation de l’intégration d’application pour Adaptive Suite.

###Pour activer l’intégration d’application pour Adaptive Suite, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Adaptive Suite**.

    ![Galerie d’applications](./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Adaptive Suite**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Adaptive Suite](./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptive Suite")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Adaptive Suite avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. La configuration de l’authentification unique pour Adaptive Suite nécessite de récupérer une valeur d’empreinte numérique dans un certificat. Si cette procédure ne vous est pas familière, consultez [Récupération de la valeur de l’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, dans la page d’intégration d’application **Adaptive Suite**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Adaptive Suite**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de réponse**, tapez votre URL selon le modèle "**https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE=*", puis cliquez sur **Suivant**.

    >[AZURE.NOTE]Vous pouvez obtenir cette valeur dans la page **SAML SSO Settings** d’Adaptive Suite.

    ![Configurer les paramètres d’application](./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Configurer les paramètres d’application")

4.  Dans la page **Configurer l’authentification unique sur Adaptive Suite**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Adaptive Suite en tant qu’administrateur.

6.  Accédez à **Admin**.

    ![Administrateur](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administrateur")

7.  Dans la section **Users and Roles**, cliquez sur **Manage SAML SSO Settings**.

    ![Manage SAML SSO Settings](./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Manage SAML SSO Settings")

8.  Dans la page **SAML SSO Settings**, procédez comme suit :

    ![SAML SSO Settings](./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "SAML SSO Settings")

    1.  Dans la zone de texte **Identity provider name**, attribuez un nom à votre configuration.
    2.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur Adaptive Suite** de la boîte de dialogue, copiez la valeur **ID d’identité**, puis collez-la dans la zone de texte **Identity provider Entity ID**.
    3.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur Adaptive Suite**, copiez la valeur **URL SSO SAML**, puis collez-la dans la zone de texte **Identity provider SSO URL**.
    4.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur Adaptive Suite**, copiez la valeur **URL SSO SAML**, puis collez-la dans la zone de texte **Custom logout URL**.
    5.  Pour charger votre certificat téléchargé, cliquez sur **Choisir un fichier**.
    6.  Pour **SAML user id**, sélectionnez **User’s Adaptive Insights user name**.
    7.  Pour **SAML user id location**, sélectionnez **User id in NameID of Subject**.
    8.  Pour **SAML NameID format**, sélectionnez **Email address**.
    9.  Pour **Enable SAML**, sélectionnez **Allow SAML SSO and direct Adaptive Insights login**.
    10. Cliquez sur **Enregistrer**.

9.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Adaptive Suite, vous devez les approvisionner dans Adaptive Suite. Dans le cas d’Adaptive Suite, cet approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Adaptive Suite** en tant qu’administrateur.

2.  Accédez à **Admin**.

    ![Administrateur](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administrateur")

3.  Dans la section **Users and Roles**, cliquez sur **Add User**.

    ![Ajouter un utilisateur](./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Ajouter un utilisateur")

4.  Dans la section **New User**, procédez comme suit :

    ![Envoyer](./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Envoyer")

    1.  Tapez le nom, l’identifiant de connexion, l’adresse de messagerie et le mot de passe de l’utilisateur Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Name**, **Login**, **Email** et **Password**.
    2.  Sélectionnez un **rôle**.
    3.  Cliquez sur **Envoyer**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur Adaptive Suite fourni par ce service pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Adaptive Suite, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application **Adaptive Suite**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->