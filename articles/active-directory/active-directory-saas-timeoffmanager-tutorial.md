<properties 
    pageTitle="Didacticiel : Intégration d’Azure AD à TimeOffManager | Microsoft Azure" 
    description="Découvrez comment utiliser TimeOffManager avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="05/03/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure AD à TimeOffManager
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et TimeOffManager.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement TimeOffManager pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à TimeOffManager pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise TimeOffManager (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour TimeOffManager
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-timeoffmanager-tutorial/IC795909.png "Scénario")

##Activation de l’intégration d’applications pour TimeOffManager
  
Cette section décrit l’activation de l’intégration d’applications pour TimeOffManager.

###Pour activer l’intégration d’applications pour TimeOffManager, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-timeoffmanager-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-timeoffmanager-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-timeoffmanager-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-timeoffmanager-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **TimeOffManager**.

    ![Galerie d’applications](./media/active-directory-saas-timeoffmanager-tutorial/IC795910.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **TimeOffManager**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/IC795911.png "TimeOffManager")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur TimeOffManager avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. 
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64 sur votre locataire TimeOffManager. 
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **TimeOffManager** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-timeoffmanager-tutorial/IC795912.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à TimeOffManager**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-timeoffmanager-tutorial/IC795913.png "Configurer l’authentification unique")

3.  Dans la zone de texte **URL de réponse de TimeOffManager** de la page **Configurer l’URL de l’application**, indiquez l’URL de votre service ACS TimeOffManager (par exemple, "https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company\_id=IC34216*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-timeoffmanager-tutorial/IC795914.png "Configurer l’URL de l’application")

    Vous pouvez obtenir l’URL de réponse dans la page de réglage de l’authentification unique de TimeOffManager.

    ![Paramètres d’authentification unique](./media/active-directory-saas-timeoffmanager-tutorial/IC795915.png "Paramètres d’authentification unique")

4.  Dans la page **Configurer l’authentification unique sur TimeOffManager**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-timeoffmanager-tutorial/IC795916.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise TimeOffManager en tant qu’administrateur.

6.  Accédez à **Account > Account Options > Single Sign-On Settings**.

    ![Paramètres d’authentification unique](./media/active-directory-saas-timeoffmanager-tutorial/IC795917.png "Paramètres d’authentification unique")

7.  Dans la section **Single Sign-On Settings**, procédez comme suit :

    ![Paramètres d’authentification unique](./media/active-directory-saas-timeoffmanager-tutorial/IC795918.png "Paramètres d’authentification unique")

    a. Créez un fichier **codé en base 64** à partir du certificat téléchargé.

        >[AZURE.TIP] For more details, see [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)

    b. Ouvrez votre certificat codé en base 64 dans le bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **X.509 Certificate**.
    
    c. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur TimeOffManager** de la boîte de dialogue, copiez la valeur de **URL de l’émetteur**, puis collez-la dans la zone de texte **Idp Issuer**.
    
    d. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur TimeOffManager**, copiez la valeur de **URL de connexion distante**, puis collez-la dans la zone de texte **IdP Endpoint URL**.
    
    e. Dans **Enforce SAML**, sélectionnez **No**.
    

    f. Dans **Auto-Create Users**, sélectionnez **Yes**.
    
    g. Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur TimeOffManager**, copiez la valeur de **URL de déconnexion distante** et collez-la dans la zone de texte **Logout URL**.
    
    h. Cliquez sur **Enregistrer les modifications**.

8.  Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur TimeOffManager**, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-timeoffmanager-tutorial/IC795919.png "Configurer l’authentification unique")

9.  Dans le menu situé en haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**.

    ![Attributs](./media/active-directory-saas-timeoffmanager-tutorial/IC795920.png "Attributs")

10. Pour ajouter les mappages d’attribut requis, procédez comme suit :

    ![Attributs du jeton SAML](./media/active-directory-saas-timeoffmanager-tutorial/123.png "Attributs du jeton SAML")

    |Nom de l'attribut|Valeur de l’attribut|
	|---|---|
    |Email|User.mail|
    |Firstname|User.givenname|
	|Lastname|User.surname|

    a. Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut utilisateur**.

    b. Dans la zone de texte **Nom de l’attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la zone de texte **Valeur de l’attribut**, sélectionnez la valeur d’attribut indiquée pour cette ligne.

    d. Cliquez sur **Terminé**.

11. Cliquez sur **Appliquer les modifications**.

##Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à TimeOffManager, les utilisateurs d’Azure AD doivent être approvisionnés dans TimeOffManager.  
TimeOffManager prend en charge l’approvisionnement juste-à-temps des utilisateurs. Vous n’avez rien à faire.  
Les utilisateurs sont ajoutés automatiquement lors de la première connexion à l’aide de l’authentification unique.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur, fourni par TimeOffManager, pour approvisionner des comptes d’utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à TimeOffManager, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’applications **TimeOffManager**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-timeoffmanager-tutorial/IC795922.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-timeoffmanager-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0504_2016-->