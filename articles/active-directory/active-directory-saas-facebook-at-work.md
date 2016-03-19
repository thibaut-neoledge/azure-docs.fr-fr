<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Facebook at Work | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Facebook at Work."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="asmalser"/>


# Didacticiel : Intégration d’Azure Active Directory à Facebook at Work

L’objectif de ce didacticiel est de vous montrer comment intégrer Facebook at Work avec Azure Active Directory (Azure AD).<br>L’intégration de Facebook at Work avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Facebook at Work. 
- Vous pouvez configurer automatiquement les comptes des utilisateurs auxquels l’accès à Facebook at Work a été accordé.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Facebook at Work (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central. 

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).


## Composants requis 

Pour configurer l’intégration d’Azure AD avec CS Stars, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Facebook at Work avec l’authentification unique activée

Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 


## Ajout de Facebook at Work à partir de la galerie
Pour configurer l’intégration de Facebook at Work avec Azure AD, vous devez ajouter Facebook at Work à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Facebook at Work à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail de gestion Azure**, cliquez sur **Active Directory**. <br><br>![Active Directory][1]<br>

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d’annuaire, cliquez sur **Applications** dans le menu du haut. <br><br>![Applications][2]<br>

4. Cliquez sur **Ajouter** en bas de la page. <br><br>![Applications][3]<br>

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**. <br><br>![Applications][4]<br>

6. Dans la zone de recherche, entrez **Facebook at Work**.

7. Dans le volet de résultats, sélectionnez **Facebook at Work**, puis cliquez sur **Terminer** pour ajouter l’application.


### Configuration de l’authentification unique Azure AD

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Facebook at Work avec leur compte Azure Active Directory, en utilisant la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique Azure AD avec Facebook at Work, procédez comme suit :**

1.	Après avoir ajouté Facebook at Work dans le portail de gestion Azure, cliquez sur **Configurer l’authentification unique**.

2.	Dans l’écran **Configurer l’URL de l’application**, entrez l’URL à l’emplacement où les utilisateurs se connecteront à votre application Facebook at Work. Voici l’URL de votre locataire Facebook at Work (exemple : https://example.facebook.com/). Une fois que vous avez terminé, cliquez sur **Suivant**.

3.	Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Facebook at Work en tant qu’administrateur.

4. Suivez les instructions à l’adresse suivante pour configurer Facebook at Work pour utiliser Azure AD comme fournisseur d’identité : [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)

5.	Une fois que vous avez terminé, revenez aux fenêtres de navigateur affichant le portail de gestion Azure, cochez la case pour confirmer que vous avez terminé la procédure, puis cliquez sur **Suivant** et **Terminer**.


## Approvisionnement automatique des utilisateurs à Facebook at Work

Azure AD prend en charge la possibilité de synchroniser automatiquement les informations de compte des utilisateurs affectés à Facebook at Work. La synchronisation automatique permet à Facebook at Work d’obtenir les données nécessaires pour autoriser l’accès des utilisateurs, avant leur première tentative de connexion. Elle annule également l’approvisionnement des utilisateurs à Facebook at Work lorsque l’accès a été révoqué dans Azure AD.

L’approvisionnement automatique peut être configuré en cliquant sur **Configurer l’approvisionnement de compte** dans la fenêtre du portail de gestion Azure.

Pour plus d’informations sur la façon de configurer l’approvisionnement automatique, consultez [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)


## Affectation d’utilisateurs à Facebook at Work

Pour que les utilisateurs AAD approvisionnés puissent voir Facebook at Work sur leur volet d’accès, l’accès doit leur avoir été affecté à l’intérieur du portail de gestion Azure.

**Pour affecter des utilisateurs à Facebook at Work :**

1.	Sur la page de démarrage de Facebook at Work dans le portail de gestion Azure, cliquez sur **Affecter des comptes**.

2.	Dans le menu **Afficher**, sélectionnez si vous souhaitez affecter un utilisateur ou un groupe à Facebook at Work et cliquez sur la coche.

3.	Dans la liste obtenue, sélectionnez les utilisateurs ou le groupe auxquels vous souhaitez affecter Facebook at Work.

4.	Dans le pied de page, cliquez sur le bouton **Affecter**.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png

<!---HONumber=AcomDC_0204_2016-->