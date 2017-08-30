---
title: "Résoudre des erreurs d’annuaire incompatible pour des domaines managés Azure AD Domain Services existants | Microsoft Docs"
description: "Comprendre et résoudre des erreurs d’annuaire incompatible pour des domaines managés Azure AD Domain Services existants"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: ca9ff29f5f91b8d796a29706ab49a82e417d1ecd
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017

---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Résoudre des erreurs d’annuaire incompatible pour des domaines managés Azure AD Domain Services existants
Vous avez un domaine managé existant qui a été activé à l’aide du portail Azure Classic. Lorsque vous accédez au nouveau portail Azure et affichez le domaine managé, vous voyez le message d’erreur suivant :

![Erreur d’annuaire incompatible](.\media\getting-started\mismatched-tenant-error.png)

Vous ne pouvez pas administrer ce domaine managé tant que l’erreur n’est pas résolue.


## <a name="whats-causing-this-error"></a>Quelle est la cause de cette erreur ?
Cette erreur se produit lorsque votre domaine managé et le réseau virtuel dans lequel il est activé appartiennent à deux locataires Azure AD différents. Par exemple, vous avez un domaine managé nommé « contoso.com » qui a été activé pour le locataire Azure AD de Contoso. En revanche, le réseau virtuel Azure dans lequel le domaine managé a été activé appartient à Fabrikam, un autre locataire Azure AD.

Le nouveau portail Azure (plus précisément l’extension Azure AD Domain Services) repose sur Azure Resource Manager. Dans l’environnement Azure Resource Manager moderne, certaines restrictions sont appliquées pour offrir une sécurité accrue et pour effectuer le contrôle d’accès en fonction du rôle (RBAC) aux ressources. L’activation d’Azure AD Domain Services pour un locataire Azure AD est une opération sensible, car elle entraîne la synchronisation de hachages d’informations d’identification sur le domaine managé. Cette opération exige que vous soyez administrateur de locataires pour le répertoire. De plus, vous devez disposer de privilèges administratifs sur le réseau virtuel dans lequel vous activez le domaine managé. Pour que les contrôles RBAC fonctionnent de façon cohérente, le domaine managé et le réseau virtuel doivent appartenir au même locataire Azure AD.

En résumé, vous ne pouvez pas activer un domaine managé pour un locataire Azure AD « contoso.com » dans un réseau virtuel appartenant à un abonnement Azure détenu par un autre locataire Azure AD « fabrikam.com ». Le portail Azure Classic ne s’appuie pas sur la plateforme du Gestionnaire de ressources et n’applique pas de telles restrictions.

**Configuration valide** : dans ce scénario de déploiement, le domaine managé Contoso est activé pour le locataire Contoso Azure AD. Le domaine managé est exposé dans un réseau virtuel appartenant à un abonnement Azure détenu par le locataire Azure AD Contoso. Par conséquent, le domaine managé et le réseau virtuel appartiennent au même locataire Azure AD. Cette configuration est valide et entièrement pris en charge.

![Configuration de locataire valide](./media/getting-started/valid-tenant-config.png)

**Configuration de locataire incompatible** : dans ce scénario de déploiement, le domaine managé Contoso est activé pour le locataire Azure AD Contoso. Toutefois, le domaine managé est exposé dans un réseau virtuel appartenant à un abonnement Azure détenu par le locataire Azure AD Fabrikam. Par conséquent, le domaine managé et le réseau virtuel appartiennent à deux locataires Azure AD différents. Cette configuration de locataire est incompatible et n’est pas prise en charge. Le réseau virtuel doit être déplacé vers le même locataire Azure AD (autrement dit, Contoso) en tant que domaine managé. Pour plus de détails, voir la section [Résolution](#resolution).

![Configuration de locataire incompatible](./media/getting-started/mismatched-tenant-config.png)

Par conséquent, dans les scénarios où le domaine managé et le réseau virtuel dans lequel il est activé appartiennent à deux locataires Azure AD différents, vous voyez cette erreur.

Les règles suivantes s’appliquent dans l’environnement du Gestionnaire de ressources :
- Un annuaire Azure AD peut avoir plusieurs abonnements Azure.
- Un abonnement Azure peut avoir plusieurs ressources telles que des réseaux virtuels.
- Un domaine managé Azure AD Domain Services unique est activé pour un annuaire Azure AD.
- Un domaine managé Azure AD Domain Services peut être activé sur un réseau virtuel appartenant à un abonnement Azure quelconque au sein du même locataire Azure AD.


## <a name="resolution"></a>Résolution :
Vous avez deux options pour résoudre l’erreur d’annuaire incompatible. Ces options sont les suivantes :

- Cliquez sur le bouton **Supprimer** pour supprimer le domaine managé existant. Recréez un domaine managé à l’aide du [portail Azure](https://portal.azure.com), de façon à ce que le domaine managé et le réseau virtuel dans lequel il est disponible appartiennent à l’annuaire Azure AD. Vous devez joindre à nouveau au domaine managé que vous venez de créer toutes les machines précédemment jointes au domaine supprimé.

- Contactez le support technique Azure pour déplacer l’abonnement Azure contenant le réseau virtuel vers l’annuaire Azure AD auquel appartient votre domaine managé. Cliquez sur **Nouvelle demande de support**, puis spécifiez **annuaire incompatible** dans la section **Détails** de la demande. Incluez dans la demande de support les informations fournies dans le message d’erreur.


## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de prise en main](active-directory-ds-getting-started.md)
* [Azure AD Domain Services : guide de dépannage](active-directory-ds-troubleshooting.md)

