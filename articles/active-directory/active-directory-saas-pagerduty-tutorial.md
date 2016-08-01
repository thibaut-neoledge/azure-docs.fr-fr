<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Pagerduty | Microsoft Azure" 
    description="Apprenez à utiliser Pagerduty avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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
    ms.date="07/08/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory à Pagerduty
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Pagerduty. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Pagerduty
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Pagerduty pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Pagerduty (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Pagerduty
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Scénario")
##Activation de l’intégration d’application pour Pagerduty
  
Cette section décrit l’activation de l’intégration d’application pour Pagerduty.

###Pour activer l’intégration d’application pour Pagerduty, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Pagerduty**.

    ![Galerie d’applications](./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Pagerduty**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![PagerDuty](./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Pagerduty avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure Classic, dans la page d’intégration d’application **Pagerduty**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Pagerduty**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Pagerduty**, tapez votre URL selon le modèle suivant « *https://\<nom-locataire>.Pagerduty.com* », puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Pagerduty**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Pagerduty en tant qu’administrateur.

6.  Dans le menu situé en haut, cliquez sur **Account Settings**.

    ![Paramètres de compte](./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Paramètres de compte")

7.  Cliquez sur **Single Sign-On**.

    ![Authentification unique](./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Authentification unique")

8.  Dans la page Enable Single Sign-on (SSO), procédez comme suit :

    ![Activer l’authentification unique](./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Activer l'authentification unique")

    1.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.

        >[AZURE.TIP] Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    2.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **X.509 Certificate**.
    3.  Dans la boîte de dialogue **Configurer l’authentification unique sur Pagerduty** du portail Azure Classic, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **Login URL**.
    4.  Dans la boîte de dialogue **Configurer l’authentification unique sur Pagerduty** du portail Azure Classic, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **Logout URL**.
    5.  Sélectionnez **Turn on Single Sign-on**.
    6.  Cliquez sur **Enregistrer les modifications**.

9.  Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour permettre aux utilisateurs Azure AD de se connecter à Pagerduty, vous devez les approvisionner dans Pagerduty. Dans le cas de Pagerduty, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre locataire **Pagerduty**.

2.  Dans le menu situé en haut, cliquez sur **Utilisateurs**.

3.  Cliquez sur **Add Users**.

    ![Ajouter des utilisateurs](./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Ajouter des utilisateurs")

4.  Dans la boîte de dialogue **Invite your team**, entrez le prénom, le nom et l’adresse de messagerie de l’utilisateur Azure AD que vous voulez approvisionner, en l’occurrence dans les zones **First and Last Name** et **Email** , cliquez sur **Add**, puis sur **Send Invites**.

    ![Inviter dans votre équipe](./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Inviter dans votre équipe")

    >[AZURE.NOTE] Tous les utilisateurs recevront une invitation pour créer un compte PagerDuty.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Pagerduty, pour approvisionner des comptes utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Pagerduty, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’application **Pagerduty**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->