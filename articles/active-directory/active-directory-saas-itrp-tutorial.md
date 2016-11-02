<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à ITRP | Microsoft Azure" 
    description="Découvrez comment utiliser ITRP avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore." 
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
    ms.date="09/07/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory à ITRP
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et ITRP. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire ITRP
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à ITRP pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise ITRP (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour ITRP
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-itrp-tutorial/IC775551.png "Scénario")
##Activation de l’intégration d’application pour ITRP
  
Cette section décrit l’activation de l’intégration d’application pour ITRP.

###Pour activer l’intégration d’application pour ITRP, procédez comme suit :

1.  Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-itrp-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-itrp-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-itrp-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **ITRP**.

    ![Galerie d’applications](./media/active-directory-saas-itrp-tutorial/IC775565.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **ITRP**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur ITRP avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. La configuration de l’authentification unique pour ITRP vous oblige à récupérer une valeur d’empreinte dans un certificat. Si cette procédure ne vous est pas familière, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **ITRP** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-itrp-tutorial/IC771709.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à ITRP**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-itrp-tutorial/IC775567.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez votre URL selon le modèle « *https://\<nom-locataire\>.ITRP.com* », puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-itrp-tutorial/IC775568.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur ITRP**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sous **c:\\ITRP.cer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-itrp-tutorial/IC775569.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise ITRP en tant qu’administrateur.

6.  Dans la barre d’outils située en haut, cliquez sur **Settings**.

    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7.  Dans le volet de navigation de gauche, sélectionnez **Single Sign-On**.

    ![Authentification unique](./media/active-directory-saas-itrp-tutorial/IC775571.png "Authentification unique")

8.  Dans la section de configuration Single Sign-On, procédez comme suit :

    ![Authentification unique](./media/active-directory-saas-itrp-tutorial/IC775572.png "Authentification unique")

    ![Authentification unique](./media/active-directory-saas-itrp-tutorial/IC775573.png "Authentification unique")

    1.  Cliquez sur **Enable**.
    2.  Dans la page de boîte de dialogue **Configurer l’authentification unique sur ITRP** du portail Azure Classic, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **URL de déconnexion distante**.
    3.  Dans la page de boîte de dialogue **Configurer l’authentification unique sur ITRP** du portail Azure Classic, copiez la valeur **URL SSO SAML**, puis collez-la dans la zone de texte **URL SSO SAML**.
    4.  Copiez la valeur **Empreinte** du certificat exporté, puis collez-la dans la zone de texte **Certificate Fingerprint**.
        
		>[AZURE.TIP]Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

    5.  Cliquez sur **Save**.

9.  Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-itrp-tutorial/IC775574.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour pouvoir se connecter à ITRP, les utilisateurs d’Azure Active Directory doivent être approvisionnés dans ITRP. Dans le cas d’ITRP, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre locataire **ITRP**.

2.  Dans la barre d’outils située en haut, cliquez sur **Records**.

    ![Administrateur](./media/active-directory-saas-itrp-tutorial/IC775575.png "Administrateur")

3.  Dans le menu contextuel, sélectionnez **People**.

    ![Personnes](./media/active-directory-saas-itrp-tutorial/IC775587.png "Personnes")

4.  Cliquez sur **Add New Person** (« + »).

    ![Administrateur](./media/active-directory-saas-itrp-tutorial/IC775576.png "Administrateur")

5.  Dans la boîte de dialogue Add New Person, procédez comme suit :

    ![Utilisateur](./media/active-directory-saas-itrp-tutorial/IC775577.png "Utilisateur")

    1.  Entrez le **nom**, et l’**adresse de messagerie** d’un compte AAD valide que vous voulez approvisionner
    2.  Cliquez sur **Save**.

>[AZURE.NOTE] Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par ITRP pour approvisionner des comptes d’utilisateurs Azure Active Directory.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Bime, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **ITRP**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-itrp-tutorial/IC775588.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-itrp-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0907_2016-->