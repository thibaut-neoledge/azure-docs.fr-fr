<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à Gigya | Microsoft Azure" 
    description="Apprenez à utiliser Gigya avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore !" 
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
    ms.date="06/09/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory à Gigya
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Gigya. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Gigya pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Gigya pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Gigya (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Gigya
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/IC789512.png "Configurer l’authentification unique")
##Activation de l’intégration d’application pour Gigya
  
Cette section décrit l’activation de l’intégration d’application pour Gigya.

###Pour activer l’intégration d’application pour Gigya, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-gigya-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-gigya-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-gigya-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Gigya**.

    ![Galerie d’applications](./media/active-directory-saas-gigya-tutorial/IC789513.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Gigya**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Gigya](./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Gigya avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Sur la page d’intégration d’applications **Gigya** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/IC789528.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Gigya**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/IC789529.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Gigya**, tapez votre URL selon le modèle suivant « *http://company.gigya.com*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-gigya-tutorial/IC789530.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Gigya**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/IC789531.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Gigya en tant qu’administrateur.

6.  Accédez à **Settings > SAML Login**, puis cliquez sur le bouton **Add**.

    ![Connexion SAML](./media/active-directory-saas-gigya-tutorial/IC789532.png "Connexion SAML")

7.  Dans la section **SAML Login**, procédez comme suit :

    ![Configuration SAML](./media/active-directory-saas-gigya-tutorial/IC789533.png "Configuration SAML")

    1.  Dans la zone de texte **Name**, tapez le nom de votre configuration.
    2.  Sur la page **Configurer l’authentification unique sur Gigya** du portail Azure Classic, copiez la valeur **URL de l’émetteur**, puis collez-la dans la zone de texte **Issuer**.
    3.  Dans la page **Configurer l’authentification unique sur Gigya** du portail Azure Classic, copiez la valeur **URL du service d’authentification unique**, puis collez-la dans la zone de texte **Single Sign-On Service URL**.
    4.  Dans le portail Azure Classic, dans la page **Configurer l’authentification unique sur Gigya**, copiez la valeur **Format de l’identification du nom**, puis collez-la dans la zone de texte **Name ID Format**.
    5.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.
        
		>[AZURE.TIP]Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    6.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **X.509 Certificate**.
    7.  Cliquez sur **Save Settings**.

8.  Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-gigya-tutorial/IC789534.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à Gigya, les utilisateurs d’Azure AD doivent être approvisionnés dans Gigya. Dans le cas de Gigya, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous au site d’entreprise **Gigya** en tant qu’administrateur.

2.  Accédez à **Admin > Manage Users**, puis cliquez sur **Invite Users**.

    ![Gérer les utilisateurs](./media/active-directory-saas-gigya-tutorial/IC789535.png "Gérer les utilisateurs")

3.  Dans la boîte de dialogue Invite Users, procédez comme suit :

    ![Inviter des utilisateurs](./media/active-directory-saas-gigya-tutorial/IC789536.png "Inviter des utilisateurs")

    1.  Dans la zone de texte **Email**, tapez l’alias de courrier électronique d’un compte Azure Active Directory valide à approvisionner.
    2.  Cliquez sur **Invite User**.
    
        >[AZURE.NOTE] Le titulaire du compte Azure Active Directory recevra un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur fourni par Gigya pour approvisionner des comptes d’utilisateurs Azure Active Directory.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Gigya, procédez comme suit :

1.  Dans le portail Azure Classic, créez un compte de test.

2.  Dans la page d’intégration d’application **Gigya**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-gigya-tutorial/IC789537.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-gigya-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0615_2016-->