<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à ClickTime | Microsoft Azure" 
    description="Apprenez à utiliser ClickTime avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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

#Didacticiel : Intégration d’Azure Active Directory à ClickTime

L’objectif de ce didacticiel est de montrer comment intégrer Azure et ClickTime. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un client ClickTime

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à ClickTime pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise ClickTime (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour ClickTime
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-clicktime-tutorial/IC777274.png "Scénario")
##Activation de l’intégration d’applications pour ClickTime

Cette section décrit l’activation de l’intégration d’applications pour ClickTime.

###Pour activer l’intégration d’applications pour ClickTime, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clicktime-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-clicktime-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-clicktime-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-clicktime-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **ClickTime**.

    ![Galerie d’applications](./media/active-directory-saas-clicktime-tutorial/IC777275.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **ClickTime**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ClickTime](./media/active-directory-saas-clicktime-tutorial/IC777276.png "ClickTime")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur ClickTime avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez charger un certificat codé en base 64 sur votre client ClickTime. Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT]Pour être en mesure de configurer l’authentification unique sur votre client ClickTime, vous devez au préalable contacter le support technique ClickTime pour faire activer cette fonctionnalité.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’applications **ClickTime**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Activer l’authentification unique](./media/active-directory-saas-clicktime-tutorial/IC777277.png "Activer l'authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à ClickTime**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-clicktime-tutorial/IC777278.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’authentification unique sur ClickTime**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-clicktime-tutorial/IC777279.png "Configurer l’authentification unique")

4.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise ClickTime en tant qu’administrateur.

5.  Dans la barre d’outils située en haut, cliquez sur **Preferences**, puis sur **Security Settings**.

6.  Dans la section de configuration **Single Sign-On Preferences**, procédez comme suit :

    ![Paramètres de sécurité](./media/active-directory-saas-clicktime-tutorial/IC777280.png "Paramètres de sécurité")

    1.  Sélectionnez **Allow** sign-in using Single Sign-On (SSO) with **OneLogin**.
    2.  Dans la page **Configurer l’authentification unique sur ClickTime** du portail Azure, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **Identity Provider Endpoint**.
    3.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.  

        >[AZURE.TIP]Pour plus d’informations, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    4.  Ouvrez le certificat codé en base 64 dans le **Bloc-notes**, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **X.509 Certificate**.
    5.  Cliquez sur **Enregistrer**.

7.  Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-clicktime-tutorial/IC777281.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Pour se connecter à ClickTime, les utilisateurs d’Azure AD doivent être approvisionnés dans ClickTime. Dans le cas de ClickTime, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **ClickTime**.

2.  Dans la barre d’outils située en haut, cliquez sur **Company**, puis sur **People**.

    ![Personnes](./media/active-directory-saas-clicktime-tutorial/IC777282.png "Personnes")

3.  Cliquez sur **Add Person**.

    ![Ajouter une personne](./media/active-directory-saas-clicktime-tutorial/IC777283.png "Ajouter une personne")

4.  Dans la section New Person, procédez comme suit :

    ![Personnes](./media/active-directory-saas-clicktime-tutorial/IC777284.png "Personnes")

    1.  Dans la zone de texte **email address**, tapez l’adresse de messagerie de votre compte Azure AD.
    2.  Dans la zone de texte **full name**, tapez le nom de votre compte Azure AD.  

        >[AZURE.NOTE]Si vous le souhaitez, vous pouvez définir d’autres propriétés relatives à l’objet de la nouvelle personne.

    3.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur fourni par ClickTime pour approvisionner des comptes d’utilisateurs AAD.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à ClickTime, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **ClickTime**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-clicktime-tutorial/IC777285.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-clicktime-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->