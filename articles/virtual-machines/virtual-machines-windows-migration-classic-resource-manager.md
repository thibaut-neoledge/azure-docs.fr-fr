---
title: Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager | Microsoft Docs
description: "Cet article décrit pas à pas la procédure de migration de ressources prise en charge par la plateforme de l’environnement Classic vers Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 525e40a424afdf6ec7486bff249b11439a8cf740


---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager
Cet article décrit la manière dont nous activons les ressources IaaS (infrastructure as a service) de l’environnement Classic pour les modèles de déploiement de Resource Manager. Pour en savoir plus, voir [Fonctionnalités et avantages d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Nous décrivons en détail comment connecter des ressources dans les deux modèles de déploiement qui coexistent dans votre abonnement en utilisant des passerelles intersites de réseau virtuel.

## <a name="goal-for-migration"></a>Objectif de la migration
Resource Manager autorise le déploiement d’applications complexes à l’aide de modèles, configure les machines virtuelles au moyen d’extensions de machines virtuelles et intègre la gestion des accès et le balisage. Azure Resource Manager inclut un déploiement extensible, en parallèle, de machines virtuelles dans des groupes à haute disponibilité. Le nouveau modèle de déploiement assure également la gestion de façon indépendante du cycle de vie des services de calcul, de réseau et de stockage. Enfin, il applique la sécurité par défaut grâce à la mise en œuvre de machines virtuelles dans un réseau virtuel.

Pratiquement toutes les fonctionnalités du modèle de déploiement Classic sont prises en charge pour le calcul, le réseau et le stockage dans Azure Resource Manager. Pour bénéficier des nouvelles fonctionnalités d’Azure Resource Manager, vous pouvez migrer des déploiements existants à partir du modèle de déploiement classique.

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Modifications de vos services d’automatisation et de vos outils après la migration
Dans le cadre de la migration de vos ressources à partir du modèle de déploiement classique vers le modèle de déploiement Resource Manager, vous devez mettre à jour votre automatisation ou vos outils pour vous assurer qu’ils continueront de fonctionner après la migration.

## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>Signification de la migration de ressources IaaS de l’environnement Classic vers Resource Manager
Avant d’entrer dans les détails, examinons le différence entre les opérations du plan de données et celles du plan de gestion pour les ressources IaaS.

* *Plan de gestion* décrit l’ensemble des appels destinés au plan de gestion ou à l’API en vue de modifier les ressources. Par exemple, les opérations telles que la création d’une machine virtuelle, le redémarrage d’une machine virtuelle et la mise à jour d’un réseau virtuel avec un nouveau sous-réseau permettent de gérer les ressources en cours d’exécution. Elles n’affectent pas directement la connexion à des instances.
* *Plan de données* (application) décrit le runtime de l’application proprement dite et implique une interaction avec les instances qui ne passent pas par l’API Azure. Par exemple, l’accès à votre site web ou l’extraction de données à partir d’une instance de serveur SQL ou d’un serveur MongoDB en cours d’exécution sont considérés comme des interactions d’application ou de plan de données. La copie d’un objet blob à partir d’un compte de stockage et l’accès à une adresse IP publique sur RDP ou SSH dans la machine virtuelle constituent également des opérations du plan de données. Ces opérations garantissent le fonctionnement continu de l’application dans l’ensemble des services de calcul, de mise en réseau et de stockage.

> [!NOTE]
> Dans certains scénarios de migration, la plateforme Azure arrête, désalloue, puis redémarre vos machines virtuelles. Cela entraîne un bref temps d’arrêt du forfait de données.
>
>

## <a name="supported-scopes-of-migration"></a>Étendues de migration prises en charge
Il existe trois étendues de migration qui visent principalement les services de calcul, de réseau et de stockage.

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migration de machines virtuelles (ne figurant pas dans un réseau virtuel)
Dans le modèle de déploiement Resource Manager, nous appliquons par défaut la sécurité de vos applications. Toutes les machines virtuelles doivent donc figurer dans un réseau virtuel du modèle Resource Manager. La plateforme Azure redémarre (`Stop`, `Deallocate`, et `Start`) les machines virtuelles dans le cadre de la migration. Vous disposez de deux options pour les réseaux virtuels :

* Vous pouvez demander à la plateforme de créer un réseau virtuel et de procéder à la migration de la machine virtuelle vers ce réseau.
* Vous pouvez effectuer la migration de la machine virtuelle vers un réseau virtuel existant dans Resource Manager.

> [!NOTE]
> Dans cette étendue de migration, les opérations plan de gestion et du plan de données peuvent ne pas être autorisées pendant un certain laps de temps durant la migration.
>
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migration de machines virtuelles (dans un réseau virtuel)
Pour la plupart des configurations de machines virtuelles, seules les métadonnées sont migrées entre le modèle de déploiement classique et le modèle de déploiement Resource Manager. Les machines virtuelles sous-jacentes s’exécutent sur le même matériel, dans le même réseau et avec le même stockage. Il se peut que les opérations du plan de gestion ne soient pas autorisées pendant un certain laps de temps durant la migration. Toutefois, le plan de données continue à fonctionner. Cela signifie que vos applications s’exécutant par dessus les machines virtuelles (Classic) ne subissent aucun temps d’arrêt lors de la migration.

Les configurations non prises en charge actuellement sont les suivantes. En cas de prise en charge à l’avenir, certaines machines virtuelles de cette configuration connaîtront peut-être un temps d’arrêt (avec exécution des opérations d’arrêt, de désallocation et de redémarrage des machines virtuelles).

* Vous disposez de plus d’un groupe à haute disponibilité dans un seul service cloud.
* Vous disposez d’un ou de plusieurs groupes à haute disponibilité et de machines virtuelles ne figurant pas dans un groupe à haute disponibilité dans un seul service cloud.

> [!NOTE]
> Dans cette étendue de migration, le plan de gestion peut ne pas être utilisable pendant un certain laps de temps lors de la migration. Certaines configurations décrites ci-dessus entraînent un temps d’arrêt du forfait de données.
>
>

### <a name="storage-accounts-migration"></a>Migration des comptes de stockage
Pour permettre une migration fluide, vous pouvez déployer des machines virtuelles Resource Manager dans un compte de stockage classique. Cette fonctionnalité permet d’effectuer la migration des ressources de calcul et de réseau indépendamment des comptes de stockage. Après avoir migré vos machines virtuelles et votre réseau virtuel, vous devrez migrer vos comptes de stockage pour achever le processus de migration.

> [!NOTE]
> Le modèle de déploiement Resource Manager n’intègre pas le concept d’images et de disques classiques. Une fois le compte de stockage migré, les images et disques classiques ne sont pas visibles dans la pile Resource Manager, mais les disques durs virtuels de secours restent dans le compte de stockage.
>
>

## <a name="unsupported-features-and-configurations"></a>Fonctionnalités et configurations non prises en charge
À ce stade, nous ne gérons pas encore certaines fonctionnalités et configurations. Les sections suivantes décrivent nos recommandations les concernant.

### <a name="unsupported-features"></a>Fonctionnalités non prises en charge
Les fonctionnalités non prises en charge actuellement sont les suivantes. Si vous le souhaitez, vous pouvez supprimer ces paramètres, effectuer la migration des machines virtuelles, puis réactiver les paramètres dans le modèle de déploiement Resource Manager.

| Fournisseur de ressources | Fonctionnalité |
| --- | --- |
| Calcul |Disques de machine virtuelle non associés. |
| Calcul |Images de machine virtuelle. |
| Réseau |Listes de contrôle d’accès de points de terminaison. |
| Réseau |Passerelles de réseau virtuel (passerelles Azure ExpressRoute, passerelle d’application). |
| Réseau |Réseaux virtuels à l’aide de l’homologation de réseaux virtuels (VNet Peering). (Migrer de réseau virtuel vers ARM, puis l’homologue) En savoir plus sur [l’homologation de réseaux virtuels (VNet Peering)](../virtual-network/virtual-network-peering-overview.md). |
| Réseau |Profils Traffic Manager. |

### <a name="unsupported-configurations"></a>Configurations non prises en charge
Les configurations non prises en charge actuellement sont les suivantes.

| de diffusion en continu | Configuration | Recommandation |
| --- | --- | --- |
| Gestionnaire de ressources |Contrôle d’accès en fonction du rôle (RBAC) pour les ressources Classic |L’URI des ressources étant modifié après la migration, il est recommandé de planifier les mises à jour de stratégie RBAC à exécuter après la migration. |
| Calcul |Plusieurs sous-réseaux associés à une machine virtuelle |Mettez à jour la configuration du sous-réseau afin de référencer uniquement des sous-réseaux. |
| Calcul |Machines virtuelles appartenant à un réseau virtuel, mais auxquelles aucun sous-réseau n’est affecté de manière explicite |Si vous le souhaitez, vous pouvez supprimer la machine virtuelle. |
| Calcul |Machines virtuelles dotées d’alertes et de stratégies de mise à l’échelle automatique |La migration a lieu et ces paramètres sont ignorés. Il est vivement recommandé d’évaluer votre environnement avant de procéder à la migration. Vous pouvez également choisir de reconfigurer les paramètres d’alerte une fois la migration terminée. |
| Calcul |Extensions XML de machines virtuelles (BGInfo 1.*, débogueur Visual Studio, Web Deploy et débogage à distance) |Ce n’est pas pris en charge. Il est recommandé de supprimer ces extensions de la machine virtuelle pour continuer la migration ; sinon, elles seront automatiquement supprimées pendant le processus de migration. |
| Calcul |Diagnostics de démarrage avec le stockage Premium |Désactivez la fonctionnalité Diagnostics de démarrage pour les machines virtuelles avant de poursuivre la migration. Vous pouvez réactiver les Diagnostics de démarrage dans la pile Resource Manager une fois la migration terminée. En outre, les objets Blob utilisés pour la capture d’écran et les journaux de série doivent être supprimés afin que vous ne soyez plus facturé pour ces objets Blob. |
| Calcul |Services cloud contenant des rôles Web/de travail |Non pris en charge actuellement. |
| Réseau |Réseaux virtuels contenant des machines virtuelles et des rôles Web/de travail |Non pris en charge actuellement. |
| Azure App Service |Réseaux virtuels contenant des environnements App Service |Non pris en charge actuellement. |
| Azure HDInsight |Réseaux virtuels contenant des services HDInsight |Non pris en charge actuellement. |
| Services de cycle de vie Microsoft Dynamics |Réseaux virtuel contenant des machines virtuelles gérées par Dynamics Lifecycle Services |Non pris en charge actuellement. |
| Services de domaine Azure AD |Réseaux virtuels contenant des services de domaine Azure AD |Non pris en charge actuellement. |
| Calcul |Extensions Azure Security Center avec un réseau virtuel disposant d’une passerelle VPN dans une connectivité en transit ou d’une passerelle ExpressRoute avec serveur DNS local |Azure Security Center installe automatiquement les extensions sur vos machines virtuelles pour contrôler leur sécurité et déclencher des alertes. Ces extensions sont généralement installées automatiquement si la stratégie d’Azure Security Center est activée sur l’abonnement. La migration de la passerelle ExpressRoute n’est pas prise en charge actuellement et les passerelles VPN avec connectivité de transit perdent l’accès local. Si vous supprimez la passerelle ExpressRoute ou migrez la passerelle VPN avec connectivité de transit, l’accès à internet au compte de stockage de la machine virtuelle est perdu lors de la validation de la migration. La migration ne se produit pas dans ce cas, car l’objet blob d’état de l’agent invité ne peut pas être rempli. Il est recommandé de désactiver la stratégie d’Azure Security Center de l’abonnement 3 heures avant de procéder à la migration. |

## <a name="the-migration-experience"></a>Expérience de migration
Avant de lancer la migration, nous vous recommandons de procéder comme suit :

* Assurez-vous que les ressources que vous souhaitez migrer n’utilisent pas de fonctionnalités ou de configurations non prises en charge. Généralement, la plateforme détecte ces problèmes et génère une erreur.
* Si certaines de vos machines virtuelles n’appartiennent pas à un réseau virtuel, elles seront arrêtées et libérées dans le cadre de l’opération de préparation. Si vous ne voulez pas perdre l’adresse IP publique, envisagez de la réserver avant de déclencher l’opération de préparation. En revanche, si les machines virtuelles se trouvent dans un réseau virtuel, elles ne sont ni arrêtées, ni désallouées.
* Planifiez votre migration en dehors des heures d’ouverture afin de tenir compte des erreurs inattendues susceptibles de survenir au cours du processus.
* Téléchargez la configuration actuelle de vos machines virtuelles à l’aide de PowerShell, de commandes de l’interface de ligne de commande ou d’API REST, afin d’en faciliter la vérification après l’étape de préparation.
* Mettez à jour vos scripts d’automatisation/d’opérationnalisation pour gérer le modèle de déploiement Resource Manager avant de commencer la migration. Vous pouvez également effectuer des opérations GET lorsque les ressources se trouvent à l’état Préparé.
* Évaluez les stratégies RBAC configurées dans les ressources IaaS Classic, et planifiez leur mise à jour une fois la migration terminée.

Procédez comme suit pour la migration

![Capture d’écran illustrant le workflow de migration](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Toutes les opérations décrites dans les sections suivantes sont idempotentes. Si vous rencontrez un problème autre qu’une fonctionnalité non prise en charge ou qu’une erreur de configuration, nous vous recommandons de réexécuter la procédure de préparation, d’abandon ou de validation. La plateforme Azure réessaie d’exécuter l’action.
>
>

### <a name="validate"></a>Valider
L’opération de validation constitue la première étape du processus de migration. L’objectif de cette étape est d’analyser les données en arrière-plan pour les ressources dont vous souhaitez effectuer la migration et d’indiquer si ces ressources peuvent ou non faire l’objet d’une migration.

Vous sélectionnez le réseau virtuel ou le service hébergé (s’il ne s’agit pas d’un réseau virtuel) que vous souhaitez valider pour la migration.

* Si la migration d’une ressource est impossible, la plateforme Azure en indique les raisons.

### <a name="prepare"></a>Préparation
L’opération de préparation constitue la deuxième étape du processus de migration. Le rôle de cette étape est de simuler la transformation des ressources IaaS Classic en ressources Resource Manager et de vous les présenter côte à côte pour que vous puissiez les visualiser.

Vous sélectionnez le réseau virtuel ou le service hébergé (s’il ne s’agit pas d’un réseau virtuel) que vous souhaitez préparer pour la migration.

* Si la ressource ne peut pas être migrée, la plateforme Azure arrête le processus de migration et indique la raison de l’échec de l’opération de préparation.
* Si la migration de la ressource est possible, la plateforme Azure commence par verrouiller les opérations du plan de gestion pour les ressources concernées. Par exemple, vous ne pourrez pas ajouter de disque de données à une machine virtuelle en cours de migration.

La plateforme Azure démarre ensuite la migration des métadonnées du déploiement Classic vers Resource Manager pour les ressources faisant l’objet de la migration.

Une fois l’opération de préparation terminée, vous avez la possibilité de visualiser les ressources tant dans le déploiement Classic quand dans le déploiement Resource Manager. Pour chaque service cloud du modèle de déploiement classique, nous créons un nom de groupe de ressources au format `cloud-service-name>-migrated`.

> [!NOTE]
> Lors de cette phase de migration, les machines virtuelles qui ne font pas partie d’un réseau virtuel Classic sont arrêtées et désallouées.
>
>

### <a name="check-manual-or-scripted"></a>Vérification (manuelle ou sur la base d’un script)
Dans le cadre de cette étape de vérification, vous pouvez éventuellement utiliser la configuration que vous avez téléchargée précédemment pour vérifier que la migration semble correcte. Une autre possibilité consiste à se connecter au portail et à procéder à une vérification ponctuelle des propriétés et des ressources pour vous assurer que la migration des métadonnées s’est déroulée de manière adéquate.

Si vous effectuez la migration d’un réseau virtuel, la majeure partie de la configuration des machines virtuelles n’est pas redémarrée. En ce qui concerne les applications installées sur ces machines virtuelles, vous pouvez vérifier que ces applications sont toujours opérationnelles.

Vous pouvez tester vos scripts de surveillance/automatisation et vos scripts d’opérations pour vous assurer du bon fonctionnement des machines virtuelles et de vos scripts mis à jour. Seules les opérations GET sont prises en charge lorsque les ressources sont en état Préparé.

Aucun délai maximum ne vous est imposé pour la validation de la migration. Vous pouvez conserver les ressources en l’état pour la durée de votre choix. Toutefois, le plan de gestion est verrouillé pour ces ressources jusqu’à ce que vous choisissiez d’abandonner ou de valider la migration.

Si vous constatez certains problèmes, vous pouvez toujours abandonner la migration et revenir au modèle de déploiement Classic. Après que vous êtes revenu en arrière, la plateforme Azure ouvre les opérations du plan de gestion sur les ressources pour vous permettre de reprendre les opérations normales sur ces machines virtuelles dans le modèle de déploiement classique.

### <a name="abort"></a>Abandon
L’abandon est une étape facultative qui vous permet d’abandonner la migration en annulant vos modifications et en revenant au modèle de déploiement Classic.

> [!NOTE]
> Vous ne pouvez plus effectuer cette opération une fois que vous avez déclenché l’opération de validation.     
>
>

### <a name="commit"></a>Validation
Après avoir terminé la validation, vous pouvez valider la migration. Les ressources n’apparaissent plus dans l’environnement Classic et sont disponibles uniquement dans le modèle de déploiement Resource Manager. Les ressources migrées peuvent être gérées uniquement dans le nouveau portail.

> [!NOTE]
> Il s’agit d’une opération idempotente. En cas d’échec, il est recommandé de retenter l’opération. Si le problème persiste, créez un ticket de support ou publiez un billet avec la balise ClassicIaaSMigration sur notre [forum consacré aux machines virtuelles](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).
>
>

## <a name="frequently-asked-questions"></a>Forum Aux Questions
**Ce plan de migration affecte-t-il l’un de mes services ou applications existants qui s’exécutent sur des machines virtuelles Azure ?**

Non. Les machines virtuelles (Classic) sont des services entièrement pris en charge des services à disponibilité générale. Vous pouvez continuer à utiliser ces ressources pour développer votre empreinte sur Microsoft Azure.

**Que se passera-t-il pour mes machines virtuelles si je n’envisage pas de procéder à cette migration dans un avenir proche ?**

Nous ne déconseillons pas l’utilisation des API et du modèle de ressource Classic existants. Compte tenu des fonctionnalités avancées offertes par le modèle de déploiement Resource Manager, notre objectif est de faciliter la migration. Nous vous recommandons donc vivement de passer en revue [certaines des avancées réalisées](../resource-manager-deployment-model.md) en matière d’IaaS dans Resource Manager.

**Quelles sont les implications de ce plan de migration pour mes outils existants ?**

La mise à jour de vos outils vers le modèle de déploiement Resource Manager constitue l’un des plus importants changements à prendre en compte dans vos plans de migration.

**Quelle sera la durée de l’arrêt du plan de gestion ?**

Cette durée dépend du nombre de ressources dont vous effectuez la migration. Pour les déploiements de plus petite taille (quelques dizaines de machines virtuelles), le processus de migration complet devrait prendre moins d’une heure. Pour les déploiements à grande échelle (plusieurs centaines de machines virtuelles), la migration peut prendre quelques heures.

**Puis-je procéder à une restauration après avoir validé la migration de mes ressources dans Resource Manager ?**

Vous pouvez abandonner la migration tant que vos ressources se trouvent à l’état Préparé. La restauration n’est plus prise en charge une fois que les ressources ont fait l’objet d’une migration par le biais de l’opération de validation.

**Puis-je restaurer ma migration en cas d’échec de l’opération de validation ?**

Vous ne pouvez pas abandonner la migration si l’opération de validation échoue. Toutes les opérations de migration, notamment l’opération de validation, sont idempotentes. Nous vous recommandons donc de retenter l’opération après une courte période. Si l’erreur persiste, créez un ticket de support ou publiez un billet avec la balise ClassicIaaSMigration sur notre [forum consacré aux machines virtuelles](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

**Dois-je acheter un autre circuit ExpressRoute si je dois utiliser la ressource IaaS sous Resource Manager ?**

Non. Nous avons récemment activé la [migration de circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](../expressroute/expressroute-move.md). Vous n’avez pas besoin d’acheter de nouveau circuit ExpressRoute si vous en possédez déjà un.

**Que se passera-t-il si j’ai configuré des stratégies de contrôle d’accès en fonction du rôle (RBAC) pour mes ressources IaaS Classic ?**

Pendant la migration, les ressources Classic sont converties en ressources Resource Manager. Nous vous recommandons donc de planifier les mises à jour de stratégies RBAC qui seront nécessaires après la migration.

**Que se passera-t-il si j’utilise actuellement des services Azure Site Recovery ou Azure Backup ?**

Pour migrer vos machines virtuelles avec sauvegarde activée, consultez [J’ai sauvegardé mes machines virtuelles classiques dans le coffre de sauvegarde. Maintenant, je souhaite les migrer du mode Classic vers le mode Resource Manager. Comment faire pour les sauvegarder dans le coffre Recovery Services ?](../backup/backup-azure-backup-ibiza-faq.md)J’ai sauvegardé mes machines virtuelles classiques dans le coffre de sauvegarde. Maintenant, je souhaite les migrer du mode Classic vers le mode Resource Manager.  Comment faire pour les sauvegarder dans le coffre Recovery Services ?

**Puis-je vérifier si mon abonnement ou mes ressources peuvent faire l’objet d’une migration ?**

Oui. Dans l’option de migration prise en charge par la plateforme, la première étape de préparation de la migration consiste à s’assurer que les ressources peuvent faire l’objet d’une migration. En cas d’échec de l’opération de validation, vous recevez des messages avec toutes les raisons pour lesquelles la migration ne peut pas aboutir.

**Que se passe-t-il si je rencontre une erreur de quota lors de la préparation des ressources IaaS pour la migration ?**

Nous vous recommandons d’abandonner votre migration et de créer ensuite une demande de support en vue d’augmenter les quotas pour la région dans laquelle vous effectuez la migration des machines virtuelles. Une fois votre demande de quota approuvée, vous pouvez réexécuter la procédure de migration.

**Comment signaler un problème ?**

Publiez vos problèmes et questions concernant la migration sur notre [forum consacré aux machines virtuelles](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows)en utilisant le mot clé ClassicIaaSMigration. Nous vous recommandons de poster toutes vos questions sur ce forum. Si vous disposez d’un contrat de support, vous pouvez également créer un ticket de support.

**Comment procéder si je n’apprécie pas les noms que la plateforme a choisis pour mes ressources lors de la migration ?**

Les noms de toutes les ressources que vous avez explicitement fournis dans le modèle de déploiement sont conservés pendant la migration. Dans certains cas, de nouvelles ressources seront créées. Par exemple, une interface réseau est créée pour chaque machine virtuelle. Actuellement, nous ne prenons pas en charge la possibilité de contrôler les noms de ces ressources créées pendant la migration. Vous pouvez voter en facteur de cette fonctionnalité via le [forum des commentaires sur Azure](http://feedback.azure.com).

**J’ai reçu un message *« La machine virtuelle signale que l’agent est dans l’état général Pas prêt. Par conséquent, la machine virtuelle ne peut pas être migrée. Assurez-vous que l’état général de l’agent signalé par l’agent de machine virtuelle est « Prêt »* ou *« La machine virtuelle contient une extension dont l’état n’est pas signalé par la machine virtuelle. Par conséquent, cette machine virtuelle ne peut pas être migrée. »***

Ce message est affiché lorsque la machine virtuelle n’a pas de connectivité sortante à Internet. L’agent de machine virtuelle utilise la connectivité sortante pour atteindre le compte de stockage Azure afin de mettre à jour l’état de l’agent toutes les 5 minutes.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous savez en quoi consiste la migration des ressources IaaS Classic vers Resource Manager, vous pouvez commencer à effectuer la migration des ressources.

* [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
* [Cloner une machine virtuelle Classic vers Azure Resource Manager à l’aide de scripts PowerShell](virtual-machines-windows-migration-scripts.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Passer en revue les erreurs courantes de migration](virtual-machines-migration-errors.md)



<!--HONumber=Nov16_HO3-->


