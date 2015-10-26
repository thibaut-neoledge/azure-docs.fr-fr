<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Kontiki | Microsoft Azure" description="Apprenez à utiliser Kontiki avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Kontiki
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=529076).
  
L'objectif de ce didacticiel est de montrer comment intégrer Azure et Kontiki. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Kontiki pour lequel l’authentification unique est activée
  
À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Kontiki pourront s’authentifier de manière unique dans l'application sur votre site d'entreprise Kontiki (connexion initiée par le fournisseur du service) ou à l'aide de la [Présentation du volet d'accès](https://msdn.microsoft.com/library/dn308586)
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l'intégration d'applications pour Kontiki
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-kontiki-tutorial/IC790235.png "Scénario")
##Activation de l'intégration d'applications pour Kontiki
  
Cette section décrit l'activation de l’intégration d’applications pour Kontiki.

###Pour activer l'intégration d’applications pour Kontiki, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-kontiki-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-kontiki-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-kontiki-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-kontiki-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, entrez **Kontiki**.

    ![Galerie d’applications](./media/active-directory-saas-kontiki-tutorial/IC790236.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Kontiki**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Kontiki](./media/active-directory-saas-kontiki-tutorial/IC790237.png "Kontiki")
##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s'authentifier sur Kontiki avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure Active Directory, puis dans la page d’intégration d’applications **Kontiki**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kontiki-tutorial/IC790238.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Kontiki**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kontiki-tutorial/IC790239.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à Kontiki**, tapez l’URL utilisée par vos utilisateurs pour se connecter à Kontiki (par ex. “*https://company.mc.eval.kontiki.com/*")), puis cliquez sur **Suivant** :

    ![Configurer l’URL de l’application](./media/active-directory-saas-kontiki-tutorial/IC790240.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Kontiki**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-kontiki-tutorial/IC790241.png "Configurer l’authentification unique")

5.  Envoyez le fichier de métadonnées à l’équipe du support technique Kontiki.

    >[AZURE.NOTE]La configuration de l’authentification unique doit être effectuée par l’équipe du support technique Kontiki. Vous recevez une notification dès qu’elle est terminée.

6.  Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kontiki-tutorial/IC790242.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs
  
Aucun élément d'action ne vous permet de configurer l’approvisionnement des utilisateurs dans Kontiki. Lorsqu'un utilisateur tente de se connecter à Kontiki à l'aide du panneau d'accès, Kontiki vérifie si cet utilisateur existe. Si aucun compte d'utilisateur n’est disponible, Kontiki le crée automatiquement.
##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Kontiki, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **Kontiki**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-kontiki-tutorial/IC790243.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-kontiki-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->