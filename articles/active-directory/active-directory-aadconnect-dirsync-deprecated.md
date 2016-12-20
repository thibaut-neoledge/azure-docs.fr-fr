---
title: "Mettre à niveau depuis DirSync et Azure AD Sync | Microsoft Docs"
description: "Décrit comment effectuer la mise à niveau depuis DirSync et Azure AD Sync vers Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d1339b36275b30438d92c9bbe94734644cc9aefe


---
# <a name="upgrade-windows-azure-active-directory-sync-dirsync-and-azure-active-directory-sync-azure-ad-sync"></a>Mettre à niveau Microsoft Azure Active Directory Sync (« DirSync ») et Azure Active Directory Sync (« Azure AD Sync »)
Azure AD Connect est la meilleure façon de connecter votre répertoire local avec Azure AD et Office 365. C’est l’occasion idéale d’effectuer la mise à niveau vers Azure AD Connect à partir de Microsoft Azure Active Directory Sync (DirSync) ou Azure AD Sync, car ces outils sont désormais déconseillés et ne seront plus pris en charge à partir du 13 avril 2017.

Les deux outils de synchronisation des identités qui sont déconseillés étaient proposés pour les clients de forêt unique (DirSync) et pour les clients à forêts multiples et expérimentés (Azure AD Sync). Ces outils plus anciens ont été remplacés par une solution unique qui est disponible pour tous les scénarios : Azure AD Connect. Cette solution offre de nouvelles fonctionnalités, des améliorations de fonctionnalités et la prise en charge de nouveaux scénarios. Pour pouvoir continuer à synchroniser vos données d’identités locales vers Azure AD et Office 365, nous vous recommandons vivement d’effectuer la mise à niveau vers Azure AD Connect.

La dernière version de DirSync a été publiée en juillet 2014 et la dernière version d’Azure AD Sync a été publiée en mai 2015.

## <a name="what-is-azure-ad-connect"></a>Qu’est-ce qu’Azure AD Connect ?
Azure AD Connect est le successeur de DirSync et d’Azure AD Sync. Il combine tous les scénarios pris en charge par les deux autres. Pour en savoir plus, consultez [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

## <a name="deprecation-schedule"></a>Planification de la désapprobation
| Date | Commentaire |
| --- | --- |
| 13 avril 2016 |Microsoft Azure Active Directory Sync (« DirSync ») et Azure Active Directory Sync (« Azure AD Sync ») sont annoncés comme déconseillés. |
| 13 avril 2017 |Fin de la prise en charge. Les clients ne seront plus en mesure d’ouvrir un cas de support sans tout d’abord effectuer la mise à niveau vers Azure AD Connect. |

## <a name="how-to-transition-to-azure-ad-connect"></a>Comment passer à Azure AD Connect
Si vous exécutez DirSync, vous disposez de deux méthodes de mise à niveau : mise à niveau sur place et déploiement parallèle. Une mise à niveau sur place est recommandée pour la plupart des clients et si vous avez un système d’exploitation récent avec moins de 50 000 objets. Dans d’autres cas, il est recommandé d’effectuer un déploiement parallèle dans lequel votre configuration DirSync est déplacée vers un nouveau serveur exécutant Azure AD Connect.

Si vous utilisez Azure AD Sync, une mise à niveau sur place est recommandée. Si vous le souhaitez, il est possible d’installer un nouveau serveur Azure AD Connect en parallèle et d’effectuer une migration « swing » du serveur Azure AD Sync vers Azure AD Connect.

| Solution | Scénario |
| --- | --- |
| [Mise à niveau à partir de DirSync](connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>Si vous disposez d’un serveur DirSync existant déjà en cours d’exécution.</li> |
| [Mise à niveau à partir d’Azure AD Sync](active-directory-aadconnect-upgrade-previous-version.md) |<li>Si vous effectuez la mise à niveau à partir d’Azure AD Sync.</li> |

Si vous souhaitez savoir comment effectuer une mise à niveau sur place depuis DirSync vers Azure AD Connect, regardez cette vidéo de Channel 9 :

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
> 
> 

## <a name="faq"></a>Forum Aux Questions
**Q: J'ai reçu une notification par courrier électronique de l'équipe Azure et/ou un message du centre de messages Office 365, mais j'utilise Connect.**  
La notification a été également envoyée aux clients à l'aide d'Azure AD Connect avec un numéro de build 1.0. \*. 0 (à l'aide d'une version pre-1.1). Microsoft recommande aux clients de maintenir à jour leurs versions d’Azure AD Connect. Grâce à la fonctionnalité de [mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) de la version 1.1., il est vraiment facile de toujours avoir une version récente d’Azure AD Connect installée.

**Q : DirSync/Azure AD Sync vont-ils cesser de fonctionner le 13 avril 2017 ?**  
 Non. La date à laquelle ils ne seront plus en mesure de communiquer avec Azure AD sera annoncée ultérieurement. Vous pourrez trouver cette information dans cette rubrique quand elle sera disponible.

**Q: Quelles versions de DirSync puis-je mettre à niveau ?**  
 Une mise à niveau est prise en charge à partir de n’importe quelle version de DirSync utilisée.

**Q : Qu’en est-il du connecteur Azure AD pour FIM/MIM ?**  
Le connecteur Azure AD pour FIM/MIM n’a **pas** été annoncé comme déconseillé. Il est dans l’état **feature freeze**; aucune nouvelle fonctionnalité n’est ajoutée et il ne reçoit aucune résolution de bogue. Microsoft recommande aux clients qui l’utilisent d’envisager de passer à Azure AD Connect. Il est fortement recommandé ne pas démarrer de nouveaux déploiements en l’utilisant. Ce connecteur sera annoncé comme déconseillé par la suite.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)




<!--HONumber=Nov16_HO3-->


