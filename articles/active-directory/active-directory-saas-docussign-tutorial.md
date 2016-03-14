<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec DocuSign | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et DocuSign."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/29/2016"
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




<br><br>![Configuration de l'authentification unique][0]<br>


 

## Activation de l’intégration d’applications pour DocuSign

L’objectif de cette section est d’expliquer comment souligner l’intégration d’application pour DocuSign.

### Pour activer l’intégration d’application pour DocuSign, procédez comme suit :

1. Dans le volet de navigation de gauche du portail Azure Classic, cliquez sur **Active Directory**. <br><br>![Configuration de l'authentification unique][1]<br>

2. Dans la liste Annuaire, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut. <br><br>![Configuration de l'authentification unique][2]<br>

4. Cliquez sur **Ajouter** en bas de la page. <br><br>![Applications][3]<br>

5. Dans la boîte de dialogue Que voulez-vous faire ?, cliquez sur **Ajouter une application à partir de la galerie**. <br><br>![Configuration de l'authentification unique][4]<br>


6. Dans la zone de recherche, saisissez **DocuSign**. <br><br>![Configuration de l'authentification unique][5]<br>

7. Dans le volet de résultats, sélectionnez **DocuSign**, puis cliquez sur **Terminer** pour ajouter l’application. <br><br>![Configuration de l'authentification unique][6]<br>




## Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur DocuSign avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.


### Pour configurer l’authentification unique, procédez comme suit :

1. Dans le portail Azure Classic, puis sur la page **Intégration d’application DocuSign**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue Configurer l’authentification unique. <br><br>![Configuration de l'authentification unique][7]<br>

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à DocuSign**, sélectionnez **Authentification unique Microsoft Azure AD**, puis cliquez sur Suivant. <br><br>![Configuration de l'authentification unique][8]<br>

3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion à DocuSign**, saisissez l’URL de votre locataire DocuSign, puis cliquez sur **Suivant**. L’URL obéit au schéma suivant : *https://<yourcompanyname>.docusign.net/Member/MemberLogin.aspx?ssoname=<yourSSOInstanceName>* <br><br>![Configuration de l'authentification unique][9]<br>


    > [AZURE.TIP] Si vous ne savez pas quelle est l’URL d’application de votre locataire, essayez de contacter Docusign via SSOSetup@Docusign.com pour obtenir l’URL d’authentification unique initiée par SP pour votre locataire.
 

4. Sur la page **Configurer l’authentification unique à DocuSign**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat en local sur votre ordinateur. <br><br>![Configuration de l'authentification unique][10]<br>


5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **Docusign** en tant qu’administrateur.


6. Dans le menu du haut, développez le menu de l’utilisateur, cliquez sur **Préférences**, puis, dans le volet de navigation de gauche, développez **Gestion des comptes**, puis cliquez sur **fonctionnalités**. <br><br>![Configuration de l'authentification unique][11]<br>

7. Cliquez sur **Configuration SAML**, puis sur le lien **Configuration SAML**.



8. Dans la section **Configuration de SAML 2.0 **, procédez comme suit : <br><br>![Configuration de l'authentification unique][13]<br>


    a. Sur la page **Configurer l’authentification unique sur DocuSign** du portail Azure Classic, copiez la valeur URL de l’émetteur**, puis collez-la dans la zone de texte **URL du point de terminaison du fournisseur d’identité**.

    > [AZURE.IMPORTANT] Si cette option de configuration n’est pas disponible, contactez votre responsable de compte Docusign ou contactez l’équipe de prise en charge de l’authentification unique par courrier électronique ([SSOSetup@docusign.com](mailto:SSOSetup@docusign.com)).
 
    b. Cliquez sur **Parcourir** pour charger le certificat téléchargé.


    c. Sélectionnez **Activer prénom, nom et adresse de messagerie**.


    d. Cliquez sur **Enregistrer**.


9. Dans le portail Azure Classic, sélectionnez la **confirmation de la configuration de l’authentification unique**, puis cliquez sur **Suivant**. <br><br>![Applications][14]<br>

10. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**. <br><br>![Applications][15]<br>


 

## Configuration de l’approvisionnement de compte

L’objectif de cette section est d’expliquer comment activer l’approvisionnement utilisateur des comptes d’utilisateurs Active Directory sur DocuSign.

### Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :

1. Dans le **portail Azure Classic**, sur la page d’**intégration d’application DocuSign**, cliquez sur **Configurer l’approvisionnement de compte** pour ouvrir la boîte de dialogue Configurer l’approvisionnement d’utilisateurs. <br><br>![Configuration de l’approvisionnement de compte][30]<br>
 

2. Sur la page **Paramètres et informations d’identification administrateur** pour activer la configuration automatique de l’utilisateur, fournissez les informations d’identification d’un compte DocuSign avec des droits suffisants, puis cliquez sur **Suivant**. <br><br>![Configuration de l’approvisionnement de compte][31]<br>

3. Dans la boîte de dialogue **Tester la connexion**, cliquez sur **Lancer le test** et en cas de test réussi, cliquez sur **Suivant**. <br><br>![Configuration de l’approvisionnement de compte][32]<br>

3. Sur la page **Confirmation**, cliquez sur **Terminé**.

<br><br>![Configuration de l’approvisionnement de compte][33]<br>
 

## Affectation d’utilisateurs

Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### Pour affecter des utilisateurs à DocuSign, procédez comme suit :

1. Dans le **portail Azure Classic**, créez un compte de test.

2. Dans la page **Intégration d’applications DocuSign**, cliquez sur **Affecter des utilisateurs**. <br><br>![Affectation d’utilisateurs][40]<br>
 

3. Sélectionnez votre utilisateur test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer l’affectation.

<br><br>![Affectation d’utilisateurs][41]<br>


À présent, patientez 10 minutes et vérifiez que le compte est bien synchronisé avec Docusign.

Une première étape de vérification consiste à contrôler l’état de l’approvisionnement en cliquant sur le tableau de bord sur le D de la page d’intégration d’application DocuSign sur le portail Azure Classic. <br><br>![Affectation d’utilisateurs][42]<br>

Si le cycle d’approvisionnement d’utilisateur a abouti, l’état associé suivant est indiqué : <br><br>![Affectation d’utilisateurs][43]<br>


Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès.

Pour plus d’informations sur le panneau d’accès, consultez Présentation du panneau d’accès.





## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_06.png
[11]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_07.png

[13]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_09.png
[14]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_18.png

<!---HONumber=AcomDC_0302_2016-->