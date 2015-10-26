<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à BlueJeans | Microsoft Azure" description="Apprenez à utiliser BlueJeans avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à BlueJeans
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=526791).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et BlueJeans. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement BlueJeans pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à BlueJeans pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise BlueJeans (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour BlueJeans
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Scénario")
##Activation de l’intégration d’applications pour BlueJeans

Cette section décrit l’activation de l’intégration d’applications pour BlueJeans.

###Pour activer l’intégration d’applications pour BlueJeans, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **BlueJeans**.

    ![Galerie d’applications](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **BlueJeans**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![BlueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur BlueJeans avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’applications **BlueJeans**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à BlueJeans**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez votre URL selon le modèle "**https://company.BlueJeans.com*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur BlueJeans**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **BlueJeans** en tant qu’administrateur.

6.  Accédez à **ADMIN > Paramètres du groupe > Sécurité**.

    ![Administrateur](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Administrateur")

7.  Dans la section **Sécurité**, procédez comme suit :

    ![Authentification unique SAML](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "Authentification unique SAML")

    1.  Sélectionnez **Authentification unique SAML**.
    2.  Sélectionnez **Activer l’approvisionnement automatique**.

8.  Poursuivez en procédant comme suit :

    ![Chemin d’accès du certificat](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Chemin d’accès du certificat")

    1.  Cliquez sur **Choisir un fichier**, puis chargez le certificat téléchargé.
    2.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur BlueJeans**, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **URL de connexion**.
    3.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur BlueJeans**, copiez la valeur **Modifier l’URL de mot de passe** et collez-la dans la zone de texte **Modifier l’URL de mot de passe**.
    4.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur BlueJeans**, copiez la valeur **URL de déconnexion distante** et collez-la dans la zone de texte **URL de déconnexion**.

9.  Poursuivez en procédant comme suit :

    ![Enregistrer les modifications](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Enregistrer les modifications")

    1.  Dans la zone de texte **ID utilisateur**, tapez ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
2.  Dans la zone de texte **E-mail**, tapez ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
3.  Cliquez sur **Enregistrer les modifications**.

10. Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à BlueJeans, vous devez les approvisionner dans BlueJeans. En l’occurrence, cet approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **BlueJeans** en tant qu’administrateur.

2.  Accédez à **ADMIN > Gérer les utilisateurs > Ajouter un utilisateur**.

    ![Administrateur](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Administrateur")

    >[AZURE.IMPORTANT]L’onglet **Ajouter un utilisateur** est disponible uniquement si, sous l’onglet **Sécurité**, l’option **Activer l’approvisionnement automatique** est désactivée.

3.  Dans la section **Ajouter un utilisateur**, procédez comme suit :

    ![Ajouter un utilisateur](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Ajouter un utilisateur")

    1.  Tapez un **nom d’utilisateur BlueJeans**, une **adresse de messagerie**, un **ID de réunion BlueJeans**, un **code secret de modérateur**, un **nom complet** et la **société** d’un compte AAD valide que vous souhaitez approvisionner dans les zones de texte correspondantes.
    2.  Cliquez sur **Ajouter un utilisateur**.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par BlueJeans pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à BlueJeans, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **BlueJeans**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->