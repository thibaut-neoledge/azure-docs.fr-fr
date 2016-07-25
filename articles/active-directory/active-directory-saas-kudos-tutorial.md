<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Kudos | Microsoft Azure" 
    description="Apprenez à utiliser Kudos avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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
    ms.date="07/08/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory à Kudos
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Kudos.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Kudos
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Kudos pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Kudos (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md)
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Kudos
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-kudos-tutorial/IC787799.png "Scénario")
##Activation de l’intégration d’application pour Kudos
  
Cette section décrit l’activation de l’intégration d’application pour Kudos.

###Pour activer l’intégration d’application pour Kudos, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-kudos-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-kudos-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-kudos-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-kudos-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Kudos**.

    ![Galerie d’applications](./media/active-directory-saas-kudos-tutorial/IC787800.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Kudos**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Kudos](./media/active-directory-saas-kudos-tutorial/IC787801.png "Kudos")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Kudos avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64.  
Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Sur la page d’intégration d’applications **Kudos** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kudos-tutorial/IC787802.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Kudos**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kudos-tutorial/IC787803.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Kudos**, tapez votre URL selon le modèle suivant « *https://company.kudosnow.com*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-kudos-tutorial/IC787804.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Kudos**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-kudos-tutorial/IC787805.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Kudos en tant qu’administrateur.

6.  Dans le menu situé en haut, cliquez sur **Settings**.

    ![Paramètres](./media/active-directory-saas-kudos-tutorial/IC787806.png "Paramètres")

7.  Cliquez sur **Integrations > SSO**.

8.  Dans la section **SSO**, procédez comme suit :

    ![Authentification unique](./media/active-directory-saas-kudos-tutorial/IC787807.png "Authentification unique")

    1.  Dans la page **Configurer l’authentification unique sur Kudos** du portail Azure Classic, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **Sign on URL**.
    2.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.

        >[AZURE.TIP]
        Pour plus d’informations, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    3.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **X.509 Certificate**.
    4.  Dans la page **Configurer l’authentification unique sur Kudos** du portail Azure Classic, copiez la valeur **URL du service de déconnexion unique**, puis collez-la dans la zone de texte **Logout To URL**.
    5.  Dans la zone de texte **Your Kudos URL**, tapez le nom de votre entreprise.
    6.  Cliquez sur **Enregistrer**.

9.  Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kudos-tutorial/IC787808.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour pouvoir se connecter à Kudos, les utilisateurs d’Azure Active Directory doivent être approvisionnés dans Kudos. Dans le cas de Kudos, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Kudos** en tant qu’administrateur.

2.  Dans le menu situé en haut, cliquez sur **Settings**.

    ![Paramètres](./media/active-directory-saas-kudos-tutorial/IC787806.png "Paramètres")

3.  Cliquez sur **User Admin**.

4.  Cliquez sur l’onglet **Users**, puis sur **Add a user**.

    ![Administration des utilisateurs](./media/active-directory-saas-kudos-tutorial/IC787809.png "Administration des utilisateurs")

5.  Dans la section **Add a User**, procédez comme suit :

    ![Ajouter un utilisateur](./media/active-directory-saas-kudos-tutorial/IC787810.png "Ajouter un utilisateur")

    1.  Tapez le prénom, le nom, l’adresse de messagerie et les autres détails d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir **First Name**, **Last Name** et **Email**.
    2.  Cliquez sur **Create User**.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Kudos pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Kudos, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’application **Kudos**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-kudos-tutorial/IC787811.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-kudos-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->