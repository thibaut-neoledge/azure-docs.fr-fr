<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Mimecast Admin Console | Microsoft Azure" description="Apprenez à utiliser Mimecast Admin Console avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory avec Mimecast Admin Console
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=529833).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Mimecast Admin Console. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Mimecast Admin Console pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Mimecast Admin Console pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Mimecast Admin Console (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Mimecast Admin Console
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "Scénario")
##Activation de l’intégration d’application pour Mimecast Admin Console
  
Cette section décrit l’activation de l’intégration d’application pour Mimecast Admin Console.

###Pour activer l’intégration d’application pour Mimecast Admin Console, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Mimecast Admin Console**.

    ![Galerie d’applications](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Mimecast Admin Console**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Console d’administration Mimecast](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Console d’administration Mimecast")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Mimecast Admin Console avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure Active Directory, puis dans la page d’intégration d’application **Mimecast Admin Console**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Mimecast Admin Console**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Mimecast Admin Console**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Mimecast Admin Console (par exemple : « https://webmail-fr.mimecast.com » ou « https://webmail-be.mimecast.com »), puis cliquez sur **Suivant**.

    >[AZURE.NOTE]L’URL d’ouverture de session est spécifique à la région.

    ![Configurer l’URL de l’application](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Mimecast Admin Console**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Mimecast Admin Console en tant qu’administrateur.

6.  Accédez à **Services > Application**.

    ![Services](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "Services")

7.  Cliquez sur **Authentication Profiles**.

    ![Profils d’authentification](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "Profils d’authentification")

8.  Cliquez sur **New Authentication Profile**.

    ![Nouveaux profils d’authentification](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "Nouveaux profils d’authentification")

9.  Dans la section **Authentication Profile**, procédez comme suit :

    ![Profil d’authentification](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "Profil d’authentification")

    1.  Dans la zone de texte **Description**, indiquez un nom pour votre configuration.
    2.  Sélectionnez **Enforce SAML Authentication for Mimecast Admin Console**.
    3.  Comme **Provider**, sélectionnez **Azure Active Directory**.
    4.  Dans la page de la boîte de dialogue **Configurer l’authentification unique sur Mimecast Admin Console** du portail Azure, copiez la valeur de **URL de l’émetteur** et collez-la dans la zone de texte **Issuer URL**.
    5.  Dans la page de la boîte de dialogue **Configurer l’authentification unique sur Mimecast Admin Console** du portail Azure, copiez la valeur de **URL de connexion distante** et collez-la dans la zone de texte **Issuer URL**.
    6.  Dans la page de la boîte de dialogue **Configurer l’authentification unique sur Mimecast Admin Console** du portail Azure, copiez la valeur de **URL de connexion distante** et collez-la dans la zone de texte **Logout URL**.  

        >[AZURE.NOTE]Les valeurs d’URL de connexion et de déconnexion sont identiques pour Mimecast Admin Console.

    7.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.

        >[AZURE.TIP]Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    8.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, supprimez la première (« *--* ») et la dernière ligne («* --* »), copiez le contenu restant dans le Presse-papiers, puis collez-le dans la zone de texte **Identity Provider Certificate (Metadata)**.
    9.  Sélectionnez **Allow Single Sign On**.
    10. Cliquez sur **Enregistrer**.

10. Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour permettre aux utilisateurs Azure AD de se connecter à Mimecast Admin Console, vous devez les approvisionner dans Mimecast Admin Console. Dans le cas de Mimecast Admin Console, l’approvisionnement est une tâche manuelle.
  
Vous devez enregistrer un domaine avant de pouvoir créer des utilisateurs.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous à **Mimecast Admin Console** en tant qu’administrateur.

2.  Accédez à **Directories > Internal**.

    ![Répertoires](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "Répertoires")

3.  Cliquez sur **Register New Domain**.

    ![Enregistrer un nouveau domaine](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "Enregistrer un nouveau domaine")

4.  Après avoir créé votre nouveau domaine, cliquez sur **New Address**.

    ![Nouvelle adresse](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "Nouvelle adresse")

5.  Dans la boîte de dialogue New Address, procédez comme suit :

    ![Enregistrer](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "Enregistrer")

    1.  Tapez l’adresse électronique, le nom global, le mot de passe et sa confirmation pour un compte Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Email Address**, **Global Name**, **Password** et **Confirm Password**.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur, fourni par Mimecast Admin Console, pour approvisionner des comptes d’utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Mimecast Admin Console, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application **Mimecast Admin Console**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->