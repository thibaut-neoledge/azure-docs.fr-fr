<properties
	pageTitle="Notifications d’approvisionnement de comptes| Microsoft Azure"
	description="Découvrez comment vous assurer d’être informé des problèmes liés à l’approvisionnement des utilisateurs qui nécessitent votre attention en activant les notifications d’approvisionnement de comptes."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="markusvi"/>


# Notifications d’approvisionnement de comptes

Avec l’approvisionnement d’utilisateurs, vous pouvez automatiser le processus de gestion des utilisateurs dans les applications SaaS tierces. <br> Bien qu’il soit automatisé, votre interaction avec ce processus est parfois nécessaire. <br> C’est par exemple le cas quand le mot de passe du compte que vous avez configuré pour échanger des données avec une application SaaS tierce a expiré.

En activant les notifications d’approvisionnement de comptes, vous pouvez vous assurer d’être informé des problèmes liés à l’approvisionnement des utilisateurs qui nécessitent votre attention.

Vous activez ou désactivez les notifications d’approvisionnement de comptes dans le cadre de votre configuration d’approvisionnement d’utilisateurs pour une application SaaS tierce.

![Approvisionnement d’utilisateurs][1]



Pour activer les notifications d’approvisionnement de comptes, cochez la case associée dans la page de boîte de dialogue **Confirmation**, puis tapez l’alias de messagerie du destinataire.

![Notifications d’approvisionnement de comptes][2]
 


Vous pouvez entrer une liste de distribution comme destinataire ; toutefois, il convient de noter que le message électronique de notification contient des liens vers des rapports qui sont accessibles uniquement aux administrateurs Azure AD.

Si vous avez activé les notifications d’approvisionnement de comptes, vous recevrez des messages électroniques à propos des problèmes critiques liés à l’approvisionnement des utilisateurs. Cependant, pour éviter une surcharge de courrier électronique, vous recevrez un seul message de notification par jour pour chaque application SaaS pour laquelle la notification par courrier électronique est activée.


[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png

<!---HONumber=AcomDC_0107_2016-->