<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Panopto | Microsoft Azure" description="Apprenez à utiliser Panopto avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Panopto
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=524766).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Panopto. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Panopto
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Panopto pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Panopto (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586)
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Panopto
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-panopto-tutorial/IC777665.png "Scénario")
##Activation de l’intégration d’application pour Panopto
  
Cette section décrit l’activation de l’intégration d’application pour Panopto.

###Pour activer l’intégration d’application pour Panopto, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-panopto-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-panopto-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-panopto-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Panopto**.

    ![Galerie d’applications](./media/active-directory-saas-panopto-tutorial/IC777666.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Panopto**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Panopto](./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Panopto avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure Active Directory, puis dans la page d’intégration d’application **Panopto**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-panopto-tutorial/IC777667.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Panopto**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-panopto-tutorial/IC777668.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Panopto**, tapez votre URL selon le modèle suivant « *https://\<nom-locataire>. Panopto.com* », puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-panopto-tutorial/IC777528.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Panopto**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-panopto-tutorial/IC777669.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Panopto en tant qu’administrateur.

6.  Dans la barre d’outils située sur la gauche, cliquez sur **System**, puis sur **Identity Providers**.

    ![Système](./media/active-directory-saas-panopto-tutorial/IC777670.png "Système")

7.  Cliquez sur **Add Provider**.

    ![Fournisseurs d’identité](./media/active-directory-saas-panopto-tutorial/IC777671.png "Fournisseurs d’identité")

8.  Dans la section du fournisseur SAML, procédez comme suit :

    ![Configuration SaaS](./media/active-directory-saas-panopto-tutorial/IC777672.png "Configuration SaaS")

    1.  Dans la liste **Provider Type**, sélectionnez **SAML20**.
    2.  Dans la zone de texte **Instance Name**, attribuez un nom à votre instance.
    3.  Dans la zone de texte **Friendly Description**, entrez une description conviviale.
    4.  Dans la page **Configurer l’authentification unique sur Panopto** du portail Azure, copiez la valeur **URL de l’émetteur**, puis collez-la dans la zone de texte **Issuer**.
    5.  Dans la page **Configurer l’authentification unique sur Panopto** du portail Azure, copiez la valeur **URL d’authentification unique SAML**, puis collez-la dans la zone de texte **Bounce Page Url**.
    6.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.  

        >[AZURE.TIP]Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    7.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Public Key**.
    8.  Cliquez sur **Save**. ![Enregistrer](./media/active-directory-saas-panopto-tutorial/IC777673.png "Enregistrer")

9.  Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-panopto-tutorial/IC777674.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Aucun élément d’action ne vous permet de configurer l’approvisionnement des utilisateurs dans Panopto. Lorsqu’un utilisateur tente de se connecter à Panopto à l’aide du panneau d’accès, Panopto vérifie si cet utilisateur existe. Si aucun compte d’utilisateur n’est disponible, Panopto le crée automatiquement.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par Panopto, pour approvisionner des comptes utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Panopto, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application **Panopto**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-panopto-tutorial/IC777675.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-panopto-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->