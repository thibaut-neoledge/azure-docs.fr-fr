<properties
	pageTitle="FAQ sur l’accès conditionnel | Microsoft Azure"
	description="Forum Aux Questions concernant l’accès conditionnel "
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="femila"/>

# FAQ sur l’accès conditionnel

## A quelles applications les stratégies d’accès conditionnel s’appliquent-elles ?
Consultez la rubrique [Accès conditionnel - applications prises en charge](active-directory-conditional-access-supported-apps.md).

## Les stratégies d’accès conditionnel s’appliquent-elles à la collaboration B2B et aux utilisateurs invités ?
Les stratégies sont appliquées aux utilisateurs dans le cadre d’une collaboration B2B. Toutefois, dans certains cas, l’utilisateur peut ne pas remplir les conditions de la stratégie, par exemple si son organisation ne prend pas en charge l’authentification multifacteur. La stratégie ne s’applique actuellement pas aux utilisateurs SharePoint invités. Comme la relation d’invité est conservée au sein de SharePoint, les comptes d’invités ne bénéficient pas des stratégies d’accès au niveau du serveur d’authentification. L’accès invité peut être géré au niveau de SharePoint.

## La stratégie SharePoint Online s’applique-t-elle également à OneDrive Entreprise ?
Oui.
 
## Pourquoi ne puis-je pas définir une stratégie pour les applications clientes telles que Word ou Outlook ?
La stratégie d’accès conditionnel définit les conditions requises pour accéder à un service et s’applique lorsque l’authentification est effectuée sur ce service. La stratégie n’est donc pas définie directement sur une application cliente, mais appliquée lorsqu’elle est appelée dans un service. Par exemple, une stratégie définie sur SharePoint s’applique aux clients qui appellent SharePoint, et la stratégie définie sur Exchange s’applique à Outlook.

<!---HONumber=AcomDC_0629_2016-->