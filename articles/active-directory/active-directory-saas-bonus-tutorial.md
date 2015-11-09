<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à Bonus.ly | Microsoft Azure" 
    description="Apprenez à utiliser Bonus.ly avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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

#Didacticiel : Intégration d’Azure Active Directory à Bonus.ly

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Bonus.ly. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire de test dans Bonus.ly

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Bonus.ly
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-bonus-tutorial/IC773679.png "Scénario")
##Activation de l’intégration d’applications pour Bonus.ly

Cette section décrit l’activation de l’intégration d’applications pour Bonus.ly.

###Pour activer l’intégration d’applications pour Bonus.ly, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Activer l’authentification unique](./media/active-directory-saas-bonus-tutorial/IC773680.png "Activer l’authentification unique")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-bonus-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-bonus-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-bonus-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Bonus.ly**.

    ![Galerie d’applications](./media/active-directory-saas-bonus-tutorial/IC773681.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Bonus.ly**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Bonus.ly](./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonus.ly")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Bonus.ly avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. La configuration de l’authentification unique pour Bonus.ly vous oblige à récupérer une valeur d’empreinte dans un certificat. Si cette procédure ne vous est pas familière, consultez [Récupération de la valeur de l’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’applications **Bonus.ly**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bonus-tutorial/IC749323.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Bonus.ly**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bonus-tutorial/IC773683.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de locataire Bonus.ly**, tapez votre URL selon le modèle « *https://\<nom\_locataire>.Bonus.ly* », puis cliquez sur **Suivant** :

    ![Configurer l’URL de l’application](./media/active-directory-saas-bonus-tutorial/IC773684.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Bonus.ly**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sous **c:\\Bonusly.cer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bonus-tutorial/IC773685.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur, connectez-vous à votre locataire **Bonus.ly**.

6.  Dans la barre d’outils située en haut, cliquez sur **Settings**, puis sélectionnez **Integrations and apps**.

    ![Bonus.ly](./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonus.ly")

7.  Sous **Single Sign-On**, sélectionnez **SAML**.

8.  Dans la page de boîte de dialogue **SAML**, procédez comme suit :

    ![Bonus.ly](./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonus.ly")

    1.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Bonus.ly**, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **URL cible de l’authentification unique IdP**.
    2.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Bonus.ly**, copiez la valeur **ID de l’émetteur** et collez-la dans la zone de texte **IdP Issuer**.
    3.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Bonus.ly**, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **IdP Login URL**.
    4.  Copiez la valeur **Empreinte numérique** du certificat exporté, puis collez-la dans la zone de texte **Cert Fingerprint**.

        >[AZURE.TIP]Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

9.  Cliquez sur **save**.

10. Dans le portail Microsoft Azure AD, sélectionnez la confirmation de configuration, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bonus-tutorial/IC773689.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Bonus.ly, vous devez les approvisionner dans Bonus.ly. En l’occurrence, cet approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Dans une fenêtre de navigateur web, connectez-vous à votre locataire Bonus.ly.

2.  Cliquez sur **Settings**.

    ![Paramètres](./media/active-directory-saas-bonus-tutorial/IC781041.png "Paramètres")

3.  Cliquez sur l’onglet **Users and bonuses**.

    ![Utilisateurs et bonus](./media/active-directory-saas-bonus-tutorial/IC781042.png "Utilisateurs et bonus")

4.  Cliquez sur **Manage Users**.

    ![Gérer les utilisateurs](./media/active-directory-saas-bonus-tutorial/IC781043.png "Gérer les utilisateurs")

5.  Cliquez sur **Add User**.

    ![Ajouter un utilisateur](./media/active-directory-saas-bonus-tutorial/IC781044.png "Ajouter un utilisateur")

6.  Dans la boîte de dialogue **Add User**, procédez comme suit :

    ![Ajouter un utilisateur](./media/active-directory-saas-bonus-tutorial/IC781045.png "Ajouter un utilisateur")

    1.  Tapez les « **adresse de messagerie**, **prénom**, **nom** » d’un compte AAD valide que vous souhaitez approvisionner dans les zones de texte correspondantes.
    2.  Cliquez sur **Enregistrer**.

    >[AZURE.NOTE]Le détenteur du compte AAD recevra un message électronique contenant un lien à suivre pour confirmer le compte avant qu’il ne soit activé.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Bonus.ly pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Bonus.ly, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications Bonus.ly, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-bonus-tutorial/IC773690.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-bonus-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->