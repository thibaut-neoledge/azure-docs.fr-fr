<properties 
    pageTitle="Didacticiel : Intégration d’Azure AD à Zoho Mail | Microsoft Azure" 
    description="Découvrez comment utiliser Zoho Mail avec Azure AD pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore." 
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
    ms.author="markvi" />

#Didacticiel : Intégration d’Azure AD à Zoho Mail
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Zoho Mail. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un client Zoho Mail
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Zoho Mail pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Zoho Mail (connexion initiée par le fournisseur de services) ou à l’aide de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Zoho Mail
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Scénario")

##Activation de l’intégration d’applications pour Zoho Mail
  
Cette section décrit l’activation de l’intégration d’applications pour Zoho Mail.

###Pour activer l’intégration d’applications pour Zoho Mail, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Zoho Mail**.

    ![Galerie d’applications](./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Zoho Mail**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Zoho Mail](./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Zoho Mail")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Zoho Mail avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **Zoho Mail** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Zoho Mail**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, procédez comme suit :

    ![Configurer l’URL de l’application](./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Configurer l’URL de l’application")

	a. Dans la zone de texte **URL de connexion de Zoho Mail**, tapez votre URL au format suivant : `http://<company name>.ZohoMail.com`

	b. Cliquez sur **Suivant**.


4.  Dans la page **Configurer l’authentification unique sur Zoho Mail**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zoho Mail en tant qu’administrateur.

6.  Accédez à **Control panel**.

    ![Panneau de configuration](./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Panneau de configuration")

7.  Cliquez sur l’onglet **SAML Authentication**.

    ![Authentification SAML](./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "Authentification SAML")

8.  Dans la section **SAML Authentication Details**, procédez comme suit :

    ![Détails de l’authentification SAML](./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "Détails de l’authentification SAML")

    1.  Dans la page **Configurer l’authentification unique sur Zoho Mail** du portail Azure Classic, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **URL de connexion**.
    2.  Dans la page **Configurer l’authentification unique sur Zoho Mail** du portail Azure Classic, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **URL de déconnexion**.
    3.  Dans la page **Configurer l’authentification unique sur Zoho Mail** du portail Azure Classic, copiez la valeur **Modifier l’URL de mot de passe**, puis collez-la dans la zone de texte **Modifier l’URL de mot de passe**.
    4.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.

        >[AZURE.TIP] Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    5.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **PublicKey**.
    6.  Comme **Algorithme**, sélectionnez **RSA**.
    7.  Cliquez sur **OK**.

9.  Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configurer l’authentification unique")

##Configuration de l'approvisionnement des utilisateurs
  
Pour que les utilisateurs d’Azure AD puissent se connecter à Zoho Mail, ils doivent être approvisionnés dans Zoho Mail. Dans le cas de Zoho Mail, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Zoho Mail** en tant qu’administrateur.

2.  Accédez à **Control Panel > Mail & Docs**.

3.  Accédez à **User Details > Add User**.

    ![Ajouter un utilisateur](./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Ajouter un utilisateur")

4.  Dans la boîte de dialogue **Add users**, procédez comme suit :

    ![Ajouter un utilisateur](./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Ajouter un utilisateur")

    1.  Tapez le prénom, le nom, l’ID de messagerie et le mot de passe du compte Azure AD valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir **First Name**, **Last Name**, **Email ID** et **Password**.
    2.  Cliquez sur **OK**.

        >[AZURE.NOTE] Le titulaire du compte Azure AD reçoit un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.

>[AZURE.NOTE] Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Zoho Mail pour approvisionner des comptes d’utilisateurs Azure AD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Zoho Mail, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’applications **Zoho Mail**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0914_2016-->