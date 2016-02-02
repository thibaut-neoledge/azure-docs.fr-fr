<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à Mindflash | Microsoft Azure" 
    description="Apprenez à utiliser Mindflash avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2015" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory avec Mindflash
  
L'objectif de ce didacticiel est de montrer comment intégrer Azure et Mindflash. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Mindflash pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Mindflash pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Mindflash (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l'intégration d'applications pour Mindflash
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-mindflash-tutorial/IC787132.png "Scénario")
##Activation de l'intégration d'applications pour Mindflash
  
Cette section décrit l'activation de l'intégration de l'application pour Mindflash.

###Pour activer l'intégration d’applications pour Mindflash, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-mindflash-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-mindflash-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-mindflash-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-mindflash-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Mindflash**.

    ![Galerie d’applications](./media/active-directory-saas-mindflash-tutorial/IC787133.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Mindflash**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Mindflash](./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s'authentifier sur Mindflash avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure Active Directory, puis dans la page d’intégration d’applications **Mindflash**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-mindflash-tutorial/IC787135.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Mindflash**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-mindflash-tutorial/IC787136.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez votre URL selon le modèle suivant « **http://company.mindflash.com*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-mindflash-tutorial/IC787137.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Mindflash**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-mindflash-tutorial/IC787138.png "Configurer l’authentification unique")

5.  Envoyez le fichier de métadonnées à l’équipe du support technique Mindflash.

    >[AZURE.NOTE]La configuration de l’authentification unique doit être effectuée par l’équipe du support technique Mindflash. Vous recevez une notification dès qu’elle est terminée.

6.  Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-mindflash-tutorial/IC787139.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour permettre aux utilisateurs Azure AD de se connecter à Mindflash, vous devez les approvisionner dans Mindflash. En l’occurrence, cet approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous au site d’entreprise **Mindflash** en tant qu’administrateur.

2.  Accédez à **Gérer les utilisateurs**.

    ![Gérer les utilisateurs](./media/active-directory-saas-mindflash-tutorial/IC787140.png "Gérer les utilisateurs")

3.  Cliquez sur **Add Users**, puis sur **New**.

4.  Dans la section **Add New Users**, procédez comme suit :

    ![Ajouter de nouveaux utilisateurs](./media/active-directory-saas-mindflash-tutorial/IC787141.png "Ajouter de nouveaux utilisateurs")

    1.  Indiquez le prénom, le nom et l’adresse de messagerie du compte AAD valide que vous souhaitez approvisionner dans les zones de texte **First Name**, **Last Name** et **Email**.
    2.  Cliquez sur **Add**.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur, fourni par Mindflash, pour approvisionner des comptes d’utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Mindflash, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **Mindflash**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-mindflash-tutorial/IC787142.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-mindflash-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->