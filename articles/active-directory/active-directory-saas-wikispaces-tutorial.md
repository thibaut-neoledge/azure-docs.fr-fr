<properties 
    pageTitle="Didacticiel : Intégration d’Azure AD à Wikispaces | Microsoft Azure" 
    description="Découvrez comment utiliser Wikispaces avec Azure AD pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Didacticiel : Intégration d’Azure AD à Wikispaces
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Wikispaces. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Wikispaces pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Wikispaces pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Wikispaces (connexion initiée par le fournisseur de services) ou à l’aide de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Wikispaces
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Scénario")

##Activation de l’intégration d’applications pour Wikispaces
  
Cette section décrit l’activation de l’intégration d’applications pour Wikispaces.

###Pour activer l’intégration d’applications pour Wikispaces, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Wikispaces**.

    ![Galerie d’applications](./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Wikispaces**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Wikispaces](./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Wikispaces avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **Wikispaces** du portail Azure AD, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Wikispaces**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Wikispaces**, tapez l’URL selon le modèle « **http://company.wikispaces.net*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Wikispaces**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configurer l’authentification unique")

5.  Envoyez le fichier de métadonnées à l’équipe du support technique Wikispaces.

    >[AZURE.NOTE]La configuration de l’authentification unique doit être effectuée par l’équipe du support technique Wikispaces. Vous recevez une notification dès qu’elle est terminée.

6.  Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configurer l’authentification unique")

##Configuration de l'approvisionnement des utilisateurs
  
Pour pouvoir se connecter à Wikispaces, les utilisateurs d’Azure AD doivent être approvisionnés dans Wikispaces. Dans le cas de Wikispaces, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Wikispaces** en tant qu’administrateur.

2.  Accédez à **Members**.

    ![Membres](./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Membres")

3.  Cliquez sur **Invite People**.

    ![Inviter des personnes](./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Inviter des personnes")

4.  Dans la section **Invite People**, procédez comme suit :

    ![Inviter des personnes](./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Inviter des personnes")

    1.  Indiquez les **noms d’utilisateur ou adresse de messagerie** d’un compte Azure AD valide que vous souhaitez approvisionner dans les zones de texte correspondantes.
    2.  Cliquez sur **Envoyer**.  

        >[AZURE.NOTE]Le titulaire du compte Azure AD reçoit un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Wikispaces pour approvisionner des comptes d’utilisateurs Azure AD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Wikispaces, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte test.

2.  Dans la page d’intégration d’applications **Wikispaces**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->