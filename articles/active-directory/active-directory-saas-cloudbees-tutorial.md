<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à CloudBees | Microsoft Azure" description="Apprenez à utiliser CloudBees avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à CloudBees
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=528737).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et CloudBees. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un client CloudBees

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à CloudBees pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise CloudBees (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du volet d’accès](https://msdn.microsoft.com/library/azure/dn308586.aspx).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour CloudBees
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-cloudbees-tutorial/IC790415.png "Scénario")

##Activation de l’intégration d’applications pour CloudBees

Cette section décrit l’activation de l’intégration d’applications pour CloudBees.

###Pour activer l’intégration d’applications pour CloudBees, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cloudbees-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-cloudbees-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-cloudbees-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-cloudbees-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **CloudBees**.

    ![Galerie d’applications](./media/active-directory-saas-cloudbees-tutorial/IC790416.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **CloudBees**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![CloudBees](./media/active-directory-saas-cloudbees-tutorial/IC790417.png "CloudBees")

##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur CloudBees avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. La configuration de l’authentification unique pour CloudBees vous oblige à récupérer une valeur d’empreinte numérique dans un certificat. Si vous n’êtes pas familiarisé avec cette procédure, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’applications **CloudBees**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue \*\*Configurer l’authentification unique\*\*.

    ![Configurer l’authentification unique](./media/active-directory-saas-cloudbees-tutorial/IC790418.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à CloudBees**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-cloudbees-tutorial/IC790419.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à CoudBees (par exemple, \**https://grandcentral.cloudbees.com/login*"), puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-cloudbees-tutorial/IC790420.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur CloudBees**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le certificat en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-cloudbees-tutorial/IC790421.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise CloudBees en tant qu’administrateur.

6.  Dans le menu situé en haut, cliquez sur **Settings**.

    ![Paramètres](./media/active-directory-saas-cloudbees-tutorial/IC790422.png "Paramètres")

7.  Cliquez sur l’onglet **SSO integration**.

8.  Dans la section **Set Up SAML 2.0 Single Sign On**, procédez comme suit :

    ![Configuration de l’authentification unique](./media/active-directory-saas-cloudbees-tutorial/IC790423.png "Configuration de l’authentification unique")

    1.  Dans la page **Configurer l’authentification unique sur CloudBees** du portail Azure AD, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **Remote login URL**.
    2.  Copiez la valeur **Empreinte numérique** dans le certificat exporté, puis collez-la dans la zone de texte **Existing certificate fingerprint**.
    
        >[AZURE.TIP]Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

    3.  Dans la zone de texte **Authentication domains**, tapez le domaine de votre entreprise.
    4.  Cliquez sur **Save**.

9.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-cloudbees-tutorial/IC790424.png "Configurer l’authentification unique")

##Configuration de l'approvisionnement des utilisateurs

Pour se connecter à **CloudBees**, les utilisateurs d’Azure AD doivent être approvisionnés dans **CloudBees**. Dans le cas de **CloudBees**, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur dans CloudBees, procédez comme suit :

1.  Connectez-vous au site d’entreprise **CloudBees** en tant qu’administrateur.

2.  Accédez à **Edit Users**.

    ![Utilisateurs](./media/active-directory-saas-cloudbees-tutorial/IC790429.png "Utilisateurs")

3.  Cliquez sur **Add**.

    ![Ajouter](./media/active-directory-saas-cloudbees-tutorial/IC790430.png "Ajouter")

4.  Dans la section **Add User**, procédez comme suit :

    ![Ajouter un utilisateur](./media/active-directory-saas-cloudbees-tutorial/IC790431.png "Ajouter un utilisateur")

    1.  Indiquez l’adresse e-mail, le prénom, le nom et d’autres attributs d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte **Email**, **First Name**, **Last Name**, etc.
    2.  Cliquez sur **Add User**.

        >[AZURE.NOTE]Un message électronique contenant des instructions de connexion est envoyé au propriétaire du compte.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur fourni par CloudBees pour approvisionner des comptes d’utilisateurs AAD.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à CloudBees, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **CloudBees**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-cloudbees-tutorial/IC790432.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-cloudbees-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](https://msdn.microsoft.com/library/azure/dn308586.aspx).

<!---HONumber=August15_HO7-->