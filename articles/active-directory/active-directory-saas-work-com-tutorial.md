<properties 
    pageTitle="Didacticiel : Intégration d’Azure AD à Work.com | Microsoft Azure" 
    description="Découvrez comment utiliser Work.com avec Azure AD pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore." 
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
    ms.date="01/12/2016" 
    ms.author="markvi" />

#Didacticiel : Intégration d’Azure AD à Work.com
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Work.com.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Work.com pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs d’AAD auxquels vous avez affecté un accès à Work.com pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Work.com (connexion initiée par le fournisseur de services) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Work.com
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-work-com-tutorial/IC794105.png "Scénario")

##Activation de l’intégration d’applications pour Work.com
  
Cette section décrit l’activation de l’intégration d’applications pour Work.com.

###Pour activer l’intégration d’applications pour Work.com, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-work-com-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-work-com-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-work-com-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Work.com**.

    ![Galerie d’applications](./media/active-directory-saas-work-com-tutorial/IC794106.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Work.com**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Work.com avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez charger un certificat vers Work.com.

>[AZURE.NOTE]Pour configurer l’authentification unique, vous devez encore configurer un nom de domaine personnalisé Work.com. Vous devez définir au moins un nom de domaine, le tester, puis le déployer dans l’ensemble de votre entreprise.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Connectez-vous à votre locataire Work.com en tant qu’administrateur.

2.  Accédez à **Setup**.

    ![Paramétrage](./media/active-directory-saas-work-com-tutorial/IC794108.png "Paramétrage")

3.  Dans le volet de navigation gauche, dans la section **Administer**, cliquez sur **Domain Management** pour développer la section associée, puis cliquez sur **My Domain** pour ouvrir la page **My Domain**.

    ![Mon domaine](./media/active-directory-saas-work-com-tutorial/IC767825.png "Mon domaine")

4.  Pour vérifier que votre domaine a été configuré correctement, assurez-vous qu’il figure dans **Step 4 Deployed to Users**, et passez en revue **My Domain Settings**.

    ![Domaine déployé pour l’utilisateur](./media/active-directory-saas-work-com-tutorial/IC784377.png "Domaine déployé pour l’utilisateur")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre portail Azure.

6.  Dans la page d’intégration d’applications **Work.com**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-work-com-tutorial/IC794109.png "Configurer l’authentification unique")

7.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Work.com**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-work-com-tutorial/IC794110.png "Configurer l’authentification unique")

8.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Work.com**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Work.com (par ex.” *http://company.my.salesforce.com*”), puis cliquez sur **Suivant** :

    ![Configurer l’URL de l’application](./media/active-directory-saas-work-com-tutorial/IC794111.png "Configurer l’URL de l’application")

9.  Dans la page **Configurer l’authentification unique sur Work.com**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-work-com-tutorial/IC794112.png "Configurer l’authentification unique")

10. Connectez-vous à votre locataire Work.com.

11. Accédez à **Setup**.

    ![Paramétrage](./media/active-directory-saas-work-com-tutorial/IC794108.png "Paramétrage")

12. Développez le menu **Security Controls**, puis cliquez sur **Single Sign-On Settings**.

    ![Paramètres d’authentification unique](./media/active-directory-saas-work-com-tutorial/IC794113.png "Paramètres d’authentification unique")

13. Dans la page **Single Sign on Settings**, procédez comme suit :

    ![SAML activé](./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML activé")

    1.  Sélectionnez **SAML Enabled**.
    2.  Cliquez sur **Nouveau**.

14. Dans la section **SAML Single Sign-On Settings**, procédez comme suit :

    ![Paramètre d’authentification unique SAML](./media/active-directory-saas-work-com-tutorial/IC794114.png "Paramètre d’authentification unique SAML")

    1.  Dans la zone de texte **Name**, tapez le nom de votre configuration.  

        >[AZURE.NOTE]Le fait d’entrer une valeur pour **Name** renseigne automatiquement la zone de texte **API Name**.

    2.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur Work.com** de la boîte de dialogue, copiez la valeur **URL de l’émetteur**, puis collez-la dans la zone de texte **Issuer**.
    3.  Pour charger le certificat téléchargé, cliquez sur **Parcourir**.
    4.  Dans la zone de texte **Entity Id**, tapez **https://salesforce-work.com**.
    5.  Pour **SAML Identity Type**, sélectionnez **Assertion contains the Federation ID from the User object**.
    6.  Pour **SAML Identity Location**, sélectionnez **Identity is in the NameIdentfier element of the Subject statement**.
    7.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur Work.com**, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **Identity Provider Login URL**.
    8.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur Work.com**, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **Identity Provider Logout URL**.
    9.  Pour **Service Provider Initiated Request Binding**, sélectionnez **HTTP POST**.
    10. Cliquez sur **Enregistrer**.

15. Dans le volet de navigation gauche du portail Work.com, cliquez sur **Domain Management** pour développer la section associée, puis sur **My Domain** pour ouvrir la page **My Domain**.

    ![Mon domaine](./media/active-directory-saas-work-com-tutorial/IC794115.png "Mon domaine")

16. Dans la section **Login Page Branding** de la page **My Domain**, cliquez sur **Edit**.

    ![Personnalisation de la page de connexion](./media/active-directory-saas-work-com-tutorial/IC767826.png "Personnalisation de la page de connexion")

17. Le nom des **SAML SSO Settings** s’affiche dans la section **Authentication Service** de la page **Login Page Branding**. Sélectionnez-le, puis cliquez sur **Save**.

    ![Personnalisation de la page de connexion](./media/active-directory-saas-work-com-tutorial/IC784366.png "Personnalisation de la page de connexion")

18. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-work-com-tutorial/IC794116.png "Configurer l’authentification unique")

##Configuration de l'approvisionnement des utilisateurs
  
Pour que les utilisateurs d’Azure AD puissent se connecter, leur accès doit être approvisionné dans Work.com.  
Dans le cas de Work.com, l’approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise Work.com en tant qu’administrateur.

2.  Accédez à **Setup**.

    ![Paramétrage](./media/active-directory-saas-work-com-tutorial/IC794108.png "Paramétrage")

3.  Accédez à **Manage Users > Users**.

    ![Gérer les utilisateurs](./media/active-directory-saas-work-com-tutorial/IC784369.png "Gérer les utilisateurs")

4.  Cliquez sur **New User**.

    ![Tous les utilisateurs](./media/active-directory-saas-work-com-tutorial/IC794117.png "Tous les utilisateurs")

5.  Dans la section User Edit, procédez comme suit :

    ![Modification de l’utilisateur](./media/active-directory-saas-work-com-tutorial/IC794118.png "Modification de l’utilisateur")

    1.  Dans les zones de texte **Last Name**, **Alias**, **Email**, **Username** et **Nickname**, tapez les attributs d’un compte Azure Active Directory valide que vous souhaitez approvisionner.
    2.  Sélectionnez **Role**, **User License** et **Profile**.
    3.  Cliquez sur **Enregistrer**.  

        >[AZURE.NOTE]Le titulaire du compte Azure AD reçoit un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Work.com pour approvisionner des comptes d’utilisateurs Azure AD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Work.com, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte test.

2.  Dans la page d’intégration d’applications Work.com, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-work-com-tutorial/IC794119.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-work-com-tutorial/IC767830.png "Oui")
  
À présent, vous devez patienter 10 minutes et vérifier que le compte a été synchronisé avec Work.com.
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0114_2016-->