<properties pageTitle="Didacticiel : Intégration d’Azure Active Directory à Canvas LMS | Microsoft Azure" description="Apprenez à utiliser Canvas LMS avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure Active Directory à Canvas LMS
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=524182).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Canvas. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Canvas

À l’issue de ce didacticiel, les utilisateurs Azure AD que vous avez affectés à Canvas pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Canvas (connexion initiée par le fournisseur du service) ou à l’aide de la [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Canvas
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Scénario")
##Activation de l’intégration d’applications pour Canvas

Cette section décrit l’activation de l’intégration d’applications pour Canvas.

###Pour activer l’intégration d’applications pour Canvas, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Canvas**.

    ![Galerie d’applications](./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Canvas**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Canevas](./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Canevas")
##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Canvas avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. La configuration de l’authentification unique pour Canvas vous oblige à récupérer une valeur d’empreinte dans un certificat. Si cette procédure ne vous est pas familière, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, puis dans la page d’intégration d’applications **Canvas**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue \*\*Configurer l’authentification unique\*\*.

    ![Configurer l’authentification unique](./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Canvas**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion**, tapez votre URL selon le modèle `https://<tenant-name>.instructure.com`, puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Canvas**, pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Canvas en tant qu’administrateur.

6.  Accédez à **Cours > Comptes gérés > Microsoft**.

    ![Canevas](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canevas")

7.  Dans le volet de navigation sur la gauche, sélectionnez **Authentification**, puis cliquez sur **Ajouter configuration SAML**.

    ![Authentification](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Authentification")

8.  Dans la page Intégration actuelle, procédez comme suit :

    ![Intégration actuelle](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Intégration actuelle")

    1.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Canvas**, copiez la valeur **ID d’entité**, puis collez-la dans la zone de texte **ID d’entité IdP**.
    2.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Canvas**, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **URL de connexion**.
    3.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Canvas**, copiez la valeur **URL de connexion distante**, puis collez-la dans la zone de texte **URL de déconnexion**.
    4.  Dans le portail Azure, dans la page de boîte de dialogue **Configurer l’authentification unique sur Canvas**, copiez la valeur **Modifier l’URL de mot de passe**, puis collez-la dans la zone de texte **Modifier le lien du mot de passe**.
    5.  Copiez la valeur **Empreinte numérique** du certificat exporté, puis collez-la dans la zone de texte **Empreinte numérique du certificat**.  

        >[AZURE.TIP]Pour plus d’informations, consultez [Comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

    6.  Dans la liste **Attribut de connexion**, sélectionnez **NameID**.
    7.  Dans la liste **Format d’identificateur**, sélectionnez **emailAddress**.
    8.  Cliquez sur **Enregistrer les paramètres d’authentification**.

9.  Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Configurer l’authentification unique")
##Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à Canvas, vous devez les approvisionner dans Canvas. En l’occurrence, cet approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre locataire **Canvas**.

2.  Accédez à **Cours > Comptes gérés > Microsoft**.

    ![Canevas](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canevas")

3.  Cliquez sur **Utilisateurs**.

    ![Utilisateurs](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Utilisateurs")

4.  Cliquez sur **Ajouter un nouvel utilisateur**.

    ![Utilisateurs](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Utilisateurs")

5.  Dans la page Ajouter un nouvel utilisateur, procédez comme suit :

    ![Ajouter un utilisateur](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Ajouter un utilisateur")

    1.  Dans la zone de texte **Nom complet**, tapez le nom de l’utilisateur.
    2.  Dans la zone de texte **E-mail**, tapez l’adresse de messagerie de l’utilisateur.
    3.  Dans la zone de texte **Connexion**, tapez l’adresse de messagerie Azure AD de l’utilisateur.
    4.  Sélectionnez **Envoyer un message électronique à l’utilisateur concernant la création de ce compte**.
    5.  Cliquez sur **Ajouter un utilisateur**.

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Canvas pour approvisionner des comptes d’utilisateur Azure Active Directory.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Canvas, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page d’intégration d’applications \*\*Canvas\*\*, cliquez sur **Affecter des utilisateurs**.

    ![Affectation d’utilisateurs](./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Affectation d’utilisateurs")

3.  Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le volet d’accès, consultez [Présentation du volet d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->