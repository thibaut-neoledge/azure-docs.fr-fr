<properties 
    pageTitle="Didacticiel : Intégration d’Azure AD à Thoughtworks Mingle | Microsoft Azure" 
    description="Découvrez comment utiliser Thoughtworks Mingle avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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
    ms.date="04/06/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure AD à Thoughtworks Mingle
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Thoughtworks Mingle. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Thoughtworks Mingle
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Thoughtworks Mingle
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Scénario")

##Activation de l’intégration d’applications pour Thoughtworks Mingle
  
Cette section décrit l’activation de l’intégration de l’application pour Thoughtworks Mingle.

###Pour activer l’intégration d’applications pour Thoughtworks Mingle, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Thoughtworks Mingle**.

    ![Galerie d’applications](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Thoughtworks Mingle**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Thoughtworks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Thoughtworks Mingle avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez charger un certificat dans Thoughtworks Mingle.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications Thoughtworks Mingle du portail Azure AD, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Thoughtworks Mingle**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Configurer l’authentification unique")

3.  Dans la zone de texte **URL du locataire Thoughtworks Mingle** de la page **Configurer l’URL de l’application**, tapez votre URL au format « **http://company.mingle.thoughtworks.com*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Thoughtworks Mingle**, cliquez sur Télécharger les métadonnées, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Configurer l’authentification unique")

5.  Connectez-vous à votre site d’entreprise **Thoughtworks Mingle** en tant qu’administrateur.

6.  Cliquez sur l’onglet **Admin** puis sur **SSO Config**.

    ![SSO Config](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "SSO Config")

7.  Dans la section **SSO Config**, procédez comme suit :

    ![SSO Config](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "SSO Config")

    1.  Pour charger le fichier de métadonnées, cliquez sur **Choose file**.
    2.  Cliquez sur **Save Changes**.

8.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Configurer l’authentification unique")

##Configuration de l'approvisionnement des utilisateurs
  
Pour que les utilisateurs AAD puissent se connecter, ils doivent être approvisionnés dans l’application Thoughtworks Mingle à l’aide de leurs noms d’utilisateur Azure AD. Dans le cas de Thoughtworks Mingle, l’approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise Thoughtworks Mingle en tant qu’administrateur.

2.  Cliquez sur **Profile**.

    ![Votre premier projet](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Votre premier projet")

3.  Cliquez sur l’onglet **Admin** puis sur **Users**.

    ![Utilisateurs](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Utilisateurs")

4.  Cliquez sur **New User**.

    ![Nouvel utilisateur](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "Nouvel utilisateur")

5.  Dans la boîte de dialogue **New User**, procédez comme suit :

    ![Nouvel utilisateur](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "Nouvel utilisateur")

    1.  Renseignez les zones de texte **Sign-in name**, **Display name**, **Choose password**, **Confirm password** pour le compte AAD valide que vous souhaitez approvisionner.
    2.  Dans **User type**, sélectionnez **Full user**.
    3.  Cliquez sur **Create This Profile**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur, fourni par Thoughtworks Mingle, pour approvisionner des comptes d’utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Thoughtworks Mingle, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte test.

2.  Dans la page d’intégration d’applications **Thoughtworks Mingle**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0413_2016-->