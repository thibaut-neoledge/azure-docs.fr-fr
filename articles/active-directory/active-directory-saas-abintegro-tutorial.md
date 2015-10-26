<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à Abintegro | Microsoft Azure" description="Apprenez à utiliser Abintegro avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore !" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Abintegro
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=529073).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Abintegro. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Abintegro pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Abintegro pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Abintegro (connexion initiée par le fournisseur du service) ou en s’appuyant sur la [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’application pour Abintegro
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-abintegro-tutorial/IC790076.png "Scénario")
##Activation de l’intégration d’application pour Abintegro

Cette section décrit l’activation de l’intégration d'application pour Abintegro.

###Pour activer l’intégration d’application pour Abintegro, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-abintegro-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-abintegro-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-abintegro-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **abintegro**.

    ![Galerie d’applications](./media/active-directory-saas-abintegro-tutorial/IC790077.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Abintegro**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Abintegro](./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Abintegro avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, dans la page d’intégration d’application **Abintegro**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-abintegro-tutorial/IC790079.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Abintegro**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-abintegro-tutorial/IC790080.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion Abintegro**, tapez l’URL utilisée par vos utilisateurs pour se connecter à Abintegro (par ex., `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`), puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-abintegro-tutorial/IC790081.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Abintegro**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-abintegro-tutorial/IC790082.png "Configurer l’authentification unique")

5.  Envoyez le fichier de métadonnées à l’équipe de support Abintegro.

    >[AZURE.NOTE]La configuration de l’authentification unique doit être effectuée par l’équipe de support Abintegro. Vous recevrez une notification dès la configuration terminée.

6.  Dans le portail Azure Active Directory, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-abintegro-tutorial/IC790083.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Aucun élément d’action ne vous permet de configurer l’approvisionnement des utilisateurs dans Abintegro. Quand un utilisateur affecté tente de se connecter à Abintegro via le volet d’accès, Abintegro vérifie si cet utilisateur existe. Si aucun compte d’utilisateur n’est encore disponible, Abintegro le crée automatiquement.
##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Abintegro, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’application **Abintegro**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-abintegro-tutorial/IC790084.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-abintegro-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez la page [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->