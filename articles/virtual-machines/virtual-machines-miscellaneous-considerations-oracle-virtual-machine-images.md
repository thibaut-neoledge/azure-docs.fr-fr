<properties title="Miscellaneous Considerations for Oracle Virtual Machine Images" pageTitle="Remarques diverses concernant les images de machines virtuelles Oracle" description="Consultez des remarques supplémentaires avant de déployer une machine virtuelle Oracle dans Microsoft Azure." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Remarques diverses concernant les images de machines virtuelles Oracle
Cet article aborde différentes considérations relatives aux machines virtuelles Oracle sur Azure, qui reposent sur les images logicielles Oracle fournies par Microsoft et utilisent le système d’exploitation Windows Server.

-  Images de machine virtuelle de base de données Oracle
-  Images de machine virtuelle Oracle WebLogic Server
-  Images de machine virtuelle JDK Oracle

##Images de machine virtuelle de base de données Oracle
### La gestion de clusters (RAC) n’est pas prise en charge.

Actuellement, Microsoft Azure ne prend pas en charge le clustering de base de données Oracle. Seules les instances de base de données Oracle autonomes sont gérées. En effet, Microsoft Azure ne prend actuellement pas en charge le partage de disques virtuels en lecture/écriture entre plusieurs instances de machines virtuelles. Par ailleurs, la multidiffusion UDP n’est pas gérée.

### Aucune adresse IP interne statique

Microsoft Azure attribue une adresse IP interne à chaque machine virtuelle. Sauf si elle fait partie d’un réseau virtuel, l’adresse IP de la machine virtuelle est de type dynamique et peut changer lorsqu’elle redémarre. Cela peut entraîner des problèmes, car la base de données Oracle s’attend à une adresse IP statique. Pour éviter ce problème, pensez à ajouter la machine virtuelle à Microsoft Azure Virtual Network. Pour en savoir plus, consultez les sections relatives aux [réseaux virtuels](http://azure.microsoft.com/documentation/services/virtual-network/) et à la [création d’un réseau virtuel cloud uniquement dans Azure](create-virtual-network.md).

### Options de configuration des disques connectés

Vous pouvez placer les fichiers de données sur le disque hébergeant le système d’exploitation de la machine virtuelle ou sur des disques connectés, également appelés « disques de données ». Les disques attachés peuvent offrir une meilleure souplesse que le disque de système d’exploitation, en termes de performances comme de taille. En effet, il peut être préférable d’opter pour un disque de système d’exploitation lorsque la base de données présente une taille inférieure à 10 Go.

Les disques attachés s’appuient sur le service de stockage d’objets blob Microsoft Azure. En théorie, chaque disque peut proposer un maximum de 500 entrées/sorties par seconde (IOPS). Il se peut que les performances des disques attachés ne soient pas optimales au départ, et que celles des IOPS s’améliorent nettement après une période d’« échauffement » en continu, de 60 à 90 minutes. Si, par la suite, un disque reste inactif, les performances des IOPS correspondantes risquent de diminuer, jusqu’à la prochaine période de fonctionnement en continu. En un mot comme en cent, plus un disque est actif, plus il a de chances de proposer des performances optimales en termes d’IOPS.

L’approche la plus simple consiste à attacher un seul disque à la machine virtuelle et à placer les fichiers de base de données sur ce disque. C’est également la méthode qui propose les performances les plus restreintes. Il s’avère que vous pouvez souvent améliorer les performances d’IOPS effectives si vous utilisez plusieurs disques attachés, répartissez les données de la base de données sur ces disques, puis lancez ASM (Oracle Automatic Storage Management). Pour en savoir plus, voir [Oracle Automatic Storage Overview](http://www.oracle.com/technetwork/database/index-100339.html). Il est possible d’utiliser l’agrégation par bandes au niveau du système d’exploitation de plusieurs disques, mais cette approche est déconseillée, car elle n’est pas connue pour améliorer les performances des IOPS.

Tenez compte des deux approches permettant d’attacher plusieurs disques, selon que vous souhaitez prioriser les performances des opérations de lecture ou d’écriture dans votre base de données :

- **Utilisé seul, le logiciel Oracle ASM** est susceptible de proposer des performances optimisées pour les opérations d’écriture, mais moins d’IOPS pour les opérations de lecture que la méthode reposant sur des pools de stockage Windows Server 2012. L’illustration suivante décrit cette organisation, de manière logique. ![](media/virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images/image2.png)

- **Utilisé avec des pools de stockage Windows Server 2012, Oracle ASM** peut proposer de meilleures performances d’IOPS pour les opérations de lecture, s’il s’agit des principales opérations gérées par votre base de données ou si vous mettez l’accent sur les opérations de lecture plutôt que sur les opérations d’écriture. Une image basée sur le système d’exploitation Windows Server 2012 est requise. Voir [Déployer des espaces de stockage sur un serveur autonome](http://technet.microsoft.com/library/jj822938.aspx) pour en savoir plus sur les pools de stockage. Dans cette organisation, deux sous-ensembles égaux de disques attachés sont tout d’abord « répartis » en tant que disques physiques dans deux volumes de pools de stockage. Ensuite, les volumes sont ajoutés à un groupe de disques ASM. L’illustration suivante décrit cette organisation, de manière logique.

	![](media/virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images/image3.png)

>[AZURE.IMPORTANT]Établissez un compromis entre les performances en écriture et les performances en lecture en procédant au cas par cas. L’utilisation de ces méthodes peut donner lieu à des résultats réels variables

### Remarques relatives à la haute disponibilité et à la récupération d’urgence

Lors de l’utilisation de la base de données Oracle dans les machines virtuelles Microsoft Azure, vous êtes responsable de l’implémentation d’une solution de récupération d’urgence et de haute disponibilité pour éviter tout temps d’arrêt. Vous êtes également chargé de sauvegarder votre application et vos données.

Pour assurer la haute disponibilité et la récupération d’urgence pour Oracle Database Enterprise Edition (sans RAC) sur Microsoft Azure, voir pouvez utiliser [Data Guard, Active Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) ou [Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate), en plaçant deux bases de données sur deux machines virtuelles distinctes. Ces deux machines doivent figurer dans le même [service cloud](cloud-services-connect-virtual-machine.md), au sein du même [réseau virtuel](http://azure.microsoft.com/documentation/services/virtual-network/), afin de pouvoir être accessibles l’une à l’autre via l’adresse IP privée persistante. En outre, nous vous recommandons de placer les machines virtuelles au sein du même [groupe à haute disponibilité](manage-availability-virtual-machines.md), afin de permettre à Microsoft Azure de les placer dans des domaines d’erreur et de mise à niveau distincts. Remarque : seules les machines virtuelles figurant dans le même service cloud peuvent faire partie du même groupe à haute disponibilité. Chaque machine virtuelle doit disposer d’au moins 2 Go de mémoire et de 5 Go d’espace disque.

Grâce à Oracle Data Guard, vous pouvez assurer la haute disponibilité du système en plaçant la base de données primaire sur une machine virtuelle et une base de données secondaire (de veille) sur une autre, et en configurant une réplication monodirectionnelle entre ces bases de données. Vous bénéficiez ainsi d’un accès en lecture à la copie de la base de données. Oracle GoldenGate vous permet de configurer une réplication bidirectionnelle entre les deux bases de données. Pour savoir comment configurer une solution de haute disponibilité pour vos bases de données en utilisant ces outils, consultez la documentation relative à [Active Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) et [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) sur le site web d’Oracle. Si vous avez besoin d’un accès en lecture et en écriture à la copie de la base de données, vous pouvez utiliser [Oracle Active Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

##Images de machine virtuelle Oracle WebLogic Server

-  **Le clustering est pris en charge sur la version Enterprise Edition uniquement.** Si vous utilisez des images WebLogic Server sous licence Microsoft (c’est-à-dire celles qui utilisent le système d’exploitation Windows Server), vous pouvez uniquement utiliser le clustering WebLogic si vous exécutez la version Enterprise Edition de WebLogic Server. N’utilisez pas le clustering avec WebLogic Server Standard Edition.

-  **Délai d’attente de la connexion :** si votre application s’appuie sur des connexions aux points de terminaison publics d’un autre service cloud Microsoft Azure (par exemple, un service de niveau base de données), Azure peut fermer ces connexions ouvertes après 4 minutes d’inactivité. Cette opération peut affecter les fonctions et applications basées sur des pools de connexions, car les connexions inactives pendant plus de 4 minutes peuvent ne plus être valides. Si votre application est affectée, envisagez d’activer la logique de connexion persistante sur vos pools de connexions.

	Notez que si un point de terminaison est *interne* au déploiement du service cloud Microsoft Azure (comme une machine virtuelle de base de données autonome au sein du *même* service cloud que vos machines virtuelles WebLogic), la connexion est directe et ne repose pas sur l’équilibreur de charge Microsoft Azure ; de ce fait, elle n’est pas soumise à un délai d’attente de la connexion.

-  **La multidiffusion UDP n’est pas gérée.** Microsoft Azure prend en charge la monodiffusion UDP, mais ni la diffusion, ni la multidiffusion ne sont gérées. WebLogic Server est capable de s’appuyer sur les fonctionnalités de monodiffusion UDP de Microsoft Azure. Pour optimiser les résultats lorsque vous recourez à la monodiffusion UDP, nous vous recommandons d’opter pour une taille de cluster statique, ce dernier incluant un maximum de 10 serveurs gérés.

-  **WebLogic Server attend des ports publics et privés identiques pour l’accès T3 (par exemple, lorsque vous utilisez Enterprise JavaBeans).** Imaginez un scénario à plusieurs niveaux, dans lequel une application de couche de service (EJB) est en cours d’exécution sur un cluster WebLogic Server comprenant deux serveurs gérés ou plus, au sein d’un service cloud nommé **SLWLS**. Le niveau client se trouve dans un autre service cloud, qui exécute un programme Java simple s’efforçant d’appeler Enterprise JavaBeans dans la couche de service. Comme il est nécessaire d’équilibrer la couche de service, un point de terminaison public avec équilibrage de charge doit être créé pour les machines virtuelles du cluster WebLogic Server. Si le port privé que vous spécifiez pour ce point de terminaison est différent du port public (par exemple, 7006:7008), une erreur semblable à ce qui suit se produit :

		[java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

		Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

	En effet, WebLogic Server attend un port d’équilibreur de charge et un port de serveur géré WebLogic identiques pour tout accès T3 distant. Dans le cas ci-dessus, le client accède au port 7006 (port d’équilibreur de charge) et le serveur géré est à l’écoute sur le port 7008 (port privé). Remarque : cette restriction porte uniquement sur l’accès T3, et non sur le protocole HTTP.

	Pour éviter ce problème, utilisez l’une des solutions de contournement suivantes :

	-  Utilisez les mêmes numéros de port public et privé pour les points de terminaison avec équilibrage de charge dédiés à l’accès T3.

	-  Incluez le paramètre JVM suivant lors du démarrage de WebLogic Server :

			-Dweblogic.rjvm.enableprotocolswitch=true

Pour en savoir plus, consultez l’article **860340.1**, à l’adresse <http://support.oracle.com>.

-  **Limitations relatives à l’équilibrage de charge et au clustering dynamique.** Partons du principe que vous souhaitez utiliser un cluster dynamique dans WebLogic Server et l’exposer en tant que point unique de terminaison public avec équilibrage de charge. Cette opération est possible tant que vous utilisez un numéro de port fixe pour chaque serveur géré (et non affecté de manière dynamique à partir d’une plage de valeurs), et que le nombre de serveurs gérés démarrés reste inférieur à celui des machines surveillées par l’administrateur (en d’autres termes, il faut un seul serveur géré par machine virtuelle). Si, suite à votre configuration, le nombre de serveurs WebLogic démarrés est supérieur à celui des machines virtuelles (c’est-à-dire si plusieurs instances WebLogic Server partagent une même machine virtuelle), une seule instance WebLogic Server à la fois pourra être liée à un numéro de port donné ; les autres instances de la machine virtuelle échoueront. 

	D’un autre côté, si vous configurez le serveur d’administration de façon à affecter automatiquement des numéros de port uniques à ses serveurs gérés, aucun équilibrage de charge ne sera possible, car Microsoft Azure ne prend pas en charge le mappage d’un port public unique à plusieurs ports privés (opération alors requise pour cette configuration).

-  **Exécution de plusieurs instances WebLogic sur une seule machine virtuelle.** Selon les exigences liées au déploiement, peut-être envisagerez-vous d’exécuter plusieurs instances WebLogic Server sur la même machine virtuelle, si la taille de cette dernière est suffisante. Ainsi, sur une machine virtuelle de taille moyenne contenant 2 cœurs, vous pouvez opter pour l’exécution de deux instances WebLogic Server. Toutefois, nous vous recommandons malgré tout d’éviter toute introduction de points de défaillance uniques dans votre architecture (situation susceptible de survenir si vous utilisez une seule machine virtuelle exécutant plusieurs instances WebLogic Server). L’utilisation d’un minimum de deux machines virtuelles peut s’avérer plus efficace ; chaque machine virtuelle peut alors exécuter plusieurs instances WebLogic Server. Chacune de ces instances peut continuer de faire partie du même cluster. Toutefois, il n’est actuellement pas possible d’utiliser Microsoft Azure pour équilibrer la charge des points de terminaison exposés par des déploiements WebLogic Server de ce type au sein d’une même machine virtuelle. En effet, l’équilibreur de charge Microsoft Azure requiert la répartition des serveurs avec équilibrage de charge sur différentes machines virtuelles uniques.

##Images de machine virtuelle JDK Oracle

-  **Dernières mises à jour de JDK 6 et 7.** Nous recommandons d’utiliser la dernière version publique de Java prise en charge (il s’agit actuellement de la version 8). Cependant, Microsoft Azure propose également des images de JDK 6 et 7. Ces images sont destinées aux applications héritées qui ne peuvent pas encore être mises à niveau vers la version 8 de JDK. Il se peut que les mises à jour des JDK précédents ne soient plus mises à la disposition du public. Toutefois, suite au partenariat entre Microsoft et Oracle, les images de JDK 6 et 7 proposées par Microsoft Azure ont pour objectif de contenir une mise à jour plus récente, non destinée au public, qui est généralement proposée par Oracle à certains clients spécifiques bénéficiant d’un support Oracle. De nouvelles versions des images de JDK seront mises à disposition au fur et à mesure, en parallèle avec les versions de JDK 6 et 7 mises à jour.

	Remarque : le JDK disponible dans les images de JDK 6 et 7 ainsi que les images et machines virtuelles dérivées peuvent uniquement être utilisées au sein de Microsoft Azure.

-  **JDK 64 bits.** Les images de machine virtuelle Oracle WebLogic Server et les images de machine virtuelle Oracle JDK fournies par Microsoft Azure contiennent les versions 64 bits de Windows Server et de JDK.

##Ressources supplémentaires
[Liste des images de machine virtuelle Oracle](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=August15_HO6-->