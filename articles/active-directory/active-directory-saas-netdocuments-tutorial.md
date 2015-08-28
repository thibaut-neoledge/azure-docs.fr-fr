<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à NetDocuments | Microsoft Azure" description="Apprenez à utiliser NetDocuments avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à NetDocuments
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=529696).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et NetDocuments. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire NetDocuments
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à NetDocuments pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise NetDocuments (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour NetDocuments
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-netdocuments-tutorial/IC795040.png "Scénario")
##Activation de l’intégration d’application pour NetDocuments
  
Cette section décrit l’activation de l’intégration d’application pour NetDocuments.

###Pour activer l’application de l’intégration pour NetDocuments, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-netdocuments-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-netdocuments-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-netdocuments-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la zone **Rechercher**, entrez **NetDocuments**.

    ![Galerie d’applications](./media/active-directory-saas-netdocuments-tutorial/IC795041.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **NetDocuments**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![NetDocuments](./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur NetDocuments avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.
La configuration de l’authentification unique pour NetDocuments oblige à récupérer une valeur d’empreinte numérique dans un certificat.
Si cette procédure ne vous est pas familière, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure Active Directory, puis dans la page d’intégration d’application **NetDocuments**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue \*\*Configurer l’authentification unique\*\*.

    ![Configurer l’authentification unique](./media/active-directory-saas-netdocuments-tutorial/IC795043.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à NetDocuments**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-netdocuments-tutorial/IC795044.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, procédez comme suit :

    ![Configurer l’URL de l’application](./media/active-directory-saas-netdocuments-tutorial/IC795045.png "Configurer l’URL de l’application")

    1.  Dans la zone de texte **URL d’authentification**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application NetDocuments (par exemple : « *https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*").
    2.  Dans la zone de texte **URL de réponse NetDocuments**, tapez la valeur que vous venez de taper dans la zone de texte **URL d’authentification**.  

        >[AZURE.NOTE]Vous trouverez la valeur correcte à la fin de la boîte de dialogue **Federated Identity** (voir la capture d’écran de l’étape 9).

    3.  Cliquez sur **Suivant**

4.  Dans la page **Configurer l’authentification unique sur NetDocuments**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-netdocuments-tutorial/IC795046.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise NetDocuments en tant qu’administrateur.

6.  Accédez à **Admin**.

7.  Cliquez sur **Add and remove users and groups**.

    ![Référentiel](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Référentiel")

8.  Cliquez sur **Configure advanced authentication options**.

    ![Configurer les options d’authentification avancées](./media/active-directory-saas-netdocuments-tutorial/IC795048.png "Configurer les options d’authentification avancées")

9.  Dans la boîte de dialogue **Federated Identity**, procédez comme suit :

    ![Identité fédérée](./media/active-directory-saas-netdocuments-tutorial/IC795049.png "Identité fédérée")

    1.  Pour **Federated identity server type**, sélectionnez **Active Directory Federation Services**.
    2.  Pour télécharger le fichier de métadonnées, cliquez sur **Choose file**.
    3.  Cliquez sur **OK**.

10. Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-netdocuments-tutorial/IC795050.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour permettre aux utilisateurs Azure AD de se connecter à NetDocuments, vous devez les approvisionner dans NetDocuments. Dans le cas de NetDocuments, l’approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **NetDocuments** en tant qu’administrateur.

2.  Dans le menu situé en haut, cliquez sur **Admin**.

    ![Administrateur](./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Administrateur")

3.  Cliquez sur **Add and remove users and groups**.

    ![Référentiel](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Référentiel")

4.  Dans la zone de texte **Email Address**, tapez l’adresse de messagerie d’un compte Azure Active Directory valide à approvisionner, puis cliquez sur **Add User**.

    ![Adresse de messagerie](./media/active-directory-saas-netdocuments-tutorial/IC795053.png "Adresse de messagerie")

    >[AZURE.NOTE]Le titulaire du compte Azure Active Directory recevra un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par NetDocuments, pour approvisionner des comptes utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à NetDocuments, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application \*\*NetDocuments\*\*, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-netdocuments-tutorial/IC795054.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-netdocuments-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!-----HONumber=August15_HO7-->