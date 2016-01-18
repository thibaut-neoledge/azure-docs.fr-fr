<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Kintone | Microsoft Azure" 
    description="Apprenez à utiliser Kintone avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." 
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
    ms.date="01/05/2016" 
    ms.author="jeedes" />

#Didacticiel : Intégration d’Azure Active Directory à Kintone
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Kintone. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Kintone pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Kintone pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Kintone (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md)
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Kintone
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-kintone-tutorial/IC785859.png "Scénario")
##Activation de l’intégration d’application pour Kintone
  
Cette section décrit l’activation de l’intégration d’application pour Kintone.

###Pour activer l’intégration d’application pour Kintone, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-kintone-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-kintone-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-kintone-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Kintone**.

    ![Galerie d’applications](./media/active-directory-saas-kintone-tutorial/IC785867.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Kintone**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Kintone](./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Kintone avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure Active Directory, puis dans la page d’intégration d’application **Kintone**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kintone-tutorial/IC785872.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Kintone**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kintone-tutorial/IC785873.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de Kintone**, tapez votre URL selon le modèle suivant « **https://company.kintone.com*", puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-kintone-tutorial/IC785875.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Kintone**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-kintone-tutorial/IC785878.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **Kintone** en tant qu’administrateur.

6.  Cliquez sur **Settings**.

    ![Paramètres](./media/active-directory-saas-kintone-tutorial/IC785879.png "Paramètres")

7.  Cliquez sur **Users & System Administration**.

    ![Administration système et utilisateurs](./media/active-directory-saas-kintone-tutorial/IC785880.png "Administration système et utilisateurs")

8.  Sous **System Administration > Security**, cliquez sur **Login**.

    ![Connexion](./media/active-directory-saas-kintone-tutorial/IC785881.png "Connexion")

9.  Cliquez sur **Enable SAML authentication**.

    ![Authentification SAML](./media/active-directory-saas-kintone-tutorial/IC785882.png "Authentification SAML")

10. Dans la section SAML Authentication, procédez comme suit :

    ![Authentification SAML](./media/active-directory-saas-kintone-tutorial/IC785883.png "Authentification SAML")

    1.  Dans la page **Configurer l’authentification unique sur Kintone** du portail Azure, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **Login URL**.
    2.  Dans la page **Configurer l’authentification unique sur Kintone** du portail Azure, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **Logout URL**.
    3.  Cliquez sur **Parcourir** pour charger votre certificat téléchargé.
    4.  Cliquez sur **Enregistrer**.

11. Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kintone-tutorial/IC785884.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Pour se connecter à Kintone, les utilisateurs d’Azure AD doivent être approvisionnés dans Kintone. Dans le cas de Kintone, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Kintone** en tant qu’administrateur.

2.  Cliquez sur **Setting**.

    ![Paramètres](./media/active-directory-saas-kintone-tutorial/IC785879.png "Paramètres")

3.  Cliquez sur **Users & System Administration**.

    ![Administration système et utilisateur](./media/active-directory-saas-kintone-tutorial/IC785880.png "Administration système et utilisateur")

4.  Sous **User Administration**, cliquez sur **Departments & Users**.

    ![Département et utilisateurs](./media/active-directory-saas-kintone-tutorial/IC785888.png "Département et utilisateurs")

5.  Cliquez sur **New User**.

    ![Nouveaux utilisateurs](./media/active-directory-saas-kintone-tutorial/IC785889.png "Nouveaux utilisateurs")

6.  Dans la section **New User**, procédez comme suit :

    ![Nouveaux utilisateurs](./media/active-directory-saas-kintone-tutorial/IC785890.png "Nouveaux utilisateurs")

    1.  Tapez le nom complet, le nom de connexion, le nouveau mot de passe et sa confirmation et l’adresse de messagerie d’un compte AAD valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Display Name**, **Login Name**, **New Password**, **Confirm Password** et **E-mail Address**.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur Kintone fourni par ce service pour approvisionner des comptes d’utilisateurs Azure Active Directory.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Kintone, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application **Kintone**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-kintone-tutorial/IC785891.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-kintone-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0107_2016-->