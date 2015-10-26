<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Rally Software | Microsoft Azure" description="Apprenez à utiliser Rally Software avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory avec Rally Software
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=521865).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Rally Software. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Rally Software
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Rally Software
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-rally-software-tutorial/IC769525.png "Scénario")
##Activation de l’intégration d’application pour Rally Software
  
Cette section décrit l’activation de l’intégration d’application pour Rally Software.

###Pour activer l’intégration d’application pour Rally Software, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-rally-software-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-rally-software-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-rally-software-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **rally**.

    ![Galerie d’applications](./media/active-directory-saas-rally-software-tutorial/IC769526.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Rally Software**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Rally Software avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez charger un certificat vers Rally Software.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, dans la page d’intégration d’application **Rally Software**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Rally**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Authentification unique Microsoft Azure AD](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Authentification unique avec Microsoft Azure AD")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Rally Software**, tapez votre URL selon le modèle suivant « *https://\<nom\_locataire>.rally.com* », puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Rally**, cliquez sur Télécharger les métadonnées, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configurer l’authentification unique")

5.  Connectez-vous à votre locataire **Rally Software**.

6.  Dans la barre d’outils située en haut, cliquez sur **Setup**, puis sélectionnez **Subscription**.

    ![Abonnement](./media/active-directory-saas-rally-software-tutorial/IC769531.png "Abonnement")

7.  Cliquez sur le bouton **Action** dans la barre d’outils située sur la droite, puis sélectionnez **Edit Subscription**.

8.  Dans la page **Subscription**, procédez comme suit, puis cliquez sur **Save & Close**.

    ![Authentification](./media/active-directory-saas-rally-software-tutorial/IC769542.png "Authentification")

    1.  Sélectionnez **Rally or SSO authentication** dans la liste déroulante Authentication
    2.  Dans la page **Configurer l’authentification unique sur Rally Software** du portail Azure, copiez la valeur de **ID du fournisseur d’identité** et collez-la dans la zone de texte **Identity Provider URL**.
    3.  Dans la page **Configurer l’authentification unique sur Rally Software** du portail Azure, copiez la valeur **URL de déconnexion distante**.

9.  Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour AAD les utilisateurs puissent se connecter, ils doivent être approvisionnés dans l’application Rally Software à l’aide de leurs noms d’utilisateur Azure Active Directory.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous à votre locataire Rally Software.

2.  Accédez à **Setup > USERS**, puis cliquez sur **+ Add New**.

    ![Utilisateurs](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Utilisateurs")

3.  Tapez le nom dans la zone de texte New User, puis cliquez sur **Add with Details**.

4.  Dans la section **Create User**, procédez comme suit :

    ![Créer un utilisateur](./media/active-directory-saas-rally-software-tutorial/IC781040.png "Créer un utilisateur")

    1.  Dans la zone de texte **User Name**, tapez le nom de l’utilisateur Azure AD que vous souhaitez approvisionner.
    2.  Dans la zone de texte **Email Address**, tapez l’adresse de messagerie de l’utilisateur Azure AD que vous souhaitez approvisionner.
    3.  Cliquez sur **Save & Close**.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Rally Software pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Rally Software, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application **Rally Software**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-rally-software-tutorial/IC769548.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->