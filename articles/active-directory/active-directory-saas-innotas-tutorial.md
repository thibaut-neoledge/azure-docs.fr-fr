<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Innotas | Microsoft Azure"
    description="Apprenez à utiliser Innotas avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory à Innotas
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Innotas. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Innotas
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Innotas pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Innotas (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Innotas
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-innotas-tutorial/IC777331.png "Scénario")
##Activation de l’intégration d’application pour Innotas
  
Cette section décrit l’activation de l’intégration d’application pour Innotas.

###Pour activer l’intégration d’application pour Innotas, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-innotas-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-innotas-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-innotas-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-innotas-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Innotas**.

    ![Galerie d’applications](./media/active-directory-saas-innotas-tutorial/IC777332.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Innotas**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Innotas](./media/active-directory-saas-innotas-tutorial/IC777333.png "Innotas")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Innotas avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure Active Directory, dans la page d’intégration d’application **Innotas**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-innotas-tutorial/IC777334.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Innotas**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-innotas-tutorial/IC777335.png "Configurer l’authentification unique")

3.  Dans la zone de texte **URL de connexion à Innotas** de la page **Configurer l’URL de l’application**, tapez l’URL au format « *https://\<nom-locataire>.Innotas.com* », puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-innotas-tutorial/IC777336.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Innotas**, pour télécharger vos métadonnées, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de données en local sous le nom **c:\\InnotasMetaData.xml**.

    ![Configurer l’authentification unique](./media/active-directory-saas-innotas-tutorial/IC777337.png "Configurer l’authentification unique")

5.  Transférez ce fichier de métadonnées à l’équipe de support d’Innotas. L’équipe de support configure l’authentification unique pour vous.

6.  Sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-innotas-tutorial/IC777338.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Aucun élément d’action ne vous permet de configurer l’approvisionnement des utilisateurs dans Innotas. Lorsqu’un utilisateur tente de se connecter à Innotas à l’aide du panneau d’accès, Innotas vérifie si cet utilisateur existe. Si aucun compte d’utilisateur n’est disponible, Innotas le crée automatiquement.
##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Innotas, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application **Innotas**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-innotas-tutorial/IC777339.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-innotas-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->