---
title: "Azure AD Connect : effectuer une mise à niveau depuis une version précédente | Microsoft Docs"
description: "Explique les différentes méthodes de mise à niveau vers la dernière version d’Azure Active Directory Connect, y compris la mise à niveau sur place et la migration « swing »."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 085706dacdcb0cd5a4169ccac4dc7fd8b8ddb6e0
ms.lasthandoff: 03/03/2017


---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect : effectuer une mise à niveau vers la dernière version
Cette rubrique décrit les différentes méthodes que vous pouvez utiliser pour mettre à niveau votre installation Azure Active Directory (Azure AD) Connect vers la dernière version. Nous vous recommandons d’utiliser systématiquement les dernières versions d’Azure AD Connect. Vous pouvez également suivre les étapes décrites dans la section [Migration « swing »](#swing-migration) lorsque vous appliquez une modification importante à la configuration.

Si vous souhaitez effectuer une mise à niveau à partir de DirSync, consultez plutôt [Effectuer une mise à niveau à partir de l’outil de synchronisation Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) .

Il existe plusieurs stratégies que vous pouvez utiliser pour mettre à niveau Azure AD Connect.

| Méthode | Description |
| --- | --- |
| [Mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) |Pour les clients avec une installation rapide, il s’agit de la méthode la plus simple. |
| [Mise à niveau sur place](#in-place-upgrade) |Si vous avez un seul serveur, vous pouvez mettre à niveau l’installation sur place sur le même serveur. |
| [Migration « swing »](#swing-migration) |Avec deux serveurs, vous pouvez en préparer un avec la nouvelle version ou configuration, et changer de serveur actif quand vous êtes prêt. |

Pour plus d’informations sur les autorisations, reportez-vous aux [autorisations requises pour une mise à niveau](active-directory-aadconnect-accounts-permissions.md#upgrade).

> [!NOTE]
> Une fois que vous avez activé votre nouveau serveur Azure AD Connect pour démarrer la synchronisation des modifications avec Azure AD, vous ne devez pas revenir à l’utilisation de DirSync ou Azure AD Sync. La rétrogradation à partir d’Azure AD Connect vers des clients hérités, notamment DirSync et Azure AD Sync, n’est pas prise en charge et peut entraîner des problèmes tels que la perte de données dans Azure AD.

## <a name="in-place-upgrade"></a>Mise à niveau sur place
Une mise à niveau sur place fonctionne à partir d’Azure AD Sync ou d’Azure AD Connect, Elle ne fonctionne pas pour la migration à partir de DirSync ou pour une solution avec Forefront Identity Manager (FIM) + connecteur Azure AD.

Nous vous recommandons cette méthode si vous avez un seul serveur et moins de 100 000 objets environ. Après la mise à niveau, une importation et une synchronisation complètes se produisent si des modifications ont été apportées aux règles de synchronisation out-of-box, pour que la nouvelle configuration s’applique à tous les objets existants dans le système. Cette opération peut prendre plusieurs heures selon le nombre d’objets figurant dans l’étendue du moteur de synchronisation. La synchronisation différentielle normale planifiée (par défaut toutes les 30 minutes) est suspendue, mais la synchronisation des mots de passe continue. Vous pouvez envisager une mise à niveau sur place pendant le week-end. S’il n’y a aucune modification de la configuration par défaut avec la nouvelle version d’Azure AD Connect, une importation/synchronisation différentielle normale démarre à la place.  
![Mise à niveau sur place](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Si vous avez apporté des modifications aux règles de synchronisation par défaut, celles-ci retrouvent leur configuration par défaut au moment de la mise à niveau. Pour conserver votre configuration d’une mise à niveau à l’autre, vérifiez que les modifications sont effectuées conformément aux [meilleures pratiques pour modifier la configuration par défaut](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## <a name="swing-migration"></a>Migration « swing »
Si vous avez un déploiement complexe ou un nombre d’objets élevé, il peut être difficile d’effectuer une mise à niveau sur place sur le système réel. Pour certains clients, ce processus peut prendre plusieurs jours pendant lesquels aucune modification différentielle n’est traitée. Vous pouvez également utiliser cette méthode lorsque vous envisagez d’apporter des modifications importantes à votre configuration et que vous souhaitez les tester avant de les envoyer dans le cloud.

La méthode recommandée pour ces scénarios consiste à utiliser une migration « swing ». Vous devez avoir (au moins) deux serveurs : un serveur actif et un serveur intermédiaire. Le serveur actif (représenté par des lignes bleues continues dans l’image ci-dessous) est responsable de la charge de production active. Le serveur intermédiaire (représenté par des lignes violettes en pointillé) est préparé avec la nouvelle version ou configuration. Lorsqu’il est prêt, ce serveur devient actif. Le serveur actif précédent, sur lequel est désormais installée l’ancienne version ou configuration, devient le serveur intermédiaire et est mis à niveau.

Les deux serveurs peuvent utiliser différentes versions. Par exemple, le serveur actif que vous projetez de désactiver peut utiliser Azure AD Sync et le nouveau serveur intermédiaire peut utiliser Azure AD Connect. Si vous utilisez la migration « swing » pour développer une nouvelle configuration, il est judicieux d’installer les mêmes versions sur les deux serveurs.  
![Serveur de test](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Certains clients préfèrent avoir trois ou quatre serveurs pour ce scénario. Pendant la mise à niveau du serveur intermédiaire, vous n’avez pas de serveur de sauvegarde pour la [récupération d’urgence](active-directory-aadconnectsync-operations.md#disaster-recovery). Avec trois ou quatre serveurs, vous pouvez préparer un ensemble de serveurs principaux/de secours disposant de la nouvelle version. Ainsi, un serveur intermédiaire est toujours prêt à prendre le relais.

Ces étapes fonctionnent également pour la migration à partir d’Azure AD Sync ou d’une solution avec FIM + connecteur Azure AD. En revanche, elles ne fonctionnent pas pour DirSync, mais on trouve cette même méthode de migration « swing » (également appelée déploiement parallèle) pour DirSync dans [Azure AD Connect : Mise à niveau à partir de DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Utiliser une migration « swing » pour effectuer la mise à niveau
1. Si vous utilisez Azure AD Connect sur les deux serveurs et que vous prévoyez uniquement de modifier la configuration, assurez-vous que votre serveur actif et votre serveur intermédiaire utilisent tous deux la même version. Il sera plus facile de comparer ensuite les différences. Si vous effectuez une mise à niveau à partir d’Azure AD Sync, ces serveurs auront des versions différentes. Si vous effectuez une mise à niveau à partir d’une ancienne version d’Azure AD Connect, il est judicieux, mais pas obligatoire, de commencer avec les deux serveurs qui utilisent la même version.
2. Si vous avez effectué une configuration personnalisée et que votre serveur intermédiaire ne l’a pas, suivez les étapes décrites dans [Déplacer une configuration personnalisée depuis le serveur actif vers le serveur intermédiaire](#move-custom-configuration-from-active-to-staging-server).
3. Si vous mettez à niveau depuis une version antérieure d’Azure AD Connect, mettez à niveau le serveur intermédiaire vers la dernière version. Si vous migrez à partir d’Azure AD Sync, installez Azure AD Connect sur votre serveur intermédiaire.
4. Laissez le moteur de synchronisation effectuer une importation et une synchronisation complètes sur votre serveur intermédiaire.
5. Vérifiez que la nouvelle configuration n’a pas entraîné de modifications inattendues à l’aide de la procédure indiquée sous « Vérifier » dans [Vérifiez la configuration d’un serveur](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Si quelque chose n’est pas comme prévu, corrigez le problème, exécutez l’importation et la synchronisation et vérifiez les données jusqu’à ce qu’elles vous conviennent en suivant les étapes.
6. Convertissez le serveur de test en serveur actif. C’est la dernière étape, « Changer de serveur actif », de la procédure [Vérifiez la configuration d’un serveur](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Si vous mettez à niveau Azure AD Connect, mettez à niveau le serveur actuellement en mode intermédiaire vers la dernière version. Suivez la même procédure pour mettre à niveau les données et la configuration. Si vous avez effectué une mise à niveau à partir d’Azure AD Sync, vous pouvez maintenant désactiver et retirer votre ancien serveur.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Déplacer une configuration personnalisée depuis le serveur actif vers le serveur intermédiaire
Si vous avez apporté des modifications à la configuration du serveur actif, vous devez vérifier que les mêmes modifications sont appliquées au serveur intermédiaire. Pour faciliter ce déplacement, vous pouvez utiliser la [documentation de configuration d’Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

Vous pouvez migrer les règles de synchronisation personnalisées que vous avez créées à l’aide de PowerShell. Vous devez appliquer d’autres modifications de la même manière sur les deux systèmes, et vous ne pouvez pas migrer les modifications. La [documentation de configuration](https://github.com/Microsoft/AADConnectConfigDocumenter) peut vous aider à comparer les deux systèmes pour vérifier qu’ils sont identiques. L’outil peut également vous aider à automatiser les étapes indiquées dans cette section.

Vous devez configurer ce qui suit de la même façon sur les deux serveurs :

* Connexion aux mêmes forêts
* Tout filtrage de domaine et d’unité organisationnelle
* Fonctionnalités facultatives identiques, telles que la synchronisation de mot de passe et l’écriture différée du mot de passe

**Migrer les règles de synchronisation personnalisées**  
Pour déplacer des règles de synchronisation personnalisées, procédez comme suit :

1. Ouvrez l’ **Éditeur de règles de synchronisation** sur votre serveur actif.
2. Sélectionnez une règle personnalisée. Cliquez sur **Exporter**. Une fenêtre Bloc-notes apparaît. Enregistrez le fichier temporaire avec une extension PS1. Le fichier devient un script PowerShell. Copiez le fichier PS1 sur le serveur intermédiaire.  
   ![Exportation des règles de synchronisation](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. Le GUID du connecteur est différent sur le serveur intermédiaire et doit être modifié. Pour obtenir le GUID, démarrez **l’Éditeur de règles de synchronisation**, sélectionnez une des règles par défaut représentant le même système connecté, puis cliquez sur **Exporter**. Remplacez le GUID dans votre fichier PS1 par le GUID se trouvant sur le serveur de test.
4. Dans une invite de PowerShell, exécutez le fichier PS1. Cette action crée la règle de synchronisation personnalisée sur le serveur intermédiaire.
5. Répétez cette procédure pour toutes vos règles personnalisées.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

