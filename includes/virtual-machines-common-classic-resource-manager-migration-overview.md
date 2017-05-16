# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager
Cet article décrit la manière dont nous activons les ressources IaaS (infrastructure as a service) de l’environnement Classic pour les modèles de déploiement de Resource Manager. Pour en savoir plus, voir [Fonctionnalités et avantages d’Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md). Nous décrivons en détail comment connecter des ressources dans les deux modèles de déploiement qui coexistent dans votre abonnement en utilisant des passerelles intersites de réseau virtuel.

## <a name="goal-for-migration"></a>Objectif de la migration
Resource Manager autorise le déploiement d’applications complexes à l’aide de modèles, configure les machines virtuelles au moyen d’extensions de machines virtuelles et intègre la gestion des accès et le balisage. Azure Resource Manager inclut un déploiement extensible, en parallèle, de machines virtuelles dans des groupes à haute disponibilité. Le nouveau modèle de déploiement assure également la gestion de façon indépendante du cycle de vie des services de calcul, de réseau et de stockage. Enfin, il applique la sécurité par défaut grâce à la mise en œuvre de machines virtuelles dans un réseau virtuel.

Pratiquement toutes les fonctionnalités du modèle de déploiement Classic sont prises en charge pour le calcul, le réseau et le stockage dans Azure Resource Manager. Pour bénéficier des nouvelles fonctionnalités d’Azure Resource Manager, vous pouvez migrer des déploiements existants à partir du modèle de déploiement classique.

## <a name="supported-resources-for-migration"></a>Ressources prises en charge pour la migration
Ces ressources IaaS classiques sont prises en charge lors de la migration

* Machines virtuelles
* Groupes à haute disponibilité
* Services cloud
* Comptes de stockage
* Virtual Network
* Passerelles VPN
* Passerelles ExpressRoute _(dans le même abonnement que Réseau virtuel uniquement)_
* Groupes de sécurité réseau 
* Tables de routage 
* IP réservées 

## <a name="supported-scopes-of-migration"></a>Étendues de migration prises en charge
Il existe 4 façons différentes de migrer les ressources de calcul, de réseau et de stockage. Les voici : 

* Migration de machines virtuelles (ne figurant PAS dans un réseau virtuel)
* Migration de machines virtuelles (dans un réseau virtuel)
* Migration des comptes de stockage
* Ressources non attachées (groupes de sécurité réseau, tables de routage et adresses IP réservées)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migration de machines virtuelles (ne figurant PAS dans un réseau virtuel)
Dans le modèle de déploiement Resource Manager, nous appliquons par défaut la sécurité de vos applications. Toutes les machines virtuelles doivent donc figurer dans un réseau virtuel du modèle Resource Manager. La plateforme Azure redémarre (`Stop`, `Deallocate`, et `Start`) les machines virtuelles dans le cadre de la migration. Vous avez deux options pour les réseaux virtuels vers lesquels les machines virtuelles seront migrées :

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

### <a name="unattached-resources-network-security-groups-route-tables--reserved-ips"></a>Ressources non attachées (groupes de sécurité réseau, tables de routage et adresses IP réservées)
Les groupes de sécurité réseau, tables de routage et adresses IP réservées qui ne sont pas rattachés à des machines virtuelles et des réseaux virtuels peuvent être migrés indépendamment.

<br>

## <a name="unsupported-features-and-configurations"></a>Fonctionnalités et configurations non prises en charge
À ce stade, nous ne gérons pas encore certaines fonctionnalités et configurations. Les sections suivantes décrivent nos recommandations les concernant.

### <a name="unsupported-features"></a>Fonctionnalités non prises en charge
Les fonctionnalités non prises en charge actuellement sont les suivantes. Si vous le souhaitez, vous pouvez supprimer ces paramètres, effectuer la migration des machines virtuelles, puis réactiver les paramètres dans le modèle de déploiement Resource Manager.

| Fournisseur de ressources | Fonctionnalité | Recommandation |
| --- | --- | --- |
| Calcul |Disques de machine virtuelle non associés. | Les objets BLOB VHD derrière ces disques seront migrés lors de la migration du compte de stockage |
| Calcul |Images de machine virtuelle. | Les objets BLOB VHD derrière ces disques seront migrés lors de la migration du compte de stockage |
| Réseau |Listes de contrôle d’accès de points de terminaison. | Supprimez les listes de contrôle d’accès des points de terminaison et réessayez la migration. |
| Réseau |Réseau virtuel doté d’une passerelle ExpressRoute et d’une passerelle VPN  | Supprimez la passerelle VPN avant de commencer la migration, puis recréez la passerelle VPN une fois la migration terminée. En savoir plus sur la [migration ExpressRoute](../articles/expressroute/expressroute-migration-classic-resource-manager.md).|
| Réseau |ExpressRoute avec des liens d’autorisation  | Supprimez le circuit ExpressRoute vers la connexion de réseau virtuel avant de commencer la migration, puis recréez la connexion une fois la migration terminée. En savoir plus sur la [migration ExpressRoute](../articles/expressroute/expressroute-migration-classic-resource-manager.md). |
| Réseau |Application Gateway | Supprimez l’Application Gateway avant de commencer la migration, puis recréez l’Application Gateway une fois la migration terminée. |
| Réseau |Réseaux virtuels à l’aide de l’homologation de réseaux virtuels (VNet Peering). | Migrez le réseau virtuel vers Resource Manager, puis homologuez-le. Apprenez-en plus sur l’[Homologation de réseaux virtuels](../articles/virtual-network/virtual-network-peering-overview.md). | 

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
| Azure RemoteApp |Réseaux virtuels contenant des déploiements Azure RemoteApp |Non pris en charge actuellement. |
| Gestion des API Azure |Réseaux virtuels contenant des déploiements Gestion des API Azure |Non pris en charge actuellement. Pour migrer le réseau virtuel IaaS, modifiez le réseau virtuel du déploiement Gestion des API. Il s’agit d’une opération sans temps d’arrêt. |
| Calcul |Extensions Azure Security Center avec un réseau virtuel disposant d’une passerelle VPN dans une connectivité en transit ou d’une passerelle ExpressRoute avec serveur DNS local |Azure Security Center installe automatiquement les extensions sur vos machines virtuelles pour contrôler leur sécurité et déclencher des alertes. Ces extensions sont généralement installées automatiquement si la stratégie d’Azure Security Center est activée sur l’abonnement. La migration de la passerelle ExpressRoute n’est pas prise en charge actuellement et les passerelles VPN avec connectivité de transit perdent l’accès local. Si vous supprimez la passerelle ExpressRoute ou migrez la passerelle VPN avec connectivité de transit, l’accès à internet au compte de stockage de la machine virtuelle est perdu lors de la validation de la migration. La migration ne se produit pas dans ce cas, car l’objet blob d’état de l’agent invité ne peut pas être rempli. Il est recommandé de désactiver la stratégie d’Azure Security Center de l’abonnement 3 heures avant de procéder à la migration. |

