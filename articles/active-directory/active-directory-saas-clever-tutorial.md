<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à Clever | Microsoft Azure" description="Apprenez à utiliser Clever avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Clever
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=551005).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Clever. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un client Clever

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Clever pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Clever (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586)

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Clever
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-clever-tutorial/IC798977.png "Scénario")
##Activation de l’intégration d’applications pour Clever

Cette section décrit l’activation de l’intégration d’applications pour Clever.

###Pour activer l’intégration d’applications pour Clever, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-clever-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-clever-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-clever-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Clever**.

    ![Galerie d’applications](./media/active-directory-saas-clever-tutorial/IC798978.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionnez **Clever**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Clever](./media/active-directory-saas-clever-tutorial/IC798979.png "Clever")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Clever avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Votre application Clever attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **Attributs du jeton SAML**. La capture d’écran suivante montre un exemple de cette opération.

![Attributs](./media/active-directory-saas-clever-tutorial/IC798980.png "Attributs")

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’applications **Clever**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-clever-tutorial/IC784682.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Clever**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-clever-tutorial/IC798981.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Clever (par exemple, **https://clever.com/in/azsandbox*), puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-clever-tutorial/IC798982.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Clever**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de métadonnées en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-clever-tutorial/IC798983.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Clever en tant qu’administrateur.

6.  Dans la barre d’outils, cliquez sur **Instant Login**.

    ![Connexion instantanée](./media/active-directory-saas-clever-tutorial/IC798984.png "Connexion instantanée")

7.  Dans la page **Instant Login**, procédez comme suit :

    ![Connexion instantanée](./media/active-directory-saas-clever-tutorial/IC798985.png "Connexion instantanée")

    1.  Renseignez le champ **Login URL**.  

        >[AZURE.NOTE]**Login URL** est une valeur personnalisée. La valeur réelle vous est fournie par l’équipe du support technique Clever.

    2.  Sous **Identity System**, sélectionnez **ADFS**.
    3.  Cliquez sur **Save**.

8.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-clever-tutorial/IC798986.png "Configurer l’authentification unique")

9.  Dans le menu situé en haut, cliquez sur **Attributs** pour ouvrir la boîte de dialogue **Attributs du jeton SAML**.

    ![Attributs](./media/active-directory-saas-clever-tutorial/IC795920.png "Attributs")

10. Pour ajouter les mappages d’attribut requis, procédez comme suit :

    ![Attributs du jeton SAML](./media/active-directory-saas-clever-tutorial/IC795921.png "Attributs du jeton SAML")

	|Nom de l'attribut|Valeur de l’attribut|
    |---|---|
    |clever.student.credentials.district\_username|User.userprincipalname|

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **ajouter un attribut utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut**, tapez le nom d’attribut pour cette ligne.
    3.  Dans la zone de texte **Valeur de l’attribut**, sélectionnez la valeur d’attribut pour cette ligne.
    4.  Cliquez sur **Terminé**.

11. Cliquez sur **Appliquer les modifications**.

##Configuration de l'approvisionnement des utilisateurs

Pour se connecter à Clever, les utilisateurs d’Azure AD doivent être approvisionnés dans Clever. Dans le cas de Clever, l’approvisionnement est une tâche manuelle qui doit être effectuée par l’équipe du support technique Clever.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur fourni par Clever pour approvisionner des comptes d’utilisateurs AAD.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Clever, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications **Clever**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-clever-tutorial/IC798987.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter** puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-clever-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->