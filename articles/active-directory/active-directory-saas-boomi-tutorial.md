<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à Boomi | Microsoft Azure" 
    description="Apprenez à utiliser Boomi avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
    services="active-directory" 
    authors="MarkusVi"  
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

#Didacticiel : Intégration d’Azure Active Directory à Boomi

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Boomi. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Boomi pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Boomi pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Boomi (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Boomi
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-boomi-tutorial/IC791134.png "Scénario")
##Activation de l’intégration d’applications pour Boomi

Cette section décrit l’activation de l’intégration d’applications pour Boomi.

###Pour activer l’intégration d’applications pour Boomi, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-boomi-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-boomi-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-boomi-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Boomi**.

    ![Galerie d’applications](./media/active-directory-saas-boomi-tutorial/IC790822.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Boomi**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Boomi](./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Boomi avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’applications **Boomi**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/IC790824.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Boomi**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/IC790825.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de réponse Boomi**, tapez votre **URL de connexion Boomi AtomSphere** (par exemple, « *https://platform.boomi.com/sso/AccountName/saml*”), puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-boomi-tutorial/IC790826.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Boomi**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/IC790827.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Boomi en tant qu’administrateur.

6.  Dans la barre d’outils située en haut, cliquez sur le nom de votre société, puis sur **Setup**.

    ![Paramétrage](./media/active-directory-saas-boomi-tutorial/IC790828.png "Paramétrage")

7.  Cliquez sur **SSO Options**.

    ![Options d’authentification unique](./media/active-directory-saas-boomi-tutorial/IC790829.png "Options d’authentification unique")

8.  Dans la section **Single Sign-On Options**, procédez comme suit :

    ![Options d’authentification unique](./media/active-directory-saas-boomi-tutorial/IC790830.png "Options d’authentification unique")

    1.  Sélectionnez **Enable SAML Single Sign-On**.
    2.  Pour charger le certificat téléchargé, cliquez sur **Import**.
    3.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Boomi**, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **Identity Provider Login URL**.
    4.  Dans **Federation Id Location**, sélectionnez **Federation Id is in NameID element of the Subject**.
    5.  Cliquez sur **Enregistrer**.

9.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-boomi-tutorial/IC775560.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Boomi, vous devez les approvisionner dans Boomi. En l’occurrence, cet approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Boomi** en tant qu’administrateur.

2.  Accédez à **User Management > Users**.

    ![Utilisateurs](./media/active-directory-saas-boomi-tutorial/IC790831.png "Utilisateurs")

3.  Cliquez sur **Add User**.

    ![Ajouter un utilisateur](./media/active-directory-saas-boomi-tutorial/IC790832.png "Ajouter un utilisateur")

4.  Dans la page de boîte de dialogue **Add User Roles**, procédez comme suit :

    ![Ajouter un utilisateur](./media/active-directory-saas-boomi-tutorial/IC790833.png "Ajouter un utilisateur")

    1.  Indiquez le prénom, le nom et l’adresse de messagerie du compte AAD valide que vous souhaitez approvisionner dans les zones de texte **First Name**, **Last Name** et **Email**.
    2.  Cliquez sur OK.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Boomi pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Boomi, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **Boomi **, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-boomi-tutorial/IC790834.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-boomi-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->