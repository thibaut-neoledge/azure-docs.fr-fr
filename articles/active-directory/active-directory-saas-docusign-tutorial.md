<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec DocuSign | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et DocuSign."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory avec DocuSign

L’objectif de ce didacticiel est de montrer comment intégrer Azure et DocuSign. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

- Un abonnement Azure valide
- Un locataire dans DocuSign



Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. [Activation de l’intégration d’applications pour DocuSign](#enabling-the-application-integration-for-docusign)


2. [Configuration de l'authentification unique](#configuring-single-sign-on)


3. [Configuration de l’approvisionnement de compte](#configuring-account-provisioning)


4. [Affectation d’utilisateurs](#assigning-users)

    ![Configuration de l'authentification unique][0]
 

## Activation de l’intégration d’applications pour DocuSign

L’objectif de cette section est d’expliquer comment souligner l’intégration d’application pour DocuSign.

### Pour activer l’intégration d’application pour DocuSign, procédez comme suit :

1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.

	![Configuration de l'authentification unique][1]

2. Dans la liste Annuaire, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Configuration de l'authentification unique][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue Que voulez-vous faire ?, cliquez sur **Ajouter une application à partir de la galerie**.

	![Configuration de l'authentification unique][4]


6. Dans la zone de recherche, tapez **DocuSign**.

	![Configuration de l'authentification unique][5]

7. Dans le volet de résultats, sélectionnez **DocuSign**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Configuration de l'authentification unique][6]


## Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur DocuSign avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.


### Pour configurer l’authentification unique, procédez comme suit :

1. Dans le portail Azure Classic, accédez à la page **Intégration d’application DocuSign**, puis cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue Configurer l’authentification unique.

	![Configuration de l'authentification unique][7]

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à DocuSign**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur Suivant.

	![Configuration de l'authentification unique][8]

3. Dans la page **Configurer les paramètres de l’application**, procédez comme suit :

	![Configuration de l'authentification unique][9]

	a. Dans la zone de texte **URL d’authentification**, tapez l’URL de votre client DocuSign au format suivant : Pour un environnement de production, le format d’URL est **https://account.docusign.com/organizations/<ORGANIZATIONID>/saml2/login/sp/<IDPID>**. Pour un environnement de démonstration, le format d’URL est **https://account-d.docusign.com/organizations/<ORGANIZATIONID>/saml2/login/sp/<IDPID>**.

	b. Dans la zone de texte **Identificateur**, tapez l’URL de votre émetteur DocuSign au format suivant : Pour un environnement de production, le format d’URL est **https://account.docusign.com/organizations/<ORGANIZATIONID>/saml2**. Pour un environnement de démonstration, le format d’URL est **https://account-d.docusign.com/organizations/<ORGANIZATIONID>/saml2**.

	c. Cliquez sur **Next**.


    > [AZURE.TIP] Si vous ne connaissez pas l’URL d’application de votre client, essayez de contacter DocuSign à l’adresse [SSOSetup@Docusign.com](emailTo:SSOSetup@Docusign.com) pour obtenir l’URL d’authentification unique initiée par SP pour votre client.
 

4. Dans la page **Configurer l’authentification unique à DocuSign**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur.

	![Configuration de l'authentification unique][10]


5. Dans une autre fenêtre de navigateur web, connectez-vous à votre **portail d’administration DocuSign** en tant qu’administrateur.


6. Dans le menu de navigation situé à gauche, cliquez sur **Domains** (Domaines).

	![Configuration de l'authentification unique][51]

7. Dans le volet droit, cliquez sur le bouton **CLAIM DOMAIN** (REVENDIQUER LE DOMAINE).

	![Configuration de l'authentification unique][52]

8. Dans la fenêtre contextuelle, entrez le nom de domaine de votre entreprise, puis cliquez sur CLAIM (REVENDIQUER). Veillez à vérifier le domaine et assurez-vous que son état est actif.

	![Configuration de l'authentification unique][53]

9. Dans le menu de navigation situé à gauche, cliquez sur **Identity Providers** (Fournisseurs d’identité).

	![Configuration de l'authentification unique][54]

10. Dans la volet droit, cliquez sur le bouton ADD IDENTITY PROVIDER (AJOUTER UN FOURNISSEUR D’IDENTITÉ). La page SSO Settings (Paramètres d’authentification unique) s’ouvre.
	
	![Configuration de l'authentification unique][55]

11. Dans la page Identity Provider Settings (Paramètres du fournisseur d’identité), effectuez les actions suivantes :

	a. Donnez un nom unique à votre configuration. N’utilisez pas d’espaces.

	b. Dans la zone de texte **Identity Provider Issuer** (Émetteur du fournisseur d’identité), copiez la valeur de **URL de l’émetteur** indiquée dans l’Assistant Configuration de l’application Azure AD.

	c. Dans la zone de texte **Identity Provider Login URL** (URL de connexion du fournisseur d’identité), copiez la valeur de **URL de connexion distante** indiquée dans l’Assistant Configuration de l’application Azure AD.

	d. Dans la zone de texte **Identity Provider Logout URL** (URL de déconnexion du fournisseur d’identité), copiez la valeur de **URL de déconnexion distante** indiquée dans l’Assistant Configuration de l’application Azure AD.

	e. Activez la case à cocher **Sign AuthN Request** (Signer la demande d’authentification).

	f. Assurez-vous que l’option **Send AuthN request by:** (Envoyer la demande d’authentification par :) est définie sur **POST**.

	g. Assurez-vous que l’option **Send logout request by:** (Envoyer la demande de déconnexion par :) est définie sur **POST**.

	![Configuration de l'authentification unique][56]

12. Dans la section **Custom Attribute Mapping** (Mappage d’attributs personnalisé), choisissez le champ à mapper avec la revendication Azure AD. Par exemple, nous avons utilisé la revendication **emailaddress** mappée avec la valeur sous la forme **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Il s’agit du nom de revendication par défaut d’Azure AD pour la revendication de courrier électronique.

	> [AZURE.NOTE] Utilisez l’identificateur d’utilisateur approprié pour mapper l’utilisateur d’Azure AD au mappage utilisateur DocuSign. Sélectionnez le champ correct et entrez la valeur appropriée en fonction des paramètres de votre organisation.

	![Configuration de l'authentification unique][57]

13. Dans la section **Identity Provider Certificate** (Certificat du fournisseur d’identité), cliquez sur **ADD CERTIFICATE** (AJOUTER UN CERTIFICAT) et chargez le certificat que vous avez téléchargé depuis l’Assistant Configuration de l’application Azure AD.

	![Configuration de l'authentification unique][58]

14. Cliquez sur le **Save** (Enregistrer) pour enregistrer tous les paramètres.

15. Dans la section **Identity Providers** (Fournisseurs d’identité), cliquez sur le bouton **Actions**, puis cliquez sur **Endpoints** (Points de terminaison).

	![Configuration de l'authentification unique][59]

16. Dans la section **View SAML 2.0 Endpoints** (Afficher les points de terminaison SAML 2.0), effectuez les étapes suivantes :

	a. Copiez la valeur de **Service Provider Issuer URL** (URL de l’émetteur du fournisseur de services) et collez-la dans la zone de texte **Identificateur** de l’Assistant Configuration d’Azure AD.

	b. Copiez la valeur de **Service Provider Login URL** (URL de connexion du fournisseur de services) et collez-la dans la zone de texte **URL d’authentification** de l’Assistant Configuration d’Azure AD.

	![Configuration de l'authentification unique][60]

	c. Cliquez sur **CLOSE** (FERMER).

15. Dans le portail Azure Classic, sélectionnez la **confirmation de la configuration de l’authentification unique**, puis cliquez sur **Suivant**.

	![Applications][14]

10. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.

	![Applications][15]
 

## Configuration de l’approvisionnement de compte

L’objectif de cette section est d’expliquer comment activer l’approvisionnement utilisateur des comptes d’utilisateurs Active Directory sur DocuSign.

### Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1. Dans le **portail Azure Classic**, accédez à la page **ntégration d’application DocuSign**, puis cliquez sur **Configurer l’approvisionnement de compte** pour ouvrir la boîte de dialogue Configurer l’approvisionnement d’utilisateurs.

	![Configuration de l’approvisionnement de compte][30]

2. Dans la page **Paramètres et informations d’identification administrateur**, pour activer l’approvisionnement automatique d’utilisateurs, fournissez les informations d’identification d’un compte DocuSign avec des droits suffisants, puis cliquez sur **Suivant**.

	![Configuration de l’approvisionnement de compte][31]

3. Dans la boîte de dialogue **Tester la connexion**, cliquez sur **Lancer le test**. Si le test réussit, cliquez sur **Suivant**.

	![Configuration de l’approvisionnement de compte][32]

3. Sur la page **Confirmation**, cliquez sur **Terminé**.

	![Configuration de l’approvisionnement de compte][33]
 

## Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### Pour affecter des utilisateurs à DocuSign, procédez comme suit :

1. Dans le **portail Azure Classic**, créez un compte de test.

2. Dans la page **Intégration d’application DocuSign**, cliquez sur **Affecter des utilisateurs**.

	![Affectation d’utilisateurs][40]
 

3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.

	![Affectation d’utilisateurs][41]


À présent, patientez 10 minutes et vérifiez que le compte est bien synchronisé avec DocuSign.

Une première étape de vérification consiste à contrôler l’état de l’approvisionnement en cliquant sur le tableau de bord sur le D de la page d’intégration d’application DocuSign dans le portail Azure Classic.

![Affectation d’utilisateurs][42]

Si le cycle d’approvisionnement d’utilisateur a abouti, l’état associé suivant est indiqué :

![Affectation d’utilisateurs][43]


Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le panneau d’accès.

Pour plus d’informations sur le panneau d’accès, consultez Présentation du panneau d’accès.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png

<!---HONumber=AcomDC_0803_2016-->