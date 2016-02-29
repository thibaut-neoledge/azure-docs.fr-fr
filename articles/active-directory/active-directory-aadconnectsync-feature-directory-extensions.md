<properties
   pageTitle="Azure AD Connect Sync : extensions d’annuaire | Microsoft Azure"
   description="Cette rubrique décrit la fonctionnalité d’extensions d’annuaire dans Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Azure AD Connect Sync : extensions d’annuaire
Les extensions d'annuaire vous permettent d'étendre le schéma dans Azure AD avec vos propres attributs à partir d'un annuaire Active Directory local. Vous pouvez ainsi créer des applications métier avec les attributs que vous continuez à gérer en local. Ces attributs peuvent être utilisés via des [extensions d’annuaire Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou [Microsoft Graph](https://graph.microsoft.io/). Vous pouvez voir les attributs disponibles à l’aide de l’[explorateur d’Azure AD Graph](https://graphexplorer.cloudapp.net) et de [l’Explorateur Microsoft Graph](https://graphexplorer2.azurewebsites.net/) respectivement.

Actuellement, aucune charge de travail Office 365 n’utilise ces attributs.

Vous pouvez configurer les attributs supplémentaires à synchroniser dans le chemin d’accès des paramètres personnalisés dans l’Assistant d’installation. ![Assistant d’extension de schéma](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) L’installation affiche les attributs suivants, qui sont des candidats valides :

- Types d’utilisateur et d’objet de groupe
- Attributs à valeur unique
- Chaînes, entier, binaire

Un objet peut avoir jusqu’à 100 attributs d’extension d’annuaire. La longueur maximale est de 250 caractères. Si une valeur d’attribut est plus longue, elle sera tronquée par le moteur de synchronisation.

Lors de l’installation d’Azure AD Connect, une application dans laquelle ces attributs seront disponibles est enregistrée. Vous pouvez voir cette application dans le portail Azure. ![Application d’extension de schéma](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

Ces attributs sont désormais disponibles via Graph : ![Graph](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Les attributs ont pour préfixe extension\_{AppClientId} \_. L’AppClientId aura la même valeur pour tous les attributs de votre annuaire Azure AD.

## Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md).

En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->