## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>Signification de la migration de ressources IaaS de l’environnement Classic vers Resource Manager
Avant d’entrer dans les détails, examinons le différence entre les opérations du plan de données et celles du plan de gestion pour les ressources IaaS.

* *Plan de gestion/contrôle* décrit l’ensemble des appels destinés au plan de gestion/contrôle ou à l’API en vue de modifier les ressources. Par exemple, les opérations telles que la création d’une machine virtuelle, le redémarrage d’une machine virtuelle et la mise à jour d’un réseau virtuel avec un nouveau sous-réseau permettent de gérer les ressources en cours d’exécution. Elles n’affectent pas directement la connexion à des instances.
* *Plan de données* (application) décrit le runtime de l’application proprement dite et implique une interaction avec les instances qui ne passent pas par l’API Azure. Par exemple, l’accès à votre site web ou l’extraction de données à partir d’une instance de serveur SQL ou d’un serveur MongoDB en cours d’exécution sont considérés comme des interactions d’application ou de plan de données. La copie d’un objet blob à partir d’un compte de stockage et l’accès à une adresse IP publique sur RDP ou SSH dans la machine virtuelle constituent également des opérations du plan de données. Ces opérations garantissent le fonctionnement continu de l’application dans l’ensemble des services de calcul, de mise en réseau et de stockage.

![Capture d’écran qui illustre la différence entre un plan de gestion/contrôle et le plan de données](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)

> [!NOTE]
> Dans certains scénarios de migration, la plateforme Azure arrête, désalloue, puis redémarre vos machines virtuelles. Cela entraîne un bref temps d’arrêt du forfait de données.
>
>

## <a name="the-migration-experience"></a>Expérience de migration
Avant de lancer la migration, nous vous recommandons de procéder comme suit :

* Assurez-vous que les ressources que vous souhaitez migrer n’utilisent pas de fonctionnalités ou de configurations non prises en charge. Généralement, la plateforme détecte ces problèmes et génère une erreur.
* Si certaines de vos machines virtuelles n’appartiennent pas à un réseau virtuel, elles seront arrêtées et libérées dans le cadre de l’opération de préparation. Si vous ne voulez pas perdre l’adresse IP publique, envisagez de la réserver avant de déclencher l’opération de préparation. En revanche, si les machines virtuelles se trouvent dans un réseau virtuel, elles ne sont ni arrêtées, ni désallouées.
* Planifiez votre migration en dehors des heures d’ouverture afin de tenir compte des erreurs inattendues susceptibles de survenir au cours du processus.
* Téléchargez la configuration actuelle de vos machines virtuelles à l’aide de PowerShell, de commandes de l’interface de ligne de commande ou d’API REST, afin d’en faciliter la vérification après l’étape de préparation.
* Mettez à jour vos scripts d’automatisation/d’opérationnalisation pour gérer le modèle de déploiement Resource Manager avant de commencer la migration. Vous pouvez également effectuer des opérations GET lorsque les ressources se trouvent à l’état Préparé.
* Évaluez les stratégies RBAC configurées dans les ressources IaaS Classic, et planifiez leur mise à jour une fois la migration terminée.

Procédez comme suit pour la migration

![Capture d’écran illustrant le workflow de migration](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Toutes les opérations décrites dans les sections suivantes sont idempotentes. Si vous rencontrez un problème autre qu’une fonctionnalité non prise en charge ou qu’une erreur de configuration, nous vous recommandons de réexécuter la procédure de préparation, d’abandon ou de validation. La plateforme Azure réessaie d’exécuter l’action.
>
>

### <a name="validate"></a>Valider
L’opération de validation constitue la première étape du processus de migration. L’objectif de cette étape est d’analyser les données en arrière-plan pour les ressources dont vous souhaitez effectuer la migration et d’indiquer si ces ressources peuvent ou non faire l’objet d’une migration.

Vous sélectionnez le réseau virtuel ou le service hébergé (s’il ne s’agit pas d’un réseau virtuel) que vous souhaitez valider pour la migration.

* Si la migration d’une ressource est impossible, la plateforme Azure en indique les raisons.

Lors de la validation de services de stockage, vous trouverez le compte migré dans un groupe de ressources portant le même nom que votre compte de stockage avec, en suffixe, «-Migrated ».  Par exemple, si votre compte de stockage est nommé « mystorage », vous trouverez la ressource activée par Azure Resource Manager dans un groupe de ressources nommé « mystorage-Migrated », contenant un compte de stockage nommé « mystorage ».

### <a name="prepare"></a>Préparation
L’opération de préparation constitue la deuxième étape du processus de migration. Le rôle de cette étape est de simuler la transformation des ressources IaaS Classic en ressources Resource Manager et de vous les présenter côte à côte pour que vous puissiez les visualiser.

Vous sélectionnez le réseau virtuel ou le service hébergé (s’il ne s’agit pas d’un réseau virtuel) que vous souhaitez préparer pour la migration.

* Si la ressource ne peut pas être migrée, la plateforme Azure arrête le processus de migration et indique la raison de l’échec de l’opération de préparation.
* Si la migration de la ressource est possible, la plateforme Azure commence par verrouiller les opérations du plan de gestion pour les ressources concernées. Par exemple, vous ne pourrez pas ajouter de disque de données à une machine virtuelle en cours de migration.

La plateforme Azure démarre ensuite la migration des métadonnées du déploiement Classic vers Resource Manager pour les ressources faisant l’objet de la migration.

Une fois l’opération de préparation terminée, vous avez la possibilité de visualiser les ressources tant dans le déploiement Classic quand dans le déploiement Resource Manager. Pour chaque service cloud du modèle de déploiement classique, nous créons un nom de groupe de ressources au format `cloud-service-name>-Migrated`.

> [!NOTE]
> Il n’est pas possible de sélectionner le nom du groupe de ressources créé pour les ressources migrées (par exemple, « -Migré »). Toutefois, une fois la migration terminée, vous pouvez utiliser la fonctionnalité de déplacement Azure Resource Manager pour déplacer des ressources vers n’importe quel groupe de ressources. Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](../articles/resource-group-move-resources.md)

Voici deux captures d’écran qui illustrent le résultat après une opération de préparation réussie. La première capture montre un groupe de ressources qui contient le service cloud d’origine. La deuxième capture montre le nouveau groupe de ressources « -Migré » qui contient les ressources Azure Resource Manager équivalentes.

![Capture d’écran illustrant le portail de service cloud classique](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Capture d’écran qui affiche les ressources du portail Azure Resource Manager lors d’une opération de préparation](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

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
> Il s’agit d’une opération idempotente. En cas d’échec, il est recommandé de retenter l’opération. Si le problème persiste, créez un ticket de support ou publiez un billet avec la balise ClassicIaaSMigration sur notre [forum consacré aux machines virtuelles](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).
>
>
<br>
Voici un diagramme de flux des étapes lors d’un processus de migration

![Capture d’écran illustrant les étapes de la migration](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-classic-to-azure-resource-manager-resources"></a>Traduction de ressources classiques en ressources Azure Resource Manager
Les représentations Classic et Resource Manager des ressources sont décrites dans la table suivante. D’autres fonctionnalités et ressources ne sont pas prises en charge actuellement.

| Représentation Classic | Représentation Resource Manager | Remarques détaillées |
| --- | --- | --- |
| Nom du service cloud |Nom DNS |Pendant la migration, un groupe de ressources est créé pour chaque service cloud correspondant au modèle de dénomination `<cloudservicename>-migrated`. Ce groupe de ressources contient toutes vos ressources. Le nom du service cloud devient un nom DNS associé à l’adresse IP publique. |
| Machine virtuelle |Machine virtuelle |Les propriétés spécifiques à la machine virtuelle sont inchangées après la migration. Certaines informations osProfile, notamment le nom de l’ordinateur, ne sont pas stockées dans le modèle de déploiement classique et restent vides après la migration. |
| Ressources de disque attachées à une machine virtuelle |Disques implicites attachés à une machine virtuelle |Les disques ne sont pas modélisés comme ressources de niveau supérieur dans le modèle de déploiement Resource Manager. Ils font l’objet d’une migration sous forme de disques implicites sous la machine virtuelle. Seuls des disques attachés à une machine virtuelle sont actuellement pris en charge. Les machines virtuelles Resource Manager peuvent désormais utiliser des comptes de stockage classiques, ce qui permet une migration facile des disques sans aucune mise à jour. |
| Extensions de machine virtuelle |Extensions de machine virtuelle |Toutes les extensions de ressource font l’objet d’une migration depuis le déploiement Classic, à l’exception des extensions XML. |
| Certificats de machine virtuelle |Certificats dans Azure Key Vault |Si un service cloud contient des certificats de service, la plateforme crée un coffre de clés Azure pour chaque service cloud et y déplace les certificats. Les machines virtuelles sont mises à jour pour référencer les certificats du coffre de clés. <br><br> **REMARQUE :** ne supprimez pas le coffre de clés, car cela peut provoquer l’échec de la machine virtuelle. Nous travaillons à l’amélioration des opérations sur le serveur principal afin que les coffres de clés puissent être supprimés ou déplacés en toute sécurité avec la machine virtuelle vers un nouvel abonnement. |
| Configuration WinRM |Configuration WinRM sous osProfile |La configuration de Windows Remote Management est déplacée sans être modifiée dans le cadre de la migration. |
| Propriété de groupe à haute disponibilité |Ressource de groupe à haute disponibilité | La spécification des groupes à haute disponibilité était une propriété de la machine virtuelle dans le modèle de déploiement Classic. Les groupes à haute disponibilité deviennent une ressource de niveau supérieur dans le cadre de la migration. Les configurations suivantes ne sont pas prises en charge : plusieurs groupes à haute disponibilité par service cloud, ou un ou plusieurs groupes à haute disponibilité en même temps que des machines virtuelles ne figurant dans aucun groupe à haute disponibilité d’un service cloud. |
| Configuration réseau sur une machine virtuelle |Interface réseau principale |La configuration réseau sur une machine virtuelle est représentée sous forme de ressource d’interface réseau principale après la migration. Dans le cas des machines virtuelles qui ne figurent pas dans un réseau virtuel, l’adresse IP interne change lors de la migration. |
| Plusieurs interfaces réseau sur une machine virtuelle |Interfaces réseau |Si plusieurs interfaces réseau sont associées à une machine virtuelle, chaque interface réseau devient une ressource de niveau supérieur dans le cadre de la migration vers le modèle de déploiement Resource Manager, avec toutes les propriétés. |
| Jeux de points de terminaison soumis à l’équilibrage de charge |Équilibrage de charge |Dans le modèle de déploiement Classic, la plateforme a affecté un équilibreur de charge implicite à chaque service cloud. Pendant la migration, une ressource d’équilibreur de charge est créée, et le jeu de points de terminaison d’équilibrage de charge constitue les règles de l’équilibreur de charge. |
| Règles NAT entrantes |Règles NAT entrantes |Les points de terminaison d’entrée définis sur la machine virtuelle sont convertis en règles de traduction NAT (Network Access Translation) du trafic entrant sous l’équilibreur de charge lors de la migration. |
| Adresse IP virtuelle |Adresse IP publique avec nom DNS |L’adresse IP virtuelle devient une adresse IP publique et est associée à l’équilibreur de charge. Une adresse IP virtuelle ne peut faire l’objet d’une migration que si un point de terminaison d’entrée lui a été attribué. |
| Réseau virtuel |Réseau virtuel |Le réseau virtuel fait l’objet d’une migration vers le modèle de déploiement Resource Manager avec toutes ses propriétés. Un nouveau groupe de ressources est créé avec le nom `-migrated`. |
| IP réservées |Adresse IP publique avec méthode d’allocation statique |La migration des IP réservées associées à l’équilibreur de charge est effectuée en même temps que la migration du service cloud ou de la machine virtuelle. La migration d’adresses IP réservées non associées n’est pas prise en charge actuellement. |
| Adresse IP publique par machine virtuelle |Adresse IP publique avec méthode d’allocation dynamique |L’adresse IP publique associée à la machine virtuelle est convertie en ressource d’adresse IP publique avec la méthode d’allocation statique. |
| Groupes de sécurité réseau |Groupes de sécurité réseau |Les groupes de sécurité réseau (NSG) associés à un sous-réseau sont clonés dans le cadre de la migration vers le modèle de déploiement Resource Manager. Le groupe de sécurité réseau (NSG) du modèle de déploiement classique n’est pas supprimé durant la migration. Toutefois, les opérations du plan de gestion relatives au NSG sont bloquées pendant le processus de migration. |
| Serveurs DNS |Serveurs DNS |La migration des serveurs DNS associés à un réseau virtuel ou à la machine virtuelle est effectuée dans le cadre de la migration des ressources correspondantes avec toutes les propriétés. |
| Itinéraires définis par l’utilisateur (UDR) |Itinéraires définis par l’utilisateur (UDR) |Les Itinéraires définis par l’utilisateur associés à un sous-réseau sont clonés dans le cadre de la migration vers le modèle de déploiement Resource Manager. L’itinéraire défini par l’utilisateur dans le modèle de déploiement classique n’est pas supprimé durant la migration. Les opérations du plan de gestion relatives pour l’itinéraire défini par l’utilisateur sont bloquées pendant le processus de migration. |
| Propriété de transfert IP sur la configuration réseau d’une machine virtuelle |Propriété de transfert IP sur la carte d’interface réseau (NIC) |La propriété de transfert IP sur une machine virtuelle est convertie en propriété sur l’interface réseau pendant la migration. |
| Équilibreur de charge avec plusieurs adresses IP |Équilibreur de charge avec plusieurs ressources d’adresses IP publiques |Chaque adresse IP publique associée à l’équilibreur de charge est convertie en ressource d’adresse IP publique et associée à l’équilibreur de charge après la migration. |
| Noms DNS internes sur la machine virtuelle |Noms DNS internes sur la carte réseau |Pendant la migration, les suffixes DNS internes pour les machines virtuelles sont migrés vers une propriété en lecture seule nommée « InternalDomainNameSuffix » sur la carte réseau. Le suffixe reste inchangé après la migration, et la résolution des machines virtuelles doit continuer à fonctionner comme auparavant. |
| Passerelle de réseau virtuel |Passerelle de réseau virtuel |Les propriétés de la passerelle de réseau virtuel sont inchangées après la migration. L’adresse IP virtuelle associée à la passerelle reste inchangée également. |
| Site de réseau local |Passerelle de réseau local |Les propriétés du site de réseau local sont migrées sans modification vers une nouvelle ressource appelée Passerelle de réseau local. Ceci représente les préfixes d’adresse locale et l’adresse IP de la passerelle distante. |
| Références de connexions |Connexion |Les références de connectivité entre la passerelle et le site de réseau local dans la configuration réseau sont représentées par une ressource nouvellement créée appelée Connexion dans Resource Manager après la migration. Toutes les propriétés de référence de connectivité dans les fichiers de configuration réseau sont copiées sans modification dans la ressource de connexion nouvellement créée. La connectivité de réseau virtuel à réseau virtuel dans le déploiement Classic est obtenue en créant deux tunnels IPsec vers les sites de réseau local qui représentent les réseaux virtuels. Elle est transformée en type de connexion Vnet2Vnet dans le modèle Resource Manager sans qu’il soit nécessaire de faire appel à des passerelles de réseau local. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Modifications de vos services d’automatisation et de vos outils après la migration
Dans le cadre de la migration de vos ressources à partir du modèle de déploiement classique vers le modèle de déploiement Resource Manager, vous devez mettre à jour votre automatisation ou vos outils pour vous assurer qu’ils continueront de fonctionner après la migration.
