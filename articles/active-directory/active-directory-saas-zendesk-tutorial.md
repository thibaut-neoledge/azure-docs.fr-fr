<properties pageTitle="Didacticiel : Intégration d’Azure AD à Zendesk | Microsoft Azure" description="Découvrez comment utiliser Zendesk avec Azure AD pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure AD à Zendesk
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=522569).
  
L’objectif de ce didacticiel est de montrer comment intégrer Azure et Zendesk. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un client Zendesk
  
À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Zendesk pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Zendesk (connexion initiée par le fournisseur de services) ou à l’aide de la [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).
  
Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Zendesk
2.  Configuration de l'authentification unique
3.  Configuration de l'approvisionnement des utilisateurs
4.  Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-zendesk-tutorial/IC773083.png "Scénario")

##Activation de l’intégration d’applications pour Zendesk
  
Cette section décrit l’activation de l’intégration d’applications pour Zendesk.

###Pour activer l’intégration d’applications pour Zendesk, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.

    ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

    ![Applications](./media/active-directory-saas-zendesk-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Ajouter l’application](./media/active-directory-saas-zendesk-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie](./media/active-directory-saas-zendesk-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Zendesk**.

    ![Galerie d’applications](./media/active-directory-saas-zendesk-tutorial/IC773084.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Zendesk**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##Configuration de l'authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier sur Zendesk avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. La configuration de l’authentification unique pour Zendesk implique que vous récupériez la valeur de l’empreinte numérique d’un certificat. Si cette procédure ne vous est pas familière, consultez [Récupération de la valeur de l’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **Zendesk** du portail Azure AD, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.

    ![Authentification unique](./media/active-directory-saas-zendesk-tutorial/IC773086.png "Authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Zendesk**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configurer l’authentification unique")

3.  Dans la zone de texte **URL de connexion à Zendesk** de la page **Configurer l’URL de l’application**, tapez l’URL selon le modèle « *https://\<nom-client>.zendesk.com* », puis cliquez sur **Suivant**.

    ![Configurer l’URL de l’application](./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configurer l’URL de l’application")

4.  Dans la page **Configurer l’authentification unique sur Zendesk**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sous le nom **c:\\zendesk.cer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zendesk en tant qu’administrateur.

6.  Cliquez sur **Admin**.

7.  Dans le volet de navigation gauche, cliquez sur **Settings**, puis sur **Security**.

    ![Sécurité](./media/active-directory-saas-zendesk-tutorial/IC773089.png "Sécurité")

8.  Dans la page **Security**, cliquez sur l’onglet **Admin & Agents**.

9.  Sélectionnez **Single sign-on (SSO) and SAML**, puis **SAML**.

10. Dans la page **Configurer l’authentification unique sur Zendesk** du portail Azure AD, copiez la valeur **URL SSO SAML**, puis collez-la dans la zone de texte **SAML SSO URL**.

11. Dans la page **Configurer l’authentification unique sur Zendesk** du portail Azure AD, copiez la valeur **URL de déconnexion distante**, puis collez-la dans la zone de texte **Remote Logout URL**.

    ![Authentification unique](./media/active-directory-saas-zendesk-tutorial/IC773090.png "Authentification unique")

12. Copiez la valeur **Empreinte** du certificat exporté, puis collez-la dans la zone de texte **Certificate Fingerprint**.

	>[AZURE.TIP]Pour plus d’informations, consultez [Récupération de la valeur de l’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

13. Cliquez sur **Enregistrer**.

14. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configurer l’authentification unique")

##Configuration de l'approvisionnement des utilisateurs
  
Pour pouvoir se connecter à **Zendesk**, les utilisateurs d’Azure AD doivent être approvisionnés dans **Zendesk**. Dans le cas de **Zendesk**, l’approvisionnement est une tâche manuelle.

###Pour approvisionner un compte d’utilisateur dans Zendesk, procédez comme suit :

1.  Connectez-vous à votre client **Zendesk**.

2.  Sélectionnez l’onglet **Customer List**.

3.  Sélectionnez l’onglet **User**, puis cliquez sur **Add**.

    ![Ajouter un utilisateur](./media/active-directory-saas-zendesk-tutorial/IC773632.png "Ajouter un utilisateur")

4.  Tapez l’adresse de messagerie du compte Azure AD que vous souhaitez approvisionner, puis cliquez sur **Save**.

    ![Nouvel utilisateur](./media/active-directory-saas-zendesk-tutorial/IC773633.png "Nouvel utilisateur")

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Zendesk pour approvisionner des comptes d’utilisateurs Azure AD.

##Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Zendesk, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte test.

2.  Dans la page d’intégration d’applications **Zendesk**, cliquez sur **Affecter des utilisateurs**.

    ![Affecter des utilisateurs](./media/active-directory-saas-zendesk-tutorial/IC773094.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

    ![Oui](./media/active-directory-saas-zendesk-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=Oct15_HO3-->