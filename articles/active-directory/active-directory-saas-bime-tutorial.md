<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à Bime | Microsoft Azure" description="Apprenez à utiliser Bime avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Bime
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=524328).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Bime. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Bime

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Bime pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Bime (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Bime
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-bime-tutorial/IC775552.png "Scénario")
##Activation de l’intégration d’applications pour Bime

Cette section décrit l’activation de l’intégration d’applications pour Bime.

###Pour activer l’intégration d’applications pour Bime, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-bime-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-bime-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-bime-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-bime-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Bime**.

    ![Galerie d’applications](./media/active-directory-saas-bime-tutorial/IC775553.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Bime**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Bime](./media/active-directory-saas-bime-tutorial/IC775554.png "Bime")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Bime avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. La configuration de l’authentification unique pour Bime vous oblige à récupérer une valeur d’empreinte dans un certificat. Si cette procédure ne vous est pas familière, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’applications **Bime**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bime-tutorial/IC771709.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Bime**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bime-tutorial/IC775555.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez votre URL selon le modèle « *https://\<nom\_locataire>.Bimeapp.com* », puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-bime-tutorial/IC775556.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Bime**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sous **c:\\Bime.cer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bime-tutorial/IC775557.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Bime en tant qu’administrateur.

6.  Dans la barre d’outils, cliquez sur **Admin**, puis sur **Compte**.

    ![Administrateur](./media/active-directory-saas-bime-tutorial/IC775558.png "Administrateur")

7.  Dans la page de configuration du compte, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-bime-tutorial/IC775559.png "Configurer l’authentification unique")

    1.  Sélectionnez **Activer l’authentification SAML**.
    2.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Bime**, copiez la valeur **URL de connexion distante** et collez-la dans la zone de texte **URL de connexion distante**.
    3.  Copiez la valeur **Empreinte numérique** du certificat exporté, puis collez-la dans la zone de texte **Empreinte numérique du certificat**.  

        >[AZURE.TIP]Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

    4.  Cliquez sur **Enregistrer**.

8.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-bime-tutorial/IC775560.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Bime, vous devez les approvisionner dans Bime. En l’occurrence, cet approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous à votre locataire **Bime**.

2.  Dans la barre d’outils, cliquez sur **Admin**, puis sur **Utilisateurs**.

    ![Administrateur](./media/active-directory-saas-bime-tutorial/IC775561.png "Administrateur")

3.  Dans la **Liste des utilisateurs**, cliquez sur **Ajouter un nouvel utilisateur** (« + »).

    ![Utilisateurs](./media/active-directory-saas-bime-tutorial/IC775562.png "Utilisateurs")

4.  Dans la page de boîte de dialogue **Détails de l’utilisateur**, procédez comme suit :

    ![Détails de l’utilisateur](./media/active-directory-saas-bime-tutorial/IC775563.png "Détails de l’utilisateur")

    1.  Entrez le prénom, le nom, la connexion et l’adresse de messagerie d’un compte AAD valide que vous voulez approvisionner
    2.  Cliquez sur Enregistrer.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Bime pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Bime, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **Bime**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-bime-tutorial/IC775564.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-bime-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->