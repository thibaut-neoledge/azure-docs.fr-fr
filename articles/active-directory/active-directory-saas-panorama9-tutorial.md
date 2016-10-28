<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Panorama9 | Microsoft Azure" 
    description="Apprenez à utiliser Panorama9 avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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

#Didacticiel : Intégration d’Azure Active Directory à Panorama9
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Panorama9. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Panorama9 pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Panorama9 pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Panorama9 (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md)
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Panorama9
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-panorama9-tutorial/IC790016.png "Scénario")
##Activation de l’intégration d’application pour Panorama9
  
Cette section décrit l’activation de l’intégration d’application pour Panorama9.

###Pour activer l’intégration d’application pour Panorama9, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-panorama9-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-panorama9-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-panorama9-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-panorama9-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Panorama9**.

    ![Galerie d’applications](./media/active-directory-saas-panorama9-tutorial/IC790017.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Panorama9**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Panorama9](./media/active-directory-saas-panorama9-tutorial/IC790018.png "Panorama9")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Panorama9 avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. La configuration de l’authentification unique pour Panorama9 vous oblige à récupérer une valeur d’empreinte dans un certificat. Si cette procédure ne vous est pas familière, consultez [Récupération de la valeur de l’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure Classic, dans la page d’intégration d’application **Panorama9**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-panorama9-tutorial/IC790019.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Panorama9**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-panorama9-tutorial/IC790020.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Panorama9**, tapez l’URL utilisée par vos utilisateurs pour se connecter à Panorama9 (par exemple, « *https://dashboard.panorama9.com/saml/access/3262*"), puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-panorama9-tutorial/IC790021.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Panorama9**, cliquez sur **Télécharger le certificat**, puis enregistrez-le en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-panorama9-tutorial/IC790022.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Panorama9 en tant qu’administrateur.

6.  Dans la barre d’outils située en haut, cliquez sur **Manage**, puis sur **Extensions**.

    ![Extensions](./media/active-directory-saas-panorama9-tutorial/IC790023.png "Extensions")

7.  Dans la boîte de dialogue **Extensions**, cliquez sur **Single Sign-On**.

    ![Authentification unique](./media/active-directory-saas-panorama9-tutorial/IC790024.png "Authentification unique")

8.  Dans la section **Settings**, procédez comme suit :

    ![Paramètres](./media/active-directory-saas-panorama9-tutorial/IC790025.png "Paramètres")

    1.  Dans la page **Configurer l’authentification unique sur Panorama9** du portail Azure Classic, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **Identity provider URL**.
    2.  Copiez la valeur **Empreinte** du certificat exporté, puis collez-la dans la zone de texte **Certificate Fingerprint**.

        >[AZURE.TIP]Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

    3.  Cliquez sur **Enregistrer**.

9.  Dans le portail Azure AD Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-panorama9-tutorial/IC790026.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour permettre aux utilisateurs Azure AD de se connecter à Panorama9, vous devez les approvisionner dans Panorama9. Dans le cas de Panorama9, l’approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous au site d’entreprise **Panorama9** en tant qu’administrateur.

2.  Dans le menu situé en haut, cliquez sur **Manage**, puis sur **Users**.

    ![Utilisateurs](./media/active-directory-saas-panorama9-tutorial/IC790027.png "Utilisateurs")

3.  Cliquez sur **+**.

4.  Dans la section User data, procédez comme suit :

    ![Utilisateurs](./media/active-directory-saas-panorama9-tutorial/IC790028.png "Utilisateurs")

    1.  Dans la zone de texte **Email**, tapez l’adresse de messagerie d’un utilisateur Azure Active Directory valide à approvisionner.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par Panorama9, pour approvisionner des comptes utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Panorama9, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’application **Panorama9**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-panorama9-tutorial/IC790029.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-panorama9-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->