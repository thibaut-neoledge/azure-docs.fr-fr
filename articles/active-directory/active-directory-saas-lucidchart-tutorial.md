<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Lucidchart | Microsoft Azure" description="Apprenez à utiliser Lucidchart avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Lucidchart
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=532346).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Lucidchart. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Lucidchart pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Lucidchart pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Lucidchart (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Lucidchart
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Scénario")
##Activation de l’intégration d’application pour Lucidchart
  
Cette section décrit l’activation de l’intégration d’application pour Lucidchart.

###Pour activer l’intégration d’application pour Lucidchart, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Lucidchart**.

    ![Galerie d’applications](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Lucidchart**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Lucidchart avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’application **Lucidchart**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Lucidchart**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Lucidchart**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Lucidchart (par exemple, « **https://chart2.office.lucidchart.com/saml/sso/azure*"), puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Lucidchart**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de données localement sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Lucidchart en tant qu’administrateur.

6.  Dans le menu situé en haut, cliquez sur **Team**.

    ![Équipe](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Équipe")

7.  Cliquez sur **Application > Manage SAML**.

    ![Gérer SAML](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Gérer SAML")

8.  Dans la boîte de dialogue **SAML Authentication Settings**, procédez comme suit :

    1.  Sélectionnez **Enable SAML Authentication**, puis cliquez sur **Optional**. ![Paramètres d’authentification SAML](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "Paramètres d’authentification SAML")
    2.  Dans la zone de texte **Domain**, entrez votre domaine, puis cliquez sur **Change Certificate**. ![Changer de certificat](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Changer de certificat")
    3.  Ouvrez votre fichier de métadonnées téléchargé, copiez son contenu, puis collez-le dans la zone de texte **Upload Metadata**. ![Charger les métadonnées](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Charger les métadonnées")
    4.  Sélectionnez **Automatically Add new user to the team**, puis cliquez sur **Save changes**. ![Enregistrer les modifications](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Enregistrer les modifications")

9.  Sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Aucun élément d’action ne vous permet de configurer l’approvisionnement des utilisateurs dans Lucidchart. Lorsqu’un utilisateur tente de se connecter à Lucidchart à l’aide du panneau d’accès, Lucidchart vérifie si cet utilisateur existe. Si aucun compte d’utilisateur n’est disponible, Lucidchart le crée automatiquement.
##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Lucidchart, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application **Lucidchart**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->