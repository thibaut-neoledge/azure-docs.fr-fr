<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à Aha! | Microsoft Azure" description="Apprenez à utiliser Aha! avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore !" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Aha!
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=550992).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Aha!. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Aha! pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Aha! pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Aha! (connexion initiée par le fournisseur du service) ou en s’appuyant sur la [Présentation du volet d'accès](https://msdn.microsoft.com/library/dn308586).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Aha!
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-aha-tutorial/IC798944.png "Scénario")
##Activation de l’intégration d’application pour Aha!

Cette section décrit l’activation de l’intégration d’application pour Aha!.

###Pour activer l’intégration d’application pour Aha!, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-aha-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-aha-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-aha-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Aha!**.

    ![Galerie d’applications](./media/active-directory-saas-aha-tutorial/IC798945.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Aha!**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Aha!](./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Aha! avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, dans la page d’intégration d’application **Aha!**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue \*\*Configurer l’authentification unique\*\*.

    ![Configurer l’authentification unique](./media/active-directory-saas-aha-tutorial/IC798946.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Aha!**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-aha-tutorial/IC798947.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion Aha!**, tapez l’URL dont se servent vos utilisateurs pour se connecter à votre application Aha! (par exemple, "\**https://company.aha.io/session/new*"), puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-aha-tutorial/IC798948.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Aha!**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-aha-tutorial/IC798949.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Aha! en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **Settings**.

    ![Paramètres](./media/active-directory-saas-aha-tutorial/IC798950.png "Paramètres")

7.  Cliquez sur **Account**.

    ![Profil](./media/active-directory-saas-aha-tutorial/IC798951.png "Profil")

8.  Cliquez sur **Security and single sign-on**.

    ![Security and single sign-on](./media/active-directory-saas-aha-tutorial/IC798952.png "Security and single sign-on")

9.  Dans la section **Single Sign-On**, pour **Identity Provider**, sélectionnez **SAML2.0**.

    ![Security and single sign-on](./media/active-directory-saas-aha-tutorial/IC798953.png "Security and single sign-on")

10. Dans la page de configuration **Single Sign-On**, procédez comme suit :

    ![Authentification unique](./media/active-directory-saas-aha-tutorial/IC798954.png "Authentification unique")

    1.  Dans la zone de texte **Name**, attribuez un nom à votre configuration.
    2.  Pour **Configure using**, sélectionnez **Metadata File**.
    3.  Pour charger le fichier de métadonnées téléchargé, cliquez sur **Browse**.
    4.  Cliquez sur **Update**.

11. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-aha-tutorial/IC798955.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Pour se connecter à Aha!, les utilisateurs d’Azure AD doivent être approvisionnés dans Aha!. Dans le cas d’Aha!, l’approvisionnement est une tâche automatisée. Vous n’avez aucune opération à effectuer.
  
Au besoin, les utilisateurs sont automatiquement créés durant la première tentative d’authentification unique.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur Aha! fourni par ce service pour approvisionner des comptes d’utilisateurs Azure Active Directory.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Aha!, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application \*\*Aha!\*\*, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-aha-tutorial/IC798956.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-aha-tutorial/IC767830.png "Oui")

Si vous voulez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez la page [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->