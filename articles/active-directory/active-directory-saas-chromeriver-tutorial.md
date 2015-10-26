<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à Chromeriver | Microsoft Azure" description="Apprenez à utiliser Chromeriver avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Chromeriver
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=615279).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Chromeriver. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Chromeriver pour lequel l’authentification unique est activée

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Chromeriver pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Chromeriver (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Chromeriver
2.  Configuration de l’authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-chromeriver-tutorial/IC802755.png "Scénario")
##Activation de l’intégration d’applications pour Chromeriver

Cette section décrit l’activation de l’intégration d’applications pour Chromeriver.

###Pour activer l’intégration d’applications pour Chromeriver, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-chromeriver-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-chromeriver-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-chromeriver-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-chromeriver-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Chromeriver**.

    ![Galerie d’applications](./media/active-directory-saas-chromeriver-tutorial/IC802756.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Chromeriver**, puis cliquez sur **Terminer** pour ajouter l’application.
##Configuration de l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Chromeriver avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’applications **Chromeriver**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-chromeriver-tutorial/IC802757.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Chromeriver**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-chromeriver-tutorial/IC802758.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres de l’application**, procédez comme suit :

    ![Configurer les paramètres d’application](./media/active-directory-saas-chromeriver-tutorial/IC802759.png "Configurer les paramètres d’application")

    1.  Dans la zone de texte **URL de réponse**, tapez votre **URL AssertionConsumerService ** Chromeriver (par exemple, **https://qa-app.chromeriver.com/login/sso/saml/consume?customerId=911*).

        >[AZURE.NOTE]L’équipe de support technique Chromeriver peut vous fournir cette valeur.

    2.  Cliquez sur **Suivant**

4.  Dans la page **Configurer l’authentification unique sur Chromeriver**, pour télécharger vos métadonnées, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-chromeriver-tutorial/IC802760.png "Configurer l’authentification unique")

5.  Envoyez le fichier de métadonnées téléchargé à l’équipe de support technique Chromeriver.

    >[AZURE.NOTE]L’équipe de support technique Chromeriver doit se charger de la configuration de l’authentification unique. Vous recevrez une notification lorsque l’authentification unique aura été activée pour votre abonnement.

6.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-chromeriver-tutorial/IC802761.png "Configurer l’authentification unique")
##Configuration de l’approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Chromeriver, vous devez les approvisionner dans Chromeriver. En l’occurrence, les comptes d’utilisateur doivent être créés par l’équipe de support technique Chromeriver.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Chromeriver pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Chromeriver, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **Chromeriver**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-chromeriver-tutorial/IC802762.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-chromeriver-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->