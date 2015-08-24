<properties pageTitle="Didacticiel : Intégration d’Azure AD à Zscaler One | Microsoft Azure" description="Découvrez comment utiliser Zscaler One avec Azure AD pour activer l’authentification unique, l’approvisionnement automatisé et bien plus encore." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Didacticiel : Intégration d’Azure AD à Zscaler One
>[AZURE.TIP]Pour envoyer des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=613694).

L’objectif de ce didacticiel est de montrer comment intégrer Azure et Zscaler One. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Zscaler One pour lequel l’authentification unique est activée  

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à Zscaler One pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise Zscaler One (connexion initiée par le fournisseur de services) ou à l’aide de la [Présentation du panneau d’accès](https://msdn.microsoft.com/library/azure/dn308586.aspx).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1.  Activation de l’intégration d’applications pour Zscaler One
2.  Configuration de l'authentification unique
3.  Configuration des paramètres de proxy
4.  Configuration de l'approvisionnement des utilisateurs
5.  Affectation d’utilisateurs  

![Scénario](./media/active-directory-saas-zscaler-one-tutorial/IC800214.png "Scénario")

##Activation de l’intégration d’applications pour Zscaler One

Cette section décrit l’activation de l’intégration d’applications pour Zscaler One.

###Pour activer l’intégration d’applications pour Zscaler One, procédez comme suit :

1.  Dans le volet de navigation gauche du portail de gestion Azure, cliquez sur **Active Directory**.  

	![Active Directory](./media/active-directory-saas-zscaler-one-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.  Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications](./media/active-directory-saas-zscaler-one-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

	![Ajouter l’application](./media/active-directory-saas-zscaler-one-tutorial/IC749321.png "Ajouter l’application")

5.  Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Ajouter une application à partir de la galerie](./media/active-directory-saas-zscaler-one-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Zscaler One**.

	![Galerie d’applications](./media/active-directory-saas-zscaler-one-tutorial/IC800215.png "Galerie d’applications")

7.  Dans le volet de résultats, sélectionnez **Zscaler One**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Zscaler One](./media/active-directory-saas-zscaler-one-tutorial/IC800216.png "Zscaler One")

##Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Zscaler One avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez charger un certificat codé en base 64 vers votre client Zscaler One. Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page d’intégration d’applications **Zscaler One** du portail Azure AD, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue \*\*Configurer l’authentification unique\*\*.

	![Configurer l’authentification unique](./media/active-directory-saas-zscaler-one-tutorial/IC800217.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs se connectent à Zscaler One**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-zscaler-one-tutorial/IC800218.png "Configurer l’authentification unique")

3.  Dans la zone de texte **URL de connexion à Zscaler One** de la page **Configurer l’URL de l’application**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Zscaler One, puis cliquez sur **Suivant**.

	![Configurer l’URL de l’application](./media/active-directory-saas-zscaler-one-tutorial/IC800219.png "Configurer l’URL de l’application")

	>[AZURE.NOTE]Vous pouvez obtenir la valeur réelle de votre environnement auprès de l’équipe du support technique Zscaler One.

4.  Dans la page **Configurer l’authentification unique sur Zscaler One**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

	![Configurer l’authentification unique](./media/active-directory-saas-zscaler-one-tutorial/IC800220.png "Configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zscaler One en tant qu’administrateur.

6.  Dans le menu situé dans la partie supérieure, cliquez sur **Administration**.

	![Administration](./media/active-directory-saas-zscaler-one-tutorial/IC800206.png "Administration")

7.  Sous **Manage Administrators & Roles**, cliquez sur **Manage Users & Authentication**.

	![Gérer les utilisateurs et l’authentification](./media/active-directory-saas-zscaler-one-tutorial/IC800207.png "Gérer les utilisateurs et l’authentification")

8.  Dans la section **Choose Authentication Options for your Organization**, procédez comme suit :

	![Authentification](./media/active-directory-saas-zscaler-one-tutorial/IC800208.png "Authentification")

	1.  Sélectionnez **Authenticate using SAML Single Sign-On**.  
	2.  Cliquez sur **Configure SAML Single Sign-On Parameters**.  

9.  Dans la boîte de dialogue **Configure SAML Single Sign-On Parameters**, procédez comme suit, puis cliquez sur **Done** :

	![Authentification unique](./media/active-directory-saas-zscaler-one-tutorial/IC800209.png "Authentification unique")

	1.  Dans la page **Configurer l’authentification unique sur Zscaler One** du portail Azure, copiez la valeur **URL de la demande d’authentification**, puis collez-la dans la zone de texte **URL of the SAML Portal to which users are sent for authentication**.  
	2.  Dans la zone de texte **Attribute containing Login Name**, tapez **NameID**.  
	3.  Pour charger le certificat téléchargé, cliquez sur **Zscaler pem**.  
	4.  Sélectionnez **Enable SAML Auto-Provisioning**.  

10. Dans la page **Configure User Authentication**, procédez comme suit :

	![Administration](./media/active-directory-saas-zscaler-one-tutorial/IC800210.png "Administration")

	1.  Cliquez sur **Enregistrer**.  
	2.  Cliquez sur **Activate Now**.  

11. Dans la boîte de dialogue **Configurer l’authentification unique sur Zscaler One** du portail Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer**.

	![Configurer l’authentification unique](./media/active-directory-saas-zscaler-one-tutorial/IC800221.png "Configurer l’authentification unique")

##Configuration des paramètres de proxy

###Pour configurer les paramètres de proxy dans Internet Explorer

1.  Démarrez **Internet Explorer**.  

2.  Dans le menu **Outils**, sélectionnez **Options Internet** pour ouvrir la boîte de dialogue **Options Internet**.

	![Options Internet](./media/active-directory-saas-zscaler-one-tutorial/IC769492.png "Options Internet")

3.  Cliquez sur l’onglet **Connexions**.

	![Connexions](./media/active-directory-saas-zscaler-one-tutorial/IC769493.png "Connexions")

4.  Cliquez sur **Paramètres réseau** pour ouvrir la boîte de dialogue **Paramètres réseau**.

5.  Dans la section Serveur proxy, procédez comme suit :

	![Serveur proxy](./media/active-directory-saas-zscaler-one-tutorial/IC769494.png "Serveur proxy")

	1.  Sélectionnez Utiliser un serveur proxy pour votre réseau local.  
	2.  Dans la zone de texte Adresse, tapez **gateway.zscalerone.net**.  
	3.  Dans la zone de texte Port, tapez **80**.  
	4.  Sélectionnez **Ne pas utiliser de serveur proxy pour les adresses locales**.  
	5.  Cliquez sur **OK** pour fermer la boîte de dialogue **Paramètres du réseau local**.  

6.  Cliquez sur **OK** pour fermer la boîte de dialogue **Options Internet**.

##Configuration de l'approvisionnement des utilisateurs

Pour pouvoir se connecter à Zscaler One, les utilisateurs d’Azure AD doivent être approvisionnés dans Zscaler One. Dans le cas de Zscaler One, l’approvisionnement est une tâche manuelle.

###Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1.  Connectez-vous au client **Zscaler One**.  

2.  Cliquez sur **Administration**.

	![Administration](./media/active-directory-saas-zscaler-one-tutorial/IC781035.png "Administration")

3.  Cliquez sur **User Management**.

	![Ajouter](./media/active-directory-saas-zscaler-one-tutorial/IC781037.png "Ajouter")

4.  Dans l’onglet **Users**, cliquez sur **Add**.

	![Ajouter](./media/active-directory-saas-zscaler-one-tutorial/IC781037.png "Ajouter")

5.  Dans la section Add User, procédez comme suit :

	![Ajouter un utilisateur](./media/active-directory-saas-zscaler-one-tutorial/IC781038.png "Ajouter un utilisateur")

	1.  Renseignez les zones de texte **UserID**, **User Display Name**, **Password** et **Confirm Password**, puis sélectionnez **Groups** ainsi que l’attribut **Department** du compte Azure AD valide que vous souhaitez approvisionner.  
	2.  Cliquez sur **Enregistrer**.  

>[AZURE.NOTE]Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Zscaler One pour approvisionner des comptes d’utilisateurs Azure AD.

##Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

###Pour affecter des utilisateurs à Zscaler One, procédez comme suit :

1.  Dans le portail Azure AD, créez un compte test.  

2.  Dans la page d’intégration d’applications **Zscaler One**, cliquez sur **Affecter des utilisateurs**.

	![Affecter des utilisateurs](./media/active-directory-saas-zscaler-one-tutorial/IC800222.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

	![Oui](./media/active-directory-saas-zscaler-one-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/azure/dn308586.aspx).

<!---HONumber=August15_HO7-->