<properties
	pageTitle="Vue d'ensemble des fonctionnalités d'Azure Batch | Microsoft Azure"
	description="Découvrez les fonctionnalités du service Batch et de ses API du point de vue du développeur."
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="03/11/2016"
	ms.author="yidingz;marsma"/>

# Vue d'ensemble des fonctionnalités d'Azure Batch

Cet article fournit une vue d’ensemble de base des fonctionnalités API principales du service Azure Batch. Si vous développez une solution informatique distribuée à l’aide des API [Batch REST][batch_rest_api] ou [Batch .NET][batch_net_api], vous allez utiliser un certain nombre d’entités et de fonctionnalités présentées ci-dessous.

> [AZURE.TIP] Pour obtenir une présentation technique générale du service Batch, consultez l’article [Concept de base d’Azure Batch](batch-technical-overview.md).

## <a name="workflow"></a>Flux de travail du service Batch

Le flux de travail de haut niveau suivant est typique de celui qui est utilisé par la quasi-totalité des scénarios de calcul développés au sein du traitement Batch :

1. Téléchargez les *fichiers de données* que vous souhaitez utiliser dans votre scénario de calcul distribué dans un compte [Azure Storage][azure_storage]. Ils doivent être situés dans le compte de stockage afin que le service Batch puisse y accéder. Les tâches vont télécharger ces fichiers sur les [nœuds de calcul](#computenode) lorsqu’elles sont exécutées.

2. Téléchargez les *fichiers binaires* dépendants dans votre compte de stockage, parmi lesquels se trouvent le programme exécuté par les tâches et l’un des assemblys dépendants. Ces fichiers doivent aussi être accessibles à partir de votre compte de stockage pour pouvoir être téléchargés vers les nœuds de calcul par les tâches.

3. Créer un [Pool](#pool) de nœuds de calcul. Spécifiez la [taille des nœuds de calcul][cloud_service_sizes] à utiliser lorsque le pool est créé, et lors de l’exécution d’une tâche, il est affecté à un nœud appartenant à ce pool.

4. Création d’un [Travail](#job). Un travail vous permet de gérer les tâches de collecte.

5. Ajoutez des [Tâches](#task) au travail. Chaque tâche utilise le programme téléchargé pour traiter les informations contenues dans le(s) fichier(s) de données téléchargé dans le compte de stockage.

6. Surveillez la progression du travail et récupérez les résultats.

> [AZURE.NOTE] Vous aurez besoin d’un [compte Batch](batch-account-create-portal.md) pour utiliser le service de traitement par lots et presque toutes les solutions utiliseront un compte [Azure Storage][azure_storage] pour le stockage et la récupération des fichiers.

Dans les sections qui suivent, vous allez apprendre chacune des ressources mentionnées dans le flux de travail ci-dessus, ainsi que de nombreuses autres fonctionnalités de traitement par lots qui activeront votre scénario de calcul.

## <a name="resource"></a> Ressources du service Batch

Lorsque vous utilisez Batch, vous utilisez une bonne partie des ressources suivantes : Certaines de ces ressources (comptes, nœuds de calcul, pools, travaux et tâches) sont utilisées dans toutes les solutions Batch. D’autres, comme les planifications des tâches et les packages d’application sont des tâches utiles, mais facultatives.

- [Compte](#account)
- [Nœud de calcul](#computenode)
- [Pool](#pool)
- [Travail](#job)
- [Tâche](#task)
	- [Tâche de démarrage](#starttask)
	- [Tâche du gestionnaire de travaux](#jobmanagertask)
	- [Tâches de préparation et lancement](#jobpreprelease)
	- [Tâches multi-instances](#multiinstance)
    - [Dépendances de la tâche](#taskdep)
- [Planifications de travaux](#jobschedule)
- [Packages d’applications](#appkg)

### <a name="account"></a>Compte

Un compte Batch est une entité identifiée de façon unique au sein du service Batch. Tout le traitement s’effectue via un compte Batch. Lorsque vous effectuez des opérations avec le service Batch, vous avez besoin du nom et de la clé du compte. Pour créer un compte Batch, consultez [Créer et gérer un compte Azure Batch dans le portail Azure](batch-account-create-portal.md).

### <a name="computenode"></a>Nœud de calcul

Un nœud de calcul est une machine virtuelle Azure dédiée à une charge de travail spécifique de votre application. La taille d’un nœud détermine le nombre de cœurs du processeur, la capacité de mémoire et la taille du système de fichiers local qui lui est allouée. Un nœud peut être n’importe quelle [taille de nœud de service cloud][cloud_service_sizes], à l’exception de A0.

Les nœuds peuvent exécuter des fichiers exécutables et les scripts, notamment les exécutables (.exe), les fichiers de commande (.cmd), les fichiers batch (.bat) et les scripts PowerShell. Un nœud présente également les attributs suivants :

- Une **structure de dossier** et les **variables d’environnement** associées standard détaillant leurs chemins d’accès sont créées sur chaque nœud de calcul. Pour plus d’informations, consultez la section [Fichiers et répertoires](#files).
- **Variables d’environnement** pouvant être référencées par des tâches.
- Paramètres de **pare-feu** configurés pour le contrôle de l’accès.
- Si l’**accès distant** à un nœud est requis (pour le débogage par exemple), un fichier RDP peut être obtenu puis utilisé pour accéder au nœud via le *Bureau à distance*.

### <a name="pool"></a>Pool

Un pool est une collection de nœuds sur lesquels votre application s’exécute. Vous pouvez créer le pool manuellement ou laisser le service Batch le créer automatiquement lorsque vous spécifiez le travail à accomplir. Vous pouvez créer et gérer un pool qui répond aux besoins de votre application, et des pools peuvent être utilisés uniquement par le compte Batch dans lequel il a été créé. Un compte Batch peut avoir plusieurs pools.

Les pools Azure Batch sont créés sur la plateforme de calcul principale Azure ; les pools Batch permettent l’affectation à grande échelle, l’installation d’applications et de données, le transfert de données, l’analyse de l’état, ainsi que le réglage flexible du nombre de nœuds de calcul avec un pool (mise à l’échelle).

Chaque nœud ajouté à un pool se voit attribuer un nom unique et l’adresse IP. Lorsqu’un nœud est supprimé d’un pool, toutes les modifications apportées au système d’exploitation ou aux fichiers sont perdues, et son nom et l’adresse IP sont libérés pour une utilisation ultérieure. Lorsqu’un nœud quitte un pool, sa durée de vie est terminée.

Vous pouvez configurer un pool pour permettre la communication entre les nœuds qu’il contient. Si la communication à l’intérieur du pool est demandée pour un pool, le service Batch active les ports supérieurs à 1100 sur chaque nœud du pool. Chaque nœud du pool est configuré pour autoriser les connexions entrantes à cette plage de ports et uniquement à partir d’autres nœuds du pool seulement. Si votre application ne nécessite pas la communication entre nœuds, le service Batch peut éventuellement allouer au pool un grand nombre de nœuds issus de différents centres de données ou clusters pour accroître la puissance de traitement parallèle.

Lorsque vous créez un pool, vous pouvez spécifier les attributs suivants :

- **Taille des nœuds** dans le pool
	- La taille de nœud appropriée doit être définie en fonction des caractéristiques et des exigences de l’application de la ou des applications qui vont être exécutées sur le nœud. La taille du nœud est en général sélectionnée en supposant qu’une tâche s’exécutera sur un nœud à la fois. Lorsqu’on prend en compte des aspects tels que la nature multithread de l’application et le volume de mémoire et qu’elle utilise, vous pouvez déterminer la taille de nœud le mieux adaptée et la plus rentable. Il est possible que plusieurs tâches soient attribuées et que plusieurs instances d’application s’exécutent en parallèle. Dans ce cas, un nœud plus volumineux est généralement choisi (voir ci-dessous « Nombre maximum de tâches autorisées par nœud »).
	- Tous les nœuds du pool doivent avoir la même taille. Si différentes applications doivent être exécutées avec des exigences système et/ou des niveaux de charge différents, plusieurs pools devront alors être créés.
	- Toutes les [tailles de nœud du service cloud][cloud_service_sizes] peuvent être configurées pour un pool, hormis A0.

- **Famille de système d’exploitation** et **version** qui s’exécute sur les nœuds.
	- Comme avec les rôles de travail dans Cloud Services, la *famille du système d’exploitation* et la *Version du système d’exploitation* peuvent être spécifiées (pour plus d’informations sur les rôles de travail, consultez la section [Connaître les services de cloud][about_cloud_services] dans *Options d’hébergement de calcul fournies par Azure*).
	- La famille de système d’exploitation détermine également les versions de .NET qui sont installées avec le système d'exploitation.
	- Comme avec les rôles de travail, il est recommandé de spécifier `*` comme version du système d’exploitation afin que les nœuds soient automatiquement mis à niveau et qu’aucun travail supplémentaire ne soit requis pour gérer ces nouvelles versions. La principale raison pour sélectionner une version de système d’exploitation spécifique est de s’assurer que la compatibilité est conservée, permettre aux tests de compatibilité descendante d’être réalisés avant d’autoriser la mise à jour de la version. Une fois validée, la version du système d’exploitation du pool peut être mise à jour et la nouvelle image du système d’exploitation peut également être installée. Toutes les tâches en cours d’exécution seront interrompues et remises en file d’attente.

- **Nombre de nœuds cibles** devant être disponible pour le pool

- **Politique de mise à l’échelle** du pool
	- Outre le nombre de nœuds, vous pouvez également spécifier une [formule de mise à l’échelle](batch-automatic-scaling.md) automatique pour chaque pool. Le service Batch exécute la formule et ajuste le nombre de nœuds dans le pool en fonction de différents paramètres de pool, de travail et de tâche que vous pouvez spécifier.

- Stratégie de **planification de tâches**
	- L’option de configuration [tâches maximales par nœud](batch-parallel-node-tasks.md) détermine le nombre maximal de tâches qui peuvent être exécutées en parallèle sur chaque nœud au sein du pool.
	- La configuration par défaut prévoit d’exécuter une tâche sur un nœud de calcul à a fois, mais dans certains scénarios, il est parfois préférable que plusieurs tâches soient exécutées simultanément sur un nœud. Un exemple consiste à augmenter le taux d’utilisation d’un nœud si une application doit attendre des E/S. Le fait d’avoir plusieurs applications exécutées simultanément augmente le taux d’utilisation du processeur. Un autre exemple consiste à réduire le nombre de nœuds dans le pool. Cela peut réduire le volume de transfert de données requis pour les grands jeux de données de référence : si une taille de nœud A1 est suffisante pour une application, la taille du nœud A4 peut en revanche être choisie et le pool configuré pour 8 tâches en parallèle, utilisant chacune un cœur.
	- Il est également possible de spécifier un « type de remplissage » qui détermine si l’option Batch répartit les tâches uniformément sur tous les nœuds, ou remplit chaque nœud avec le nombre maximal de tâches avant d’affecter des tâches à un autre nœud du pool.

- **État de communication** des nœuds dans le pool.
	- Un pool peut être configuré pour autoriser la communication entre les nœuds au sein de ce dernier, ce qui détermine son infrastructure réseau sous-jacente. Notez que cela affecte également le positionnement des nœuds de clusters.
	- Dans la plupart des scénarios, les tâches fonctionnent indépendamment les unes des autres et n’ont pas besoin de communiquer entre elles, mais il existe certaines applications dans lesquelles des tâches doivent communiquer.

- **Lancez la tâche** des nœuds sur le pool.
	- Une *tâche de démarrage* peut être spécifiée et exécutée à chaque fois qu’un nœud de calcul rejoint le pool, et lorsqu’un nœud est redémarré. Cette tâche est souvent utilisée pour installer une application qui sera utilisée par les tâches s’exécutant sur le nœud.

### <a name="job"></a>Travail

Un travail est une collecte de données et spécifie comment le calcul est effectué sur les nœuds de calcul d’un pool.

- Le travail spécifie le **pool** sur lequel le travail sera exécuté. Le pool peut être un pool existant créé au préalable pour être utilisé par de nombreux travaux, ou créé sur demande pour chaque travail associé à un calendrier de travail, ou pour tous les travaux associés à un calendrier de travail.
- Une **priorité de travail** facultative peut être spécifiée. Quand un travail dont la priorité est plus élevée que celle des autres travaux en cours d’exécution est envoyé, les tâches du travail dont la priorité est la plus élevée sont insérées dans la file d’attente, devant les tâches des travaux de priorité inférieure. Les tâches de priorité inférieure qui sont déjà en cours d’exécution ne seront pas annulées.
- Les **contraintes** de travail spécifient certaines limites pour vos travaux.
	- Une **durée maximale** peut être définie pour les travaux. Si la durée d’exécution des travaux est supérieure à la durée maximale spécifiée, le travail et toutes les tâches qui lui sont associées seront terminés.
	- Azure Batch peut détecter les tâches qui échouent et les relancer. Le **nombre maximal de tentatives de tâche** peut être spécifié comme une contrainte, et indiquer notamment si une tâche doit toujours être systématiquement relancée ou ne jamais l’être. Lorsqu’une tâche est relancée, cela signifie qu’elle est remise en file d’attente afin d’être réexécutée.
- Les tâches peuvent être ajoutées au travail par votre application cliente, ou une [Tâche du gestionnaire de travaux](#jobmanagertask) peut être spécifiée. Une tâche de gestionnaire de travaux utilise l’API Batch et contient les informations nécessaires à la création des tâches requises pour un travail, avec la tâche qui s’exécute sur l’un des nœuds de calcul au sein du pool. La tâche du gestionnaire de travaux est gérée spécifiquement par Batch : elle est remise en file d’attente dès que le travail est créé et elles sont relancées lorsqu’elle échoue. Une tâche de gestionnaire de travaux est requise pour les travaux créés dans le cadre d’un calendrier du travail, car il s’agit du seul moyen de définir les tâches avant que le travail ne soit instancié. D’autres informations sur les tâches du gestionnaire du travail apparaissent ci-dessous.

### <a name="task"></a>Tâche

Une tâche est une unité de calcul associée à un travail et exécutée sur un nœud. Les tâches sont affectées à un nœud afin d’être exécutées ou sont mises en file d’attente jusqu’à ce qu’un nœud soit disponible. Elle utilise les ressources suivantes :

- L’application spécifiée dans la **ligne de commande** de la tâche.

- **Fichiers de ressources** contenant les données à traiter. Ces fichiers sont automatiquement copiés sur le nœud depuis le stockage d’objets blobs dans un compte de stockage Azure. Pour plus d’informations, consultez la section [Fichiers et répertoires](#files) ci-dessous.

- Les **variables d’environnement** sont requises par l’application. Pour plus d’informations, consultez la section [Paramètres d’environnement des tâches](#environment) ci-dessous.

- Les **contraintes** sous lesquelles le calcul doit se produire. Par exemple, la durée maximale pendant laquelle la tâche est autorisée à s’exécuter, le nombre maximal de nouvelles tentatives en cas d’échec de la tâche, ainsi que la durée maximale pendant laquelle les fichiers du répertoire de travail sont conservés.

Outre les tâches que vous pouvez définir pour effectuer des calculs sur un nœud, les tâches spéciales suivantes sont également fournies par le service Batch :

- [Tâche de démarrage](#starttask)
- [Tâche du gestionnaire de travaux](#jobmanagertask)
- [Tâches de préparation et lancement](#jobmanagertask)
- [Tâches multi-instances](#multiinstance)
- [Dépendances de la tâche](#taskdep)

#### <a name="starttask"></a>Tâche de démarrage

En associant une **tâche de démarrage** avec un pool, vous pouvez configurer l’environnement d’exploitation de ses nœuds en effectuant des actions telles que l’installation de logiciels ou en démarrant des processus en arrière-plan. Elle s’exécute chaque fois qu’un nœud démarre pendant sa durée de présence dans le pool, et notamment lorsque le nœud est ajouté au pool en premier lieu. Le principal avantage de la tâche de démarrage est qu’il contient toutes les informations nécessaires pour configurer les nœuds de calcul et installer les applications nécessaires à l’exécution de la tâche du travail. Par conséquent, pour augmenter le nombre de nœuds dans un pool, il suffit de spécifier un nombre de nœuds cibles. Batch dispose déjà de toutes les informations nécessaires pour configurer les nouveaux nœuds et les préparer à accepter des tâches.

Comme avec n’importe quelle tâche Batch, une **liste de fichiers de ressources** peut être spécifiée dans le [stockage Azure][azure_storage], en plus d’une **ligne de commande** à exécuter. Azure Batch copiera d’abord les fichiers du stockage Azure et exécutera ensuite la ligne de commande. Pour une tâche de démarrage du pool, la liste des fichiers contient généralement un package ou des fichiers d’application, mais elle peut également inclure des données de référence qui seront utilisées par toutes les tâches qui s’exécutent sur les nœuds de calcul. La ligne de commande de la tâche de démarrage peut exécuter un script PowerShell ou effectuer une opération `robocopy`, par exemple, pour copier les fichiers d’application dans le dossier « partagé », puis exécuter un MSI ou `setup.exe`.

Il est généralement préférable pour le service Batch d’attendre que la tâche de démarrage soit terminée avant de considérer que le nœud est prêt à recevoir des tâches, bien que ceci soit configurable.

Si une tâche de démarrage échoue sur un nœud du pool, l’état du nœud est mis à jour pour refléter l’échec et le nœud n’est plus disponible pour les tâches à affecter. Une tâche de démarrage peut échouer en cas de problème de copie des fichiers de ressources depuis le stockage, ou si le processus exécuté par sa ligne de commande retourne un code de sortie différent de zéro.

#### <a name="jobmanagertask"></a>Tâche du gestionnaire de travaux

Une **tâche de gestionnaire de tâche** est généralement pour le contrôle et/ou la surveillance de l’exécution du travail. Par exemple, la création et l’envoi des d’un travail, la détermination d’autres tâches à exécuter et la détermination du fonctionnement est complète. Une tâche de gestionnaire de travaux n’est pas limitée à ces activités, cependant, il s’agit d’une tâche à part entière qui peut exécuter n’importe quelle action requise pour le travail. Par exemple, une tâche de gestionnaire de tâche peut télécharger un fichier spécifié en tant que paramètre, analyser le contenu de ce fichier et envoyer des tâches supplémentaires en fonction de son contenu.

Une tâche de gestionnaire de tâche est démarrée avant toutes les autres tâches et offre les caractéristiques suivantes :

- Elle est automatiquement soumise en tant que tâche par le service Batch lorsque le travail est créé.

- Elle est planifiée pour s’exécuter avant les autres tâches d’un projet.

- Le nœud associé est le dernier à être supprimé d’un pool lorsque la taille de ce dernier diminue.

- Son arrêt peut être lié à l'arrêt de toutes les tâches du travail.

- La tâche de gestionnaire de travaux reçoit la priorité la plus élevée lorsqu’elle doit être redémarrée. Si aucun nœud inactif n’est disponible, le service Batch peut mettre fin à une tâche en cours d’exécution dans le pool pour lui laisser la place de s’exécuter.

- Une tâche du gestionnaire de travaux associée à un travail n’a pas la priorité sur les tâches d’autres travaux. Parmi les travaux, seules les priorités au niveau du travail sont observées.

#### <a name="jobpreprelease"></a>Tâches de préparation et lancement

Btach fournit la tâche de préparation de travail pour le programme d’installation de l’exécution du travail préliminaire et la tâche de version de maintenance postérieure au travail ou de nettoyage.

- **Tâche de préparation de travail** -la tâche de préparation de tâche s’exécute sur tous les nœuds de calcul pour exécuter les tâches avant que les autres tâches du travail soient exécutées. Utilisez la tâche de préparation de travail pour copier des données partagées par toutes les tâches, mais unique au travail, par exemple.
- **Tâche de validation de travail** - lorsqu’un travail est terminé, la tâche de validation s’exécute sur chaque nœud dans le pool ayant exécuté au moins une tâche. Utilisez la tâche de validation pour supprimer les données copiées par la tâche de préparation de travail, ou compresser et télécharger des données de journaux de diagnostic, par exemple.

Les tâches de préparation et de validation vous permettent de spécifier une ligne de commande à exécuter lorsque la tâche est appelée et offrent des fonctionnalités telles que le téléchargement de fichiers, l’exécution élevée, les variables d’environnement personnalisées, la durée maximale d’exécution, le nombre de nouvelles tentatives et la période de rétention de fichier.

Pour plus d’informations sur les tâches de préparation de travail et de validation, consultez [Exécuter les tâches de préparation de travail et de validation sur les nœuds de calcul Azure Batch](batch-job-prep-release.md).

#### <a name="multiinstance"></a>Tâches multi-instances

Une [tâche multi-instance](batch-mpi.md) est une tâche configurée pour s’exécuter simultanément sur plusieurs nœuds de calcul. Avec des tâches multi-instances, vous pouvez activer des scénarios de calcul haute performance, comme MPI (Message Passing Interface), qui requièrent un groupe de nœuds de calcul alloués ensemble pour traiter une seule et même charge de travail.

Pour une présentation détaillée de l’exécution des travaux MPI dans Batch à l’aide de la bibliothèque .NET de Batch, consultez l’article [Utiliser des tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](batch-mpi.md).

#### <a name="taskdep"></a>Dépendance entre tâches

La dépendance entre tâches, comme son nom l’indique, vous permet de préciser que l’exécution d’une tâche dépend de l’achèvement préalable d’autres tâches. Cette fonctionnalité prend en charge des situations dans lesquelles une tâche « en aval » consomme le résultat d’une sortie d’une tâche « en amont », ou lorsqu’une tâche en amont effectue une initialisation requise par une tâche en aval. Pour utiliser cette fonctionnalité, vous devez d’abord activer les dépendances de tâche sur la tâche Batch. Ensuite, pour chaque tâche qui dépend d’une autre (ou de plusieurs autres), vous devez spécifier les tâches dont elle dépend.

Avec l’interdépendance des tâches, vous pouvez configurer des scénarios suivants :

* *taskB* dépend de *taskA* (*taskB* ne commence pas tant que l’exécution de *taskA* n’est pas terminée)
* *taskC* dépend de *taskA* et de *taskB*
* *taskD* dépend d’une suite de tâches, notamment les tâches *1* à *10*, avant de pouvoir s’exécuter

Découvrez l’exemple de code [TaskDependencies][github_sample_taskdeps] dans le référentiel GitHub [azure-batch-samples][github_samples]. Vous apprendrez grâce à lui à configurer des tâches qui dépendent d’autres tâches à l’aide de la bibliothèque [Batch .NET][batch_net_api].

### <a name="jobschedule"></a>Travaux planifiés

Les planifications de travail permettent de créer des tâches récurrentes au sein du service Batch. Une planification de travail indique le moment où exécuter des tâches et les spécifications des travaux à exécuter. Une planification de travail permet la spécification de la durée de la planification (la durée de vie et l’entrée en vigueur de la planification) et la fréquence à laquelle des travaux juridiques doivent être créés.

### <a name="appkg"></a>Packages d’application

La fonctionnalité [packages d’application](batch-application-packages.md) offre une gestion facile et permet le déploiement d’applications sur les nœuds de calcul dans vos pools. Avec les packages d’applications, vous pouvez facilement télécharger et gérer plusieurs versions des applications exécutées par vos tâches, et notamment les fichiers binaires et fichiers de prise en charge, puis déployer automatiquement une ou plusieurs de ces applications sur les nœuds de calcul dans le pool.

Batch gère les détails de l’utilisation de stockage Azure en arrière-plan pour stocker et déployer en toute sécurité vos packages d’application pour le calcul des nœuds, donc votre code et vos frais de gestion peuvent être simplifiés.

Pour en savoir plus sur la fonctionnalité du package d’application, consultez [Déploiement de l’application avec des packages d’applications Azure Batch](batch-application-packages.md).

## <a name="files"></a>Fichiers et répertoires

Chaque tâche possède un répertoire de travail sous lequel elle crée zéro ou plusieurs fichiers et des répertoires pour stocker le programme exécuté par la tâche, les données qu’il traite, et le résultat du traitement exécuté par la tâche. Ces fichiers et répertoires sont ensuite disponibles pour une utilisation par d’autres tâches pendant l’exécution d’un travail. L’ensemble des tâches, fichiers et répertoires d’un nœud sont la propriété d’un seul compte d’utilisateur.

Le service Batch expose une partie du système de fichiers sur un nœud en tant que répertoire racine. Le répertoire racine est disponible pour une tâche par le biais d’un accès à la variable d’environnement `%AZ_BATCH_NODE_ROOT_DIR%`. Pour plus d’informations sur l’utilisation de variables d’environnement, consultez la section [Paramètres d’environnement des tâches](#environment).

![Structure de répertoire du nœud de calcul][1]

Le répertoire racine contient a structure de répertoires suivante :

- **Partagé** – cet emplacement est un répertoire partagé pour toutes les tâches qui s’exécutent sur un nœud, quel que soit le travail. Sur le nœud, le répertoire partagé est accessible via `%AZ_BATCH_NODE_SHARED_DIR%`. Ce répertoire fournit l’accès en lecture/écriture à toutes les tâches qui s’exécutent sur le nœud. Les tâches peuvent créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire.

- **Démarrage** – Cet emplacement est utilisé par une tâche de démarrage en tant que répertoire de travail. Tous les fichiers téléchargés par le service Batch pour lancer la tâche de démarrage sont également stockés dans ce répertoire. Sur le nœud, le répertoire de démarrage est disponible via la variable d’environnement `%AZ_BATCH_NODE_STARTUP_DIR%`. La tâche de démarrage peut créer, lire, mettre à jour et supprimer des fichiers présents dans ce répertoire, et ce dernier est utilisable par les tâches de démarrage pour configurer le système d’exploitation.

- **Tâches** -un répertoire est créé pour chaque tâche qui s’exécute sur le nœud, accessible via `%AZ_BATCH_TASK_DIR%`. Dans chaque répertoire de la tâche, le service Batch crée un répertoire de travail (`wd`) dont le chemin unique est spécifié par la variable d’environnement `%AZ_BATCH_TASK_WORKING_DIR%`. Ce répertoire fournit un accès en lecture/écriture à la tâche. La tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire, et ce dernier est conservé en fonction de la contrainte *RetentionTime* spécifiée pour la tâche.
  - `stdout.txt` et `stderr.txt` -ces fichiers sont écrits dans le dossier des tâches lors de l’exécution de la tâche.

Lorsqu’un nœud est supprimé du pool, tous les fichiers stockés dans le nœud sont supprimés.

## <a name="lifetime"></a>Durée de vie de nœud de pool et de calcul

Lorsque vous créez votre solution Azure Batch, une décision de conception doit être prise quant à la procédure et le moment de la création, et la durée sur laquelle les nœuds de calcul de ces pools restent disponibles.

D’un côté, un pool peut être créé pour chaque travail au moment de l’envoi, et ses nœuds peuvent être supprimés dès lors que les tâches cessent de s’exécuter. Ceci permet d’optimiser l’utilisation puisque les nœuds ne sont alloués que lorsque cela est absolument nécessaire et qu’ils s’arrêtent dès qu’ils deviennent inactifs. Cela signifie que le travail doit attendre que les nœuds soient alloués, mais il est important de noter que les tâches seront planifiées sur les nœuds dès qu’elles seront individuellement disponibles, allouées, et que cette tâche de démarrage sera terminée. Batch *n’*attend pas, par exemple, que tous les nœuds d’un pool soient disponibles avant d’affecter des tâches, car cela entraînerait une faible utilisation des nœuds disponibles.

À l’autre extrémité du spectre, si la priorité absolue consiste à démarrer immédiatement une tâche, un pool eut être créé avant l’heure et ses nœuds seront mis à disposition avant l’envoi de travaux. Dans ce scénario, les tâches du travail peuvent démarrer immédiatement, mais les nœuds peuvent rester inactifs en attendant les tâches à affecter.

Une approche combinée, généralement utilisée pour la gestion de la charge variable, mais continue, consiste à disposer d’un pool auquel plusieurs travaux sont soumis, mais à mettre à l’échelle le nombre de nœuds (dans un sens ou dans l’autre) en fonction de la charge de travail (voir *Mise à l’échelle des applications* ci-dessous). Cela peut être fait en réaction, en fonction de la charge actuelle, de façon proactive, si la charge peut être prévue.

## <a name="scaling"></a>Mise à l'échelle des applications

Avec la [mise à l’échelle automatique](batch-automatic-scaling.md), le service Batch peut ajuster de manière dynamique le nombre de nœuds de calcul d’un pool en fonction de la charge de travail actuelle et de l’utilisation des ressources de votre scénario de calcul. Cela vous permet de réduire le coût global d’exécution de votre application en utilisant uniquement les ressources dont vous avez besoin et en libérant les autres. Vous pouvez indiquer les paramètres de mise à l’échelle automatique d’un pool au moment de sa création ou les activer plus tard, tout comme vous pouvez mettre à jour ces paramètres dans un pool compatible avec la mise à l’échelle automatique.

Pour effectuer une telle mise à l’échelle, vous devez indiquer une **formule de mise à l’échelle automatique** pour un pool. Le service Batch utilise la formule suivante pour déterminer le nombre cible de nœuds dans le pool pour le prochain intervalle de mise à l’échelle (intervalle que vous indiquez).

Par exemple, il se peut qu’un travail exige que vous envoyiez un grand nombre de tâches dont l’exécution doit être planifiée. Vous pouvez attribuer au pool une formule de mise à l’échelle qui règle le nombre de nœuds du pool en fonction du nombre actuel de tâches en attente et du degré d’achèvement de ces tâches. Le service Batch évalue la formule régulièrement et redimensionne le pool en fonction de la charge de travail et des paramètres de votre formule.

Une formule de mise à l’échelle peut être basée sur les mesures suivantes :

- **Mesures temporelles** – Celles-ci sont basées sur les statistiques collectées toutes les cinq minutes dans le nombre d'heures spécifié.

- **Mesures de ressources** – Celles-ci sont basées sur l’utilisation du processeur, de la bande passante et de la mémoire, et sur le nombre de nœuds.

- **Mesures de tâches** – Celles-ci sont basées sur l'état des tâches (Actif, En attente et Terminé).

Lorsque la mise à l’échelle automatique diminue le nombre de nœuds de calcul d’un pool, les tâches en cours d’exécution doivent être prises en compte. Pour cela, la formule peut inclure un paramètre de stratégie de désallocation de nœud qui indique si les tâches en cours d’exécution sont arrêtées immédiatement ou autorisées à se terminer avant que le nœud ne soit supprimé du pool.

> [AZURE.TIP] Pour optimiser l’utilisation des ressources de calcul, définissez à zéro le nombre cible de nœuds à la fin d’un travail tout en autorisant les tâches en cours à s’achever.

Pour plus d’informations sur la mise à l’échelle automatique d’une application, consultez la section [Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch](batch-automatic-scaling.md).

## <a name="cert"></a>Sécurité avec certificats

Vous devez en principe utiliser des certificats lors du chiffrement ou du déchiffrement des informations sensibles pour les tâches, par exemple, la clé d’un [compte de stockage Azure][azure_storage]. Pour prendre en charge ces opérations, il est possible d’installer des certificats sur les nœuds. Les secrets chiffrés sont transmis aux tâches dans les paramètres de ligne de commande ou incorporés dans l’une des ressources et les certificats installés peuvent être utilisés pour les déchiffrer.

Pour ajouter un certificat à un compte Batch, utilisez l’opération [Add certificate][rest_add_cert] (API Batch REST) ou la méthode [CertificateOperations.CreateCertificate][net_create_cert] (API Batch .NET) pour ajouter un certificat à un compte Batch. Vous pouvez ensuite associer le certificat à un pool existant ou nouveau. Lorsqu’un certificat est associé à un pool, le service Batch installe le certificat sur chaque nœud du pool. Le service Batch installe les certificats appropriés au démarrage du nœud, avant de lancer une tâche quelconque, et notamment les tâches de démarrage et celles du gestionnaire de travaux.

## <a name="scheduling"></a>Priorité de la planification

Vous pouvez établir une priorité pour les travaux que vous créez dans Batch. Le service Batch utilise les valeurs de priorité du travail pour déterminer l’ordre de planification du travail dans un compte. Les valeurs de priorité sont comprises entre -1000 et 1000, -1000 étant la priorité la plus basse et 1000 la plus élevée. Vous pouvez mettre à jour la priorité d’un travail à l’aide de l’opération [Mettre à jour les propriétés d’un travail][rest_update_job] (API BATCH REST) ou en modifiant la propriété [CloudJob.Priority][net_cloudjob_priority] (API BATCH .NET).

Dans le même compte, les tâches à la priorité plus élevée ont la priorité en termes de planification sur les tâches avec une priorité plus faible. Un travail avec une priorité plus élevée dans un compte n'a pas de priorité de planification sur un autre travail avec une valeur de priorité inférieure dans un autre compte.

La planification de travail entre pools est indépendante. Entre des pools différents, un travail avec une priorité plus élevée n’est pas systématiquement planifié en premier, si le pool auquel il est associé n’a pas suffisamment de nœuds inactifs. Dans le même pool, les travaux avec le même niveau de priorité ont autant de chance d’être planifiés.

## <a name="environment"></a>Paramètres d'environnement des tâches

Chaque tâche qui s’exécute dans un travail Batch a accès aux variables d’environnement définies à la fois par le service Batch (définie par le système, consultez le tableau ci-dessous) et par les variables d’environnement définies par l’utilisateur. Les applications et les scripts exécutés par des tâches sur les nœuds de calcul ont accès à ces variables d’environnement pendant leur exécution sur le nœud.

Les variables d’environnement définies par l’utilisateur lors de l’utilisation de l’opération [Ajouter une tâche à un travail][rest_add_task] (API Batch REST) ou la modification de la propriété [CloudTask.EnvironmentSettings][net_cloudtask_env] (API Batch .NET) lorsque vous ajoutez des tâches à un travail.

Obtenir des variables d’environnement d’une tâche, les deux systèmes - et définies par l’utilisateur, à l’aide de la [obtenir des informations sur une tâche][rest_get_task_info] opération (API REST de lot) ou en accédant à la [CloudTask.EnvironmentSettings][net_cloudtask_env] propriété (API .NET de lot). Comme indiqué, les processus qui s’exécutent sur un nœud de calcul peuvent également accéder à toutes les variables d’environnement, par exemple à l’aide de la syntaxe classique `%VARIABLE_NAME%`.

Pour chaque tâche planifiée d’un travail, l’ensemble suivant de variables d’environnement définies par le système est défini par le service Batch :

| Nom de variable d'environnement | Description |
|---------------------------------|--------------------------------------------------------------------------|
| `AZ_BATCH_ACCOUNT_NAME` | Nom du compte auquel appartient la tâche. |
| `AZ_BATCH_JOB_ID` | ID du travail auquel appartient la tâche. |
| `AZ_BATCH_JOB_PREP_DIR` | Chemin d’accès complet du répertoire de la tâche de préparation du travail du nœud. |
| `AZ_BATCH_JOB_PREP_WORKING_DIR` | Chemin d’accès complet du répertoire de travail de la tâche de préparation du travail du nœud. |
| `AZ_BATCH_NODE_ID` | ID du nœud sur lequel la tâche s’exécute. |
| `AZ_BATCH_NODE_ROOT_DIR` | Chemin d’accès complet du répertoire racine du nœud. |
| `AZ_BATCH_NODE_SHARED_DIR` | Chemin d’accès complet du répertoire partagé du nœud. |
| `AZ_BATCH_NODE_STARTUP_DIR` | Chemin d’accès complet du répertoire de tâche de démarrage du nœud de calcul. |
| `AZ_BATCH_POOL_ID` | ID du pool sur lequel la tâche s’exécute. |
| `AZ_BATCH_TASK_DIR` | Chemin d’accès complet du répertoire de la tâche du nœud. |
| `AZ_BATCH_TASK_ID` | ID de la tâche en cours. |
| `AZ_BATCH_TASK_WORKING_DIR` | Chemin d’accès complet du répertoire de travail de la tâche du nœud. |

>[AZURE.NOTE] Vous ne pouvez pas remplacer les variables définies par le système ci-dessus. Elles sont en lecture seule.

## <a name="errorhandling"></a>Gestion des erreurs

Il peut s’avérer utile de gérer les échecs de tâche et application au sein de votre solution Batch.

### Gestion des échecs de tâche
Les échecs de tâche peuvent être classés suivant les catégories suivantes :

- **Échecs de planification**
	- Si le transfert de fichiers spécifié pour une tâche échoue pour une raison quelconque, une « erreur de planification » est définie pour la tâche.
	- Les causes d’erreur de planification peuvent être dues au fait que les fichiers ont été déplacés, que le compte de stockage est indisponible ou au fait qu’un autre problème est survenu et a empêché la copie des fichiers vers le nœud.
- **Échecs d’application**
	- Le processus de la tâche spécifié par la ligne de commande peut également échouer. Le processus est considéré comme ayant échoué lorsqu’un code de sortie différent de zéro est renvoyé par le processus exécuté par la tâche.
	- Pour les échecs d’application, il est possible de configurer Batch pour relancer automatiquement la tâche autant de fois que l’utilisateur l’aura spécifié.
- **Échecs de contrainte**
	- Une contrainte peut être définie, qui spécifie la durée maximale d’exécution d’un travail ou d’une tâche, le *maxWallClockTime*. Cela peut s’avérer utile lorsqu’il s’agit de terminer les tâches « suspendues ».
	- Lorsque le nombre maximal de tentatives est atteint, la tâche est marquée comme *terminée*, mais le code de sortie est défini sur le `0xC000013A`, et le champ *schedulingError* est marqué comme `{ category:"ServerError", code="TaskEnded"}`.

### Débogage des échecs d’application

Pendant l’exécution, une application peut produire des diagnostics pouvant être utilisés pour résoudre les problèmes. Comme mentionné dans la section [Fichiers et répertoires](#files), le service Batch envoie stdout et stderr aux fichiers `stdout.txt` et `stderr.txt` situés dans le répertoire de tâche sur le nœud de calcul. En utilisant [ComputeNode.GetNodeFile][net_getfile_node] et [CloudTask.GetNodeFile][net_getfile_task] dans l’API Batch .NET, vous pouvez récupérer ces fichiers et d’autres à des fins de dépannage.

Même les débogages étendus peuvent être effectués en se connectant à un nœud de calcul à l’aide du *Bureau à distance*. Vous pouvez [obtenir un fichier de protocole de bureau à distance depuis un nœud][rest_rdp] (API Batch REST) ou d’utiliser la méthode [ComputeNode.GetRDPFile][net_rdp] (API Batch .NET) pour la connexion à distance.

>[AZURE.NOTE] Pour vous connecter à un nœud via RDP, vous devez d’abord créer un utilisateur sur le nœud. [Ajouter un compte d’utilisateur à un nœud][rest_create_user] dans l’API Batch REST ou l’utilisation de [ComputeNode.CreateComputeNodeUser][net_create_user] dans Batch.NET.

### Prise en compte des échecs ou des interruptions de tâche

Les tâches peuvent parfois échouer ou être interrompues. L’application de la tâche elle-même peut échouer, le nœud sur lequel elle s’exécute peut être redémarré ou retiré du pool pendant le redimensionnement de ce dernier si la stratégie de désallocation définie prévoit la suppression immédiate des nœuds, sans attendre la fin des tâches. Dans tous les cas, la tâche peut être automatiquement remise en file d’attente par Batch pour exécution sur un autre nœud.

Un problème intermittent peut également provoquer la suspension d’une tâche ou ralentir son exécution. Une durée maximale d’exécution de la tâche peut être définie, et, en cas de dépassement, Batch interrompt l’application de la tâche.

### Résolution des problèmes de « mauvais » nœuds de calcul

Quand certaines de vos tâches échouent, votre application cliente Batch ou un service peut examiner les métadonnées des tâches en échec pour identifier un nœud présentant un dysfonctionnement. Chaque nœud d’un pool se voit attribuer un ID unique et le nœud sur lequel s’exécute une tâche est inclus dans les métadonnées de la tâche. Une fois ce nœud identifié, vous pouvez effectuer plusieurs actions :

- **Redémarrer le nœud** ([REST][rest_reboot] | [.NET][net_reboot])

	Le fait de redémarrer le nœud peut parfois résoudre des problèmes latents comme des processus bloqués ou défaillants. Notez que si votre pool utilise une tâche de démarrage ou si votre travail utilise une tâche de préparation, ces deux éléments s’exécuteront au redémarrage du nœud.

- **Réinitialiser le nœud** ([REST][rest_reimage] | [.NET][net_reimage])

	Cette opération réinstalle le système d’exploitation sur le nœud. Comme avec le redémarrage d’un nœud, les tâches de démarrage et celles de préparation d’un travail sont relancées une fois le nœud réinitialisé.

- **Supprimer le nœud du pool** ([REST][rest_remove] | [.NET][net_remove])

	Il est parfois nécessaire de supprimer entièrement le nœud à partir du pool.

- **Désactiver la planification des tâches sur le nœud** ([REST][rest_offline] | [.NET][net_offline])

	Cette opération est efficace puisqu’elle place le nœud « hors connexion ». Ainsi, aucune tâche ultérieure ne peut lui être assignée. Toutefois, le nœud est autorisé à poursuivre son exécution et à rester dans le pool. Cela vous permet de faire une recherche approfondie sur la cause des échecs sans perdre les données de la tâche en échec et sans que le nœud n’occasionne d’autres échecs de tâche supplémentaires. Par exemple, vous pouvez désactiver la planification des tâches sur le nœud, puis vous connecter à distance pour examiner les journaux des événements de ce nœud ou encore résoudre d’autres problèmes. Une fois que vous avez terminé votre recherche, vous pouvez remettre le nœud en ligne en activant la planification des tâches ([REST][rest_online], [.NET][net_online]), ou effectuer l’une des actions ci-dessus.

> [AZURE.IMPORTANT] Pour chaque action mentionnée ci-dessus (redémarrer, réinitialiser, supprimer, désactiver la planification des tâches), vous pouvez indiquer la manière dont les tâches en cours d’exécution sur le nœud sont gérées lorsque vous effectuez l’action. Par exemple, lorsque vous désactivez la planification des tâches sur un nœud avec la bibliothèque cliente Batch.NET, vous pouvez indiquer une valeur d’énumération [DisableComputeNodeSchedulingOption][net_offline_option]. Celle-ci sert à préciser s’il faut **interrompre** les tâches en cours d’exécution, les **remettre en file d’attente** pour les planifier sur d’autres nœuds ou finaliser les tâches en cours avant d’exécuter l’action (**TaskCompletion**).

## Étapes suivantes

- Créez votre première application Batch en suivant les étapes dans [Démarrer avec la bibliothèque de commandes Azure pour .NET](batch-dotnet-get-started.md)
- Téléchargez et créez l’exemple de projet [Batch Explorer][batch_explorer_project] à utiliser lors du développement de vos solutions Batch. En utilisant l’explorateur Batch, vous pouvez effectuer ce qui suit, et bien plus encore :
  - Analysez et manipulez les pools, les travaux et les tâches au sein de votre compte Batch
  - Téléchargez `stdout.txt`, `stderr.txt` et d’autres fichiers à partir des nœuds
  - Créez des utilisateurs sur les nœuds et téléchargez les fichiers RDP pour la connexion à distance

[1]: ./media/batch-api-basics/node-folder-structure.png

[about_cloud_services]: https://azure.microsoft.com/documentation/articles/fundamentals-application-models/#tell-me-about-cloud-services
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloud_service_sizes]: https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies

[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

<!---HONumber=AcomDC_0413_2016-->