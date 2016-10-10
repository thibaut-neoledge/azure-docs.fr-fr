<properties
	pageTitle="Services de domaine Azure Active Directory : guide de dépannage | Microsoft Azure"
	description="Guide de dépannage pour les services de domaine Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2016"
	ms.author="maheshu"/>

# Services de domaine Azure AD : guide de dépannage
Cet article fournit des conseils de dépannage pour les problèmes que vous pouvez rencontrer pendant la configuration ou l’administration des services de domaine Azure Active Directory (AD).


### Vous ne pouvez pas activer les services de domaine Azure AD pour votre annuaire Azure AD
Si vous essayez d’activer les services de domaine Azure AD pour votre annuaire et que cette opération échoue, procédez comme suit :

- Vérifiez qu’aucun domaine existant avec le même nom de domaine n’est disponible sur ce réseau virtuel. Supposons par exemple qu’un domaine appelé « contoso.com » soit déjà disponible sur le réseau virtuel sélectionné. Par la suite, vous essayez d’activer un domaine géré par les services de domaine Azure AD portant le même nom de domaine (soit « contoso.com ») sur ce réseau virtuel. Vous rencontrez un échec lorsque vous essayez d’activer les services de domaine Azure AD en raison des conflits de noms de domaine sur ce réseau virtuel. Dans ce cas, vous devez utiliser un autre nom pour configurer votre domaine géré par les services de domaine Azure AD. Vous pouvez également annuler l’approvisionnement du domaine existant, puis passer à l’activation des services de domaine Azure AD.

- Vérifiez si votre annuaire Azure AD contient une application nommée « Synchronisation des services de domaine Azure AD ». Si cette application existe, vous devez la supprimer, puis réactiver les services de domaine Azure AD. Pour vérifier la présence de cette application et la supprimer le cas échant, procédez comme suit :

  1. Accédez au **portail Azure Classic** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. Sélectionnez le nœud **Active Directory** dans le volet gauche.
  3. Sélectionnez le client Azure AD (annuaire) pour lequel vous souhaitez activer les services de domaine Azure AD.
  4. Accédez à l’onglet **Applications**.
  5. Dans la liste déroulante, sélectionnez l’option **Applications que ma société possède**.
  6. Recherchez l’application **Synchronisation des services de domaine Azure AD**. Si cette application existe, supprimez-la.
  7. Une fois que vous avez supprimé l’application, essayez de nouveau d’activer les services de domaine Azure AD.


### Les utilisateurs sont incapables de se connecter aux services de domaine Asure AD gérés
Si un ou plusieurs utilisateurs de votre locataire Azure AD sont incapables de se connecter au domaine géré nouvellement créé, effectuez les étapes de dépannage suivantes :

- Essayez de vous connecter en utilisant le format UPN (par exemple, « joeuser@contoso.com ») au lieu du format SAMAccountName (« CONTOSO\\joeuser »). Parfois, le format SAMAccountName peut être généré automatiquement pour les utilisateurs dont le préfixe UPN est trop long ou identique à un autre utilisateur sur le domaine géré. Le format UPN garantit des données uniques au sein d’Azure AD.

> [AZURE.NOTE] Nous vous recommandons d’utiliser le format UPN pour vous connecter au domaine géré des services de domaine Azure AD.

- Assurez-vous d'avoir [activé la synchronisation du mot de passe](active-directory-ds-getting-started-password-sync.md) selon les étapes décrites dans le guide de mise en route.

- **Comptes externes** : assurez-vous que le compte d’utilisateur affecté n’est pas un compte externe dans le locataire Azure AD. Les exemples de comptes externes incluent les comptes Microsoft (par exemple, 'joe@live.com') ou les comptes d'utilisateurs d'un annuaire Azure AD externe. Dans la mesure où les services de domaine Azure AD n’ont pas d'informations d'identification pour ces comptes d'utilisateurs, ces utilisateurs ne peuvent pas se connecter au domaine géré.

- **Préfixe UPN trop long** : assurez-vous que le préfixe UPN du compte d’utilisateur affecté (la première partie de l’UPN) dans votre client Azure AD comporte moins de 20 caractères. Par exemple, pour l’UPN « joereallylongnameuser@contoso.com », le préfixe (« joereallylongnameuser ») est composé de plus de 20 caractères et ce compte n’est pas disponible dans le domaine géré.

- **Préfixe UPN en double** : assurez-vous qu’aucun autre compte d’utilisateur dans votre client Azure AD n’a le même préfixe UPN (la première partie de l’UPN) que celui du compte d’utilisateur affecté. Par exemple, si vous avez deux comptes d’utilisateurs « joeuser@finance.contoso.com » et « joeuser@engineering.contoso.com », les deux utilisateurs rencontrent des problèmes de connexion au domaine géré. Ce problème peut également se produire si l’un des comptes d’utilisateurs est un compte externe (par exemple, « joeuser@live.com »). Nous recherchons une solution à ce problème.

- **Comptes synchronisés** : si les comptes d’utilisateurs affectés sont synchronisés à partir d’un annuaire local, vérifiez que les points suivants sont respectés :
    - Vous avez déployé la [dernière version recommandée d’Azure AD Connect](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect) ou procédé à la mise à jour vers cette version.

    - Vous avez configuré Azure AD Connect pour [effectuer une synchronisation complète](active-directory-ds-getting-started-password-sync.md).

    - Selon la taille de votre annuaire, la mise à disposition des comptes d'utilisateurs et hachages d’informations d’identification dans les services de domaine Azure AD peut prendre du temps. Patientez suffisamment avant de tenter à nouveau de vous authentifier (de quelques heures, voire même un ou deux jours pour les annuaires volumineux).

    - Si le problème persiste après la vérification des étapes ci-dessus, essayez de redémarrer le service Microsoft Azure AD Sync. Ouvrez une invite de commande et exécutez les commandes suivantes sur votre ordinateur de synchronisation :
      1. net stop 'Microsoft Azure AD Sync'
      2. net start 'Microsoft Azure AD Sync'

- **Comptes cloud uniquement** : si le compte d’utilisateur affecté est un compte d’utilisateur dans le cloud uniquement, assurez-vous que l’utilisateur a modifié son mot de passe après avoir activé les services de domaine Azure AD. Cette étape permet de générer les hachures d’informations d'identification requises pour les services de domaine Azure AD.


### Nous contacter
Contactez l’équipe produit des Services de domaine Azure Active Directory pour [partager vos commentaires ou pour obtenir de l’aide](active-directory-ds-contact-us.md).

<!---HONumber=AcomDC_0928_2016-->