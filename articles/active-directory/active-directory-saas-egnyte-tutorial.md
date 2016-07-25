<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Egnyte | Microsoft Azure" 
    description="Apprenez à utiliser Egnyte avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/11/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory à Egnyte
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Egnyte.
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Egnyte pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Egnyte pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Egnyte (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Egnyte
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-egnyte-tutorial/IC787812.png "Scénario")
##Activation de l’intégration d’application pour Egnyte
  
Cette section décrit l’activation de l’intégration d’application pour Egnyte.

###Pour activer l’intégration d’application pour Egnyte, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-egnyte-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-egnyte-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-egnyte-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-egnyte-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Egnyte**.

    ![Galerie d’applications](./media/active-directory-saas-egnyte-tutorial/IC787813.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Egnyte**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Egnyte](./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Egnyte avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64.  
Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Sur la page d’intégration d’applications **Egnyte** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-egnyte-tutorial/IC787815.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Egnyte**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-egnyte-tutorial/IC787816.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Egnyte**, tapez votre URL selon le modèle suivant « *https://company.egnyte.com*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-egnyte-tutorial/IC787817.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Egnyte**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-egnyte-tutorial/IC787818.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Egnyte en tant qu’administrateur.

6.  Cliquez sur **Settings**.

    ![Paramètres](./media/active-directory-saas-egnyte-tutorial/IC787819.png "Paramètres")

7.  Dans le menu, cliquez sur **Settings**.

    ![Paramètres](./media/active-directory-saas-egnyte-tutorial/IC787820.png "Paramètres")

8.  Cliquez sur l’onglet **Configuration** puis sur **Security**.

    ![Sécurité](./media/active-directory-saas-egnyte-tutorial/IC787821.png "Sécurité")

9.  Dans la section **Single Sign-On Authentication**, procédez comme suit :

    ![Authentification unique](./media/active-directory-saas-egnyte-tutorial/IC787822.png "Authentification unique")

    1.  Pour **Single sign-on authentication**, sélectionnez **SAML 2.0**.
    2.  Pour **Identity provider**, sélectionnez **AzureAD**.
    3.  Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Egnyte** de la boîte de dialogue, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **Identity provider login URL**.
    4.  Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Egnyte**, copiez la valeur **ID d’identité**, puis collez-la dans la zone de texte **Identity provider entity ID**.
    5.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.

        >[AZURE.TIP]Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    6.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Identity provider certificate**.
    7.  Pour **Default user mapping**, sélectionnez **Email address**.
    8.  Pour **Use domain-specific issuer value**, sélectionnez **disabled**.
    9.  Cliquez sur **Enregistrer**.

10. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-egnyte-tutorial/IC787823.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à Egnyte, les utilisateurs d’Azure AD doivent être approvisionnés dans Egnyte.  
Dans le cas de Egnyte, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Egnyte** en tant qu’administrateur.

2.  Accédez à **Settings > Users & Groups**.

3.  Cliquez sur **Add New User**, puis sélectionnez le type d’utilisateur à ajouter.

    ![Utilisateurs](./media/active-directory-saas-egnyte-tutorial/IC787824.png "Utilisateurs")

4.  Dans la section **New Standard User**, procédez comme suit :

    ![Nouvel utilisateur standard](./media/active-directory-saas-egnyte-tutorial/IC787825.png "Nouvel utilisateur standard")

    1.  Tapez l’**adresse de messagerie**, le **nom d’utilisateur** et les autres informations d’un compte Azure Active Directory valide à approvisionner.
    2.  Cliquez sur **Enregistrer**.

    >[AZURE.NOTE] Le titulaire du compte Azure Active Directory recevra une notification par courrier électronique.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur Egnyte fourni par ce site pour approvisionner des comptes d’utilisateurs AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Egnyte, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’application **Egnyte**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-egnyte-tutorial/IC787826.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-egnyte-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->