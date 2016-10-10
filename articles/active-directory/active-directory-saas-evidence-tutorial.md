<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à Evidence.com | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Evidence.com."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="asmalser"/>


# Didacticiel : Intégration d’Azure Active Directory à Evidence.com

L’objectif de ce didacticiel est de montrer comment configurer l’authentification unique entre Azure Active Directory (AAD) et Evidence.com. Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :
	
* Un abonnement Microsoft Azure valide
* Un abonnement Evidence.com avec l’authentification unique activée (envoyez un e-mail à l’adresse earlyaccess@evidence.com si l’authentification unique SAML n’est pas activée)

À l’issue de ce didacticiel, les utilisateurs AAD auxquels vous avez affecté l’accès à Evidence.com pourront s’authentifier de manière unique à l’aide du volet d’accès AAD.

## Ajout de Evidence.com à votre répertoire

Cette section explique comment ajouter Evidence.com en tant qu’application intégrée dans Azure Active Directory.

**Pour activer l’intégration d’application pour Evidence :**

1.	Dans le volet de navigation gauche du [portail Azure Classic](https://manage.windowsazure.com), cliquez sur **Active Directory**.

2.	Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3.	Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

4.	Pour ouvrir la galerie d’applications, cliquez sur **Ajouter**, puis sur **Ajouter une application à partir de la galerie**.

5.	Dans la zone de recherche, tapez **Evidence.com**.

6.	Dans le volet des résultats, sélectionnez **Evidence.com**, puis cliquez sur **Terminer** pour ajouter l’application.


## Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur Evidence.com avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique, procédez comme suit :**

1.	Après avoir ajouté Evidence.com dans le portail Azure Classic, cliquez sur **Configurer l’authentification unique**.
 
2.	Dans l’écran suivant, sélectionnez **Authentification unique avec Azure AD**, puis cliquez sur **Suivant**.

3.	Dans l’écran Configurer l’URL de l’application, entrez l’URL à laquelle les utilisateurs se connecteront à votre client Evidence.com (exemple : https://yourtenant.evidence.com), puis cliquez sur **Suivant**.

4.	Cliquez sur le lien **Télécharger le certificat**, puis enregistrez-le sur votre disque local. Ce certificat et les URL de métadonnées (ID d’entité, URL d’authentification unique et URL de déconnexion unique) seront utilisés pour configurer l’authentification unique sur le site Evidence.com.

5.	Dans une fenêtre de navigateur web distincte, connectez-vous à votre client Evidence.com en tant qu’administrateur et accédez à l’onglet **Admin**.
      
6.	Cliquez sur **Agency Single Sign On**
 
7.	Sélectionnez **SAML Based Single Sign On**.
 
8.	Dans le portail Azure Classic, copiez les valeurs de **URL de l’émetteur**, **URL du service d’authentification unique** et **URL du service de déconnexion unique** et collez-les dans les champs correspondants (Issuer URL, Single Sign On et Single Sign Out) dans Evidence.com.

9.	Ouvrez le certificat téléchargé à l’étape 4 à l’aide d’un éditeur de texte comme Notepad.exe et copiez et collez le contenu dans la zone **Security Certificate**.

10. Enregistrez la configuration dans Evidence.com.
 
11.	Dans le portail Azure Classic, activez la case à cocher **Confirmez que vous avez configuré l’authentification unique comme décrit plus haut**. Si vous activez cette case à cocher, le certificat actuel commence à fonctionner pour cette application.
 
12.	Sur la page Confirmation de l’authentification unique, cliquez sur **Terminer**.


## Création d’un utilisateur de test Evidence.com

Pour que les utilisateurs Azure AD puissent se connecter, leur accès doit être approvisionné dans l’application Evidence.com. Cette section décrit comment créer des comptes d’utilisateur Azure AD dans Evidence.com.

**Pour approvisionner un compte d’utilisateur dans Evidence.com :**

1.	Dans une fenêtre de navigateur web, connectez-vous à votre site d’entreprise Evidence.com en tant qu’administrateur.

2.	Accédez à l’onglet **Admin**.

3.	Cliquez sur **Add User**.

4.	Cliquez sur le bouton **Add**.

5.  La valeur **Email Address** (Adresse de messagerie) de l’utilisateur ajouté doit correspondre au nom d’utilisateur de l’utilisateur auquel vous souhaitez accorder l’accès dans Azure AD. Si le nom d’utilisateur et l’adresse de messagerie n’ont pas la même valeur dans votre organisation, vous pouvez modifier la section **Evidence.com > Attributs > Authentification unique** du portail Azure Classic de sorte que l’identificateur du nom envoyé à Evidence.com soit l’adresse de messagerie.


## Affectation d’utilisateurs à Evidence.com

Pour que les utilisateurs AAD approvisionnés puissent voir Evidence.com sur leur volet d’accès, l’accès doit leur avoir été affecté à l’intérieur du portail Azure Classic.

**Pour affecter des utilisateurs à Evidence.com :**

1.	Sur la page de démarrage rapide de Evidence.com dans le portail Azure Classic, cliquez sur **Affecter des utilisateurs à Evidence.com**.
 
2.	Dans le menu **Afficher**, indiquez si vous souhaitez affecter un utilisateur ou un groupe à Evidence.com et cliquez sur la coche.
 
3.	Dans la liste **Utilisateurs**, sélectionnez les utilisateurs ou le groupe à affecter à Evidence.com.
 
4.	Dans le pied de page, cliquez sur le bouton **Attribuer**.

<!---HONumber=AcomDC_0928_2016-->