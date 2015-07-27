<properties
   pageTitle="Didacticiel : intégration de Google Apps avec Azure Active Directory | Microsoft Azure"
   description="Apprenez à utiliser Google Apps avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore."
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="TerryLanfear"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/15/2015"
   ms.author="liviodlc"/>

#Didacticiel : intégration de Google Apps avec Azure Active Directory

Ce didacticiel explique comment connecter votre environnement Google Apps à Azure Active Directory (Azure AD). Il présente les méthodes à suivre pour configurer l’authentification unique à Google Apps, activer l’approvisionnement automatique des utilisateurs et affecter des utilisateurs pour qu’ils puissent accéder à Google Apps.

##Conditions préalables

1. Pour accéder à Active Directory Azure via le [portail de gestion Azure](https://manage.windowsazure.com), vous devez d’abord avoir un abonnement Azure valide.

2. Vous devez avoir un client valide pour [Google Apps for Work](https://www.google.com/work/apps/) ou [Google Apps for Education](https://www.google.com/edu/products/productivity-tools/). Vous pouvez utiliser un compte d’essai gratuit pour chaque service.

##Étape 1 : ajout de Google Apps à votre annuaire

1. Dans le volet de navigation gauche du [portail de gestion Azure](https://manage.windowsazure.com), cliquez sur **Active Directory**.

	![Sélectionnez Active Directory dans le volet de navigation gauche.][0]

2. Dans la liste **Annuaire**, sélectionnez l'annuaire auquel vous voulez ajouter Google Apps.

3. Dans le menu principal, cliquez sur **Applications**.

	![Cliquez sur Applications.][1]

4. Cliquez sur **Ajouter** en bas de la page.

	![Cliquez sur Ajouter pour ajouter une nouvelle application.][2]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Cliquez sur Ajouter une application à partir de la galerie.][3]

6. Dans la zone **Rechercher**, entrez **Google Apps**. Puis sélectionnez **Google Apps** dans les résultats et cliquez sur **Terminer** pour ajouter l’application.

	![Ajoutez Google Apps.][4]

7. La page Démarrage rapide de Google Apps doit maintenant s’afficher :

	![Page Démarrage rapide de Google Apps dans Azure AD][5]

##Étape 2 : activation de l’authentification unique

1. Sur la page Démarrage rapide de Google Apps dans Azure AD, cliquez sur le bouton **Configurer l’authentification unique**.

	![Bouton Configurer l’authentification unique][6]

2. Une boîte de dialogue s’affiche dans laquelle un écran vous demande « Comment voulez-vous que les utilisateurs se connectent à Google Apps ? » Sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.

	![Sélectionner l’authentification unique Azure AD][7]

	> [AZURE.NOTE]Pour en savoir plus sur les différentes options d’authentification unique, [cliquez ici](https://msdn.microsoft.com/library/azure/dn308588.aspx).

3. Sur la page **Configurer les paramètres de l’application**, entrez votre URL de client Google Apps dans le champ **URL de connexion** en respectant le format suivant : `https://mail.google.com/a/<yourdomain>`

	![Entrer l’URL de votre client][8]

4. Sur la page **Configurer l’authentification unique sur Google Apps**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

	![Téléchargez le certificat.][9]

5. Ouvrez un nouvel onglet dans votre navigateur et utilisez votre compte d’administrateur pour vous connecter à la [Console d’administration de Google Apps](http://admin.google.com/).

6. Cliquez sur **Sécurité**. Si le lien ne s’affiche pas, il est peut-être masqué par le menu **Autres contrôles** situé en bas de l’écran.

	![Cliquez sur Sécurité.][10]

7. Sur la page **Sécurité**, cliquez sur **Configurer authentification unique (SSO).**

	![Cliquez sur Authentification unique.][11]

8. Modifiez la configuration comme suit :

	![Configurer l’authentification unique][12]

	- Sélectionnez **Configurer l’authentification unique avec un fournisseur d’identité tiers**.

	- Dans Azure AD, copiez la valeur du champ **URL du service d’authentification unique** et collez-la dans le champ **URL de la page de connexion** dans Google Apps.

	- Dans Azure AD, copiez la valeur du champ **URL du service de déconnexion unique** et collez-la dans le champ **URL de la page de déconnexion** de Google Apps.

	- Dans Azure AD, copiez la valeur du champ **Modifier l’URL de mot de passe**, puis collez-la dans le champ **Modifier l’URL de mot de passe** de Google Apps.

	- Dans Google Apps, pour le **Certificat de vérification**, chargez le certificat que vous avez téléchargé à l’étape 4.

	- Cliquez sur **Enregistrer les modifications**.

9. Dans Azure AD, cochez la case de confirmation de configuration de l’authentification unique pour activer le certificat que vous avez chargé dans Google Apps. Cliquez ensuite sur **Suivant**.

	![Cocher la case de confirmation][13]

10. Dans la dernière page de la boîte de dialogue, entrez une adresse de messagerie pour recevoir des notifications relatives aux erreurs et aux avertissements de maintenance de cette configuration d’authentification unique.

	![Entrez votre adresse de messagerie.][14]

11. Cliquez sur **Terminer** pour fermer la boîte de dialogue. Pour tester votre configuration, consultez la section ci-dessous, intitulée [Affectation d’utilisateurs à Google Apps](#step-4-assign-users-to-google-apps).

##Étape 3 : activation de l’approvisionnement automatique des utilisateurs

> [AZURE.NOTE]Il existe une autre option pour automatiser l’approvisionnement des utilisateurs avec Google Apps : il s’agit d’utiliser [Google Apps Active Sync (GADS)](https://support.google.com/a/answer/106368?hl=en), qui approvisionne vos identités Active Directory en local pour Google Apps. En revanche, la solution proposée dans ce didacticiel approvisionne vos utilisateurs Azure Active Directory (cloud) et vos groupes à extension messagerie pour Google Apps.

1. Connectez-vous à la [Console d’administration de Google Apps](http://admin.google.com/) avec votre compte d’administrateur, puis cliquez sur **Sécurité**. Si le lien ne s’affiche pas, il est peut-être masqué par le menu **Autres contrôles** situé en bas de l’écran.

	![Cliquez sur Sécurité.][10]

2. Sur la page **Sécurité**, cliquez sur **Référence de l’API**.

	![Cliquez sur Référence d’API.][15]

3. Sélectionnez **Activer l'accès à l'API**.

	![Cliquez sur Référence d’API.][16]

	> [AZURE.IMPORTANT]Le nom d’utilisateur Active Directory Azure de chaque utilisateur que vous souhaitez approvisionner pour Google Apps *doit* être associé à un domaine personnalisé. Par exemple, les noms d’utilisateur qui ressemblent à bob@contoso.onmicrosoft.com ne sont pas acceptés par Google Apps, tandis que ceux ressemblant à bob@contoso.com sont acceptés. Vous pouvez modifier le domaine d’un utilisateur existant en modifiant ses propriétés dans Azure AD. Vous trouverez ci-dessous des instructions sur la configuration d’un domaine personnalisé pour Azure Active Directory et Google Apps.

4. Si vous n’avez pas encore ajouté un nom de domaine personnalisé pour Azure Active Directory, suivez la procédure ci-dessous :

	- Dans le volet de navigation gauche du [portail de gestion Azure](https://manage.windowsazure.com), cliquez sur **Active Directory**. Dans la liste Annuaire, sélectionnez votre annuaire. 

	- Dans le menu principal, cliquez sur **Domaines**, puis sur**Ajouter un domaine personnalisé**.

		![Ajout d'un domaine personnalisé][17]

	- Tapez votre nom de domaine dans le champ **Nom de domaine**. Ce nom de domaine doit être identique à celui que vous souhaitez utiliser pour Google Apps. Lorsque vous êtes prêt, cliquez sur le bouton **Ajouter**.

		![Entrez votre nom de domaine.][18]

	- Cliquez sur **Suivant** pour accéder à la page de vérification. Pour vérifier que vous possédez ce domaine, vous devez modifier les enregistrements DNS du domaine en fonction des valeurs fournies dans cette page. Vous pouvez choisir d’effectuer la vérification en utilisant des **enregistrements MX** ou des **enregistrements TXT**, selon ce que vous sélectionnez pour l’option **Type d’enregistrement**. Pour obtenir des instructions plus complètes sur la vérification de nom de domaine avec Azure AD, consultez [Ajout de votre propre domaine à Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).

		![Vérifiez votre nom de domaine.][19]

	- Répétez la procédure précédente pour tous les domaines que vous souhaitez ajouter à votre annuaire.

5. Maintenant que vous avez vérifié tous vos domaines avec Azure AD, vous devez maintenant les vérifier à nouveau avec Google Apps. Pour chaque domaine qui n’est pas déjà inscrit auprès de Google Apps, procédez comme suit :

	- Dans la [Console d’administration de Google Apps](http://admin.google.com/), cliquez sur **Domaines**.

		![Cliquer sur Domaines][20]

	- Cliquez sur **Ajouter un domaine ou un alias de domaine**.

		![Ajouter un nouveau domaine][21]

	- Sélectionnez **Ajouter un autre domaine**, puis tapez le nom du domaine que vous souhaitez ajouter.

		![Entrer votre nom de domaine][22]

	- Cliquez sur **Continuer et vérifier la propriété du domaine**. Puis suivez les étapes pour vérifier que vous possédez le nom de domaine. Pour obtenir des instructions complètes sur la vérification de votre domaine avec Google Apps, consultez [Choisir une méthode de validation](https://support.google.com/webmasters/answer/35179).

	- Répétez la procédure précédente pour tous les domaines que vous souhaitez ajouter à Google Apps.

	> [AZURE.WARNING]Si vous modifiez le domaine principal pour votre client Google Apps tout en ayant déjà configuré l’authentification unique avec Azure AD, vous devez répéter l’étape 3 sous [Étape 2 : activation de l’authentification unique](#step-two-enable-single-sign-on).

6. Dans la [Console d’administration de Google Apps](http://admin.google.com/), cliquez sur **Rôles d’administrateur**.

	![Cliquer sur Google Apps][26]

7. Déterminez le compte d’administrateur à utiliser pour gérer l’approvisionnement de l’utilisateur. Pour le **rôle d’administrateur** de ce compte, modifiez les **privilèges** pour ce rôle. Veuillez vérifier qu’il possède tous **Privilèges d’administrateur d’API** pour que ce compte puisse être utilisé pour l’approvisionnement.

	![Cliquer sur Google Apps][27]

	> [AZURE.NOTE]Si vous configurez un environnement de production, nous vous recommandons de créer un compte d’administrateur dans Google Apps spécialement pour cette étape. Ce compte doit avoir un rôle d’administrateur associé qui possède les privilèges d’API nécessaires.

8. Dans le menu principal d’Azure Active Directory, cliquez sur **Applications**, puis sur **Google Apps**.

	![Cliquer sur Google Apps][23]

9. Sur la page Démarrage rapide de Google Apps, cliquez sur **Configurer l’approvisionnement de l’utilisateur**.

	![Configurer l’approvisionnement des utilisateurs][24]

10. Dans la boîte de dialogue qui s’ouvre, cliquez sur **Activer l’approvisionnement de l’utilisateur** pour procéder à l’authentification du compte d’administrateur Google Apps que vous voulez utiliser pour gérer l’approvisionnement.

	![Activer l’approvisionnement][25]

11. Confirmez que vous souhaitez permettre à Azure Active Directory d’apporter des modifications à votre client Google Apps.

	![Confirmez les autorisations.][28]

12. Cliquez sur **Terminer** pour fermer la boîte de dialogue.

##Étape 4 : affectation d’utilisateurs à Google Apps

1. Pour tester votre configuration, commencez par créer un compte de test dans l’annuaire.

2. Sur la page Démarrage rapide de Google Apps, cliquez sur le bouton **Affecter des utilisateurs**.

	![Cliquer sur Affecter des utilisateurs][29]

3. Sélectionnez votre utilisateur de test, puis cliquez sur le bouton **Affecter** situé en bas de l’écran :

 - Si vous n’avez pas activé l’approvisionnement automatique des utilisateurs, le message de confirmation suivant s’affiche :

		![Confirm the assignment.][30]

 - Si vous avez activé l’approvisionnement automatique des utilisateurs, une invite s’affiche, dans laquelle vous pouvez définir le type de rôle de l’utilisateur dans Google Apps. Les utilisateurs récemment approvisionnés doivent apparaître dans votre environnement Google Apps au bout de quelques minutes.

4. Pour tester vos paramètres d’authentification unique, ouvrez le volet d’accès à l’adresse [https://myapps.microsoft.com](https://myapps.microsoft.com/), puis connectez-vous au compte de test et cliquez sur **Google Apps**.

##Voir aussi

- [Liste des didacticiels d’intégration d’applications SaaS](active-directory-saas-tutorial-list.md)
- [Accès aux applications dans Azure AD](https://msdn.microsoft.com/library/azure/dn308590.aspx)
- [Présentation du volet d’accès](https://msdn.microsoft.com/library/azure/dn308586.aspx)

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-google-apps-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-google-apps-tutorial/add-app.png
[3]: ./media/active-directory-saas-google-apps-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-google-apps-tutorial/add-gapps.png
[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png

<!---HONumber=July15_HO3-->