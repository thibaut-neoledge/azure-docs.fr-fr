<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Jobscience | Microsoft Azure" 
    description="Apprenez à utiliser Jobscience avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore !" 
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
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory à Jobscience
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Jobscience.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Jobscience pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Jobscience pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Jobscience (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Jobscience
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-jobscience-tutorial/IC784341.png "Scénario")
##Activation de l’intégration d’application pour Jobscience
  
Cette section décrit l’activation de l’intégration d’application pour Jobscience.

###Pour activer l’intégration d’application pour Jobscience, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-jobscience-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-jobscience-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-jobscience-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Jobscience**.

    ![Galerie d’applications](./media/active-directory-saas-jobscience-tutorial/IC784342.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Jobscience**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Jobscience avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
La configuration de l’authentification unique pour Jobscience oblige à récupérer une valeur d’empreinte numérique dans un certificat.  
Si cette procédure ne vous est pas familière, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Connectez-vous au site d’entreprise Jobscience en tant qu’administrateur.

2.  Accédez à **Setup**.

    ![Paramétrage](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Paramétrage")

3.  Dans le volet de navigation gauche, dans la section **Administer**, cliquez sur **Domain Management** pour développer la section associée, puis cliquez sur **My Domain** pour ouvrir la page **My Domain**.

    ![Mon domaine](./media/active-directory-saas-jobscience-tutorial/IC767825.png "Mon domaine")

4.  Pour vérifier que votre domaine a été configuré correctement, assurez-vous qu’il figure dans **Step 4 Deployed to Users**, et passez en revue **My Domain Settings**.

    ![Domaine déployé pour l’utilisateur](./media/active-directory-saas-jobscience-tutorial/IC784377.png "Domaine déployé pour l’utilisateur")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre portail Azure.

6.  Dans la page d’intégration d’application **Jobscience**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configurer l’authentification unique")

7.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Jobscience**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configurer l’authentification unique")

8.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Jobscience**, tapez votre URL selon le modèle suivant "*http://company.my.salesforce.com*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configurer l’URL de l’application")

9.  Dans la page **Configurer l’authentification unique sur Jobscience**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configurer l’authentification unique")

10. Sur le site d’entreprise Jobscience, cliquez sur **Security Controls**, puis sur **Single Sign-On Settings**.

    ![Contrôles de sécurité](./media/active-directory-saas-jobscience-tutorial/IC784364.png "Contrôles de sécurité")

11. Dans la section **Single Sign-On Settings**, procédez comme suit :

    ![Paramètres d’authentification unique](./media/active-directory-saas-jobscience-tutorial/IC781026.png "Paramètres d’authentification unique")

    1.  Sélectionnez **SAML Enabled**.
    2.  Cliquez sur **Nouveau**.

12. Dans la boîte de dialogue **SAML Single Sign-On Setting Edit**, procédez comme suit :

    ![Paramètre d’authentification unique SAML](./media/active-directory-saas-jobscience-tutorial/IC784365.png "Paramètre d’authentification unique SAML")

    1.  Dans la zone de texte **Name**, attribuez un nom à votre configuration.
    2.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur Jobscience** de la boîte de dialogue, copiez la valeur **URL de l’émetteur** et collez-la dans la zone de texte **Issuer**.
    3.  Dans la zone de texte **Entity Id**, tapez **https://salesforce-jobscience.com**.
    4.  Cliquez sur **Parcourir** pour charger votre certificat Azure AD.
    5.  Pour **SAML Identity Type**, sélectionnez **Assertion contains the Federation ID from the User object**.
    6.  Pour **SAML Identity Location**, sélectionnez **Identity is in the NameIdentfier element of the Subject statement**.
    7.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur Jobscience**, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **Identity Provider Login URL**.
    8.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur Jobscience**, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **Identity Provider Logout URL**.
    9.  Cliquez sur **Enregistrer**.

13. Dans le volet de navigation gauche, dans la section **Administer**, cliquez sur **Domain Management** pour développer la section associée, puis cliquez sur **My Domain** pour ouvrir la page **My Domain**.

    ![Mon domaine](./media/active-directory-saas-jobscience-tutorial/IC767825.png "Mon domaine")

14. Dans la section **Personnalisation de la page de connexion** de la page **Mon domaine**, cliquez sur **Edit**.

    ![Personnalisation de la page de connexion](./media/active-directory-saas-jobscience-tutorial/IC767826.png "Personnalisation de la page de connexion")

15. Le nom des **SAML SSO Settings** s’affiche dans la section **Authentication Service** de la page **Login Page Branding**. Sélectionnez-le, puis cliquez sur **Save**.

    ![Personnalisation de la page de connexion](./media/active-directory-saas-jobscience-tutorial/IC784366.png "Personnalisation de la page de connexion")

16. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configurer l’authentification unique")
  
Pour obtenir l’URL d’authentification unique initiée par le fournisseur de services , cliquez sur **Single Sign On settings** dans la section **Security Controls**.

![Contrôles de sécurité](./media/active-directory-saas-jobscience-tutorial/IC784368.png "Contrôles de sécurité")
  
Cliquez sur le profil d’authentification unique créé à l’étape précédente.  
Cette page affiche l’URL d’authentification unique de votre entreprise (par exemple, *https://companyname.my.salesforce.com?so=companyid*).
##Configuration de l'approvisionnement des utilisateurs
  
Pour permettre aux utilisateurs Azure AD de se connecter à Jobscience, vous devez les approvisionner dans Jobscience.  
Dans le cas de Jobscience, cet approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous au site d’entreprise **Jobscience** en tant qu’administrateur.

2.  Accédez à Setup.

    ![Paramétrage](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Paramétrage")

3.  Accédez à **Manage Users > Users**.

    ![Utilisateurs](./media/active-directory-saas-jobscience-tutorial/IC784369.png "Utilisateurs")

4.  Cliquez sur **New User**.

    ![Tous les utilisateurs](./media/active-directory-saas-jobscience-tutorial/IC784370.png "Tous les utilisateurs")

5.  Dans la boîte de dialogue **Edit User**, procédez comme suit :

    ![Modification de l’utilisateur](./media/active-directory-saas-jobscience-tutorial/IC784371.png "Modification de l’utilisateur")

    1.  Indiquez le prénom, le nom, l’alias, l’adresse de messagerie, le nom d’utilisateur et le surnom de l’utilisateur Azure AD que vous souhaitez approvisionner, dans les zones de texte correspondantes.
    2.  Cliquez sur **Enregistrer**.

    >[AZURE.NOTE]Le titulaire du compte Azure AD reçoit alors un message électronique qui contient un lien pour confirmer le compte avant qu’il soit activé.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Jobscience pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Jobscience, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application **Jobscience**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-jobscience-tutorial/IC784372.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-jobscience-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->

