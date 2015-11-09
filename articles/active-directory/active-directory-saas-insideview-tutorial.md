<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec InsideView | Microsoft Azure" 
    description="Apprenez à utiliser InsideView avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#Didacticiel : Intégration d’Azure Active Directory à InsideView
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et InsideView. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire InsideView
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à InsideView pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise InsideView (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour InsideView
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-insideview-tutorial/IC794128.png "Scénario")
##Activation de l’intégration d’application pour InsideView
  
Cette section décrit l’activation de l’intégration d’application pour InsideView.

###Pour activer l’intégration d’application pour InsideView, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-insideview-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-insideview-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-insideview-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **InsideView**.

    ![Galerie d’applications](./media/active-directory-saas-insideview-tutorial/IC794129.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **InsideView**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur InsideView avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64. Si cette procédure ne vous est pas familière, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, dans la page d’intégration d’application **InsideView**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-insideview-tutorial/IC794131.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à InsideView**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-insideview-tutorial/IC794132.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de réponse InsideView**, tapez votre URL d’authentification InsideView (par exemple, `https://my.insideview.com/iv/<STS Name>/login.iv`), puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-insideview-tutorial/IC794133.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur InsideView**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-insideview-tutorial/IC794134.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise InsideView en tant qu’administrateur.

6.  Dans la barre d’outils située en haut, cliquez sur **Admin**, **SingleSignOn Settings**, puis sur **Add SAML**.

    ![Paramètres d’authentification unique SAML](./media/active-directory-saas-insideview-tutorial/IC794135.png "Paramètres d’authentification unique SAML")

7.  Dans la section **Add a New SAML**, procédez comme suit :

    ![Ajouter un nouveau paramètre SAML](./media/active-directory-saas-insideview-tutorial/IC794136.png "Ajouter un nouveau paramètre SAML")

    1.  Dans la zone de texte **STS Name**, attribuez un nom à votre configuration.
    2.  Dans le portail Azure, dans la page **Configurer l’authentification unique sur InsideView**, copiez la valeur **Point de terminaison initié du fournisseur du services**, puis collez-la dans la zone de texte **SamlP/WS-Fed Unsolicated EndPoint**.
    3.  Créez un fichier **codé en base 64** à partir du certificat téléchargé.
        
		>[AZURE.TIP]Pour plus d’informations, consultez [Conversion d’un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    4.  Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **STS Certificate**.
    5.  Dans la zone de texte **Crm User Id Mapping**, entrez ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
6.  Dans la zone de texte **Crm Email Mapping**, entrez ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
7.  Dans la zone de texte **Crm First Name Mapping**, entrez ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
8.  Dans la zone de texte **Crm lastName Mapping**, entrez ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
9.  Cliquez sur **Enregistrer**.

8.  Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-insideview-tutorial/IC794137.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour permettre aux utilisateurs Azure AD de se connecter à InsideView, vous devez les approvisionner dans InsideView. Dans le cas d’InsideView, l’approvisionnement est une tâche manuelle.
  
Pour obtenir des utilisateurs ou des contacts créés dans InsideView, contactez votre responsable de réussite client ou envoyez un courrier électronique à ****support@insideview.com**

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel outil ou API de création de compte d’utilisateur, fourni par InsideView, pour approvisionner des comptes utilisateur AAD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à InsideView, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application **InsideView**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-insideview-tutorial/IC794138.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-insideview-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=Nov15_HO1-->