---
title: "Azure AD Connect : intégration de vos identités locales avec Azure Active Directory. | Microsoft Docs"
description: "Azure AD Connect intègre vos répertoires locaux à Azure Active Directory. Cela vous permet de fournir une identité commune pour les applications Office 365, Azure et SaaS intégrées à Azure AD."
keywords: "introduction à Azure AD Connect, présentation d’Azure AD Connect, qu’est-ce qu’Azure AD Connect, installation d’active directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/04/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eedb788b2a174d01a2ef661cf4093ff938649bce


---
# <a name="integrating-your-onpremises-identities-with-azure-active-directory"></a>Intégration de vos identités locales avec Azure Active Directory
Azure AD Connect intègre vos répertoires locaux à Azure Active Directory. Cela vous permet de fournir une identité commune à vos utilisateurs pour les applications Office 365, Azure et SaaS intégrées à Azure AD. Cette rubrique vous guide dans les étapes de planification, de déploiement et opérationnelles. Elle comporte un ensemble de liens vers les rubriques associées à cette zone.

> [!IMPORTANT]
> [Azure AD Connect est la meilleure façon de connecter votre répertoire local avec Azure AD et Office 365. C’est l’occasion idéale d’effectuer la mise à niveau vers Azure AD Connect à partir de Microsoft Azure Active Directory Sync (DirSync) ou Azure AD Sync, car ces outils sont désormais déconseillés et ne seront plus pris en charge à partir du 13 avril 2017.](active-directory-aadconnect-dirsync-deprecated.md)
> 
> 

![Qu’est-ce qu’Azure AD Connect ?](./media/active-directory-aadconnect/arch.png)

## <a name="why-use-azure-ad-connect"></a>Pourquoi utiliser Azure AD Connect
L’intégration de vos annuaires locaux avec Azure AD améliore la productivité de vos utilisateurs en leur fournissant une identité commune pour accéder aux ressources cloud et locales. Les utilisateurs et les organisations bénéficient des avantages suivants :

* Les utilisateurs peuvent utiliser une identité unique pour accéder aux applications locales et aux services cloud comme Office 365.
* Outil unique offrant une expérience de déploiement simple pour la synchronisation et la connexion.
* Fournit les fonctionnalités les plus récentes pour vos scénarios. Azure AD Connect remplace les versions antérieures des outils d’intégration d’identité tels que DirSync et Azure AD Sync. Pour plus d’informations, consultez [Identité hybride : Comparaison des outils d’intégration d’annuaire](active-directory-hybrid-identity-design-considerations-tools-comparison.md).

### <a name="how-azure-ad-connect-works"></a>Fonctionnement d’Azure AD Connect
Azure Active Directory Connect est constitué de trois composants principaux : les services de synchronisation, le composant Active Directory Federation Services facultatif et le composant d’analyse nommé [Azure AD Connect Health](active-directory-aadconnect-health.md).

<center>![Pile Azure AD Connect](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png)
</center>

* Synchronisation : ce composant est chargé de créer des utilisateurs, des groupes et autres objets. Il permet également de s’assurer que les informations d’identité relatives aux utilisateurs et aux groupes dans votre environnement local correspondent à celles qui se trouvent dans le cloud.
* AD FS : ceci est une partie facultative d’Azure AD Connect, qui peut être utilisée pour configurer un environnement hybride à l’aide d’une infrastructure AD FS locale. Cette partie permet aux organisations de faire face à des déploiements complexes, par exemple l’authentification unique de jonction de domaine, l’application de la stratégie de connexion AD ou l’utilisation de cartes à puce ou d’une solution tierce d’authentification multifacteur.
* Analyse d’intégrité : Azure AD Connect Health peut assurer une surveillance robuste et offrir l’accès à un emplacement central dans le portail Azure pour afficher ces activités. Pour plus d’informations, voir [Azure Active Directory Connect Health](active-directory-aadconnect-health.md).

## <a name="install-azure-ad-connect"></a>Installer Azure AD Connect
Vous pouvez trouver le téléchargement d’Azure AD Connect sur le [Centre de téléchargement Microsoft](http://go.microsoft.com/fwlink/?LinkId=615771).

| Solution | Scénario |
| --- | --- |
| Avant de commencer : [Matériel et conditions préalables](active-directory-aadconnect-prerequisites.md) |<li>Étapes à suivre avant de commencer à installer Azure AD Connect.</li> |
| [Paramètres Express](connect/active-directory-aadconnect-get-started-express.md) |<li>Si vous ne disposez que d’une seule forêt Active Directory, cette option est recommandée.</li> <li>Connexion de l’utilisateur avec le même mot de passe à l’aide de la synchronisation du mot de passe.</li> |
| [Paramètres personnalisés](connect/active-directory-aadconnect-get-started-custom.md) |<li>Utilisés lorsque vous disposez de plusieurs forêts. Prise en charge de nombreuses [topologies](active-directory-aadconnect-topologies.md) locales.</li> <li>Personnalisez votre option de connexion, par exemple AD FS pour la fédération, ou utilisez un fournisseur d’identité tiers.</li> <li>Personnalisez les fonctionnalités de synchronisation, telles que le filtrage et l’écriture différée.</li> |
| [Mise à niveau à partir de DirSync](connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>Utilisé lorsque vous disposez d’un serveur DirSync existant déjà en cours d’exécution.</li> |
| [Mise à niveau à partir d’Azure AD Sync ou d’Azure AD Connect](active-directory-aadconnect-upgrade-previous-version.md) |<li>Il existe plusieurs méthodes différentes, en fonction de vos préférences.</li> |

[Après l’installation](active-directory-aadconnect-whats-next.md) , vous devez vérifier que tout fonctionne comme prévu et affecter des licences aux utilisateurs.

### <a name="next-steps-to-install-azure-ad-connect"></a>Étapes suivantes pour installer Azure AD Connect
| Rubrique |
| --- | --- |
| Téléchargez Azure AD Connect |
| Installation à l’aide de la configuration rapide |
| Installation à l’aide des paramètres personnalisés |
| Mise à niveau à partir de DirSync |
| Après l’installation |

### <a name="learn-more-about-install-azure-ad-connect"></a>En savoir plus sur l’installation d’Azure AD Connect
Il peut également être judicieux de se préparer aux préoccupations [opérationnelles](active-directory-aadconnectsync-operations.md) . Vous pouvez souhaiter disposer d’un serveur de secours afin de pouvoir facilement basculer en cas de [sinistre](active-directory-aadconnectsync-operations.md#disaster-recovery). Si vous envisagez d’effectuer des modifications de configuration fréquentes, vous devriez planifier un serveur en [mode de préproduction](active-directory-aadconnectsync-operations.md#staging-mode) .

| Rubrique |
| --- | --- |
| Topologies prises en charge |
| Principes de conception |
| Comptes utilisés pour l’installation |
| Planification opérationnelle |
| Options de connexion utilisateur |

## <a name="configure-sync-features"></a>Configuration de fonctionnalités de synchronisation
Azure AD Connect est doté de plusieurs fonctionnalités que vous pouvez activer ou qui sont activées par défaut. Certaines fonctionnalités peuvent parfois nécessiter une configuration supplémentaire dans des topologies et scénarios spécifiques.

[filtrage](active-directory-aadconnectsync-configure-filtering.md) est utilisé lorsque vous souhaitez limiter le nombre d’objets synchronisés sur Azure AD. Par défaut, tous les utilisateurs, contacts, groupes et ordinateurs Windows 10 sont synchronisés. Vous pouvez modifier le filtrage en fonction des domaines, des unités d’organisation ou des attributs.

[synchronisation de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md) synchronise le hachage de mot de passe dans Active Directory sur Azure AD. Cela permet à l’utilisateur final d’utiliser le même mot de passe en local et dans le cloud, mais uniquement de le gérer dans un seul emplacement. Dans la mesure où cela utilise votre Active Directory local en tant qu’autorité, vous pourrez également utiliser votre propre stratégie de mot de passe.

[écriture différée du mot de passe](active-directory-passwords-getting-started.md) permettra à vos utilisateurs de modifier et de réinitialiser leurs mots de passe dans le cloud et d’appliquer votre stratégie de mot de passe locale.

[L’écriture différée d’appareils](active-directory-aadconnect-feature-device-writeback.md) autorise un appareil inscrit dans Azure AD à bénéficier de l’écriture différée dans Active Directory en local afin de pouvoir être utilisé pour l’accès conditionnel.

La fonctionnalité de [prévention des suppressions accidentelles](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) est activée par défaut et protège votre répertoire du cloud d’un grand nombre de suppressions en même temps. Par défaut, elle permet 500 suppressions par exécution. Vous pouvez modifier ce paramètre en fonction de la taille de votre organisation.

La [mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) est activée par défaut pour une installation rapide des paramètres. Elle garantit qu’Azure AD Connect est toujours à jour avec la dernière version.

### <a name="next-steps-to-configure-sync-features"></a>Étapes suivantes pour configurer des fonctionnalités de synchronisation
| Rubrique |
| --- | --- |
| Configurer le filtrage |
| Synchronisation du mot de passe |
| Écriture différée du mot de passe |
| Écriture différée des appareils |
| prévention des suppressions accidentelles |
| Mise à jour automatique |

## <a name="customize-azure-ad-connect-sync"></a>Personnaliser Azure AD Connect Sync
Azure Connect AD Sync est doté d’une configuration par défaut qui est destinée à fonctionner pour la plupart des clients et des topologies. Toutefois, il existe toujours des situations dans lesquelles la configuration par défaut ne fonctionne pas et doit être ajustée. Il est possible d’apporter les modifications documentées dans cette section, ainsi que dans les rubriques connexes.

Si vous n’avez jamais travaillé avec une topologie de synchronisation auparavant, vous devriez commencer par assimiler les notions de base et les termes utilisés décrits dans les [concepts techniques](active-directory-aadconnectsync-technical-concepts.md). Azure AD Connect est l’évolution de MIIS2003, ILM2007 et FIM2010. Bien que certains éléments soient identiques, beaucoup de choses ont changé.

La [configuration par défaut](active-directory-aadconnectsync-understanding-default-configuration.md) suppose la présence possible de plusieurs forêts. Dans ces topologies, un objet utilisateur peut être représenté comme un contact dans une autre forêt. L’utilisateur peut également disposer d’une boîte aux lettres liée dans une autre forêt de ressources. Le comportement de la configuration par défaut est décrit dans [Utilisateurs et contacts](active-directory-aadconnectsync-understanding-users-and-contacts.md).

Le modèle de configuration dans la synchronisation est appelé [Approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md). Les flux des attributs avancés utilisent des [fonctions](active-directory-aadconnectsync-functions-reference.md) pour exprimer les transformations des attributs. Vous pouvez afficher et examiner l’intégralité de la configuration à l’aide des outils fournis avec Azure AD Connect. Si vous devez apporter des modifications à la configuration, assurez-vous de suivre les [meilleures pratiques](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) afin que les nouvelles versions soient plus faciles à adopter.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Étapes suivantes pour personnaliser Azure AD Connect Sync
| Rubrique |
| --- | --- |
| Tous les articles sur la synchronisation Azure AD Connect |
| concepts techniques |
| Présentation de la configuration par défaut |
| Présentation des utilisateurs et des contacts |
| Approvisionnement déclaratif |
| Modifier la configuration par défaut |

## <a name="configure-federation-features"></a>Configuration de fonctionnalités de fédération
ADFS peut être configuré de manière à prendre en charge [plusieurs domaines](active-directory-aadconnect-multiple-domains.md). Par exemple, vous pouvez avoir plusieurs domaines principaux que vous devez utiliser pour la fédération.

Si votre serveur ADFS n’a pas été configuré pour mettre à jour automatiquement les certificats Azure AD ou si vous utilisez une solution non ADFS, vous recevrez une notification lorsque vous devrez [mettre à jour des certificats](active-directory-aadconnect-o365-certs.md).

### <a name="next-steps-to-configure-federation-features"></a>Étapes suivantes pour configurer des fonctionnalités de fédération
| Rubrique |
| --- | --- |
| Tous les articles AD FS |
| Configuration d’ADFS avec des sous-domaines |
| Gérer la batterie AD FS |
| Mise à jour manuelle de certificats de fédération |

## <a name="more-information-and-references"></a>Plus d’informations et de références
| Rubrique |
| --- | --- |
| Historique des versions |
| Comparatif DirSync, Azure ADSync et Azure AD Connect |
| Liste de compatibilité non-ADFS pour Azure AD |
| Attributs synchronisés |
| Surveillance à l’aide d’Azure AD Connect Health |
| Forum Aux Questions (FAQ) |

**Ressources supplémentaires**

Présentation d’Ignite 2015 sur l'extension de vos répertoires locaux dans le cloud.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3862/player]
> 
> 




<!--HONumber=Nov16_HO2-->


