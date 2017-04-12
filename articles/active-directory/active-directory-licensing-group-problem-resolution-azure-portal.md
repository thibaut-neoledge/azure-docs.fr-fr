---

title: "Résolution des problèmes de licence pour un groupe dans Azure Active Directory | Microsoft Docs"
description: "Identification et résolution des problèmes d’affectation de licences avec la licence basée sur le groupe Azure Active Directory"
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
ms.date: 02/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 68155ebaa6af36500bfe856c9bcd49f5efb6cbc2
ms.lasthandoff: 03/23/2017


---

# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identification et résolution des problèmes d’affectation de licences pour un groupe dans Azure Active Directory

Les licences basées sur le groupe dans Azure Active Directory (Azure AD) introduisent le concept d’utilisateurs en état d’erreur d’attribution de licence. Cet article explique les raisons pour lesquelles les utilisateurs peuvent se trouver dans cet état.

Lorsque vous affectez directement des licences à des utilisateurs individuels, sans recourir à une licence basée sur le groupe, l’opération d’affectation peut échouer. Par exemple, lorsque vous exécutez l’applet de commande PowerShell `Set-MsolUserLicense` sur un utilisateur, l’applet de commande peut échouer pour diverses raisons liées à la logique métier. Par exemple, il peut y avoir un nombre insuffisant de licences ou bien un conflit entre deux plans de service qui ne peuvent pas être affectés en même temps. Le problème vous est immédiatement signalé.

Lorsque vous utilisez un système de licence basée sur le groupe, les mêmes erreurs peuvent se produire, mais elles apparaissent en arrière-plan lorsque le service Azure AD affecte les licences. C’est pourquoi les erreurs ne peuvent pas vous être communiquées immédiatement. Au lieu de cela, elles sont enregistrées sur l’objet utilisateur et signalées par le biais du portail d’administration. Notez que l’intention initiale d’attribuer une licence à l’utilisateur perdure, mais qu’elle est enregistrée dans un état d’erreur et fera l’objet d’un examen et d’une résolution ultérieurs.

Pour rechercher les utilisateurs qui se trouvent à l’état d’erreur pour chaque groupe, ouvrez le panneau correspondant à chaque groupe. Sous **Licences**, une notification s’affiche si des utilisateurs se trouvent à l’état d’erreur. Sélectionnez la notification pour ouvrir la liste de tous les utilisateurs concernés. Vous pouvez afficher les utilisateurs individuellement afin de comprendre le problème sous-jacent. Cet article décrit les problèmes potentiels et explique comment les résoudre.

## <a name="not-enough-licenses"></a>Nombre insuffisant de licences

**Problème :** il n’y pas assez de licences disponibles pour l’un des produits spécifiés dans le groupe. Vous devez acheter des licences produit supplémentaires ou libérer des licences inutilisées par d’autres utilisateurs ou d’autres groupes.

Pour connaître le nombre de licences disponibles, accédez à **Azure Active Directory** > **Licences** > **Tous les produits**.

Pour voir quels utilisateurs et quels groupes utilisent des licences, cliquez sur un produit. Sous **Utilisateurs sous licence**, vous pouvez voir tous les utilisateurs auxquels des licences ont été affectées directement ou par le biais d’un ou de plusieurs groupes. Sous **Groupes sous licence**, vous pouvez voir tous les groupes auxquels ce produit est attribué.

**PowerShell :** les applets de commande PowerShell signalent cette erreur en tant que _CountViolation_.

## <a name="conflicting-service-plans"></a>Plans de service en conflit

**Problème :** l’un des produits spécifiés dans le groupe contient un plan de service en conflit avec un autre plan de service déjà attribué à l’utilisateur par le biais d’un autre produit. Certains plans de service sont configurés de sorte qu’ils ne puissent pas être attribués à l’utilisateur d’un autre plan de service lié.

Considérez l'exemple suivant. Un utilisateur possède une licence Office 365 Entreprise **E1** qui lui a été attribuée directement, avec tous les plans activés. L’utilisateur a été ajouté à un groupe auquel le produit Office 365 Entreprise **E3** est attribué. Ce produit contient des plans de service incompatibles avec les plans contenus dans E1. Par conséquent, l’attribution de la licence de groupe échoue et l’erreur « Plans de service en conflit » s’affiche. Dans cet exemple, les plans de service en conflit sont les suivants :

-   SharePoint Online (Plan 2) est en conflit avec SharePoint Online (Plan 1).
-   Exchange Online (Plan 2) est en conflit avec Exchange Online (Plan 1).

Pour résoudre ce conflit, vous devez désactiver ces deux plans sur la licence E1 qui a été directement attribuée à l’utilisateur. Sinon, vous devez modifier l’attribution de licence de l’ensemble du groupe et désactiver les plans dans la licence E3. Vous pouvez également supprimer la licence E1 à partir de l’utilisateur si elle est redondante dans le cadre de la licence E3.

Seul l’administrateur peut décider de la méthode à utiliser pour résoudre les problèmes de conflit de licences produit. Azure AD ne résout pas automatiquement les conflits de licences.

**PowerShell :** les applets de commande PowerShell signalent cette erreur en tant que _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>D’autres produits dépendent de cette licence

**Problème :** l’un des produits spécifiés dans le groupe contient un plan de service qui doit être activé pour un autre plan de service, dans un autre produit, afin de fonctionner. Cette erreur se produit lorsqu’Azure AD tente de supprimer le plan de service sous-jacent. Par exemple, cela peut se produire lorsque l’utilisateur est supprimé du groupe.

Pour résoudre ce problème, vous devez vérifier que le plan requis est toujours attribué aux utilisateurs par une autre méthode ou que les services dépendants sont désactivés pour ces utilisateurs. Après cela, vous pouvez supprimer la licence de groupe sur ces utilisateurs.

**PowerShell :** les applets de commande PowerShell signalent cette erreur en tant que _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>L’emplacement d’utilisation n’est pas autorisé

**Problème :** certains services Microsoft ne sont pas disponibles partout en raison de lois et réglementations locales. Avant de pouvoir attribuer une licence à un utilisateur, vous devez spécifier la propriété « Emplacement d’utilisation » pour l’utilisateur. Vous pouvez effectuer cette opération sous la section **Utilisateur** > **Profil** > **Paramètres** dans le portail Azure.

Si le service Azure AD tente d’attribuer une licence de groupe à un utilisateur dont l’emplacement d’utilisation n’est pas pris en charge, il échoue et enregistre l’erreur correspondante pour cet utilisateur.

Pour résoudre ce problème, retirez du groupe sous licence les utilisateurs associés à des emplacements non pris en charge. Autrement, si les valeurs d’emplacement d’utilisation actuelles ne représentent pas l’emplacement réel des utilisateurs, vous pouvez modifier ceux-ci de sorte que les licences soient attribuées correctement la prochaine fois (à condition que le nouvel emplacement soit pris en charge).

**PowerShell :** les applets de commande PowerShell signalent cette erreur en tant que _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Lorsqu’Azure AD attribue des licences de groupe, tous les utilisateurs sans emplacement d’utilisation spécifié héritent de l’emplacement du répertoire. Nous recommandons aux administrateurs de définir des valeurs d’emplacement d’utilisation correctes pour les utilisateurs avant d’utiliser la licence groupée afin de se conformer aux lois et réglementations locales.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>Que se passe-t-il lorsqu’un groupe est concerné par plusieurs licences produit ?

Vous pouvez attribuer plusieurs licences produit à un même groupe. Par exemple, vous pouvez attribuer Office 365 Entreprise E3 et Enterprise Mobility + Security à un groupe afin de faciliter l’activation de tous les services inclus pour les utilisateurs.

Azure AD tente d’attribuer toutes les licences spécifiées dans le groupe à chaque utilisateur. Si Azure AD ne peut pas attribuer l’un des produits en raison de problèmes de logique métier (par exemple, nombre insuffisant de licences pour l’ensemble du groupe ou conflits avec d’autres services activés pour l’utilisateur), les autres licences ne pourront pas non plus être attribuées au groupe.

Vous pourrez voir les utilisateurs pour lesquels l’attribution a échoué et vérifier les produits concernés.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>Comment faire pour forcer le traitement des licences dans un groupe afin de résoudre les erreurs ?

Selon les actions entreprises pour résoudre les erreurs, il peut être nécessaire de déclencher manuellement le traitement d’un groupe afin de mettre à jour l’état de l’utilisateur.

Par exemple, si vous avez libéré des licences en retirant des affectations directes de licences à des utilisateurs, vous devez déclencher le traitement des groupes ayant échoué précédemment afin d’attribuer des licences complètes à tous les utilisateurs membres. Pour ce faire, accédez au panneau du groupe, puis ouvrez **Licences** et cliquez sur le bouton **Retraiter** de la barre d’outils.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur d’autres scénarios de gestion des licences par le biais des groupes, consultez les articles suivants :

* [Assigning licenses to a group in Azure Active Directory (Attribution de licences à un groupe dans Azure Active Directory)](active-directory-licensing-group-assignment-azure-portal.md)
* [What is group-based licensing in Azure Active Directory? (Présentation des licences basées sur le groupe dans Azure Active Directory)](active-directory-licensing-whatis-azure-portal.md)
* [Migration des utilisateurs individuels sous licence vers une licence basée sur le groupe dans Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Autres scénarios de licences basées sur les groupes Azure Active Directory](active-directory-licensing-group-advanced.md)

