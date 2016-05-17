<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à Salesforce | Microsoft Azure"
    description="Apprenez à utiliser Salesforce avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
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

#Didacticiel : intégration de Salesforce avec Azure Active Directory

Ce didacticiel explique comment connecter votre environnement Salesforce à Azure Active Directory. Il présente les méthodes à suivre pour configurer l’authentification unique à Salesforce, activer l’approvisionnement automatique des utilisateurs et affecter des utilisateurs pour qu’ils puissent accéder à Salesforce.

##Composants requis

1. Pour accéder à Active Directory Azure via le [portail de gestion Azure](https://manage.windowsazure.com), vous devez d’abord avoir un abonnement Azure valide.

2. Vous devez avoir un client valide dans [Salesforce.com](https://www.salesforce.com/).

> [AZURE.IMPORTANT] Si vous utilisez un compte d’**essai** Salesforce.com, vous ne pouvez pas configurer l’approvisionnement automatique des utilisateurs. Les comptes d’essai n’ont pas l’accès d’API requis tant qu’ils ne sont pas achetés.
> 
> Vous pouvez contourner cette limitation en utilisant un [compte de développeur gratuit](https://developer.salesforce.com/signup) pour suivre ce didacticiel.

Si vous utilisez un environnement Salesforce Sandbox, veuillez consulter la page [Didacticiel : intégration d’Azure Active Directory à Salesforce Sandbox](https://go.microsoft.com/fwLink/?LinkID=521879).

##Didacticiels vidéo

Ce didacticiel est aussi disponible sous forme de vidéos, ci-dessous.

**Didacticiel vidéo - première partie : méthode d’activation de l’authentification unique**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**Didacticiel vidéo - deuxième partie : méthode d’automatisation de l’approvisionnement des utilisateurs**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##Étape 1 : ajout de Salesforce à votre annuaire

1. Dans le volet de navigation gauche du [portail de gestion Azure](https://manage.windowsazure.com), cliquez sur **Active Directory**.

	![Sélectionnez Active Directory dans le volet de navigation gauche.][0]

2. Dans la liste **Annuaire**, sélectionnez l’annuaire auquel vous voulez ajouter Salesforce.

3. Dans le menu principal, cliquez sur **Applications**.

	![Cliquez sur Applications.][1]

4. Cliquez sur **Ajouter** en bas de la page.

	![Cliquez sur Ajouter pour ajouter une nouvelle application.][2]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Cliquez sur Ajouter une application à partir de la galerie.][3]

6. Dans la **zone de recherche**, tapez **Salesforce**. Puis sélectionnez **Salesforce** dans les résultats et cliquez sur **Terminer** pour ajouter l’application.

	![Ajoutez Salesforce.][4]

7. La page Démarrage rapide de Salesforce doit maintenant s’afficher :

	![Page Démarrage rapide de Salesforce dans Azure AD][5]

##Étape 2 : activation de l’authentification unique

1. Avant de pouvoir configurer l’authentification unique, vous devez configurer et déployer un domaine personnalisé pour votre environnement Salesforce. Pour savoir comment procéder, consultez la page sur la [configuration d’un nom de domaine](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US).

2. Sur la page Démarrage rapide de Salesforce dans Azure AD, cliquez sur le bouton **Configurer l’authentification unique**.

	![Bouton Configurer l’authentification unique][6]

3. Une boîte de dialogue s’affiche dans laquelle un écran vous demande « Comment voulez-vous que les utilisateurs se connectent à Salesforce ? » Sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Sélectionner l’authentification unique Azure AD][7]

	> [AZURE.NOTE] Pour en savoir plus sur les différentes options d’authentification unique, [cliquez ici](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work).

4. Sur la page **Configurer les paramètres de l’application**, remplissez le champ **URL de connexion** en entrant l’URL de votre domaine Salesforce au format suivant :
 - Compte d’entreprise : `https://<domain>.my.salesforce.com`
 - Compte de développeur :`https://<domain>-dev-ed.my.salesforce.com` 

	![Entrer votre URL d’authentification unique][8]

5. Sur la page **Configurer l’authentification unique à Salesforce**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

	![Télécharger le certificat][9]

6. Ouvrez un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur Salesforce.

7. Sous le volet de navigation **Administrateur**, cliquez sur **Contrôles de sécurité** pour développer la section associée. Puis cliquez sur **Paramètres de l’authentification unique**.

	![Cliquer sur Paramètres de l’authentification unique sous Contrôles de sécurité][10]

8. Sur la page **Paramètres de l’authentification unique**, cliquez sur le bouton **Modifier**.

	![Cliquer sur le bouton Modifier][11]

	> [AZURE.NOTE] Si vous ne pouvez pas activer les paramètres de l’authentification unique pour votre compte Salesforce, il vous faudra peut-être contacter l’équipe du support technique Salesforce pour qu’elle active la fonctionnalité pour vous.

9. Sélectionnez **SAML activé**, puis cliquez sur **Enregistrer**.

	![Sélectionner SAML activé][12]

10. Pour configurer vos paramètres d’authentification unique SAML, cliquez sur **Nouveau**.

	![Sélectionner SAML activé][13]

11. Sur la page **Modifier les paramètres d’authentification unique SAML**, procédez à la configuration suivante :

	![Capture d’écran des configurations à effectuer][14]

 - Dans le champ **Nom**, entrez un nom convivial pour cette configuration. Le fait d’entrer une valeur pour **Nom** entraîne le remplissage automatique de la zone de texte **Nom API**.

 - Dans Azure AD, copiez la valeur **URL de l’émetteur** et collez-la dans le champ **Émetteur** dans Salesforce.

 - Dans la zone de texte **ID d’entité**, tapez votre nom de domaine Salesforce en suivant ce modèle :
     - Compte d’entreprise : `https://<domain>.my.salesforce.com`
     - Compte de développeur :`https://<domain>-dev-ed.my.salesforce.com`

 - Cliquez sur **Parcourir** ou **Choisir un fichier** pour ouvrir la boîte de dialogue **Choisir un fichier à télécharger**, sélectionnez votre certificat Salesforce, puis cliquez sur **Ouvrir** pour télécharger le certificat.

 - Pour **Type d’identité SAML**, sélectionnez **L’assertion contient le nom d’utilisateur de l’utilisateur salesforce.com**.

 - Pour **Emplacement de l’identité SAML**, sélectionnez **L’identité est dans l’élément NameIdentifier de l’instruction Subject**

 - Dans Azure AD, copiez la valeur **URL de connexion distante** et collez-la dans le champ **URL de connexion du fournisseur d’identité** dans Salesforce.

 - Pour **Liaison de demande initiée par le fournisseur de service**, sélectionnez **Redirection HTTP**.

 - Enfin, cliquez sur **Enregistrer** pour appliquer vos paramètres d’authentification unique SAML.

12. Dans le volet de navigation de gauche de Salesforce, cliquez sur **Gestion de domaine** pour développer la section associée, puis cliquez sur **Mon domaine**.

	![Cliquer sur Mon domaine][15]

13. Faites défiler le contenu de la fenêtre jusqu’à la section **Configuration de l’authentification**, puis cliquez sur le bouton **Modifier**.

	![Cliquer sur le bouton Modifier][16]

14. Dans la section **Service d’authentification**, sélectionnez le nom convivial de votre configuration d’authentification unique SAML, puis cliquez sur **Enregistrer**.

	![Sélectionner votre configuration d’authentification unique][17]

	> [AZURE.NOTE] Si plusieurs services d’authentification sont sélectionnés, les utilisateurs qui tentent d’initier une authentification unique sur votre environnement Salesforce devront choisir un service d’authentification pour se connecter. Si vous ne voulez pas que cela se produise, vous devez **décocher toutes les cases en regard des autres services d’authentification**.

15. Dans Azure AD, cochez la case de confirmation de configuration de l’authentification unique pour activer le certificat que vous avez chargé dans Salesforce. Cliquez ensuite sur **Suivant**.

	![Cocher la case de confirmation][18]

16. Dans la dernière page de la boîte de dialogue, entrez une adresse de messagerie pour recevoir des notifications relatives aux erreurs et aux avertissements de maintenance de cette configuration d’authentification unique.

	![Entrez votre adresse de messagerie.][19]

17. Cliquez sur **Terminer** pour fermer la boîte de dialogue. Pour tester votre configuration, consultez la section ci-dessous, intitulée [Affectation d’utilisateurs à Salesforce](#step-4-assign-users-to-salesforce).

##Étape 3 : activation de l’approvisionnement automatique des utilisateurs

1. Sur la page Démarrage rapide d’Azure AD pour Salesforce, cliquez sur le bouton **Configurer l’approvisionnement d’utilisateurs**.

	![Cliquer sur le bouton Configurer l’approvisionnement d’utilisateurs][20]

2. Dans la boîte de dialogue **Configurer l’approvisionnement d’utilisateurs**, entrez votre nom d’utilisateur et votre mot de passe d’administrateur Salesforce.

	![Entrer votre nom d’utilisateur ou mot de passe d’administrateur][21]

	> [AZURE.NOTE] Si vous configurez un environnement de production, nous vous recommandons de créer un compte d’administrateur dans Salesforce spécialement pour cette étape. Vous devez attribuer le profil **Administrateur système** à ce compte dans Salesforce.

3. Pour obtenir le jeton de sécurité Salesforce, ouvrez un nouvel onglet et connectez-vous au même compte d’administration Salesforce. Dans le coin supérieur droit de la page, cliquez sur votre nom, puis cliquez sur **Mes paramètres**.

	![Cliquer sur votre nom, puis cliquer sur Mes paramètres][22]

4. Dans le volet de navigation gauche, cliquez sur **Personnel** pour développer la section associée, puis sur **Réinitialiser mon jeton de sécurité**.

	![Cliquer sur votre nom, puis cliquer sur Mes paramètres][23]

5. Sur la page **Réinitialiser mon jeton de sécurité**, cliquez sur le bouton **Réinitialiser le jeton de sécurité**.

	![Lisez les avertissements.][24]

6. Contrôlez la boîte de réception associée à ce compte d’administrateur. Recherchez un message électronique provenant de Salesforce.com qui contient le nouveau jeton de sécurité.

7. Copiez le jeton, accédez à votre fenêtre Azure AD et collez-le dans le champ **Jeton de sécurité utilisateur**. Cliquez ensuite sur **Suivant**.

	![Coller le jeton de sécurité][25]

8. Sur la page de confirmation, vous pouvez choisir de recevoir des notifications par courrier électronique lorsque surviennent des échecs d’approvisionnement. Cliquez sur **Terminer** pour fermer la boîte de dialogue.

	![Entrer votre adresse de messagerie pour recevoir des notifications][26]

##Étape 4 : affectation d’utilisateurs à Salesforce

1. Pour tester votre configuration, commencez par créer un compte de test dans l’annuaire.

2. Sur la page Démarrage rapide de Salesforce, cliquez sur le bouton **Affecter des utilisateurs**.

	![Cliquer sur Affecter des utilisateurs][27]

3. Sélectionnez votre utilisateur de test, puis cliquez sur le bouton **Affecter** situé en bas de l’écran :

 - Si vous n’avez pas activé l’approvisionnement automatique des utilisateurs, le message de confirmation suivant s’affiche :

		![Confirm the assignment.][28]

 - Si vous avez activé l’approvisionnement automatique des utilisateurs, une invite s’affiche, dans laquelle vous pouvez définir le type de profil Salesforce de l’utilisateur. Les utilisateurs récemment approvisionnés doivent apparaître dans votre environnement Salesforce au bout de quelques minutes.

		![Confirm the assignment.][29]

		> [AZURE.IMPORTANT] Si vous configurez un environnement **développeur** Salesforce, vous ne disposerez que d'un nombre de licences limité pour chaque profil. Pour cette raison, il vaut mieux proposer aux utilisateurs un profil **Utilisateur Chatter Free**, pour lequel 4 999 licences sont disponibles.

4. Pour tester vos paramètres d’authentification unique, ouvrez le volet d’accès à l’adresse [https://myapps.microsoft.com](https://myapps.microsoft.com/), puis connectez-vous au compte de test et cliquez sur **Salesforce**.

##Articles connexes

- [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png

<!---HONumber=AcomDC_0427_2016-->
