<properties 
    pageTitle="Didacticiel : Intégration d’Azure AD à Veracode | Microsoft Azure" 
    description="Découvrez comment utiliser Veracode avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
    services="active-directory" 
    authors="markusvi"  
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

#Didacticiel : Intégration d’Azure AD à Veracode
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Veracode. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Veracode pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Veracode pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Veracode (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md)
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Veracode
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-veracode-tutorial/IC802903.png "Scénario")

##Activation de l’intégration d’applications pour Veracode
  
Cette section décrit l’activation de l’intégration d’applications pour Veracode.

###Pour activer l’intégration d’applications pour Veracode, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-veracode-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-veracode-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-veracode-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Veracode**.

    ![Galerie d’applications](./media/active-directory-saas-veracode-tutorial/IC802904.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Veracode**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Veracode](./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Veracode avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Votre application Veracode s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **Attributs du jeton SAML**. La capture d’écran suivante montre un exemple :

![Attributs](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributs")

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **Veracode** du portail Azure AD, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-veracode-tutorial/IC802907.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Veracode**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-veracode-tutorial/IC802908.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres de l’application**, cliquez sur **Suivant**.

    ![Configurer les paramètres d’application](./media/active-directory-saas-veracode-tutorial/IC802909.png "Configurer les paramètres d’application")

4.  Dans la page **Configurer l’authentification unique sur Veracode**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-veracode-tutorial/IC802910.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Veracode en tant qu’administrateur.

6.  Dans le menu situé en haut, cliquez sur **Settings** puis sur **Admin**.

    ![Administration](./media/active-directory-saas-veracode-tutorial/IC802911.png "Administration")

7.  Cliquez sur l’onglet **SAML**.

8.  Dans la section **Organization SAML Settings**, procédez comme suit :

    ![Administration](./media/active-directory-saas-veracode-tutorial/IC802912.png "Administration")

    1.  Dans la page de la boîte de dialogue **Configurer l’authentification unique sur Veracode** du portail Azure, copiez la valeur de **URL de l’émetteur** et collez-la dans la zone de texte **Issuer**.
    2.  Pour charger votre certificat téléchargé, cliquez sur**Choose File**.
    3.  Sélectionnez **Enable Self Registration**.

9.  Dans la section **Self Registration Settings**, procédez comme suit, puis cliquez sur **Save** :

    ![Administration](./media/active-directory-saas-veracode-tutorial/IC802913.png "Administration")

    1.  Dans **New User Activation**, sélectionnez **No Activation Required**.
    2.  Dans **User Data Updates**, sélectionnez **Preference Veracode User Data**.
    3.  Dans **SAML Attribute Details**, sélectionnez les éléments suivants :
        -   **Rôles d’utilisateur**
        -   **Administrateur de la stratégie**
        -   **Réviseur**
        -   **Responsable de la sécurité**
        -   **Responsable**
        -   **Expéditeur**
        -   **Créateur**
        -   **Tous les types d’analyse**
        -   **Appartenance aux équipes**
        -   **Équipe par défaut**

10. Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-veracode-tutorial/IC802914.png "Configurer l’authentification unique")

11. Dans le menu situé en haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**.

    ![Attributs](./media/active-directory-saas-veracode-tutorial/IC795920.png "Attributs")

12. Pour ajouter les mappages d’attribut requis, procédez comme suit :

    ![Attributs](./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributs")

	| Nom de l'attribut | Valeur de l’attribut |
	|:---------------|:----------------|
	| firstname | User.givenname |
	| lastname | User.Surname |
	| email | User.mail |

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut utilisateur**.
    
	2.  Dans la zone de texte **Nom de l’attribut**, tapez le nom d’attribut indiqué pour cette ligne.

    3.  Dans la zone de texte **Valeur de l’attribut**, sélectionnez la valeur d’attribut indiquée pour cette ligne.

    4.  Cliquez sur **Terminé**.

13. Cliquez sur **Appliquer les modifications**.

##Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à Veracode, les utilisateurs d’Azure AD doivent être approvisionnés dans Veracode. Dans le cas de Veracode, l’approvisionnement est une tâche automatique. Vous n’avez rien à faire.
  
Au besoin, les utilisateurs sont automatiquement créés lors de la première tentative d’authentification unique.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par Veracode, pour approvisionner des comptes d’utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Veracode, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte test.

2.  Dans la page d’intégration d’applications **Veracode**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-veracode-tutorial/IC802915.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-veracode-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->