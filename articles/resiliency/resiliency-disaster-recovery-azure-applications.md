<properties
   pageTitle="Récupération d’urgence des applications Microsoft Azure | Microsoft Azure"
   description="Présentations techniques et informations détaillées sur la conception d’applications pour la récupération d’urgence sur Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#Récupération d’urgence des applications développées sur Microsoft Azure

Alors que la haute disponibilité concerne la gestion des défaillances temporaires, la récupération d’urgence est conçue pour la perte catastrophique des fonctionnalités d’application. Par exemple, prenez une situation où votre région rencontre une défaillance. Dans ce cas, vous devez avoir un plan pour exécuter votre application ou accéder à vos données en dehors de la région Azure. La mise en œuvre de ce plan implique des personnes, des processus et la prise en charge d’applications permettant le fonctionnement du système. Les entrepreneurs et propriétaires de technologies qui définissent le mode opérationnel du système dans une situation d’urgence déterminent également le niveau de fonctionnalité du service en cas d’incident. Le niveau de fonctionnalité peut prendre plusieurs formes : complètement indisponible, partiellement disponible (fonctionnalité dégradée ou traitement différé) ou entièrement disponible.

##Fonctionnalités de la récupération d’urgence Azure

Comme pour les considérations relatives à la disponibilité, Azure dispose d’un [Guide technique de la résilience](./resiliency-technical-guidance.md), conçu pour prendre en charge la récupération d’urgence. Il existe également une relation entre certaines des fonctionnalités de disponibilité d’Azure et la récupération d’urgence. Par exemple, la gestion des rôles sur les domaines d’erreur augmente la disponibilité d’une application. Sans cette gestion, une défaillance matérielle non gérée deviendrait un scénario « catastrophe ». L’application correcte des stratégies et fonctionnalités de disponibilité fait partie intégrante de la protection de votre application contre les situations d’urgence. Toutefois, cet article va au-delà des problèmes de disponibilité générale pour traiter des événements d’urgence plus graves (et plus rares).

##Plusieurs régions contenant des centres de données

Azure exploite des centres de données dans plusieurs régions à l’échelle internationale. Cette infrastructure prend en charge plusieurs scénarios de récupération d’urgence, tels que la géoréplication d’Azure Storage vers des régions secondaires, fournie par le système. Vous pouvez également déployer un service cloud vers de nombreux emplacements dans le monde entier facilement et de manière économique. Comparez cela au coût et à la difficulté que représente l’exécution de vos propres centres de données dans plusieurs régions différentes. Le déploiement de services et de données dans plusieurs régions contribue à protéger votre application des pannes majeures survenant dans une seule région.

##Azure Traffic Manager

En cas de défaillance spécifique à une région, vous devez rediriger le trafic vers les services ou les déploiements dans une autre région. Vous pouvez effectuer ce routage manuellement, mais il est plus efficace d’utiliser un processus automatisé. Azure Traffic Manager est conçu pour cette tâche. Vous pouvez vous en servir pour gérer automatiquement le basculement du trafic utilisateur vers une autre région en cas de défaillance de la région primaire. Étant donné que la gestion du trafic représente un aspect majeur de la stratégie globale, il est important de comprendre les principes de base de Traffic Manager.

Dans le diagramme suivant, les utilisateurs se connectent à une URL spécifiée pour Traffic Manager (__http://myATMURL.trafficmanager.net__) qui extrait les URL réelles des sites (__http://app1URL.cloudapp.net__ et \_\_http://app2URL.cloudapp.net__. En fonction de la configuration des critères de routage des utilisateurs, ces derniers sont envoyés au site réel correct lorsque la stratégie le détermine. Les options de stratégie sont tourniquet (round robin), performance ou basculement. Dans cet article, nous traiterons uniquement l’option de basculement.

![Routage via Microsoft Azure Traffic Manager](./media/resiliency-disaster-recovery-azure-applications/routing-using-azure-traffic-manager.png)

Lorsque vous configurez Traffic Manager, vous fournissez un nouveau préfixe DNS Traffic Manager. Il s’agit du préfixe d’URL que vous fournirez à vos utilisateurs pour que ceux-ci puissent accéder à votre service. Traffic Manager extrait à présent l’équilibrage de charge à un niveau supérieur et pas au niveau régional. Le DNS Traffic Manager mappe vers un enregistrement CNAME pour tous les déploiements qu’il gère.

Dans Traffic Manager, vous spécifiez la priorité des déploiements vers lesquels les utilisateurs doivent être routés en cas de défaillance. Traffic Manager surveille les points de terminaison des déploiements et signale toute défaillance d’un déploiement principal. En cas de défaillance, Traffic Manager analyse la liste des déploiements, classée par ordre de priorité, et route les utilisateurs vers le prochain déploiement sur la liste.

Bien que Traffic Manager décide de la destination en cas de basculement, vous pouvez décider si votre domaine de basculement doit être dormant ou actif lorsque vous n’êtes pas en mode de basculement. Cette fonctionnalité n’a rien à voir avec Azure Traffic Manager. Traffic Manager détecte une défaillance du site principal et effectue la substitution vers le site de basculement. Traffic Manager effectue la substitution indépendamment du fait que ce site réponde actuellement aux demandes des utilisateurs ou non.

Pour plus d’informations sur le fonctionnement d’Azure Traffic Manager, consultez :

 * [Vue d’ensemble de Traffic Manager](../traffic-manager/traffic-manager-overview.md)
 * [Configurer la méthode de routage par basculement](../traffic-manager/traffic-manager-configure-failover-routing-method.md)

##Scénarios d’urgence Azure

Les sections suivantes abordent les différents types de scénarios d’urgence. Les interruptions de service à l’échelle régionale ne sont pas la seule cause des défaillances des applications. Elles peuvent également être provoquées par une conception médiocre ou des erreurs d’administration. Il est important de prendre en compte les causes possibles d’une défaillance lors des phases de conception et de test de votre plan de récupération. Un plan efficace tire parti des fonctionnalités d’Azure et les complète avec les stratégies conçues pour l’application. La réponse choisie est définie par l’importance de l’application, l’objectif de point de récupération (RPO) et l’objectif de délai de récupération (RTO).

###Échec de l’application

Azure Traffic Manager gère automatiquement les défaillances résultant du logiciel de système d’exploitation ou du matériel sous-jacent dans la machine virtuelle hôte. Azure crée une instance du rôle sur un serveur opérationnel et l’ajoute à la rotation d’équilibrage de charge. Si le nombre d’instances de rôle est supérieur à une instance, Azure déplace le traitement vers les autres instances de rôle en cours d’exécution, tout en remplaçant le nœud défaillant.

Des erreurs graves d’application peuvent se produire indépendamment des défaillances matérielles ou du système d’exploitation. L’application peut échouer en raison d’exceptions graves provoquées par une logique incorrecte ou des problèmes d’intégrité des données. Vous devez incorporer suffisamment de télémétrie dans le code pour qu’un système de surveillance puisse détecter les conditions d’échec et en informer un administrateur d’application. Un administrateur connaissant l’ensemble des processus de récupération d’urgence peut décider d’appeler un processus de basculement. Sinon, il peut simplement accepter un manque de disponibilité pour résoudre les erreurs critiques.

###Corruption des données

Azure stocke automatiquement vos données de la base de données SQL Azure et d’Azure Storage trois fois de manière redondante dans différents domaines d’erreur de la même région. Si vous utilisez la géoréplication, les données sont stockées dans trois domaines supplémentaires dans une autre région. Toutefois, si vos utilisateurs ou votre application endommagent ces données dans la copie principale, celles-ci sont rapidement répliquées dans les autres copies. Malheureusement, cela aura pour résultat trois copies de données endommagées.

Vous disposez de deux options pour gérer l’altération potentielle de vos données. Vous pouvez d’abord gérer une stratégie de sauvegarde personnalisée. Vous pouvez stocker vos sauvegardes dans Azure ou localement selon les besoins de votre entreprise ou la réglementation en matière de gouvernance. Une autre option consiste à utiliser la nouvelle option de limite de restauration dans le temps pour la récupération de base de données SQL. Pour plus d’informations, consultez la section consacrée aux [stratégies de données pour une récupération d’urgence](#data-strategies-for-disaster-recovery).

###Panne du réseau

Lorsque certaines parties du réseau Azure sont inaccessibles, vous ne pouvez pas toujours accéder à votre application ou à vos données. Si une ou plusieurs instances de rôle ne sont pas disponibles en raison de problèmes de réseau, Azure utilise les autres instances disponibles de votre application. Si votre application ne peut pas accéder à ses données en raison d’une panne du réseau Azure, vous pouvez éventuellement l’exécuter dans un mode dégradé localement à l’aide des données mises en cache. Vous devez créer la stratégie de récupération d’urgence pour l’exécution en mode dégradé dans votre application. Pour certaines applications, cela peut se révéler peu pratique.

Une autre option consiste à stocker des données à un autre emplacement jusqu’au rétablissement de la connectivité. Si le mode de fonctionnement dégradé n’est pas une option, il vous reste les interruptions de service ou le basculement vers une autre région. La conception d’une application exécutée en mode dégradé relève aussi bien d’une décision économique que technique. Ce sujet est abordé plus en détail dans la section [Fonctionnement des applications dégradées](#degraded-application-functionality).

###Échec d’un service dépendant

Azure fournit plusieurs services pouvant rencontrer des temps d’arrêt périodiques. Prenons l’exemple du [Cache Redis Azure](https://azure.microsoft.com/services/cache/). Ce service à l’architecture mutualisée fournit des fonctionnalités de mise en cache à votre application. Il est important de savoir ce qui se passe dans votre application en cas d’indisponibilité du service dépendant. Ce scénario est assez semblable au scénario de panne du réseau. Toutefois, le fait de considérer chaque service de manière indépendante favorise les améliorations potentielles de votre plan global.

Le Cache Redis Azure permet la mise en cache de votre application à partir de votre déploiement de service cloud, ce qui facilite la récupération d’urgence. Le service s’exécute désormais sur des rôles propres à votre déploiement. Par conséquent, vous pouvez mieux surveiller et gérer l’état du cache dans le cadre de votre processus de gestion globale pour le service cloud. Ce type de mise en cache inclut également de nouvelles fonctionnalités. L’une des nouvelles fonctionnalités est la haute disponibilité pour les données mises en cache. Cela permet de conserver les données mises en cache en cas de défaillance d’un nœud, par la conservation des copies en double sur d’autres nœuds.

Notez que la haute disponibilité diminue le débit et augmente la latence en raison de la mise à jour de la copie secondaire lors de l’écriture. Gardez également à l’esprit qu’elle double la quantité de mémoire utilisée pour chaque élément. Cet exemple montre que chaque service dépendant peut présenter des fonctionnalités pouvant améliorer votre disponibilité globale et la résistance aux défaillances catastrophiques.

Vous devez comprendre les conséquences d’une interruption de service pour chaque service dépendant. Dans l’exemple de mise en cache, il est parfois possible d’accéder aux données directement à partir d’une base de données jusqu’à ce que vous restauriez votre cache. Ce mode serait dégradé en termes de performances, mais sa fonctionnalité serait complète en ce qui concerne les données.

###Interruption du service à l’échelle régionale

Les échecs précédents étaient principalement des défaillances pouvant être gérées au sein de la même région Azure. Toutefois, vous devez également prévoir l’éventualité d’une interruption du service dans l’ensemble de la région. Le cas échéant, les copies localement redondantes de vos données ne sont pas disponibles. Si vous avez activé la géoréplication, trois copies supplémentaires de vos tables et objets blob sont stockées dans une autre région. Si Microsoft déclare la région comme étant perdue, Azure remappe toutes les entrées DNS vers la région géorépliquée.

>[AZURE.NOTE] N’oubliez pas que vous n’avez aucun contrôle sur ce processus et qu’il ne se produit que pour les interruptions du service au niveau régional. Ainsi, vous devez vous appuyer sur d’autres stratégies de sauvegarde propres à l’application pour atteindre le plus haut niveau de disponibilité. Pour plus d’informations, consultez la section consacrée aux [stratégies de données pour une récupération d’urgence](#data-strategies-for-disaster-recovery).

###Interruption du service dans l’ensemble des régions Azure

Vous devez prendre en compte l’ensemble des scénarios catastrophe lors de la planification d’urgence. L’une des interruptions de service les plus graves impliquerait l’interruption simultanée de toutes les régions Azure. Comme pour les autres interruptions de service, vous pouvez alors décider de prendre le risque d’un arrêt temporaire. Les interruptions de service importantes, couvrant des régions entières, sont beaucoup plus rares que les interruptions de service isolées impliquant des services dépendants ou des régions uniques.

Toutefois, pour certaines applications critiques, vous souhaiterez peut-être disposer d’un plan de sauvegarde pour ce scénario. Ce plan peut comprendre le basculement vers des services dans [d’autres clouds](#alternative-cloud) ou des [solutions hybrides en local et dans le cloud](#hybrid-on-premises-and-cloud-solution).

###Fonctionnalités d’application dégradée

Une application bien conçue utilise généralement un ensemble de modules qui communiquent entre eux via l’implémentation de modèles d’échange d’informations faiblement couplés. Une application prenant en charge la récupération d’urgence nécessite la séparation des tâches au niveau du module. Cela vise à éviter qu’une interruption de service dépendant ne provoque l’arrêt de l’application. Par exemple, les modules suivants peuvent composer une application de commerce en ligne pour la société Y :

 * __Catalogue de produits__ permet aux utilisateurs de parcourir les produits.
 * __Panier__ permet aux utilisateurs d’ajouter/supprimer des produits dans leur panier.
 * __État de la commande__ affiche le statut d’expédition des commandes des utilisateurs.
 * __Finalisation de la commande__ finalise la session d’achat par la soumission de la commande avec le paiement.
 * __Traitement des commandes__ valide la commande pour l’intégrité des données et contrôle les stocks.

Lorsqu’une dépendance d’un module de cette application rencontre un échec, comment le module continue-t-il à fonctionner jusqu’à la récupération de ce composant ? Un système bien conçu implémente des limites d’isolement en séparant les tâches lors de la conception et de l’exécution. Vous pouvez classer chaque erreur comme étant récupérable ou non récupérable. Les erreurs non récupérables provoqueront l’arrêt du module tandis que les erreurs récupérables peuvent être atténuées par le biais d’autres solutions. Comme vu dans la section sur la haute disponibilité, vous pouvez « cacher » certains problèmes aux utilisateurs en gérant les erreurs et en prenant d’autres mesures. En cas d’une interruption de service plus sérieuse, l’application peut s’en trouver totalement indisponible. Toutefois, une troisième option consiste à poursuivre le traitement des demandes des utilisateurs en mode dégradé.

Par exemple, si la base de données pour l’hébergement des commandes tombe en panne, le module de traitement des commandes perd sa capacité à traiter les transactions commerciales. Selon l’architecture, il peut être difficile, voire impossible pour les modules de finalisation des commandes et de traitement des commandes de continuer à fonctionner. Si l’application n’est pas conçue pour gérer ce type de scénario, cette dernière pourrait se déconnecter entièrement.

Toutefois, dans ce même scénario, il est possible que les données de produit soient stockées à un emplacement différent. Dans ce cas, le module Catalogue de produits peut toujours être utilisé pour afficher les produits. En mode dégradé, l’application continue à offrir aux utilisateurs des fonctionnalités telles que l’affichage du catalogue de produits. Les autres composants de l’application sont toutefois indisponibles, tels que les commandes ou le contrôle des stocks.

Une autre variante du mode dégradé est plus axée sur les performances que sur les fonctionnalités. Prenez l’exemple d’un scénario dans lequel le catalogue de produits est mis en cache via Cache Redis Azure. Si la mise en cache n’est plus disponible, l’application peut accéder directement au stockage serveur pour récupérer les informations du catalogue de produits. Mais cet accès peut être plus lent que la version mise en cache. Pour cette raison, les performances de l’application seront dégradées jusqu’au rétablissement complet du service caching.

Déterminer quels composants d’une application continueront à fonctionner en mode dégradé est une décision à la fois économique et technique. L’application doit également décider comment informer les utilisateurs de ces problèmes temporaires. Dans cet exemple, l’application peut autoriser l’affichage des produits, voire leur ajout au panier. Cependant, lorsque l’utilisateur tente d’effectuer un achat, l’application l’informe que le module de ventes est temporairement inaccessible. Ce n’est pas idéal pour le client, mais permet d’éviter une interruption de service de l’application dans son intégralité.

##Stratégies de données pour la récupération d’urgence

La gestion correcte des données constitue l’aspect le plus difficile d’un plan de récupération d’urgence. La restauration des données fait également partie du processus de récupération le plus chronophage. Des choix différents quant aux modes de dégradation entraînent des défis difficiles à relever en matière de récupération et de cohérence des données suite à une défaillance.

Le besoin de restaurer ou de conserver une copie des données de l’application en constitue l’un des facteurs. Vous utiliserez ces données à des fins référentielles et transactionnelles sur un site secondaire. Une configuration locale requiert un processus de planification long et coûteux afin d’implémenter une stratégie de récupération d’urgence dans plusieurs régions. Heureusement, la plupart des fournisseurs cloud, notamment Azure, permettent le déploiement d’applications dans plusieurs régions. La répartition géographique de ces régions minimise l’interruption de service dans plusieurs régions. La stratégie de gestion des données entre les régions est l’un des facteurs décisifs pour la réussite d’un plan de récupération d’urgence.

Les sections suivantes décrivent les techniques de récupération d’urgence liées aux sauvegardes de données, aux données de référence et aux données transactionnelles.

###Sauvegarde et restauration

Des sauvegardes régulières des données d’application peuvent prendre en charge certains scénarios de récupération d’urgence. Les différentes ressources de stockage nécessitent des techniques différentes.

Pour les niveaux de base, Standard et Premium de la base de données SQL, vous pouvez tirer parti de la limite de restauration dans le temps pour récupérer votre base de données. Pour en savoir plus, consultez l’article [Vue d’ensemble : continuité des activités cloud et récupération d’urgence d’une base de données avec SQL Database](../sql-database/sql-database-business-continuity.md). Une autre option consiste à utiliser la géoréplication active pour la base de données SQL. Celle-ci réplique automatiquement les modifications de base de données sur les bases de données secondaires dans la même région Azure, voire dans une région différente. Cela représente une alternative possible à certaines techniques de synchronisation de données plus manuelles, présentées dans cet article. Pour plus d’informations, consultez l’article [Vue d’ensemble : géoréplication active de base de données SQL](../sql-database/sql-database-geo-replication-overview.md).

Vous pouvez également avoir recours à une approche plus manuelle pour la sauvegarde et la restauration. Utilisez la commande DATABASE COPY pour créer une copie de la base de données. Vous devez utiliser cette commande pour obtenir une sauvegarde avec une cohérence transactionnelle. Vous pouvez également utiliser le service Import/Export de la base de données SQL Azure. Celui-ci prend en charge l’exportation des bases de données en des fichiers BACPAC, stockés dans le stockage d’objets blob Azure.

La redondance intégrée d’Azure Storage crée deux réplicas du fichier de sauvegarde dans la même région. Toutefois, la fréquence d’exécution du processus de sauvegarde détermine votre RPO, c’est-à-dire la quantité de données que vous risquez de perdre dans les scénarios d’urgence. Par exemple, imaginez que vous effectuez une sauvegarde toutes les heures et qu’un incident survient deux minutes avant la nouvelle sauvegarde. Vous perdez ainsi 58 minutes de données récoltées depuis la dernière sauvegarde. En outre, pour vous protéger contre une interruption de service à l’échelle régionale, vous devez copier les fichiers BACPAC vers une autre région. Vous avez ensuite la possibilité de restaurer ces sauvegardes dans l’autre région. Pour en savoir plus, consultez l’article [Vue d’ensemble : continuité des activités cloud et récupération d’urgence d’une base de données avec SQL Database](../sql-database/sql-database-business-continuity.md).

Pour Azure Storage, vous pouvez développer votre propre processus de sauvegarde personnalisé ou utiliser l’un des nombreux outils de sauvegarde tiers. Notez que la plupart des conceptions d’applications comportent des complexités supplémentaires dans la mesure où les ressources de stockage se référencent mutuellement. Prenez l’exemple d’une base de données SQL comportant une colonne liée à un objet blob dans Azure Storage. Si les sauvegardes ne sont pas effectuées simultanément, cela peut être dû à l’absence de sauvegarde du pointeur vers un objet blob de la base de données avant la défaillance. L’application ou le plan de récupération d’urgence doit implémenter des processus pour gérer cette incohérence une fois la récupération effectuée.

###Modèle de données de référence pour la récupération d’urgence

Les données de référence sont des données en lecture seule qui prennent en charge les fonctionnalités de l’application. Celles-ci ne changent pas souvent. La sauvegarde et restauration constitue l’une des méthodes pour gérer des interruptions de service à l’échelle régionale ; cependant, l’objectif de délai de récupération (RTO) est relativement long. Lorsque vous déployez l’application vers une région secondaire, certaines stratégies peuvent améliorer le RTO pour les données de référence.

Étant donné que les données de référence ne changent pas souvent, vous pouvez améliorer le RTO en conservant une copie permanente des données de référence dans la région secondaire. Cela élimine le temps nécessaire à la restauration des sauvegardes en cas de sinistre. Pour répondre aux exigences en matière de récupération d’urgence dans plusieurs régions, vous devez déployer l’application et les données de référence ensemble dans plusieurs régions. Comme indiqué dans la section [Modèle de données de référence pour la haute disponibilité](./resiliency-high-availability-azure-applications.md#reference-data-pattern-for-high-availability), vous pouvez déployer des données de référence vers le rôle lui-même, le stockage externe ou une combinaison des deux.

Le modèle de déploiement des données de référence au sein des nœuds de calcul répond implicitement aux exigences de récupération d’urgence. Le déploiement des données de référence vers la base de données SQL requiert le déploiement d’une copie de ces données vers chaque région. La même stratégie s’applique à Azure Storage. Vous devez déployer une copie de toutes les données de référence stockées dans Azure Storage vers les régions primaires et secondaires.

![Publication de données de référence vers les régions primaires et secondaires](./media/resiliency-disaster-recovery-azure-applications/reference-data-publication-to-both-primary-and-secondary-regions.png)

Vous devez implémenter vos routines de sauvegarde propres à l’application pour l’ensemble de vos données, y compris les données de référence. Les copies géorépliquées entre les régions ne sont utilisées qu’en cas d’interruption de service à l’échelle régionale. Pour éviter des temps d’arrêt prolongés, déployez les parties critiques des données de l’application vers la région secondaire. Pour obtenir un exemple de cette topologie, consultez le [modèle actif/passif](#active-passive).

###Modèle de données transactionnelles pour la récupération d’urgence

L’implémentation d’une stratégie de récupération d’urgence pleinement opérationnelle nécessite la réplication asynchrone des données transactionnelles vers la région secondaire. Les fenêtres temporelles pratiques dans lesquelles la réplication peut se produire déterminent les caractéristiques du RPO de l’application. Vous pourrez peut-être récupérer les données perdues de la région primaire pendant la durée de réplication. Vous serez peut-être également en mesure de fusionner avec la région secondaire ultérieurement.

Les exemples suivants d’architecture donnent quelques idées sur les différentes façons de gérer les données transactionnelles dans un scénario de basculement. Veuillez noter que ces exemples ne sont pas exhaustifs. Par exemple, les emplacements de stockage intermédiaires, tels que des files d’attente, peuvent être remplacés par la base de données SQL Azure. Ces files d’attente peuvent être issues d’Azure Storage ou d’Azure Service Bus ; consultez la page [Files d’attente Azure et files d’attente Service Bus : comparaison et différences](../service-bus/service-bus-azure-and-service-bus-queues-compared-contrasted.md). Les destinations de stockage serveur peuvent également varier (par exemple, des tables Azure au lieu de la base de données SQL). En outre, des rôles de travail peuvent être insérés en tant qu’intermédiaires lors des différentes étapes. Il est important de ne pas émuler ces architectures au détail près, mais d’envisager différentes solutions de récupération des données transactionnelles et des modules associés.

####Réplication des données transactionnelles en vue d’une récupération d’urgence

Considérez une application qui utilise des files d’attente Azure Storage pour stocker les données transactionnelles. Cela permet aux rôles de travail de traiter les données transactionnelles dans la base de données de serveur dans une architecture découplée. Cela requiert l’utilisation d’un type de mise en cache temporaire par les transactions si les rôles frontaux nécessitent l’interrogation immédiate de ces données. Selon le niveau de tolérance de perte de données, vous pouvez choisir de répliquer les files d’attente, la base de données ou l’ensemble des ressources de stockage. Si vous utilisez uniquement la réplication de base de données lorsque la région primaire échoue, vous pouvez toujours récupérer les données présentes dans les files d’attente lorsque la région primaire revient en ligne.

Le schéma suivant illustre une architecture dans laquelle la base de données du serveur est synchronisée entre les régions.

![Réplication des données transactionnelles en vue d’une récupération d’urgence](./media/resiliency-disaster-recovery-azure-applications/replicate-transactional-data-in-preparation-for-disaster-recovery.png)

Le plus gros défi que pose l’implémentation de cette architecture est la stratégie de réplication entre les régions. Le service de synchronisation des données SQL Azure active ce type de réplication. Toutefois, il s’agit d’une version préliminaire de ce service et il n’est pas recommandé de l’utiliser pour les environnements de production. Pour en savoir plus, consultez l’article [Vue d’ensemble : continuité des activités cloud et récupération d’urgence d’une base de données avec SQL Database](../sql-database/sql-database-business-continuity.md). Pour les applications de production, vous devez acheter une solution tierce ou créer votre propre logique de réplication dans le code. Selon l’architecture, la réplication peut être bidirectionnelle (plus complexe).

L’une des implémentations possibles serait d’utiliser la file d’attente intermédiaire de l’exemple précédent. Le rôle de travail qui traite les données vers la destination de stockage définitive peut apporter la modification dans la région primaire et secondaire. Ces tâches sont importantes ; cet article n’aborde cependant pas de manière détaillée la question du code de réplication. Le point essentiel est qu’une partie importante de votre temps et de vos tests doit être consacrée à la façon dont vous répliquez vos données vers la région secondaire. Des traitements et des tests supplémentaires peuvent vous permettre de vous assurer que les processus de récupération et de basculement gèrent correctement les incohérences des données ou les transactions en double.

>[AZURE.NOTE] La majeure partie de ce document porte sur la fonctionnalité PaaS (Platform as a Service). Cependant, d’autres options de réplication et de disponibilité pour les applications hybrides utilisent des machines virtuelles Azure. Ces applications hybrides utilisent la fonctionnalité IaaS (Infrastructure as a Service) pour héberger SQL Server sur des machines virtuelles dans Azure. Cela permet d’avoir des approches de disponibilité traditionnelles dans SQL Server, telles que les groupes de disponibilité AlwaysOn ou la copie des journaux de transaction. Certaines techniques, telles que AlwaysOn, fonctionnent uniquement entre les machines virtuelles Azure et les instances SQL Server. Pour plus d’informations, consultez [Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

####Mode d’application dégradé pour la capture des transactions

Considérez une deuxième architecture fonctionnant en mode dégradé. L’application de la région secondaire désactive toutes les fonctionnalités, telles que la création de comptes rendus, la Business Intelligence (BI) ou le traitement des files d’attente. Elle accepte uniquement les types de flux de travail les plus importants, tels que définis par les besoins de l’entreprise. Le système capture les transactions et les écrit dans les files d’attente. Le système peut différer le traitement des données lors de la phase initiale de l’interruption de service. Si le système de la région primaire est réactivé dans l’intervalle de temps attendu, les rôles de travail peuvent traiter les files d’attente dans la région primaire. Ce processus élimine la nécessité de fusionner les bases de données. Si l’interruption de service de la région primaire dépasse la fenêtre de temps acceptable, l’application peut commencer à traiter les files d’attente.

Dans ce scénario, la base de données de la région secondaire contient des données transactionnelles incrémentielles qui doivent être fusionnées après la réactivation de la région primaire. Le schéma suivant illustre cette stratégie pour le stockage temporaire des données transactionnelles, jusqu’à la restauration de la région primaire.

![Mode d’application dégradé pour la capture des transactions](./media/resiliency-disaster-recovery-azure-applications/degraded-application-mode-for-transaction-capture.png)

Pour plus d’informations sur les techniques de gestion de données pour les applications Azure résilientes, consultez l’article [Failsafe: guidance for resilient cloud architectures](https://channel9.msdn.com/Series/FailSafe) (Sécurité intégrée : recommandations applicables à la résilience des architectures cloud).

##Topologies de déploiement pour la récupération d’urgence

Vous devez préparer les applications critiques à l’éventualité d’une interruption de service à l’échelle régionale. Pour cela, vous devez incorporer à la planification opérationnelle une stratégie de déploiement de plusieurs régions.

Les déploiements de plusieurs régions peuvent impliquer la publication de l’application et des données de référence par des processus professionnels de l’informatique vers la région secondaire suite à un incident. Si l’application requiert un basculement instantané, le processus de déploiement peut comprendre une configuration en mode actif/passif ou en mode actif/actif. Pour ce type de déploiement, des instances existantes de l’application s’exécutent dans l’autre région. Un outil de routage tel qu’Azure Traffic Manager fournit des services d’équilibrage de charge au niveau du DNS. Il peut détecter des interruptions de service et router les utilisateurs vers différentes régions lorsque cela s’avère nécessaire.

Une récupération d’urgence Azure efficace réussit à intégrer cette récupération dans la solution dès le début. Le cloud propose des options supplémentaires pour la récupération des données lors d’un incident ; celles-ci ne sont pas proposées par un fournisseur d’hébergement traditionnel. Plus précisément, vous pouvez rapidement et dynamiquement affecter les ressources à une autre région. De cette manière, les ressources inactives ne vous coûteront que très peu en attendant qu’une erreur se produise.

Les sections suivantes abordent différentes topologies de déploiement pour la récupération d’urgence. En règle générale, il existe un compromis entre l’augmentation des coûts ou la complexité liée à une disponibilité accrue.

###Déploiement dans une seule région

Un déploiement dans une seule région ne constitue pas réellement une topologie de récupération d’urgence, mais sert plutôt à créer un contraste avec les autres architectures. Les déploiements dans une seule région sont courants pour les applications dans Azure. Cependant, ce type de déploiement ne peut pas servir en tant que plan de récupération d’urgence.

Le diagramme suivant illustre une application exécutée dans une seule région Azure. Azure Traffic Manager et l’utilisation des domaines d’erreur et de mise à niveau augmentent la disponibilité de l’application dans la région.

![Déploiement dans une seule région](./media/resiliency-disaster-recovery-azure-applications/single-region-deployment.png)

Ici, il est évident que la base de données constitue un point de défaillance unique. Bien qu’Azure réplique les données dans différents domaines d’erreur vers des réplicas internes, tout cela se produit dans la même région. L’application ne peut pas résister aux pannes catastrophiques. Si la région rencontre une défaillance, il en est de même pour l’ensemble des domaines d’erreur, ce qui inclut toutes les instances de service et les ressources de stockage.

Pour toutes les applications les moins critiques, vous devez concevoir un plan pour déployer vos applications dans plusieurs régions. Vous devez également considérer le RTO et les contraintes de coût lorsque vous décidez quelle topologie de déploiement utiliser.

Examinons à présent les modèles spécifiques pour prendre en charge le basculement entre les différentes régions. Ces exemples utilisent tous deux régions pour décrire le processus.

###Redéploiement vers une région Azure secondaire

Dans le modèle de redéploiement vers une région secondaire, seule la région primaire contient des applications et des bases de données en cours d’exécution. La région secondaire n’est pas configurée pour un basculement automatique. Par conséquent, en cas d’incident, vous devez activer toutes les parties du service dans la nouvelle région. Cela inclut le chargement d’un service cloud sur Azure, le déploiement du service cloud, la restauration des données et la modification du DNS afin de rerouter le trafic.

Bien que cette option soit la plus abordable des options couvrant plusieurs régions, elle présente les caractéristiques RTO les moins avantageuses. Dans ce modèle, les sauvegardes de base de données et de packages de service sont stockées localement ou dans l’instance du stockage d’objets blob Azure de la région secondaire. Toutefois, vous devez déployer un nouveau service et restaurer les données avant la reprise des opérations du modèle. Même si vous automatisez entièrement le transfert de données du stockage secondaire, la mise en place d’un nouvel environnement de base de données est très chronophage. Le déplacement des données à partir du stockage de sauvegarde sur disque vers la base de données vide de la région secondaire constitue la partie la plus coûteuse du processus de restauration. Cette opération est cependant nécessaire pour que la nouvelle base de données puisse fonctionner, cette dernière n’étant pas répliquée.

La meilleure approche consiste à stocker les packages de service dans le stockage d’objets blob de la région secondaire. Cela élimine la nécessité de charger le package vers Azure ; c’est ce qui se produit lorsqu’un déploiement est effectué à partir d’un ordinateur de développement local. À l’aide des scripts PowerShell, vous pouvez rapidement déployer les packages de service vers un nouveau service cloud à partir d’un stockage d’objets blob.

Cette option est seulement pratique pour les applications non critiques pouvant tolérer un RTO élevé. Cela peut, par exemple, fonctionner pour une application pouvant être arrêtée plusieurs heures, mais qui doit s’exécuter à nouveau dans les 24 heures.

![Redéploiement vers une région Azure secondaire](./media/resiliency-disaster-recovery-azure-applications/redeploy-to-a-secondary-azure-region.png)

###Actif/Passif

Le modèle actif/passif a été adopté par de nombreuses sociétés. Ce modèle apporte des améliorations au RTO, avec une augmentation relativement faible du coût par rapport au modèle de redéploiement. Dans ce scénario, des régions Azure primaire et secondaire sont de nouveau présentes. Tout le trafic est acheminé vers le déploiement actif de la région primaire. La région secondaire est mieux préparée pour la récupération d’urgence, car la base de données s’exécute dans les deux régions. En outre, un mécanisme de synchronisation est mis en place entre les deux régions. Cette approche de secours comprend deux variantes : une approche basée uniquement sur la base de données ou un déploiement complet dans la région secondaire.

####Base de données uniquement

Dans la première variante du modèle actif/passif, seule la région primaire contient une application de service cloud déployée. Toutefois, contrairement au modèle de redéploiement, les deux régions sont synchronisées avec les contenus de la base de données. (Pour plus d’informations, consultez la section consacrée au [modèle de données transactionnel pour une récupération d’urgence](#transactional-data-pattern-for-disaster-recovery).) En cas d’incident, les exigences d’activation sont réduites. Vous démarrez l’application dans la région secondaire, modifiez les chaînes de connexion à la nouvelle base de données et modifiez les entrées DNS afin de rerouter le trafic.

Comme pour le modèle de redéploiement, vous devez déjà avoir stocké les packages de service dans le stockage d’objets blob Azure dans la région secondaire pour un déploiement plus rapide. Contrairement au modèle de redéploiement, vous échappez à la majorité du traitement que nécessite l’opération de restauration de base de données. La base de données est prête et en cours d’exécution. Ce modèle permet de gagner beaucoup de temps, ce qui en fait une solution de récupération d’urgence économique. C’est aussi le modèle de récupération d’urgence le plus populaire.

![Actif/passif : base de données uniquement](./media/resiliency-disaster-recovery-azure-applications/active-passive-database-only.png)

####Réplica complet

Dans la deuxième variante du modèle actif/passif, la région primaire et la région secondaire connaissent un déploiement complet. Ce déploiement inclut les services cloud et une base de données synchronisée. Toutefois, seule la région primaire traite activement les requêtes réseau des utilisateurs. La région secondaire devient active uniquement lorsque la région primaire subit une interruption de service. Dans ce cas, toutes les nouvelles requêtes réseau effectuent le routage vers la région secondaire. Azure Traffic Manager gère automatiquement ce basculement.

Le basculement se produit plus rapidement que dans la variante contenant uniquement la base de données, les services étant déjà déployés. Ce modèle fournit un objectif de délai de récupération (RTO) très faible. La région de basculement secondaire doit être opérationnelle dès que la région primaire rencontre une défaillance.

Avec un temps de réponse plus rapide, ce modèle présente l’avantage de préaffecter et déployer des services de sauvegarde. Vous n’avez pas à vous soucier du fait qu’une région ne dispose pas d’assez d’espace pour y allouer de nouvelles instances en cas de sinistre. C’est un élément important si votre région Azure secondaire est proche de sa capacité maximale. Il n’existe aucune garantie (SLA) selon laquelle vous pourrez immédiatement déployer un nombre quelconque de nouveaux services cloud dans n’importe quelle région.

Pour obtenir un temps de réponse optimal avec ce modèle, vous devez disposer d’une échelle semblable (nombre d’instances de rôle) dans les régions primaires et secondaires. Malgré les avantages, le fait de payer pour des instances de calcul inutilisées se révèle coûteux ; cela peut ne pas être le meilleur choix d’un point de vue financier. Pour cette raison, il est plus courant d’utiliser une version des services cloud dont la puissance a été légèrement réduite dans la région secondaire. Vous pouvez ensuite rapidement faire basculer et monter en charge le déploiement secondaire, si nécessaire. Vous devriez automatiser le processus de basculement afin de pouvoir activer des instances supplémentaires, selon la charge, lorsque la région primaire devient inaccessible. Cela peut impliquer l’utilisation d’un mécanisme de mise à l’échelle automatique, tel que des [jeux de mise à l’échelle de machine virtuelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Le schéma suivant présente un modèle dans lequel les régions primaires et secondaires contiennent un service cloud entièrement déployé selon un modèle actif/passif.

![Actif/passif : réplica complet](./media/resiliency-disaster-recovery-azure-applications/active-passive-full-replica.png)

###Actif/actif

À ce stade, vous avez sans doute compris l’évolution des modèles : diminuer le RTO augmente les coûts et la complexité. La solution actif/actif rompt avec cette tendance d’un point de vue financier.

Dans un modèle actif/actif, les services cloud et la base de données sont entièrement déployés dans les deux régions. Contrairement au modèle actif/passif, les deux régions reçoivent le trafic utilisateur. Cette option réduit au minimum le temps de récupération. Les services ont déjà été mis à l’échelle pour gérer une partie de la charge dans chaque région. DNS est déjà activé pour une utilisation dans la région secondaire. La décision visant à router les utilisateurs vers la région appropriée présente des difficultés supplémentaires. La planification par tourniquet (round robin) pourrait se révéler une option. Il est plus probable que certains utilisateurs utilisent une région dans laquelle se trouve la copie principale de leurs données.

En cas de basculement, vous devez simplement désactiver DNS dans la région primaire. Ainsi, tout le trafic est acheminé vers la région secondaire.

Ce modèle présente également des variantes. Par exemple, le schéma suivant présente un modèle dans lequel la région primaire possède la copie principale de la base de données. Les services cloud des deux régions écrivent vers cette base de données primaire. Le déploiement secondaire peut effectuer la lecture à partir de la base de données principale ou répliquée. Dans cet exemple, la réplication est unidirectionnelle.

![Actif/actif](./media/resiliency-disaster-recovery-azure-applications/active-active.png)

L’architecture actif/actif du schéma précédent présente cependant un inconvénient. La deuxième région doit accéder à la base de données de la première région car cette dernière détient la copie principale. Les performances connaissent une forte baisse lorsque vous accédez à des données en dehors d’une région. Pour les appels de base de données entre les régions, vous devez envisager une stratégie de traitement par lots afin d’améliorer les performances de ces appels. Pour en savoir plus, consultez l’article [Comment utiliser le traitement par lots pour améliorer les performances des applications de base de données SQL](../sql-database/sql-database-use-batching-to-improve-performance.md).

Dans une autre architecture possible, chaque région peut directement accéder à sa propre base de données. Dans ce modèle, un type quelconque de réplication bidirectionnelle peut être nécessaire pour synchroniser les bases de données dans chaque région.

Dans le modèle actif/actif, la région primaire peut ne pas nécessiter autant d’instances que dans le modèle actif/passif. Si, dans une architecture actif/passif, 10 instances sont présentes dans la région primaire, une architecture actif/actif ne nécessiterait en revanche que 5 instances dans chaque région. Les deux régions partagent à présent la même charge. Cela peut représenter des économies d’argent par rapport au modèle actif/passif si vous avez conservé une solution de secours actif pour la région passive, avec 10 instances en attente de basculement.

Sachez que, jusqu’à ce que vous restauriez la région primaire, la région secondaire peut connaître une hausse soudaine du nombre de nouveaux utilisateurs. Si chaque serveur accueille 10 000 utilisateurs au moment de l’interruption de service de la région primaire, la région secondaire doit soudain gérer 20 000 utilisateurs. Les règles de surveillance de la région secondaire doivent détecter cette hausse et donc doubler le nombre d’instances présentes dans la région secondaire. Pour plus d’informations à ce sujet, consultez la section [Détection des défaillances](#failure-detection).

##Solution hybride en local et dans le cloud

Une stratégie supplémentaire de récupération d’urgence consiste à créer une application hybride qui s’exécute à la fois en local et dans le cloud. La région primaire peut se trouver à l’un des deux emplacements, selon l’application. Prenez l’exemple des architectures précédentes et imaginez la région primaire ou secondaire comme étant un emplacement local.

Ces architectures hybrides posent certains défis. Cet article a traité en grande partie des modèles d’architecture PaaS. Les applications PaaS classiques dans Azure s’appuient sur des constructions Azure spécifiques, telles que les rôles, les services cloud et Traffic Manager. La création d’une solution locale pour ce type d’application PaaS nécessiterait une architecture bien plus différente. Cela peut être dissuasif d’un point de vue financier ou administratif.

Cependant, une solution hybride de récupération d’urgence pose moins de défis que les architectures traditionnelles ayant simplement été déplacées vers le cloud. Cela se vérifie avec les architectures utilisant IaaS. Les applications IaaS utilisent des machines virtuelles dans le cloud pouvant avoir des équivalents directs en local. Vous pouvez utiliser des réseaux virtuels pour connecter des machines virtuelles dans le cloud aux ressources du réseau local. Cela crée davantage de possibilités, ce qui ne serait pas possible avec les applications basées uniquement sur PaaS. Par exemple, SQL Server peut ainsi tirer parti de solutions de récupération d’urgence telles que les groupes de disponibilité AlwaysOn et la mise en miroir des bases de données. Pour en savoir plus, consultez [Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

Les solutions IaaS fournissent également un moyen plus simple aux applications locales d’utiliser Azure comme option de basculement. Vous pourriez avoir une application entièrement fonctionnelle dans une région locale existante. Mais que se passe-t-il si vous ne disposez pas d’assez de ressources pour entretenir une région géographiquement distincte pour le basculement ? Vous pouvez décider d’utiliser des machines virtuelles et des réseaux virtuels pour exécuter votre application dans Azure. Dans ce cas, définissez des processus pouvant synchroniser des données vers le cloud. Le déploiement Azure devient ensuite la région secondaire à utiliser pour le basculement. La région primaire reste l’application en local. Pour plus d’informations sur les architectures et les fonctionnalités IaaS, consultez la [documentation sur les machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/).

##Autre cloud

Dans certaines situations, même la fiabilité d’un cloud Microsoft peut ne pas satisfaire aux règles de conformité internes ou aux stratégies exigées par votre organisation. Même la meilleure préparation et conception pour l’implémentation des systèmes de sauvegarde en cas d’incident peuvent faillir dans le scénario où le fournisseur de service cloud rencontrait une interruption généralisée du service.

Vous devriez comparer les exigences en matière de disponibilité au coût et à la complexité que représente une disponibilité accrue. Effectuez une analyse des risques et définissez le RTO et le RPO pour votre solution. Si votre application ne peut tolérer aucun temps d’arrêt, il peut être judicieux pour vous d’envisager une autre solution cloud. À moins qu’Internet rencontre une panne générale, une autre solution cloud peut toujours être disponible si les services Azure tombent en panne dans le monde entier.

Comme pour le scénario hybride, les déploiements de basculement dans les architectures de récupération d’urgence précédentes peuvent également exister dans une autre solution cloud. D’autres sites de récupération d’urgence par cloud doivent être utilisés uniquement pour les solutions dont le RTO autorise peu de temps d’arrêt, voire aucun. Notez qu’une solution utilisant un site de récupération d’urgence en dehors d’Azure nécessite plus de travail pour la configuration, le développement, le déploiement ainsi que l’entretien. Il est également plus difficile d’implémenter les meilleures pratiques dans une architecture intégrant plusieurs clouds. Bien que les plateformes cloud aient des concepts de haut niveau similaires, les API et les architectures sont différentes.

Si vous décidez de fractionner la récupération d’urgence entre différentes plateformes, il serait judicieux d’intégrer des couches d’abstraction dans la solution. Si vous le faites, vous n’aurez pas à développer et entretenir deux versions différentes de la même application pour différentes plateformes cloud en cas d’urgence. Comme pour le scénario hybride, l’utilisation de machines virtuelles Azure ou d’Azure Container Service peut s’avérer plus facile dans ce cas que l’utilisation des conceptions PaaS propres au cloud.

##Automatisation

Certains des modèles présentés nécessitent l’activation rapide des déploiements hors connexion, ainsi que la restauration d’éléments spécifiques d’un système. L’automation, ou la création de scripts, prend en charge la possibilité d’activer des ressources à la demande et de déployer des solutions rapidement. Dans cet article, l’automation associée à la récupération d’urgence est assimilée à [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx), mais [l’API REST de gestion de service](https://msdn.microsoft.com/library/azure/ee460799.aspx) constitue également une option.

Le développement de scripts aide à gérer certains aspects de la récupération d’urgence qu’Azure ne gère pas de manière transparente. Cela présente l’avantage de produire des résultats cohérents à chaque fois, ce qui réduit le risque d’erreur humaine. Le fait de prédéfinir les scripts de récupération d’urgence réduit également le temps nécessaire pour reconstruire un système et ses composants lors d’un sinistre. Il n’est pas judicieux de rechercher manuellement des solutions pour restaurer votre site alors qu’il est hors connexion ; cela vous fait perdre de l’argent à chaque minute qui passe.

Après avoir créé les scripts, testez-les à plusieurs reprises du début à la fin. Après avoir vérifié leurs fonctionnalités de base, testez-les en [simulant un incident](#disaster-simulation). Cela permet de détecter les imperfections dans les scripts ou les processus.

Une meilleure pratique de l’automation consiste à créer un référentiel de scripts PowerShell ou de scripts d’interface de ligne de commande (CLI) de récupération d’urgence Azure. Marquez-les et classez-les clairement pour simplifier la recherche. Désignez une personne pour gérer le référentiel et le contrôle de version des scripts. Documentez ces derniers de manière exhaustive avec des explications des paramètres et des exemples d’utilisation de script. Assurez-vous que cette documentation est synchronisée avec vos déploiements Azure. Cela souligne l’importance d’avoir une seule personne responsable de l’ensemble du référentiel.

##Détection des défaillances

Pour gérer correctement les problèmes liés à la disponibilité et à la récupération d’urgence, vous devez être en mesure de détecter et diagnostiquer les défaillances. Vous devez surveiller de manière étendue les serveurs et les déploiements afin de savoir rapidement si un système ou ses composants tombent en panne. Les outils d’analyse qui examinent l’intégrité globale du service cloud et de ses dépendances peuvent effectuer une partie de ce travail. L’un des outils Microsoft appropriés est [System Center 2016](https://www.microsoft.com/fr-FR/server-cloud/products/system-center-2016/). Des outils tiers proposent également des fonctionnalités d’analyse. La plupart des solutions d’analyse suivent les principaux compteurs de performance et de disponibilité des services.

Bien que ces outils soient essentiels, ils ne remplacent pas la planification de la détection des erreurs et de création de rapports au sein d’un service cloud. Vous devez planifier l’utilisation appropriée des diagnostics Microsoft Azure. Des compteurs de performance personnalisés ou des entrées du journal des événements peuvent également faire partie de la stratégie globale. Cela fournit davantage de données lors des défaillances, ce qui permet de diagnostiquer le problème rapidement et de restaurer l’ensemble des fonctionnalités. Des métriques supplémentaires sont également fournies qui permettent aux outils d’analyse de déterminer l’intégrité de l’application. Pour plus d’informations, consultez la page [Activation des diagnostics Azure dans Azure Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics.md). Pour une présentation de la planification d’un « modèle d’intégrité » général, consultez [Failsafe: Guidance for Resilient Cloud Architectures](https://channel9.msdn.com/Series/FailSafe) (Sécurité intégrée : recommandations applicables à la résilience des architectures cloud).

##Simulation d’incident

Les tests de simulation impliquent la création de petites situations réelles sur le lieu de travail afin d’observer la réaction des membres de l’équipe. Les simulations permettent également d’avoir une indication de l’efficacité des solutions dans le plan de récupération. Réalisez ces simulations de telle sorte que les scénarios créés ne perturbent pas votre activité commerciale, tout en donnant l’impression d’une situation « réelle ».

Envisagez de créer un type de « menu général » dans l’application pour simuler manuellement les problèmes de disponibilité. Par exemple : déclenchez, à l’aide d’un commutateur logiciel, des exceptions d’accès à la base de données pour un module de commande en provoquant son dysfonctionnement. Vous pouvez mener des approches légères similaires pour d’autres modules au niveau de l’interface réseau.

La simulation souligne les problèmes non résolus. Les scénarios simulés doivent être entièrement contrôlables. Cela signifie que, même si le plan de récupération échoue, vous pouvez restaurer l’état normal sans entraîner de dommage significatif. Il est également important que vous informiez vos dirigeants de quand et comment les exercices de simulation seront conduits. Ce plan doit comprendre des informations sur le temps ou les ressources qui peuvent s’avérer improductives pendant l’exécution du test de simulation. Lorsque vous testez votre plan de récupération d’urgence, il est également important de déterminer comment la réussite sera mesurée.

Plusieurs autres techniques existent pour tester les plans de récupération d’urgence. Toutefois, la plupart d’entre elles sont simplement des versions modifiées de ces techniques de base. Le principal objectif de ce test consiste à évaluer la faisabilité et la fiabilité du plan de récupération. Les tests des plans de récupération d’urgence recherchent tout défaut présent dans ces plans.

##Étapes suivantes

Cet article fait partie d’une série d’articles axés sur la thématique [Récupération d’urgence et haute disponibilité des applications développées sur Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). L’article précédent de cette série est [Haute disponibilité pour les applications développées sur Microsoft Azure](./resiliency-high-availability-azure-applications.md).

<!---HONumber=AcomDC_0824_2016-->