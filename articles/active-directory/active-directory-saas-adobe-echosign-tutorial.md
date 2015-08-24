<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à Adobe EchoSign | Microsoft Azure" description="Apprenez à utiliser Adobe EchoSign avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore !" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Adobe EchoSign
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=528195).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Adobe EchoSign. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Adobe EchoSign pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Adobe EchoSign pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Adobe EchoSign (connexion initiée par le fournisseur du service) ou en s’appuyant sur la [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Adobe EchoSign
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Scénario")
##Activation de l’intégration d’application pour Adobe EchoSign

Cette section décrit l’activation de l’intégration d’application pour Adobe EchoSign.

###Pour activer l’intégration d’application pour Adobe EchoSign, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la zone **Rechercher**, tapez **Adobe EchoSign**.

    ![Galerie d’applications](./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Adobe EchoSign**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Adobe EchoSign](./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Adobe EchoSign avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, dans page d’intégration d’application **Adobe EchoSign**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue \*\*Configurer l’authentification unique\*\*.

    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Adobe EchoSign**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Adobe EchoSign**, tapez votre URL selon le modèle "\**https://company.echosign.com/*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Adobe EchoSign**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Adobe EchoSign en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **Account** puis, dans le volet de navigation à gauche, cliquez sur **SAML Settings** sous **Account Settings**.

    ![Compte](./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Compte")

7.  Dans la section SAML Settings, procédez comme suit :

    ![SAML Settings](./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "SAML Settings")

    1.  Pour **SAML Mode**, sélectionnez **SAML Mandatory**.
    2.  Sélectionnez **Allow EchoSign Account Administrators to log in using their EchoSign Credentials**.
    3.  Pour **User Creation**, sélectionnez **Automatically add users authenticated through SAML**.

8.  Ensuite, effectuez les étapes suivantes :

    ![SAML Settings](./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "SAML Settings")

    1.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur Adobe EchoSign**, copiez la valeur **ID d’identité**, puis collez-la dans la zone de texte **IdP Entity ID**.
    2.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur Adobe EchoSign**, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **IdP Login URL**.
    3.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur Adobe EchoSign**, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **IdP Logout URL**.
    4.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.  

		>[AZURE.TIP]Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).
    5.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **IdP Certificate**.
    6.  Cliquez sur **Save Changes**.

9.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Adobe EchoSign, vous devez les approvisionner dans Adobe EchoSign. Dans le cas d’Adobe EchoSign, cet approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Adobe EchoSign** en tant qu’administrateur.

2.  Dans le menu du haut, cliquez sur **Account** puis, dans le volet de navigation situé à gauche, cliquez sur **Users & Groups**, puis sur **Create a new user**.

    ![Compte](./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Compte")

3.  Dans la section **Create New User**, procédez comme suit :

    ![Create User](./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Create User")

    1.  Tapez dans l’adresse électronique, le prénom et le nom du compte AAD valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Email Address**, **First Name** et **Last Name**.
    2.  Cliquez sur **Create User**.

		>[AZURE.NOTE]Le titulaire du compte Azure Active Directory recevra un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur Adobe EchoSign fourni par ce service pour approvisionner des comptes d’utilisateurs Azure Active Directory.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Adobe EchoSign, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application \*\*Adobe EchoSign\*\*, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Oui")

Si vous voulez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez la page [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->