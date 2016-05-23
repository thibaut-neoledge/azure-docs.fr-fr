<properties
	pageTitle="Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager"
	description="Cet article décrit pas à pas les fonctionnalités du service de migration de ressources prise en charge par la plateforme vers Azure Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager

Voici près d’un an, nous annoncions la prise de charge de Virtual Machines sous Azure Resource Manager. Cet article vous présente en détail les améliorations et les fonctionnalités supplémentaires offertes par cette prise en charge. À l’époque, nous avions également fourni des conseils sur la meilleure façon de procéder pour connecter et faire coexister des ressources des deux modèles de déploiement dans votre abonnement à l’aide de passerelles de réseau virtuel de site à site. Dans cet article, nous souhaitons à présent expliquer la façon dont nous prenons en charge la migration des ressources IaaS (infrastructure as a service) Classic vers Resource Manager.

## Quel est l’objectif de cette migration ?

Nous sommes véritablement enthousiasmés par la puissance et les capacités que procurent la nouvelle expérience et les API disponibles sur Virtual Machines. Nous sommes convaincus que cette approche va révolutionner les possibilités d’utilisation du cloud d’une multitude de façons. En effet, le lancement du nouveau modèle vous permet de déployer, de gérer et de surveiller les services associés dans un groupe de ressources. Resource Manager autorise le déploiement d’applications complexes à l’aide de modèles, configure les machines virtuelles au moyen d’extensions de machine virtuelle et intègre la gestion des accès et le balisage. Cette infrastructure inclut également le déploiement évolutif en parallèle de machines virtuelles dans des groupes à haute disponibilité. En outre, le nouveau modèle gère les cycles de vie des ressources réseau, de calcul et de stockage de manière indépendante. Et il applique une sécurité par défaut grâce à la mise en œuvre de machines virtuelles dans un réseau virtuel.

Du point de vue des fonctionnalités, la quasi-totalité d’entre elles sont prises en charge pour les services de calcul, de réseau et de stockage sous Azure Resource Manager, à quelques exceptions près que nous nous employons activement à éliminer d’ici quelques mois. En outre, nous nous efforçons continuellement d’améliorer l’expérience utilisateur et d’ajouter d’autres fonctionnalités au Portail Azure afin de relier les expériences.

Du fait de cette nouvelle capacité et de cette base de déploiement en pleine expansion dans la nouvelle infrastructure Azure Resource Manager, nous voulons offrir aux clients une possibilité de migration des déploiements existants dans Classic.

>[AZURE.NOTE] Par le biais de cette annonce, nous lançons la version préliminaire publique de ce service de migration. Nous vous recommandons de n’utiliser la version préliminaire publique que pour la migration de vos charges de travail de développement et de test liées à votre abonnement Azure.

## Modifications de vos services d’automatisation et de vos outils après la migration

Notez que l’une des modifications majeures que vous devrez effectuer dans le cadre de la migration de vos ressources Classic vers le modèle Resource Manager consistera à mettre à jour vos services d’automatisation ou vos outils existants pour garantir leur bon fonctionnement après la migration des ressources.

## En quoi consiste la migration des ressources IaaS Classic vers Resource Manager ?

Avant que nous n’entrions dans les détails, commençons par examiner brièvement les différences entre les opérations du plan de données et celles du plan de gestion pour les ressources IaaS. Il est indispensable de bien comprendre ces différences, car elles sous-tendent l’approche que nous avons choisi de suivre pour prendre en charge cette migration.

- Plan de gestion : ensemble des appels destinés au plan de gestion ou à l’API en vue de modifier les ressources. Exemple : création d’une machine virtuelle, redémarrage d’une machine virtuelle, mise à jour d’un réseau virtuel avec un nouveau sous-réseau, etc. Toutes ces opérations gèrent les ressources en cours d’exécution, mais n’ont pas d’incidence directe sur la connexion aux instances.
- Plan de données (application) : « runtime » de l’application proprement dite, impliquant une interaction avec les instances qui ne passent pas par l’API Azure. Par exemple, l’accès à votre site web ou l’extraction de données à partir d’un serveur SQL Server ou mongoDB en cours d’exécution sont considérés comme des interactions d’application ou de plan de données. La copie d’un objet blob à partir d’un compte de stockage et l’accès à une adresse IP publique sur RDP ou SSH dans la machine virtuelle constituent également des opérations du plan de données. Ces opérations garantissent le fonctionnement continu de l’application dans l’ensemble des services de calcul, de mise en réseau et de stockage.

>[AZURE.NOTE] Dans certains scénarios de migration (voir les détails dans la section relative aux configurations non prises en charge), nous devrons arrêter, libérer et redémarrer vos machines virtuelles, ce qui entraînera un bref temps d’arrêt du plan de données.

## Quelles sont les étendues de migration prises en charge ?

Dans le cadre de la version préliminaire publique, nous offrons deux étendues de migration ciblant principalement les services de calcul et de mise en réseau. La prise en charge de la migration des comptes de stockage est prévue et sera assurée très prochainement. Toutefois, pour permettre une migration transparente, nous avons autorisé les comptes de stockage Classic à contenir des disques pour machines virtuelles Resource Manager. Vous trouverez des détails supplémentaires sur ce sujet dans la suite de cet article.

### Migration de machines virtuelles (ne figurant pas dans un réseau virtuel)

Dans le modèle de déploiement Resource Manager, nous assurons par défaut la sécurité de vos applications. Par conséquent, toutes les machines virtuelles doivent figurer dans un réseau virtuel du modèle Resource Manager. Dans le cadre de la migration, ,nous procéderons donc au redémarrage (arrêt, libération et démarrage) des machines virtuelles. Vous disposez de deux options concernant les réseaux virtuels :
- Vous pouvez demander à la plateforme de créer un réseau virtuel et procéder à la migration de la machine virtuelle vers ce réseau,
- ou vous pouvez effectuer la migration de la machine virtuelle vers un réseau virtuel existant dans Resource Manager.

>[AZURE.NOTE] Dans cette étendue de migration, les opérations du plan de gestion et du plan de données peuvent ne pas être autorisées pendant un certain laps de temps lors de la migration.

### Migration de machines virtuelles (dans un réseau virtuel)

Pour la plupart des configurations de machines virtuelles dans cette étendue, nous nous contentons d’effectuer la migration des métadonnées Classic vers Resource Manager. Les machines virtuelles sous-jacentes s’exécutent sur le même matériel, dans le même réseau et avec le même stockage. Par conséquent, lorsque nous parlons de la migration des métadonnées Classic vers Resource Manager, les opérations du plan de gestion peuvent ne pas être autorisées pendant un certain laps de temps durant la migration. Toutefois, le plan de données continuera de fonctionner ; par exemple, vos applications s’exécutant sur des machines virtuelles (Classic) ne subiront aucun temps d’arrêt lors de la migration.

Pour l’instant, les configurations ci-après ne sont pas prises en charge. Toutefois, lorsque nous les prendrons en charge à l’avenir, certaines machines virtuelles de cette configuration entraîneront peut-être un temps d’arrêt (arrêt, libération et redémarrage) :

-	si vous disposez de plus d’un groupe à haute disponibilité dans un seul service cloud ;
-	si vous possédez un ou plusieurs groupes à haute disponibilité et des machines virtuelles ne figurant pas dans un groupe à haute disponibilité dans un seul service cloud.

>[AZURE.NOTE] Dans cette étendue de migration, le plan de gestion peut ne pas être utilisable pendant un certain laps de temps lors de la migration. Certaines configurations spéciales, décrites ci-dessus, donneront lieu à un temps d’arrêt du plan de données.

### Comptes de stockage et migration

Pour l’instant, la version préliminaire publique ne prend pas en charge la migration des comptes de stockage. Toutefois, la prise en charge de cette migration est prévue et sera assurée très prochainement. En matière de migration et de comportement des comptes de stockage, deux aspects importants sont à prendre en compte.

- Possibilité de déploiement de machines virtuelles Resource Manager sur un compte de stockage Classic : pour permettre une migration transparente, nous avons mis en œuvre la possibilité de déployer des machines virtuelles Resource Manager dans un compte de stockage Classic. Cette fonctionnalité permet d’effectuer la migration des ressources de calcul et de réseau indépendamment des comptes de stockage.
- Meilleures pratiques en matière de migration de comptes de stockage : lorsque la migration des comptes de stockage sera prise en charge, la plateforme mettra en œuvre les ressources de calcul et de réseau séparément des comptes de stockage afin de garantir une expérience transparente.

## Fonctionnalités et configurations non prises en charge

À ce stade, nous ne gérons pas encore certaines fonctionnalités et configurations spécifiques. Nous travaillons à la prise en charge prochaine de ces dernières ; toutefois, la section suivante décrit nos recommandations et prévisions en la matière.

### Fonctionnalités non prises en charge

La liste ci-après répertorie les fonctionnalités non prises en charge dans la version préliminaire publique. Si vous le souhaitez, vous pouvez supprimer ces paramètres, effectuer la migration des machines virtuelles, puis réactiver les paramètres dans le modèle de déploiement Resource Manager. La prise en charge de la plupart de ces fonctionnalités est prévue et sera assurée dès que ces dernières deviendront disponibles.

Fournisseur de ressources | Fonctionnalité
---------- | ------------
Calcul | Diagnostics de démarrage
Calcul | Disques de machine virtuelle non associés
Calcul | Images de machine virtuelle
Réseau | IP réservées non associées (si non attachées à une machine virtuelle) ; prise en charge des IP réservées attachées aux machines virtuelles
Réseau | Groupes de sécurité réseau (NSG) non associés (si non attachés à un réseau virtuel ou à une interface réseau) ; prise en charge des NSG référencés par des réseaux virtuels
Réseau | Listes de contrôle d’accès de points de terminaison
Réseau | Passerelles de réseau virtuel (de site à site, ExpressRoute, de point à site)
Storage | Comptes de stockage

### Configurations non prises en charge

La liste ci-après répertorie les configurations non prises en charge dans la version préliminaire publique. Vous trouverez nos recommandations ci-dessous. La prise en charge de certaines de ces configurations est prévue et sera assurée dès que ces dernières deviendront disponibles.

de diffusion en continu | Configuration | Recommandation
---------- | ------------ | ------------
Gestionnaire de ressources | Contrôle d’accès en fonction du rôle (RBAC) pour les ressources Classic | Étant donné que l’URI des ressources est modifié après la migration, nous vous recommandons de planifier au préalable les mises à jour de stratégies RBAC qui seront nécessaires après la migration.
Calcul | Plusieurs sous-réseaux associés à une machine virtuelle | Vous devez mettre à jour la configuration de sous-réseau pour ne référencer que le sous-réseau.
Calcul | Machines virtuelles appartenant à un réseau virtuel, mais auxquelles aucun sous-réseau n’est affecté de manière explicite | Si vous le souhaitez, vous pouvez supprimer la machine virtuelle. La prise en charge de cette fonctionnalité est prévue.
Calcul | Machines virtuelles dotées d’alertes et de stratégies de mise à l’échelle automatique | Pour l’instant, la migration s’effectue en supprimant ces paramètres. Nous vous recommandons donc vivement d’évaluer votre environnement avant de procéder à la migration. Vous pouvez également choisir de reconfigurer les paramètres d’alerte une fois la migration terminée.
Calcul | Extensions de machine virtuelle XML (VS Debugger, WebDeploy et RemoteDebug) | Ces extensions ne seront pas prises en charge. Nous vous recommandons de les supprimer de la machine virtuelle pour poursuivre la migration.
Calcul | Services cloud contenant des rôles Web/de travail | Cette configuration n’est pas prise en charge pour l’instant, mais elle est en cours de planification.
Réseau | Réseaux virtuels contenant des machines virtuelles et des rôles Web/de travail | Cette configuration n’est pas prise en charge pour l’instant, mais elle est en cours de planification.
Réseau | Sous-réseaux dont le nom contient des espaces | La prise en charge de cette fonctionnalité est prévue.
App Services | Réseau virtuel contenant des options App Service Environment | Cette configuration n’est pas prise en charge pour l’instant, mais elle est en cours de planification.
Services HDInsight | Réseau virtuel contenant des services HDInsight | Cette configuration n’est pas prise en charge pour l’instant, mais elle est en cours de planification.
Dynamics Lifecycle Services | Réseau virtuel contenant des machines virtuelles gérées par Dynamics Lifecycle Services | Cette configuration n’est pas prise en charge pour l’instant, mais elle est en cours de planification.

## Expérience de migration

Avant que vous ne démarriez le processus de migration, nous vous recommandons vivement de suivre la procédure ci-après :

1. Assurez-vous que les ressources dont vous avez planifié la migration n’ont pas recours à des fonctionnalités ou configurations non prises en charge. Dans la plupart des cas, la plateforme détecte ce type de situation et génère une erreur.
2. Si certaines de vos machines virtuelles n’appartiennent pas à un réseau virtuel, elles seront arrêtées et libérées dans le cadre de l’opération de préparation. Par conséquent, si vous ne voulez pas perdre l’adresse IP publique, envisagez de réserver l’adresse IP avant de déclencher l’étape de préparation. En revanche, si vos machines virtuelles se trouvent dans un réseau virtuel, elles ne seront ni arrêtées, ni libérées.
3. Azure vous recommande de configurer un environnement de test présentant une configuration similaire et d’effectuer la migration de ces ressources avant de procéder à la migration de vos déploiements de production.
4. Planifiez votre migration en dehors des heures d’ouverture afin de tenir compte des erreurs inattendues susceptibles de survenir au cours du processus.
5. Téléchargez la configuration actuelle de vos machines virtuelles à l’aide de PowerShell, de commandes de l’interface de ligne de commande ou d’API REST afin d’en faciliter la vérification après l’étape de préparation.
6. Mettez à jour vos scripts d’automatisation/d’opérationnalisation pour gérer le modèle de déploiement Resource Manager avant de commencer la migration. Si vous le souhaitez, vous pouvez également effectuer des opérations GET lorsque les ressources se trouvent à l’état Préparé.
7. Évaluez les stratégies RBAC configurées sur les ressources IaaS Classic et planifiez leur mise à jour une fois la migration terminée.

Dans le cadre de l’annonce de la version préliminaire publique, nous avons ajouté la prise en charge du déclenchement du processus de migration par le biais d’API REST, de PowerShell et de l’interface de ligne de commande Azure. La migration par le Portail Azure sera prochainement prise en charge pour vous permettre de visualiser et de suivre la procédure de migration d’un déploiement Classic vers un déploiement Azure Resource Manager.

![Capture d’écran illustrant le workflow de migration](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

1.	Préparation
	* Il s’agit de la première étape du processus de migration. Le rôle de cette étape est de simuler la transformation des ressources IaaS Classic en ressources Resource Manager et de vous les présenter côte à côte pour vous aider à les visualiser. Le déroulement des opérations correspondant est décrit en détail ci-après.
  * Vous sélectionnez le réseau virtuel ou le service hébergé (s’il ne s’agit pas d’un réseau virtuel) que vous souhaitez préparer pour la migration.
  *	Dans un premier temps, la plateforme exécute systématiquement une analyse des données en arrière-plan pour la ou les ressources dont vous souhaitez effectuer la migration et indique si ces ressources peuvent ou non faire l’objet d’une migration.
	*	Si la migration d’une ressource est impossible, nous en indiquons les raisons.
	* Si la migration de la ressource est prise en charge, la plateforme commence par verrouiller les opérations du plan de gestion pour la ou les ressources concernées. Par exemple, vous ne pourrez pas ajouter de disque de données à une machine virtuelle en cours de migration.
  *	La plateforme démarre ensuite la migration des métadonnées Classic vers Resource Manager pour la ou les ressources concernées par la migration.  
  *	Une fois l’opération de préparation terminée, vous avez la possibilité de visualiser les ressources dans les modèles Classic et Resource Manager simultanément. Pour chaque service cloud du modèle de déploiement Classic, nous créons un nom de groupe de ressources au format `cloud-service-name>-migrated`.

2.	Vérification manuelle ou à base de script
  * Dans le cadre de cette étape, vous pouvez éventuellement utiliser la configuration que vous avez téléchargée précédemment pour vérifier que la migration semble correcte. Une autre possibilité consiste à se connecter au portail et à procéder à une vérification ponctuelle des propriétés et des ressources pour vous assurer que la migration des métadonnées s’est déroulée de manière adéquate.
	* Si vous effectuez la migration d’un réseau virtuel, la majeure partie de la configuration des machines virtuelles ne sera pas redémarrée. En ce qui concerne les applications installées sur ces machines virtuelles, vous pouvez vérifier que ces applications sont toujours opérationnelles.
	* Vous pouvez tester vos scripts de surveillance/automatisation et vos scripts d’opérations pour vous assurer du bon fonctionnement des machines virtuelles et de vos scripts mis à jour. Notez que seules les opérations GET sont prises en charge lorsque les ressources présentent l’état Préparé.
  * Aucun délai maximal ne vous est imposé pour la validation de la migration. Vous pouvez conserver les ressources en l’état pour la durée de votre choix. Toutefois, notez que le plan de gestion sera verrouillé pour ces ressources jusqu’à ce que vous choisissiez d’abandonner ou de valider la migration.
  * Si vous constatez certains problèmes, vous pouvez toujours abandonner la migration et revenir au modèle de déploiement Classic. Une fois que vous avez choisi de revenir en arrière, nous déverrouillons les opérations du plan de gestion sur les ressources pour vous permettre de reprendre vos opérations normales sur ces machines virtuelles dans le modèle de déploiement Classic.

3. Abandon
  * Cette étape facultative vous permet d’abandonner la migration en annulant vos modifications et en revenant au modèle de déploiement Classic. Notez que vous ne pouvez plus effectuer cette opération une fois que vous avez déclenché l’opération de validation. 	

4.	Validation
  * Une fois la vérification effectuée, vous pouvez choisir de valider la migration. Les ressources disparaissent alors du modèle Classic et deviennent donc uniquement disponibles dans le modèle de déploiement Resource Manager. Cela signifie également que les ressources soumises à la migration ne pourront être gérées que dans le nouveau portail.
	* Si cette opération échoue, nous vous recommandons de la relancer à plusieurs reprises. Si le problème persiste, créez un ticket de support ou publiez un billet avec l’étiquette ClassicIaaSMigration [ici](https://social.msdn.microsoft.com/Forums/azure/fr-FR/home?forum=WAVirtualMachinesforWindows).

>[AZURE.NOTE] Notez que toutes les opérations décrites ci-dessous sont idempotent. Si vous rencontrez un problème autre qu’une fonctionnalité non prise en charge ou qu’une erreur de configuration, nous vous recommandons de réexécuter la procédure de préparation et d’abandon ou de validation pour que la plateforme tente une nouvelle fois l’opération.

## Étapes suivantes
À présent que vous savez en quoi consiste la migration des ressources IaaS Classic vers Resource Manager, vous pouvez commencer à effectuer la migration de vos ressources.

- [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Cloner une machine virtuelle Classic vers Azure Resource Manager à l’aide de scripts PowerShell](virtual-machines-windows-migration-scripts.md)

## Forum Aux Questions (FAQ)

**Ce plan de migration affecte-t-il l’un de mes services ou applications existants qui s’exécutent sur Azure Virtual Machines ?**

Non. Les machines virtuelles (Classic) sont des services à disponibilité générale entièrement pris en charge, et vous pouvez donc continuer à tirer profit de ces ressources pour étendre votre présence sur le service cloud Microsoft Azure.

**Que se passera-t-il pour mes machines virtuelles si je n’envisage pas de procéder à cette migration dans un avenir proche ?**

Nous ne déconseillons pas l’utilisation des API et du modèle de ressource Classic existants. Compte tenu des fonctionnalités avancées offertes par le modèle de déploiement Resource Manager, notre objectif est de faciliter considérablement la migration vers ce modèle. Nous vous recommandons donc vivement de passer en revue certaines des avancées réalisées en matière d’IaaS sous Resource Manager [ici](virtual-machines-windows-compare-deployment-models.md).

**Quelles sont les implications de ce plan de migration pour mes outils existants ?**

La mise à jour de vos outils vers le modèle de déploiement Resource Manager constitue l’un des plus importants changements à prendre en compte dans vos plans de migration.

**Quelle sera la durée de l’arrêt du plan de gestion ?**

Cette durée dépend du nombre de ressources dont vous effectuez la migration. Pour les déploiements restreints (quelques dizaines de machines virtuelles), la migration complète devrait prendre moins d’une heure. En revanche, pour les déploiements à grande échelle (plusieurs centaines de machines virtuelles), ce processus peut demander plusieurs heures. Étant donné que le service est disponible en version préliminaire publique, nous vous recommandons vivement de l’exécuter sur votre abonnement de développement ou de test pour en évaluer l’impact.

**Puis-je procéder à une restauration après avoir validé la migration de mes ressources dans Resource Manager ?**

Vous pouvez abandonner la migration tant que vos ressources se trouvent à l’état Préparé. La restauration n’est plus prise en charge une fois que les ressources ont fait l’objet d’une migration à l’aide de l’opération de validation.

**Puis-je restaurer ma migration en cas d’échec de l’opération de validation ?**

Vous ne pouvez pas abandonner la migration si l’opération de validation échoue. Toutes les opérations de migration, y compris l’opération de validation, sont idempotent. Nous vous recommandons donc de relancer l’opération après une courte période. Si l’erreur persiste, créez un ticket de support ou publiez un billet avec l’étiquette ClassicIaaSMigration [ici](https://social.msdn.microsoft.com/Forums/azure/fr-FR/home?forum=WAVirtualMachinesforWindows).

**Dois-je acheter un autre circuit ExpressRoute si je dois exploiter la ressource IaaS sous Resource Manager ?**

Non. Nous avons récemment rendu possible la [coexistence d’un circuit ExpressRoute à la fois dans Classic et dans Resource Manager](../expressroute/expressroute-howto-coexist-resource-manager.md). Vous n’avez pas besoin d’acheter de nouveau circuit ExpressRoute si vous en possédez déjà un.

**Existe-t-il une feuille de route concernant la date d’ajout des scénarios non pris en charge dans la liste de migrations ?**

À ce stade, nous avons prévu d’assurer la prise en charge d’un premier ensemble de scénarios au cours du premier semestre 2016. Nous procéderons ensuite à la prise en charge progressive des fonctionnalités non encore gérées après cette première version.

**Que se passera-t-il si j’ai configuré des stratégies de contrôle d’accès en fonction du rôle (RBAC) pour mes ressources IaaS Classic ?**

Pendant la migration, les ressources Classic sont converties en ressources Resource Manager. Nous vous recommandons donc de planifier au préalable les mises à jour de stratégies RBAC qui seront nécessaires après la migration.

**Que se passera-t-il si j’utilise actuellement des services Azure Site Recovery ou Backup sur Azure ?**

La prise en charge des services Azure Site Recovery et Backup pour Virtual Machines sous Resource Manager a été ajoutée récemment. Nous collaborons activement avec les équipes concernées dans le but de prendre également en charge la migration des machines virtuelles utilisant ces services dans Resource Manager. À ce stade, nous vous déconseillons d’effectuer la migration si vous tirez profit de ces fonctionnalités.

**Puis-je vérifier si mon abonnement ou mes ressources peuvent faire l’objet d’une migration ?**

À l’heure actuelle, l’opération de préparation vérifie implicitement les ressources dont vous planifiez la migration. Dans l’option de migration prise en charge par la plateforme, la première étape de préparation de la migration consiste à s’assurer que les ressources peuvent faire l’objet de cette migration. Si cette vérification échoue, les ressources ne seront aucunement affectées. Toutefois, nous avons également prévu de proposer une nouvelle action de vérification qui simplifiera sensiblement le processus.

**Que se passe-t-il si je rencontre une erreur de quota lors de la préparation des ressources IaaS pour la migration ?**

Nous vous recommandons d’abandonner la migration. Créez ensuite une demande de support en vue d’augmenter les quotas pour la région dans laquelle vous effectuez la migration des machines virtuelles. Une fois votre demande de quota approuvée, réexécutez la procédure de migration.

**Comment signaler un problème si je n’ai pas souscrit de contrat de support ?**

Publiez vos problèmes et questions concernant la migration sur notre Forum Virtual Machines en utilisant le mot clé ClassicIaaSMigration [ici](https://social.msdn.microsoft.com/Forums/azure/fr-FR/home?forum=WAVirtualMachinesforWindows). Nous vous recommandons de publier toutes vos questions sur ce forum. Toutefois, si vous ne disposez d’aucun contrat de support, n’hésitez pas à créer également un ticket de support.

**Comment procéder si je n’apprécie pas les noms que la plateforme a choisis pour mes groupes de ressources lors de la migration ?**

Nous allons prochainement annoncer la prise en charge du déplacement de ressources entre groupes de ressources. Une fois cette fonctionnalité prise en charge, vous pourrez déplacer les ressources vers le groupe de ressources de votre choix.

**Comment procéder si je n’apprécie pas les noms que la plateforme a choisis pour mes ressources lors de la migration ?**

Dans le cas des ressources dont vous avez explicitement fourni le nom dans le modèle de déploiement Classic, ces noms seront conservés pendant la migration. Dans certains cas, de nouvelles ressources seront créées. Par exemple, une interface réseau sera créée pour chaque machine virtuelle. À ce stade, nous ne prenons pas en charge la possibilité de contrôler les noms de ces ressources créées pendant la migration. Si vous souhaitez voter pour cette fonctionnalité, merci de nous en faire part [ici](http://feedback.azure.com).

<!---HONumber=AcomDC_0511_2016-->