<properties
	pageTitle="Ajouter et vérifier un nom de domaine personnalisé dans Azure Active Directory | Microsoft Azure"
	description="Comment ajouter vos domaines existants à Azure Active Directory dans le cadre de la prise en main d’Azure AD. Configurez votre domaine personnalisé pour synchroniser les informations de compte utilisateur avec votre infrastructure d’identité locale."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>

# Ajouter et vérifier un nom de domaine personnalisé dans Azure Active Directory

Pour ajouter un nom de domaine personnalisé et vérifier s’il est utilisable avec Azure Active Directory, vous devez procéder comme suit.

1.  Effectuez l’une des actions suivantes :

    -   [Ajouter un nom de domaine personnalisé qui est fédéré avec Active Directory localement](#add-a-custom-domain-name-that-will-be-federated)

    -   [Ajouter un nom de domaine personnalisé qui n’est pas fédéré avec Active Directory localement](#add-a-custom-domain-name-that-will-not-be-federated)

2.  Vérifiez le nom de domaine personnalisé.

    -   Ajoutez les entrées DNS qu’Azure AD doit utiliser pour vérifier que votre organisation possède le nom de domaine personnalisé dans le bureau d’enregistrement de noms de domaine pour votre domaine.

    -   Vérifiez le domaine dans Azure AD.

## Ajouter un nom de domaine personnalisé qui est fédéré

Pour plus d’informations sur l’intégration d’annuaire local à l’aide d’Azure AD Connect, consultez

**Pour ajouter un nom de domaine personnalisé à votre annuaire Azure AD**

1.  Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) avec un compte disposant des privilèges d’administrateur général dans Azure AD.

2.  Sélectionnez Active Directory.

3.  Ouvrez votre annuaire.

4.  Sélectionnez l’onglet **Domaines**.

5.  Dans la barre de commandes, sélectionnez **Ajouter**.

6.  Entrez le nom de votre domaine personnalisé. Incluez l’extension .com.

7.  Cochez la case **Je prévois de configurer ce domaine pour la connexion unique à mon annuaire Active Directory local**.

8.  Cliquez sur **Add**.

## Ajouter un nom de domaine personnalisé qui n’est pas fédéré

La plupart des noms de domaines personnalisés sont des domaines de deuxième niveau « contoso.com ».

**Pour ajouter le nom de domaine personnalisé à votre annuaire Azure AD**

1.  Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) avec un compte disposant des privilèges d’administrateur général dans Azure AD.

2.  Sélectionnez Active Directory.

3.  Ouvrez votre annuaire.

4.  Sélectionnez l’onglet **Domaines**.

5.  Dans la barre de commandes, sélectionnez **Ajouter**.

6.  Entrez le nom de votre domaine personnalisé. Incluez l’extension .com.

7.  Assurez-vous que la case **Je prévois de configurer ce domaine pour la connexion unique à mon annuaire Active Directory local** n’est pas cochée.

8.  Sélectionnez **Ajouter**.

9.  Vérifiez que votre domaine a été ajouté à l’annuaire.

## Vérifier un domaine auprès de n’importe quel bureau d’enregistrement de noms de domaine

Pour vérifier votre domaine, créez un enregistrement DNS auprès d’un bureau d’enregistrement de noms de domaine, ou à l’endroit où est hébergé votre DNS et Azure AD utilise cet enregistrement pour confirmer que vous possédez le domaine. [Instructions pour ajouter des entrées DNS à des bureaux d’enregistrement DNS courants](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

Si le domaine est déjà enregistré auprès d’un bureau d’enregistrement de noms de domaine, les enregistrements DNS nécessaires existent déjà.

Si vous avez ajouté un domaine personnalisé, mais que le domaine n’a pas encore été vérifié, l’état indique **Non vérifié**.

## Vérifiez un nom de domaine personnalisé que vous n’allez pas fédérer avec un annuaire local
Une fois que tous les enregistrements que vous avez créés pour votre domaine ont été ajoutés avec succès par le biais du système DNS à votre bureau d’enregistrement de domaines, procédez comme suit :

1.  Dans Azure Active Directory dans le [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur **Domaines**.

2.  Dans la liste **Domaines**, recherchez le domaine que vous vérifiez et, selon le portail que vous utilisez, cliquez sur **Cliquez pour vérifier le domaine** ou **Vérifiez**.

3.  Suivez les instructions fournies pour terminer le processus de vérification.

    -   Si la vérification du domaine réussit, vous êtes averti que votre domaine a été ajouté à votre compte.

    -   En cas d’échec de la vérification du domaine, les modifications que vous avez éventuellement effectuées auprès du bureau d’enregistrement de domaines peuvent nécessiter du temps pour se propager. Annulez la vérification et réessayez ultérieurement.

S’il s’est écoulé plus de 72 heures depuis que vous avez apporté des modifications à votre domaine, connectez-vous au site web du bureau d’enregistrement de domaines et vérifiez que vous avez correctement entré les informations d’alias. Si vous avez entré les informations de manière incorrecte, vous devez supprimer l’enregistrement DNS incorrect et en créer un autre avec les informations correctes.

## Vérifiez votre domaine personnalisé pour la fédération avec votre annuaire local

1.  Téléchargez et exécutez Azure AD Connect. L’outil Azure AD Connect [vous invitera à ajouter les entrées DNS qu’il vous fournit](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

## Noms de domaine de troisième niveau

Vous pouvez utiliser des domaines de troisième niveau comme « europe.contoso.com » avec Azure AD. Pour ajouter et utiliser un domaine de troisième niveau :

1.  Ajoutez et vérifiez le domaine de deuxième niveau « contoso.com ».

2.  Ajouter des sous-domaines comme « europe.contoso.com » à Azure AD. Quand vous ajoutez un sous-domaine d’un domaine de deuxième niveau vérifié, le domaine de troisième niveau est automatiquement vérifié par Azure AD. Vous n’avez pas besoin d’ajouter davantage d’entrées DNS.

Ces étapes peuvent également être effectuées à l’aide de PowerShell et Graph.

Une fois votre domaine vérifié, vous pouvez le configurer pour qu’il fonctionne avec vos comptes.

## Étapes suivantes

- [Utilisation de noms de domaine personnalisés pour simplifier l’expérience de connexion pour vos utilisateurs](active-directory-add-domain.md)
- [Ajout d’une marque de société aux pages de connexion et du volet d’accès](active-directory-add-company-branding.md)
- [Affecter des utilisateurs à un domaine personnalisé](active-directory-add-domain-add-users.md)
- [Modifier le bureau d’enregistrement DNS pour votre nom de domaine personnalisé](active-directory-add-domain-change-registrar.md)
- [Supprimer un domaine personnalisé dans Azure Active Directory](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0302_2016-->