<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à Benefitsolver | Microsoft Azure"
    description="Apprenez à utiliser Benefitsolver avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Didacticiel : Intégration d’Azure Active Directory à Benefitsolver

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Benefitsolver. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Benefitsolver pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Benefitsolver pourront s’authentifier de manière unique dans l’application à l’aide de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md)

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Benefitsolver
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Scénario")
##Activation de l’intégration d’applications pour Benefitsolver

Cette section décrit l’activation de l’intégration d’applications pour Benefitsolver.

###Pour activer l’intégration d’applications pour Benefitsolver, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Benefitsolver**.

    ![Galerie d’applications](./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Benefitsolver**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Benefitsolver](./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitsolver")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Benefitsolver avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Votre application Benefitsolver s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration des **attributs du jeton SAML**. La capture d’écran suivante montre un exemple :

![Attributs](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributs")

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, dans la page d’intégration d’applications **Benefitsolver**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Benefitsolver**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres de l’application**, procédez comme suit :

    ![Configurer les paramètres d’application](./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Configurer les paramètres d’application")

    1.  Dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Benefitsolver (par exemple, “*http://azure-dev.benefitsolver.com*
    2.  Dans la zone de texte **URL de réponse**, tapez votre URL AssertionConsumerService Benefitsolver (par exemple, “*https://dev.benefitsolver.com/benefits/BenefitSolverView?page\_name=single\_signon\_saml*”).  

        >[AZURE.NOTE]Vous pouvez obtenir la valeur réelle pour votre environnement auprès de l’équipe de support technique Benefitsolver.

    3.  Cliquez sur **Next**.

4.  Dans la page **Configurer l’authentification unique sur Benefitsolver**, pour télécharger vos métadonnées, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Configurer l’authentification unique")

5.  Envoyez le fichier de métadonnées téléchargé à l’équipe de support technique Benefitsolver.

    >[AZURE.NOTE]L’équipe de support technique Benefitsolver doit se charger de la configuration de l’authentification unique. Vous recevrez une notification lorsque l’authentification unique aura été activée pour votre abonnement.

6.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Configurer l’authentification unique")

7.  Dans le menu situé en haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**.

    ![Attributs](./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Attributs")

8.  Pour ajouter les mappages d’attribut requis, procédez comme suit :

    ![Attributs](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributs")

	|Nom de l'attribut|Valeur de l’attribut|
    |---|---|
    |ClientID|L’équipe de support technique Benefitsolver doit vous fournir cette valeur.|
    |ClientKey|L’équipe de support technique Benefitsolver doit vous fournir cette valeur.|
    |LogoutURL|L’équipe de support technique Benefitsolver doit vous fournir cette valeur.|
    |EmployeeID|L’équipe de support technique Benefitsolver doit vous fournir cette valeur.|

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut**, tapez le nom d’attribut indiqué pour cette ligne.
    3.  Dans la zone de texte **Valeur de l’attribut**, sélectionnez la valeur d’attribut indiquée pour cette ligne.
    4.  Cliquez sur **Terminé**.

9.  Cliquez sur **Appliquer les modifications**.
##Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Benefitsolver, vous devez les approvisionner dans Benefitsolver. Dans le cas de Benefitsolver, les utilisateurs doivent être créés manuellement par l’équipe de support technique Benefitsolver.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Benefitsolver pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Benefitsolver, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **Benefitsolver**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->