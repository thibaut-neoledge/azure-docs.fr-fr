<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à Central Desktop | Microsoft Azure" description="Apprenez à utiliser Central Desktop avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Central Desktop
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=522411).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Central Desktop. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Central Desktop pour lequel l’authentification unique est activée / locataire Central Desktop

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Central Desktop
2.  Configuration de l’authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scénario")
##Activation de l’intégration d’applications pour Central Desktop

Cette section décrit l’activation de l’intégration d’applications pour Central Desktop.

###Pour activer l’intégration d’applications pour Central Desktop, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Central Desktop**.

    ![Galerie d’applications](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Central Desktop**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
##Configuration de l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Central Desktop avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat codé en base 64 sur votre locataire Central Desktop.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).



###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’applications **Central Desktop**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Central Desktop**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, procédez comme suit, puis cliquez sur **Suivant** :

    -   Dans la zone de texte **URL de connexion de Central Desktop**, tapez l’URL de votre client Central Desktop (par exemple, *http://contoso.centraldesktop.com*).
    -   Dans la zone de texte URL de réponse Central Desktop, tapez votre URL AssertionConsumerService Central Desktop (par exemple, https://contoso.centraldesktop.com/saml2-assertion.php).

    >[AZURE.NOTE]Vous pouvez obtenir la valeur à partir des métadonnées de Central Desktop (par exemple, *http://contoso.centraldesktop.com*).

    ![Configurer l’URL de l’application](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Central Desktop**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configurer l’authentification unique")

5.  Connectez-vous à votre client **Central Desktop**.

6.  Accédez à **Paramètres**, cliquez sur **Avancé**, puis sur **Authentification unique**.

    ![Paramétrage - Avancé](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Paramétrage - Avancé")

7.  Dans la page **Paramètres de l’authentification unique**, procédez comme suit :

    ![Paramètres d’authentification unique](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Paramètres d’authentification unique")

    1.  Sélectionnez **Activer l’authentification unique SAMLv2**.
    2.  Dans le portail Azure AD, dans la page **Configurer l’authentification unique sur Central Desktop**, copiez la valeur **URL de l’émetteur**, puis collez-la dans la zone de texte **URL de SSO**.
    3.  Dans le portail Azure AD, dans la page **Configurer l’authentification unique sur Central Desktop**, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **URL de connexion à SSO**.
    4.  Dans le portail Azure AD, dans la page **Configurer l’authentification unique sur Central Desktop**, copiez la valeur **URL du service de déconnexion unique**, puis collez-la dans la zone de texte **URL de déconnexion de SSO**.

8.  Dans la section **Méthode de vérification de signature de message**, procédez comme suit :

    ![Méthode de vérification de signature de message](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Méthode de vérification de signature de message")

    1.  Sélectionnez **Certificat**.
    2.  Dans la liste **Certificat SSO**, sélectionnez **RSH SHA256**.
    3.  Créez un fichier texte à partir du certificat téléchargé, copiez le contenu du fichier texte et collez-le dans le champ **Certificat SSO**.  

        >[AZURE.TIP]Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    4.  Sélectionnez **Afficher un lien vers votre page de connexion SAMLv2**.

9.  Cliquez sur **Mettre à jour**.

10. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configurer l’authentification unique")
##Configuration de l’approvisionnement des utilisateurs

Pour que les utilisateurs AAD puissent se connecter, ils doivent être approvisionnés dans l’application Central Desktop. Cette section explique comment créer des comptes d’utilisateur AAD dans Central Desktop.

###Pour approvisionner des comptes d’utilisateur dans Central Desktop :

1.  Connectez-vous à votre locataire Central Desktop.

2.  Accédez à **Personnes > Membres internes**.

3.  Cliquez sur **Ajouter des membres internes**.

    ![Personnes](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "Personnes")

4.  Dans la zone de texte **Adresses de messagerie des nouveaux membres**, tapez un compte AAD à approvisionner, puis cliquez sur **Suivant**.

    ![Adresses de messagerie des nouveaux membres](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Adresses de messagerie des nouveaux membres")

5.  Cliquez sur **Ajouter des membres internes**.

    ![Ajouter un membre interne](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Ajouter un membre interne")

    >[AZURE.NOTE]Les utilisateurs que vous avez ajoutés recevront un message électronique contenant un lien de confirmation sur lequel ils doivent cliquer pour activer le compte.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Central Desktop pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Central Desktop, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **Central Desktop**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!----HONumber=Sept15_HO2-->