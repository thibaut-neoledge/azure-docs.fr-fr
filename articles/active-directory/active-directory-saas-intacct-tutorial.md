<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Intacct | Microsoft Azure" 
    description="Apprenez à utiliser Intacct avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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
    ms.date="07/09/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory à Intacct
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Intacct. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Intacct
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Intacct pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Intacct (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md)
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Intacct
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-intacct-tutorial/IC790030.png "Scénario")
##Activation de l’intégration d’application pour Intacct
  
Cette section décrit l’activation de l’intégration d’application pour Intacct.

###Pour activer l’intégration d’application pour Intacct, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-intacct-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-intacct-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-intacct-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Intacct**.

    ![Galerie d’applications](./media/active-directory-saas-intacct-tutorial/IC790031.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Intacct**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Intacct avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Sur la page d’intégration d’applications **Intacct** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-intacct-tutorial/IC790033.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Intacct**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-intacct-tutorial/IC790034.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Intacct**, tapez votre URL selon le modèle suivant « *https://Intacct.com/company*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-intacct-tutorial/IC790035.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Intacct**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-intacct-tutorial/IC790036.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Intacct en tant qu’administrateur.

6.  Cliquez sur l’onglet **Company**, puis sur **Company Info**.

    ![Entreprise](./media/active-directory-saas-intacct-tutorial/IC790037.png "Entreprise")

7.  Cliquez sur l’onglet **Security**, puis sur **Edit**.

    ![Sécurité](./media/active-directory-saas-intacct-tutorial/IC790038.png "Sécurité")

8.  Dans la section **Single sign on (SSO)**, procédez comme suit :

    ![Authentification unique](./media/active-directory-saas-intacct-tutorial/IC790039.png "Authentification unique")

    1.  Sélectionnez **Enable Single Sign-On**.
    2.  Pour **Identity provider type**, sélectionnez **SAML 2.0**.
    3.  Dans la page **Configurer l’authentification unique sur Intacct** du portail Azure Classic, copiez la valeur **URL de l’émetteur**, puis collez-la dans la zone de texte **Issuer URL**.
    4.  Sur la page **Configurer l'authentification unique sur Intacct** du portail Azure Classic, copiez la valeur de l'**URL de connexion à distance**, puis collez-la dans la zone de texte **Login URL**.
    5.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.
        
		>[AZURE.TIP]Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    6.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificate**.
    7.  Cliquez sur **Enregistrer**.

9.  Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-intacct-tutorial/IC790040.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour pouvoir se connecter à Intacct, les utilisateurs d’Azure Active Directory doivent être approvisionnés dans Intacct. Dans le cas d’Intacct, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre locataire **Intacct**.

2.  Cliquez sur l’onglet **Company**, puis sur **Users**.

    ![Utilisateurs](./media/active-directory-saas-intacct-tutorial/IC790041.png "Utilisateurs")

3.  Cliquez sur l’onglet **Add**.

    ![Ajouter](./media/active-directory-saas-intacct-tutorial/IC790042.png "Ajouter")

4.  Dans la section **User Information**, procédez comme suit :

    ![Informations utilisateur](./media/active-directory-saas-intacct-tutorial/IC790043.png "Informations utilisateur")

    1.  Tapez l’ID utilisateur, le nom, le prénom, l’adresse de messagerie, le titre et le numéro de téléphone d’un compte Azure Active Directory que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **User ID**, **Last Name**, **First Name**, **Email address**, **Title** et **Phone**.
    2.  Sélectionnez les **privilèges d’administrateur** d’un compte Azure AD que vous voulez approvisionner.
    3.  Cliquez sur **Enregistrer**.
        
		>[AZURE.NOTE] Le détenteur du compte AAD recevra un message électronique et suivra un lien pour confirmer le compte avant qu’il ne soit activé.

>[AZURE.NOTE] Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Intacct pour approvisionner des comptes d’utilisateurs Azure Active Directory.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Intacct, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’application **Intacct**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-intacct-tutorial/IC790044.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-intacct-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->