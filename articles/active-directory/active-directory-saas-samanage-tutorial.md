<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Samanage | Microsoft Azure" description="Apprenez à utiliser Samanage avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Samanage
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=522516).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Samanage. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Samanage
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Samanage pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Samanage (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586)
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Samanage
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-samanage-tutorial/IC771705.png "Scénario")
##Activation de l’intégration d’application pour Samanage
  
Cette section décrit l’activation de l’intégration d’application pour Samanage.

###Pour activer l’intégration d’application pour Samanage, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-samanage-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-samanage-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-samanage-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-samanage-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Samanage**.

    ![Galerie d'applications](./media/active-directory-saas-samanage-tutorial/IC771707.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Samanage**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Samanage](./media/active-directory-saas-samanage-tutorial/IC771708.png "Samanage")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Samanage avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si vous n’êtes pas familiarisé avec cette procédure, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure Active Directory, puis dans la page d’intégration d’application **Samanage**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue \*\*Configurer l’authentification unique\*\*.

    ![Configurer l’authentification unique](./media/active-directory-saas-samanage-tutorial/IC771709.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Samanage**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Authentification unique Microsoft Azure AD](./media/active-directory-saas-samanage-tutorial/IC771710.png "Authentification unique Microsoft Azure AD")

3.  Dans la zone de texte **URL de connexion à Samanage** de la page **Configurer l’URL de l’application**, tapez votre URL au format « *https://\<nom\_locataire>.samanage.com* », puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-samanage-tutorial/IC771711.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Samanage**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-samanage-tutorial/IC777613.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Samanage en tant qu’administrateur.

6.  Cliquez sur **Dashboard** et sélectionnez **Setup** dans le volet de navigation de gauche.

    ![Tableau de bord](./media/active-directory-saas-samanage-tutorial/IC771712.png "Tableau de bord")

7.  Cliquez sur **Single Sign-On**.

    ![Authentification unique](./media/active-directory-saas-samanage-tutorial/IC771713.png "Authentification unique")

8.  Dans la page **Login using SAML**, procédez comme suit, puis cliquez sur **Save Changes** :

    1.  Cliquez sur **Enable Single Sign-On with SAML**. ![Connexion avec SAML](./media/active-directory-saas-samanage-tutorial/IC771719.png "Connexion avec SAML")
    2.  Dans la page **Configurer l’authentification unique sur Samanage** du portail Azure, copiez la valeur de **ID du fournisseur d’identité** et collez-la dans la zone de texte \*\*Identity Provider URL\*\*.![Configurer l’authentification unique](./media/active-directory-saas-samanage-tutorial/IC771720.png "Configurer l’authentification unique")
3.  Dans la page **Configurer l’authentification unique sur Samanage** du portail Azure, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **Login URL**.
    4.  Dans la page **Configurer l’authentification unique sur Samanage** du portail Azure, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **Logout URL**.
    5.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.  

        >[AZURE.TIP]Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    6.  Ouvrez votre certificat codé en base 64 dans le bloc-notes, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **X.509 Certificate**.
    7.  Cliquez sur **Create users if they do not exist in Samanage**. ![Mettre à jour](./media/active-directory-saas-samanage-tutorial/IC771722.png "Mettre à jour")
    8.  Cliquez sur **Update**.

9.  Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-samanage-tutorial/IC771723.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à Samanage, les utilisateurs d’Azure AD doivent être approvisionnés dans Samanage. Dans le cas de Samanage, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre locataire **Samanage**.

2.  Accédez à **Dashboard > Setup**.

    ![Paramétrage](./media/active-directory-saas-samanage-tutorial/IC771724.png "Paramétrage")

3.  Cliquez sur l’onglet **Users**.

    ![Utilisateurs](./media/active-directory-saas-samanage-tutorial/IC771725.png "Utilisateurs")

4.  Cliquez sur **New User**.

    ![Nouvel utilisateur](./media/active-directory-saas-samanage-tutorial/IC771726.png "Nouvel utilisateur")

5.  Entrez l’adresse de messagerie et le nom du compte Azure AD que vous souhaitez approvisionner dans les zones de texte **Email address** et **Name** , puis cliquez sur **Create user**.

    >[AZURE.NOTE]Le détenteur du compte AAD recevra un message électronique et suivra un lien pour confirmer le compte avant qu’il ne soit activé.

    ![Créer un utilisateur](./media/active-directory-saas-samanage-tutorial/IC771727.png "Créer un utilisateur")

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Samanage pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Samanage, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application \*\*Samanage\*\*, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-samanage-tutorial/IC771728.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-samanage-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->