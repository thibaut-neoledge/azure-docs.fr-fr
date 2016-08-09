<properties
   pageTitle="Haute disponibilité des applications Azure | Microsoft Azure"
   description="Présentation technique et informations détaillées sur la conception et le développement d’applications pour la haute disponibilité sur Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="hanuk;jroth;aglick"/>

#Haute disponibilité pour les applications développées sur Microsoft Azure

Une application hautement disponible absorbe les variations de disponibilité, de charge et les défaillances temporaires des services dépendants et du matériel. L’application continue à fonctionner correctement aux niveaux systémique et de l’utilisateur, tel que défini par les besoins de votre entreprise ou les contrats de niveau de service de l’application.

##Fonctionnalités de haute disponibilité Azure

Azure dispose de nombreuses fonctionnalités intégrées de plateforme qui prennent en charge des applications hautement disponibles. Cette section décrit certaines de ces fonctionnalités clés. Pour une analyse plus complète de la plateforme, consultez la page [Guide technique de la résilience Azure](./resiliency-technical-guidance.md).

###Contrôleur de structure

Le contrôleur de structure Azure approvisionne et surveille l’état des instances de calcul Azure. Le contrôleur de structure vérifie l’état du matériel et des logiciels des instances hôtes et de la machine invitée. Lorsqu’il détecte une défaillance, il applique les contrats de niveau de service en déplaçant automatiquement les instances de machines virtuelles. Le concept de domaines d’erreur et de mise à niveau englobe en outre les contrats de niveau de service de calcul.

Quand plusieurs instances de rôle de service cloud sont déployées, Azure les répartit sur différents domaines d’erreur. En fait, une limite de domaine d’erreur est un autre rack matériel de la région. Les domaines d’erreur réduisent la probabilité qu’une défaillance matérielle localisée interrompe le service d’une application. Vous ne pouvez pas gérer le nombre de domaines d’erreur alloués à vos rôles web ou de travail. Le contrôleur de structure sollicite des ressources dédiées qui sont séparées des applications Azure hébergées. Il présente un temps d’activité de 100 %, dans la mesure où il constitue le noyau du système Azure. Il surveille et gère les instances de rôle au sein des domaines d’erreur.

Le diagramme suivant représente les ressources Azure partagées qui sont déployées et gérées par le contrôleur de structure au sein des différents domaines d’erreur.

![Vue simplifiée de l’isolement de domaines d’erreur](./media/resiliency-high-availability-azure-applications/fault-domain-isolation.png)

Les domaines de mise à niveau, qui présentent une fonctionnalité similaire à celle des domaines d’erreur, prennent en charge des mises à niveau, non des défaillances. Un domaine de mise à niveau est une unité logique de séparation d’instance qui détermine les instances d’un service spécifique à mettre à niveau à un point donné dans le temps. Par défaut, pour votre déploiement de service hébergé, cinq domaines de mise à niveau sont définis. Toutefois, vous pouvez modifier cette valeur dans le fichier de définition de service. Par exemple, imaginez que vous disposez de huit instances de votre rôle Web. Deux instances seront affectées à trois domaines de mise à niveau, et deux instances à un autre domaine de mise à niveau. Azure définit la séquence de mise à jour, qui est par ailleurs basée sur le nombre de domaines de mise à niveau. Pour plus d’informations sur les domaines de mise à niveau, consultez [Mettre à jour un service cloud](../cloud-services/cloud-services-update-azure-service.md).

###Fonctionnalités dans d’autres services

En plus des fonctionnalités de plateforme qui prennent en charge une haute disponibilité de calcul, Azure intègre des fonctionnalités de haute disponibilité dans ses autres services. Par exemple, Azure Storage conserve trois réplicas de l’ensemble des données d’objets blob, de tables et de files d’attente. La solution prend également en charge l’option de géoréplication pour stocker des sauvegardes d’objets blob et de tables dans une région secondaire. Le réseau de distribution de contenu Azure permet la mise en cache d’objets blob dans le monde entier, à des fins de redondance et d’extensibilité. La Base de données SQL Azure gère également plusieurs réplicas.

En plus de la série d’articles [Guide technique de la résilience Azure](https://aka.ms/bctechguide), consultez le document [Meilleures pratiques pour la création de services à grande échelle sur les services Azure Cloud Services](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/). Ces références approfondissent la thématique sur les fonctionnalités de disponibilité de la plateforme Azure.

Bien qu’Azure offre de nombreuses fonctionnalités qui prennent en charge la haute disponibilité, il est important de comprendre leurs limitations :

- Pour le calcul, Azure garantit que vos rôles sont disponibles et opérationnels, mais ne sait pas si votre application est exécutée ou surchargée.
- Pour la Base de données SQL Azure, les données sont répliquées de manière synchrone au sein de la région. Vous pouvez choisir la géoréplication active, qui autorise jusqu’à quatre copies supplémentaires de base de données dans la même région (ou différentes régions). Ces réplicas de base de données ne sont pas des sauvegardes ponctuelles. Les bases de données SQL fournissent des fonctionnalités de sauvegarde ponctuelle. Pour en savoir plus sur les fonctionnalités de limite dans le temps de la Base de données SQL, consultez [ Azure SQL Database Point in Time Restore](https://azure.microsoft.com/blog/azure-sql-database-point-in-time-restore/) (Limite de restauration dans le temps de la Base de données SQL Azure).
- Pour Azure Storage, les données de tables et des objets blob sont répliquées par défaut dans une autre région. Toutefois, vous ne pouvez pas accéder aux réplicas avant que Microsoft ne décide de basculer sur l’autre site. Un basculement de région se produit uniquement en cas d’interruption de service prolongée à l’échelle de la région, et il n’existe aucun contrat de niveau de service dédié au temps de basculement géographique. Il est également important de noter que les corruptions de données se propagent rapidement entre les réplicas.

Pour ces raisons, vous devez compléter les fonctionnalités de disponibilité de plateforme par les fonctionnalités de disponibilité propres aux applications. Les fonctionnalités de disponibilité d’application incluent la fonction d’instantané d’objet blob pour créer des sauvegardes ponctuelles de données d’objets blob.

###Groupes à haute disponibilité pour Azure Virtual Machines

La majeure partie de cet article est axée sur les services cloud qui utilisent un modèle Platform as a Service (PaaS). Toutefois, il existe également des fonctions de disponibilité spécifiques pour Azure Virtual Machines qui utilisent un modèle Infrastructure as a Service (IaaS). Pour atteindre vos objectifs de haute disponibilité avec Virtual Machines, vous devez utiliser des groupes à haute disponibilité. Un groupe à haute disponibilité joue un rôle identique avec les domaines d’erreur et de mise à niveau. Au sein d’un groupe à haute disponibilité, Azure positionne les machines virtuelles de manière à ce que les défaillances matérielles localisées et les activités de maintenance ne provoquent pas la panne de l’ensemble des machines de ce groupe. Des groupes à haute disponibilité sont requis pour respecter les contrats de niveau de service dédiés à la disponibilité de Virtual Machines.

Le diagramme suivant propose une représentation de deux groupes à haute disponibilité qui réunissent les machines virtuelles Web et SQL Server, respectivement.

![Groupes à haute disponibilité pour Azure Virtual Machines](./media/resiliency-high-availability-azure-applications/availability-set-for-azure-virtual-machines.png)

>[AZURE.NOTE] Dans le diagramme précédent, SQL Server est installé et exécuté sur des machines virtuelles. Cette configuration est différente de la précédente description relative à la Base de données SQL Azure, dans laquelle la base de données est fournie en tant que service géré.

##Stratégies d’application pour la haute disponibilité

La plupart des stratégies d’application dédiées à la haute disponibilité impliquent la redondance ou la suppression de dépendances rigides entre les composants applicatifs. La conception des applications doit prendre en charge la tolérance de panne durant les temps d’arrêt sporadiques des services Azure ou tiers. Les sections suivantes décrivent des modèles d’application dédiés à l’augmentation de la disponibilité de vos services cloud.

###Communication asynchrone et files d’attente durables

Pensez à la communication asynchrone intervenant entre des services faiblement couplés pour augmenter la disponibilité dans les applications Azure. Dans ce modèle, vous rédigez des messages transmis vers des files d’attente de stockage ou des files d’attente Azure Service Bus, pour traitement ultérieur. Lorsque vous rédigez le message vers la file d’attente, le contrôle revient immédiatement à l’expéditeur du message. Un autre niveau de l’application gère le traitement du message, généralement implémenté en tant que rôle de travail. Si le rôle de travail tombe en panne, les messages s’accumulent dans la file d’attente, jusqu’à restauration du service de traitement. Tant que la file d’attente reste disponible, il n’existe aucune dépendance directe entre l’expéditeur frontal et le processeur de message. Cela élimine la nécessité des appels synchrones du service, qui peuvent constituer un goulot d’étranglement du débit dans les applications distribuées.

Une variante de cette fonctionnalité utilise Azure Storage (objets blob, tables, files d’attente) ou les files d’attente Service Bus en tant qu’emplacements de basculement des appels échoués de la base de données. Par exemple, un appel synchrone au sein d’une application vers un autre service (comme la Base de données SQL Azure) échoue de façon répétée. Vous pouvez être en mesure de sérialiser ces données en instances de stockage durable. Ultérieurement, lors de la remise en ligne du service ou de la base de données, l’application peut de nouveau envoyer la requête à partir du stockage. Cependant, dans ce modèle, l’emplacement intermédiaire n’est pas une composante constante du workflow applicatif. Il est utilisé uniquement dans les scénarios de défaillance.

Dans les deux scénarios, une communication asynchrone et un stockage intermédiaire empêchent que la panne d’un service principal n’arrête l’intégralité de l’application. Les files d’attente font office d’intermédiaires logiques. Pour plus d’informations sur la sélection du service approprié de file d’attente, consultez [Files d’attente Azure et files d’attente Service Bus : comparaison et différences](../service-bus/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

###Logique de détection des erreurs et de nouvelle tentative

L’un des composants clés dans la conception d’une application hautement disponible est la capacité à utiliser une logique de nouvelle tentative au sein du code pour faciliter le traitement d’un service temporairement en panne. Le [bloc applicatif de gestion des erreurs temporaires](https://msdn.microsoft.com/library/hh680934.aspx), développé par l’équipe Microsoft en charge des modèles et des pratiques, assiste les développeurs d’applications dans le cadre de ce processus. Le mot « temporaire » désigne un état non permanent, défini pour un intervalle de temps relativement court. Dans le cadre de cet article, le traitement des défaillances temporaires fait partie intégrante du développement d’une application hautement disponible. Parmi les états temporaires possibles, citons les erreurs intermittentes de réseaux, ou encore les pertes de connexions de bases de données.

Le bloc applicatif de gestion des erreurs temporaires constitue un moyen simple de traiter sans heurt les défaillances au sein de votre code. Vous pouvez l’utiliser pour améliorer la disponibilité de vos applications, en ajoutant une logique robuste de traitement des erreurs temporaires. Dans la plupart des cas, la logique de nouvelle tentative gère la brève interruption et reconnecte l’expéditeur et le destinataire après une ou plusieurs tentatives échouées. Une tentative réussie passe généralement inaperçue auprès des utilisateurs de l’application.

Les développeurs disposent de trois options de gestion de leur logique de nouvelle tentative : incrémentielle, à intervalle fixe et exponentielle. Avec la logique incrémentielle, vous attendez généralement plus longtemps entre chaque tentative, suivant un modèle linéaire croissant (par exemple, 1, 2, 3 et 4 secondes). Avec la logique à intervalle fixe, vous attendez autant de temps entre chaque tentative (par exemple, 2 secondes). Si vous recherchez une option plus aléatoire, sachez que la temporisation exponentielle vous fait attendre plus longtemps entre chaque tentative. Toutefois, ce modèle valorise un comportement exponentiel (par exemple, 2,4, 8 et 16 secondes).

Au sein de votre code, la stratégie de haut niveau se présente ainsi :

1. Définissez votre stratégie de nouvelle tentative.
1. Essayez d’exécuter l’opération pouvant résulter en une erreur temporaire.
1. Si une erreur temporaire se produit, invoquez la stratégie de nouvelle tentative.
1. Si toutes les tentatives échouent, interceptez une exception finale.

Testez votre logique de nouvelle tentative sur les défaillances simulées pour vous assurer que les nouvelles tentatives sur des opérations successives n’entraînent pas un délai d’une longueur non anticipée. Effectuez cette action avant de décider de basculer l’intégralité de la tâche.

###Modèle de données de référence pour la haute disponibilité

Les données de référence sont les données en lecture seule d’une application. Ces données fournissent le contexte professionnel au sein duquel l’application génère les données transactionnelles durant une opération commerciale. Les données transactionnelles sont une fonction ponctuelle des données de référence. Par conséquent, leur intégrité dépend de l’instantané des données de référence au moment de la transaction. Cette définition, imprécise, devrait s’avérer suffisante ici.

Les données de référence d’une application sont nécessaires à son bon fonctionnement. Les applications respectives créent et gèrent les données de référence ; les systèmes de gestion des données de référence (MDM, Master Data Management) remplissent souvent cette fonction. Ces systèmes sont responsables du cycle de vie des données de référence. Parmi les données de référence, citons les catalogues de produits, les informations sur les employés, sur les pièces et sur les équipements. Les données de référence peuvent également provenir de l’extérieur de l’organisation, comme les codes postaux ou les taux d’imposition. Les stratégies dédiées à l’augmentation de la disponibilité des données de référence sont généralement moins complexes que celles associées aux données transactionnelles. Les données de référence présentent l’avantage d’être principalement immuables.

Vous pouvez créer des rôles Web et de travail Azure qui consomment de manière autonome des données de référence lors de l’exécution en déployant ces données de référence avec l’application. Si la taille du stockage local permet un déploiement de ce type, il s’agit d’un état idéal. Les bases de données intégrées (SQL, NoSQL) ou les fichiers XML déployés sur un système de fichiers local contribuent à l’autonomie des unités d’échelle de calcul Azure. Toutefois, vous devez disposer d’un mécanisme permettant de mettre à jour les données de chaque rôle sans nécessiter de nouveau déploiement. Pour ce faire, placez les mises à jour des données de référence sur un point de terminaison de stockage cloud (par exemple, le stockage d’objets blob Azure ou une Base de données SQL). Sur chaque rôle, ajoutez un code téléchargeant les mises à jour des données dans les nœuds de calcul lors du démarrage. Sinon, ajoutez un code qui donne les moyens à un administrateur d’effectuer un téléchargement forcé dans les instances de rôle.

Pour augmenter la disponibilité, les rôles doivent également contenir un ensemble de données de référence, à utiliser en cas de défaillance du stockage. Ainsi, les rôles démarrent avec un ensemble de base de données de référence, jusqu’à la mise à disposition pour les mises à jour des ressources de stockage.

![Haute disponibilité des applications via les nœuds de calcul autonomes](./media/resiliency-high-availability-azure-applications/application-high-availability-through-autonomous-compute-nodes.png)

Un élément important à prendre en compte pour ce modèle est la vitesse de déploiement et de démarrage de vos rôles. Si vous déployez et téléchargez de gros volumes de données de référence au démarrage, cela peut augmenter le délai de lancement de nouveaux déploiements ou de nouvelles instances de rôle. Cela peut constituer un compromis acceptable, dans la mesure où les données de référence sont immédiatement disponibles à chaque rôle, sans plus dépendre des services de stockage externes.

###Modèle de données transactionnel pour la haute disponibilité

Les données transactionnelles sont les données générées par l’application dans un contexte professionnel. Les données transactionnelles sont une combinaison de l’ensemble de processus d’entreprise implémentés par l’application et des données de référence qui prennent en charge ces processus. Parmi les données transactionnelles, citons les commandes, les avis d’expédition anticipée, les factures et les opportunités de gestion des relations client (CRM, Customer Relationship Management). Les données transactionnelles générées ainsi sont transmises aux systèmes externes à des fins de consignation ou de traitement ultérieur.

N’oubliez pas que les données de référence peuvent changer au sein des systèmes qui en sont responsables. Pour cette raison, les données transactionnelles doivent enregistrer le contexte des données de référence ponctuelles, de manière à ce qu’elles présentent un volume minimal de dépendances externes, et donc une cohérence sémantique. Par exemple, imaginez la suppression d’un produit du catalogue quelques mois après l’exécution d’une commande. La meilleure pratique consiste à intégrer autant de contexte de données de référence que possible dans la transaction. Cela préserve la sémantique associée à la transaction, même si une modification des données de référence était prévue une fois la transaction capturée.

Comme mentionné précédemment, les architectures qui valorisent un couplage faible et une communication asynchrone bénéficient de niveaux de disponibilité plus élevés. Cela est également vrai pour les données transactionnelles, mais l’implémentation est plus complexe. Pour garantir la transaction, les notions transactionnelles traditionnelles reposent généralement sur la base de données. Lorsque vous introduisez des couches intermédiaires, le code d’application doit traiter correctement les données des différentes couches pour assurer une cohérence et une durabilité suffisantes.

La séquence suivante décrit un workflow qui sépare la capture des données transactionnelles de son traitement :

1. Nœud de calcul Web : présenter les données de référence.
1. Stockage externe : enregistrer les données transactionnelles intermédiaires.
1. Nœud de calcul Web : exécuter la transaction de l’utilisateur final.
1. Nœud de calcul Web : envoyer les données transactionnelles complètes avec le contexte des données de référence vers un stockage durable temporaire garantissant la fourniture d’une réponse prévisible.
1. Nœud de calcul Web : signaler à l’utilisateur final l’exécution de la transaction.
1. Nœud de calcul d’arrière-plan : extraire les données transactionnelles, les post-traiter si nécessaire et les envoyer vers leur emplacement de stockage final au sein du système actuel.

Le diagramme suivant représente une implémentation possible de cette conception dans un service cloud hébergé Azure.

![Haute disponibilité via un couplage faible](./media/resiliency-disaster-recovery-high-availability-azure-applications/application-high-availability-through-loose-coupling.png)

Les flèches en pointillés du diagramme précédent font état d’un traitement asynchrone. Le rôle web frontal n’a pas connaissance de ce traitement asynchrone. Avec cette configuration, la transaction est stockée à sa destination finale avec une référence au système actuel. En raison de la latence introduite par ce modèle asynchrone, les données transactionnelles ne peuvent pas être immédiatement interrogées. Par conséquent, pour satisfaire aux besoins immédiats de l’interface utilisateur, chaque unité de données transactionnelles doit être enregistrée dans un cache ou une session d’utilisateur.

Le rôle Web est autonome du reste de l’infrastructure. Son profil de disponibilité est une combinaison du rôle web et de la file d’attente Azure, non pas de l’intégralité de l’infrastructure. En plus de la haute disponibilité, cette approche prend en charge la mise à l’échelle horizontale du rôle Web, indépendamment du stockage principal. Ce modèle de haute disponibilité peut avoir une incidence sur la rentabilité des opérations. Les composants supplémentaires comme les files d’attente Azure et les rôles de travail peuvent affecter les coûts mensuels d’utilisation.

Notez que le diagramme précédent représente une implémentation de cette approche dissociée des données transactionnelles. Il existe de nombreuses autres implémentations possibles. La liste suivante vous propose quelques alternatives :

 * Un rôle de travail peut être placé entre le rôle web et la file d’attente de stockage.
 * Une file d’attente Service Bus peut remplacer une file d’attente Azure Storage.
 * La destination finale peut être Azure Storage ou un fournisseur différent de base de données.
 * La fonctionnalité Azure Cache peut être utilisée au niveau de la couche Web pour prendre en charge les besoins immédiats de mise en cache après la transaction.

###Modèles d’extensibilité

Il est important de noter que l’extensibilité du service cloud affecte directement la disponibilité. Si l’accroissement de la charge affecte la réactivité de votre service, l’utilisateur a le sentiment que l’application est en panne. Suivez les meilleures pratiques d’extensibilité en fonction des prévisions de charge de votre application et des besoins futurs. Une mise à l’échelle plus importante implique de nombreuses questions : devez-vous utiliser un ou plusieurs comptes de stockage ? Comment se déroule le partage entre plusieurs bases de données ? Quelles stratégies de mise en cache implémenter ? Pour une présentation approfondie de ces modèles, consultez [Best Practices for the Design of Large-Scale Services on Azure Cloud Services](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) (Meilleures pratiques de conception de service à grande échelle sur Azure Cloud Services).

##Étapes suivantes

Cet article fait partie d’une série d’articles axés sur la [récupération d’urgence et la haute disponibilité des applications développées sur Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). Le prochain article de cette série est [Récupération d’urgence des applications développées sur Microsoft Azure](./resiliency-disaster-recovery-azure-applications.md).

<!---HONumber=AcomDC_0727_2016-->