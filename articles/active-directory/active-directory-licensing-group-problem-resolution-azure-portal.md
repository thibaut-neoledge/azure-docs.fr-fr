---

title: "Identification et résolution des problèmes de licence pour un groupe dans Azure Active Directory | Microsoft Docs"
description: "Identification et résolution des problèmes d’attribution de licence avec la licence basée sur le groupe Azure Active Directory"
services: active-directory
keywords: Gestion des licences Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 6a9cebafd1ad8f513bfab897970241f7b82b2a53
ms.openlocfilehash: 9a434cf35d7934dc5eb759851fb65ad2a9f06eef
ms.lasthandoff: 02/22/2017


---

# <a name="identifying-and-resolving-license-problems-for-a-group-in-azure-active-directory"></a>Identification et résolution des problèmes de licence pour un groupe dans Azure Active Directory


Les licences basées sur le groupe dans Azure Active Directory (Azure AD) introduisent le concept d’utilisateurs en état d’erreur d’attribution de licence. Cet article explique les raisons pour lesquelles les utilisateurs peuvent se trouver dans cet état. Lorsque des licences sont attribuées directement à des utilisateurs individuels, sans recours à une licence basée sur le groupe, l’opération d’attribution peut échouer. Par exemple, lorsque l’administrateur exécute l’applet de commande PowerShell `Set-MsolUserLicense` sur un utilisateur, cette applet de commande peut échouer pour diverses raisons liées à la logique métier, comme un nombre insuffisant de licences ou un conflit entre deux plans de service qui ne peuvent pas être attribués en même temps. Ce problème est signalé immédiatement à l’utilisateur qui exécute la commande.

Avec les licences basées sur le groupe, les mêmes erreurs peuvent se produire, mais elles restent en arrière-plan lors de l’attribution des licences par le service Azure AD. C’est pourquoi elles ne peuvent pas être communiquées immédiatement à l’administrateur. Au lieu de cela, elles sont enregistrées sur l’objet utilisateur et signalées par le biais du portail d’administration. L’intention initiale d’attribuer une licence à l’utilisateur perdure, mais elle peut être appliquée dans un état actif ou être enregistrée dans un état d’erreur pour aboutir à un examen ultérieur et à la résolution du problème.

Pour rechercher des utilisateurs en état d’erreur dans chaque groupe, ouvrez le panneau de chaque groupe. Sous **Licences**, une notification indique si des utilisateurs sont en état d’erreur. Sélectionnez la notification pour faire apparaître la liste de tous les utilisateurs affectés. Vous pouvez afficher chaque utilisateur un par un afin de comprendre le problème sous-jacent. Cet article décrit les problèmes potentiels et explique comment les résoudre.

## <a name="not-enough-licenses"></a>Nombre insuffisant de licences

Il n’y pas assez de licences disponibles pour l’un des produits spécifiés dans le groupe. Vous devez acheter des licences produit supplémentaires ou libérer des licences inutilisées par d’autres utilisateurs ou d’autres groupes.

Pour connaître le nombre de licences disponibles, accédez à **Azure Active Directory &gt; Licences &gt; Tous les produits**.

Pour voir quels utilisateurs et quels groupes utilisent des licences, cliquez sur un produit. Sous **Utilisateurs sous licence**, vous pouvez voir tous les utilisateurs auxquels des licences ont été attribuées directement ou par le biais d’un ou de plusieurs groupes. Sous **Groupes sous licence**, vous pouvez voir tous les groupes auxquels ce produit est attribué.

## <a name="conflicting-service-plans"></a>Plans de service en conflit

L’un des produits spécifiés dans le groupe contient un plan de service en conflit avec un autre plan de service déjà attribué à l’utilisateur par le biais d’un autre produit. Certains plans de service sont configurés de sorte qu’ils ne puissent pas être attribués à l’utilisateur d’un autre plan de service lié.

Prenons l’exemple suivant : un utilisateur possède une licence Office 365 Entreprise **E1** qui lui a été attribuée directement, avec tous les plans activés. L’utilisateur a été ajouté à un groupe auquel le produit Office 365 Entreprise **E3** est attribué. Ce produit contient des plans de service incompatibles entre E1 et E3. Par conséquent, l’attribution de la licence de groupe échoue et l’erreur « Plans de service en conflit » s’affiche. Dans cet exemple, les plans de service en conflit sont les suivants :

-   SharePoint Online (Plan 2) est en conflit avec SharePoint Online (Plan 1)

-   Exchange Online (Plan 2) est en conflit avec Exchange Online (Plan 1)

Pour résoudre ce conflit, vous devez désactiver ces deux plans, soit sur la licence E1 directement attribuée à l’utilisateur, soit en modifiant l’attribution de licence de groupe et en désactivant les plans dans la licence E3. Vous pouvez également supprimer la licence E1 à partir de l’utilisateur si elle est redondante dans le cadre de la licence E3.

Seul l’administrateur peut décider de la méthode à utiliser pour résoudre les problèmes de conflit de licences produit. Azure AD ne résout pas automatiquement les conflits de licences.

## <a name="other-products-depend-on-this-license"></a>D’autres produits dépendent de cette licence

L’un des produits spécifiés dans le groupe contient un plan de service qui doit être activé pour un autre plan de service, dans un autre produit, afin de fonctionner. Cette erreur se produit lorsque Azure AD tente de supprimer le plan de service sous-jacent, par exemple après la suppression d’un utilisateur dans le groupe.

## <a name="usage-location-not-allowed"></a>Emplacement d’utilisation non autorisé

Certains services Microsoft ne sont pas disponibles partout en raison de lois et réglementations locales. Avant de pouvoir attribuer une licence à un utilisateur, l’administrateur doit spécifier la propriété « Emplacement d’utilisation » relative à l’utilisateur. Pour ce faire, il doit se rendre dans la section **Utilisateur &gt; Profil &gt; Paramètres** du portail Azure.

Lorsque vous attribuez une licence à un groupe, tous les utilisateurs sans emplacement d’utilisation spécifié héritent de l’emplacement du répertoire. Si certains de vos utilisateurs se trouvent dans des emplacements où les plans ne sont pas disponibles, pensez à modifier l’attribution de licence au niveau du groupe afin de désactiver les plans concernés. Vous pouvez également déplacer ces utilisateurs vers un autre groupe dont les attributions de licence ne sont pas en conflit avec l’emplacement.

Si vos utilisateurs se trouvent à différents emplacements, assurez-vous de bien prendre en compte cette situation dans vos objets utilisateur avant d’ajouter des utilisateurs à des groupes avec licences.

## <a name="what-happens-when-there-is-more-than-1-product-license-on-a-group"></a>Que se passe-t-il lorsqu’un groupe est concerné par plusieurs licences produit ?

Vous pouvez attribuer plusieurs licences produit à un même groupe. Par exemple, vous pouvez attribuer Office 365 Entreprise E3 et Enterprise Mobility + Security à un groupe afin de faciliter l’activation de tous les services inclus pour les utilisateurs.

Azure AD tente d’attribuer toutes les licences spécifiées dans le groupe à chaque utilisateur. Si nous ne pouvons pas attribuer l’un des produits en raison de problèmes de logique métier (par exemple, nombre insuffisant de licences et conflits avec d’autres services activés pour l’utilisateur), nous n’attribuons pas les autres licences au groupe non plus.

Vous pourrez voir les utilisateurs pour lesquels l’attribution a échoué et vérifier les produits concernés.

## <a name="how-to-force-processing-of-licenses-in-a-group-to-resolve-errors"></a>Comment faire pour forcer le traitement des licences dans un groupe afin de résoudre les erreurs ?

Selon les actions entreprises pour résoudre les erreurs, il peut être nécessaire de déclencher manuellement le traitement d’un groupe afin de mettre à jour l’état utilisateur.

Par exemple, si vous avez acheté des licences supplémentaires pour couvrir tous les utilisateurs, vous devez déclencher le traitement des groupes ayant échoué précédemment afin d’attribuer des licences complètes à tous les utilisateurs membres. Pour ce faire, accédez au panneau du groupe, puis ouvrez **Licences** et cliquez sur le bouton **Retraiter** de la barre d’outils.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur d’autres scénarios de gestion des licences par le biais des groupes, consultez :

* [Assigning licenses to a group in Azure Active Directory (Attribution de licences à un groupe dans Azure Active Directory)](active-directory-licensing-group-assignment-azure-portal.md)
* [What is group-based licensing in Azure Active Directory? (Présentation des licences basées sur le groupe dans Azure Active Directory)](active-directory-licensing-whatis-azure-portal.md)
* [Migration des utilisateurs individuels sous licence vers une licence basée sur le groupe dans Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory group-based licensing additional scenarios (Autres scénarios de licence basée sur le groupe Azure Active Directory)](active-directory-licensing-group-advanced.md)

