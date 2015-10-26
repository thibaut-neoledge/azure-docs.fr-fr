<properties pageTitle="Didacticiel : Intégration d’Azure AD à Thirdlight | Microsoft Azure" description="Découvrez comment utiliser Salesforce avec Azure AD pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure AD à Thirdlight
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=529835).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Thirdlight. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Thirdlight pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Thirdlight pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Thirdlight (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Thirdlight
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-thirdlight-tutorial/IC805836.png "Scénario")

##Activation de l’intégration d’applications pour Thirdlight
  
Cette section décrit l’activation de l’intégration de l’application pour Thirdlight.

###Pour activer l’application de l’intégration pour Thirdlight, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-thirdlight-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-thirdlight-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-thirdlight-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Thirdlight**.

    ![Galerie d’applications](./media/active-directory-saas-thirdlight-tutorial/IC805837.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Thirdlight**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ThirdLight](./media/active-directory-saas-thirdlight-tutorial/IC805838.png "ThirdLight")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Thirdlight avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. La configuration de l’authentification unique pour Thirdlight oblige à récupérer une valeur d’empreinte numérique dans un certificat. Si vous n’êtes pas familiarisé avec cette procédure, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/YKQF266SAxI).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **Thirdlight** du portail Azure AD, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Thirdlight**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Configurer l’authentification unique")

3.  Dans la zone de texte **URL de connexion à Thirdlight** de la page **Configurer l’URL de l’application**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Thirdlight (par ex., **http://azuresso2.thirdlight.com/*")), puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-thirdlight-tutorial/IC805841.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique à Thirdlight**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Thirdlight en tant qu’administrateur.

6.  Accédez à **Configuration > System Administration**, puis cliquez sur **SAML2**.

    ![Administration système](./media/active-directory-saas-thirdlight-tutorial/IC805843.png "Administration système")

7.  Dans la section de configuration de SAML2, procédez comme suit :

    ![Authentification unique SAML](./media/active-directory-saas-thirdlight-tutorial/IC805844.png "Authentification unique SAML")

    1.  Sélectionnez **Enable SAML2 Single Sign-On**.
    2.  Dans **Source for IdP Metadata**, sélectionnez **Load IdP Metadata from XML**.
    3.  Ouvrez le fichier de métadonnées téléchargé, copiez son contenu, puis collez-le dans la zone de texte **Idp Metadata XML**.
    4.  Cliquez sur **Save SAML2 settings**.

8.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Configurer l’authentification unique")

##Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à Thirdlight, les utilisateurs d’Azure AD doivent être approvisionnés dans Thirdlight. Dans le cas de Thirdlight, l’approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous au site d’entreprise **Thirdlight** en tant qu’administrateur.

2.  Cliquez sur l’onglet **Users**.

3.  Sélectionnez **Users and Groups**.

4.  Cliquez sur le bouton **Add new User**.

5.  Renseignez les zones de texte **Username, Name or Description, Email, Choose a Preset or Group of New Members** du compte AAD valide que vous souhaitez approvisionner.

6.  Cliquez sur **Create**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur, fourni par Thirdlight, pour approvisionner des comptes d’utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Thirdlight, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte test.

2.  Dans la page d’intégration d’applications **Thirdlight**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-thirdlight-tutorial/IC805846.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-thirdlight-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->