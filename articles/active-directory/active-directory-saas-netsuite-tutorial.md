<properties
    pageTitle="Didacticiel : Intégration d’Azure AD à NetSuite | Microsoft Azure"
    description="Apprenez à utiliser NetSuite avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#Didacticiel : intégration de NetSuite avec Azure Active Directory

Ce didacticiel explique comment connecter votre environnement NetSuite à Azure Active Directory (Azure AD). Il présente les méthodes à suivre pour configurer l’authentification unique à NetSuite, activer l’approvisionnement automatique des utilisateurs et affecter des utilisateurs pour qu’ils puissent accéder à NetSuite.

##Composants requis

1. Pour accéder à Active Directory Azure via le [portail de gestion Azure](https://manage.windowsazure.com), vous devez d’abord avoir un abonnement Azure valide.

2. Vous devez avoir un accès administrateur à un abonnement [NetSuite](http://www.netsuite.com/portal/home.shtml). Vous pouvez utiliser un compte d’essai gratuit pour chaque service.

##Étape 1 : ajout de NetSuite à votre annuaire

1. Dans le volet de navigation gauche du [portail de gestion Azure](https://manage.windowsazure.com), cliquez sur **Active Directory**.

	![Sélectionnez Active Directory dans le volet de navigation gauche.][0]

2. Dans la liste **Annuaire**, sélectionnez l’annuaire auquel vous voulez ajouter NetSuite.

3. Dans le menu principal, cliquez sur **Applications**.

	![Cliquez sur Applications.][1]

4. Cliquez sur **Ajouter** en bas de la page.

	![Cliquez sur Ajouter pour ajouter une nouvelle application.][2]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Cliquez sur Ajouter une application à partir de la galerie.][3]

6. Dans la zone **Rechercher**, entrez **NetSuite**. Puis sélectionnez **NetSuite** dans les résultats et cliquez sur **Terminer** pour ajouter l’application.

	![Ajoutez NetSuite.][4]

7. La page Démarrage rapide de NetSuite doit maintenant s’afficher :

	![Page Démarrage rapide de NetSuite dans Azure AD][5]

##Étape 2 : activation de l’authentification unique

1. Sur la page Démarrage rapide de NetSuite dans Azure AD, cliquez sur le bouton **Configurer l’authentification unique**.

	![Bouton Configurer l’authentification unique][6]

2. Une boîte de dialogue s’affiche dans laquelle un écran vous demande « Comment voulez-vous que les utilisateurs se connectent à NetSuite ? » Sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Sélectionner l’authentification unique Azure AD][7]

	> [AZURE.NOTE] Pour en savoir plus sur les différentes options d’authentification unique, [cliquez ici](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work).

3. Dans la page **Configurer les paramètres de l’application**, entrez votre URL de client NetSuite dans le champ **URL de réponse** en respectant l’un des formats suivants :
	- `https://<tenant-name>.netsuite.com/saml2/acs`
	- `https://<tenant-name>.na1.netsuite.com/saml2/acs`
	- `https://<tenant-name>.na2.netsuite.com/saml2/acs`
	- `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
	- `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
	- `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

	![Entrer l’URL de votre client][8]

4. Sur la page **Configurer l’authentification unique à NetSuite**, cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

	![Télécharger les métadonnées.][9]

5. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre site d’entreprise Netsuite en tant qu’administrateur.

6. Dans la barre d’outils située en haut de la page, cliquez sur **Configuration**, puis sur **Gestionnaire de configuration**.

	![Accéder au Gestionnaire de configuration][10]

7. Dans la liste **Tâches de configuration**, sélectionnez **Intégration**.

	![Accéder à l’intégration][11]

8. Dans la section **Gérer l’authentification**, cliquez sur **Authentification unique SAML**.

	![Accéder à l’authentification unique SAML][12]

9. Sur la page **Configuration SAML**, procédez comme suit :

	- Dans Azure Active Directory, copiez la valeur **URL de connexion distante** et collez-la dans le champ **Page de connexion du fournisseur d’identité** dans NetSuite.

		![Page de configuration de SAML.][13]

	- Dans NetSuite, sélectionnez **Principale méthode d’authentification**.

	- Pour le champ intitulé **Métadonnées du fournisseur d’identité SAMLV2**, sélectionnez **Charger un fichier de métadonnées IDP**. Puis cliquez sur **Parcourir** pour charger le fichier de métadonnées que vous avez téléchargé à l’étape 4.

		![Charger les métadonnées][16]

	- Cliquez sur **Envoyer**.

9. Dans Azure AD, cochez la case de confirmation de configuration de l’authentification unique pour activer le certificat que vous avez chargé dans NetSuite. Cliquez ensuite sur **Suivant**.

	![Cocher la case de confirmation][14]

10. Dans la dernière page de la boîte de dialogue, entrez une adresse de messagerie pour recevoir des notifications relatives aux erreurs et aux avertissements de maintenance de cette configuration d’authentification unique.

	![Entrez votre adresse de messagerie.][15]

11. Cliquez sur **Terminer** pour fermer la boîte de dialogue. Cliquez ensuite sur **Attributs** en haut de la page.

	![Cliquez sur Attributs.][17]

12. Cliquez sur **Ajouter un attribut utilisateur**.

	![Cliquez sur Ajouter un attribut utilisateur.][18]

13. Dans le champ **Nom de l’attribut**, entrez `account`. Dans le champ **Valeur de l’attribut**, entrez votre ID de compte NetSuite. Vous trouverez ci-dessous des instructions sur la recherche de votre ID de compte :

	![Ajoutez votre ID de compte NetSuite.][19]

	- Dans NetSuite, cliquez sur **Configurer** dans le menu de navigation principal.
	- Puis cliquez sous la section **Tâches de configuration** du menu de navigation situé à gauche, sélectionnez la section **Intégration**, puis cliquez sur **Préférences de services web**.
	- Copiez votre ID de compte NetSuite et collez-le dans le champ **Valeur de l’attribut** dans Azure AD.

		![Obtenir votre ID de compte][20]

14. Dans Azure AD, cliquez sur **Terminer** pour terminer l’ajout de l’attribut SAML. Puis cliquez sur **Appliquer les modifications** dans le menu inférieur.

	![Enregistrez vos modifications.][21]

15. Avant que les utilisateurs puissent utiliser l’authentification unique dans NetSuite, vous devez d’abord leur affecter les autorisations appropriées dans NetSuite. Procédez comme suit pour attribuer ces instructions.

	- Dans le menu de navigation principal, cliquez sur **Configuration**, puis sur **Gestionnaire de configuration**.

		![Accéder au Gestionnaire de configuration][10]

	- Dans le menu de navigation situé à gauche, sélectionnez **Utilisateurs/Rôles**, puis cliquez sur **Gérer les rôles**.

		![Cliquer sur Gérer les rôles][22]

	- Cliquez sur **Nouveau rôle**.

	- Entrez un **Nom** pour le nouveau rôle, puis cochez la case **Authentification unique seulement**.

		![Nommez le nouveau rôle.][23]

	- Cliquez sur **Enregistrer**.

	- Dans le menu situé en haut, cliquez sur **Autorisations**. Cliquez sur **Configuration**.

		![Accéder aux autorisations][24]

	- Sélectionnez **Configurer l’authentification unique SAM**, puis cliquez sur **Ajouter**.

	- Cliquez sur **Enregistrer**.

	- Dans le menu de navigation principal, cliquez sur **Configuration**, puis sur **Gestionnaire de configuration**.

		![Accéder au Gestionnaire de configuration][10]

	- Dans le menu de navigation situé à gauche, sélectionnez **Utilisateurs/Rôles**, puis cliquez sur **Gérer les utilisateurs**.

		![Accéder à la gestion des utilisateurs][25]

	- Sélectionnez un utilisateur de test. Puis cliquez sur **Modifier**.

		![Accéder à la gestion des utilisateurs][26]

	- Dans la boîte de dialogue Rôles, sélectionnez le rôle que vous avez créé et cliquez sur **Ajouter**.

		![Accéder à la gestion des utilisateurs][27]

	- Cliquez sur **Enregistrer**.

19. Pour tester votre configuration, consultez la section ci-dessous, intitulée [Affectation d’utilisateurs à NetSuite](#step-4-assign-users-to-netsuite).

##Étape 3 : activation de l’approvisionnement automatique des utilisateurs

> [AZURE.NOTE] Par défaut, vos utilisateurs approvisionnés sont ajoutés à la filiale racine de votre environnement NetSuite.

1. Dans Azure Active Directory, dans la page Démarrage rapide de NetSuite, cliquez sur **Configurer l’approvisionnement des utilisateurs**.

	![Configurer l’approvisionnement des utilisateurs][28]

2. Dans la boîte de dialogue qui s’ouvre, entrez vos informations d’identification d’administrateur pour NetSuite, puis cliquez sur **Suivant**.

	![Entrez vos informations d’identification d’administrateur NetSuite.][29]

3. Sur la page de confirmation, entrez votre adresse de messagerie pour recevoir des notifications sur les échecs d’approvisionnement.

	![Confirmez.][30]

4. Cliquez sur **Terminer** pour fermer la boîte de dialogue.

##Étape 4 : affectation d’utilisateurs à NetSuite

1. Pour tester votre configuration, commencez par créer un compte de test dans l’annuaire.

2. Dans la page Démarrage rapide de NetSuite, cliquez sur le bouton **Affecter des utilisateurs**.

	![Cliquer sur Affecter des utilisateurs][31]

3. Sélectionnez votre utilisateur de test, puis cliquez sur le bouton **Affecter** situé en bas de l’écran :

 - Si vous n’avez pas activé l’approvisionnement automatique des utilisateurs, le message de confirmation suivant s’affiche :

		![Confirm the assignment.][32]

 - Si vous avez activé l’approvisionnement automatique des utilisateurs, une invite s’affiche, dans laquelle vous pouvez définir le type de rôle de l’utilisateur dans NetSuite. Les utilisateurs récemment approvisionnés doivent apparaître dans votre environnement NetSuite au bout de quelques minutes.

4. Pour tester vos paramètres d’authentification unique, ouvrez le volet d’accès à l’adresse [https://myapps.microsoft.com](https://myapps.microsoft.com/), puis connectez-vous au compte de test et cliquez sur **NetSuite**.

##Articles connexes

- [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png

<!---HONumber=AcomDC_0427_2016-->