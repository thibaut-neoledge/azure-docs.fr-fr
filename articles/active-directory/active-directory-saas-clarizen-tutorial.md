<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à Clarizen | Microsoft Azure" description="Apprenez à utiliser Clarizen avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Clarizen
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=526793).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Clarizen. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Clarizen pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Clarizen pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Clarizen (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Clarizen
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-clarizen-tutorial/IC784679.png "Scénario")
##Activation de l’intégration d’applications pour Clarizen

Cette section décrit l’activation de l’intégration d’applications pour Clarizen.

###Pour activer l’intégration d’applications pour Clarizen, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-clarizen-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-clarizen-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-clarizen-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Clarizen**.

    ![Galerie d’applications](./media/active-directory-saas-clarizen-tutorial/IC784680.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Clarizen**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Clarizen avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’applications **Clarizen**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Clarizen**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’authentification unique sur Clarizen**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configurer l’authentification unique")

4.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **Clarizen** en tant qu’administrateur (par exemple, **https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).

5.  Cliquez sur votre nom d’utilisateur, puis sur **Settings**.

    ![Paramètres](./media/active-directory-saas-clarizen-tutorial/IC784685.png "Paramètres")

6.  Cliquez sur l’onglet **Global Settings**, puis cliquez sur **edit** en regard de **Federated Authentication**.

    ![Paramètres globaux](./media/active-directory-saas-clarizen-tutorial/IC786906.png "Paramètres globaux")

7.  Dans la boîte de dialogue **Federated Authentication**, procédez comme suit :

    ![Authentification fédérée](./media/active-directory-saas-clarizen-tutorial/IC785892.png "Authentification fédérée")

    1.  Cliquez sur **Upload** pour charger votre certificat téléchargé.
    2.  Dans la page **Configurer l’authentification unique sur Clarizen** du portail Azure, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **Sign-in URL**.
    3.  Dans la page **Configurer la déconnexion unique sur Clarizen** du portail Azure, copiez la valeur **URL du service de déconnexion unique**, puis collez-la dans la zone de texte **Sign-out URL**.
    4.  Sélectionnez **Use POST**.
    5.  Cliquez sur **Save**.

8.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Pour se connecter à Clarizen, les utilisateurs d’Azure AD doivent être approvisionnés dans Clarizen. Dans le cas de Clarizen, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous au site d’entreprise **Clarizen** en tant qu’administrateur.

2.  Cliquez sur **People**.

    ![Personnes](./media/active-directory-saas-clarizen-tutorial/IC784689.png "Personnes")

3.  Cliquez sur **Invite User**.

    ![Inviter des utilisateurs](./media/active-directory-saas-clarizen-tutorial/IC784690.png "Inviter des utilisateurs")

4.  Dans la boîte de dialogue Invite People, procédez comme suit :

    ![Inviter des personnes](./media/active-directory-saas-clarizen-tutorial/IC784691.png "Inviter des personnes")

    1.  Dans la zone de texte **Email**, tapez l’adresse e-mail d’un compte Azure Active Directory valide à approvisionner.
    2.  Cliquez sur **Invite**.

    >[AZURE.NOTE]Le titulaire du compte Azure Active Directory reçoit un message électronique contenant un lien à suivre pour confirmer son compte et l’activer.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Clarizen, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **Clarizen**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-clarizen-tutorial/IC784692.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-clarizen-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->