<properties
	pageTitle="Vue d’ensemble des fonctionnalités d’Azure Batch pour développeurs | Microsoft Azure"
	description="Découvrez les fonctionnalités du service Batch et de ses API du point de vue du développeur."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="08/22/2016"
	ms.author="marsma"/>

# Présentation des fonctionnalités du service Batch pour les développeurs

Dans cette vue d’ensemble des principaux composants du service Azure Batch, nous aborderons les fonctionnalités et ressources du service Batch qui permettent aux développeurs de créer des solutions de calcul parallèles à grande échelle.

Si vous développez une application ou un service de calcul distribué, qui émet des appels d’API [Batch REST][batch_rest_api] directs, ou si vous utilisez l’un des [Kits de développement logiciel (SDK) Batch](batch-technical-overview.md#batch-development-apis), vous utiliserez la plupart des ressources et des fonctionnalités présentées dans cet article.

> [AZURE.TIP] Pour une présentation plus poussée du service Batch, consultez l’article [Notions de base d’Azure Batch](batch-technical-overview.md).

## Flux de travail du service Batch

Le flux de travail de haut niveau suivant est caractéristique de la plupart des applications et services utilisant le service Batch pour le traitement des charges de travail parallèles :

1. Chargez les **fichiers de données** que vous souhaitez traiter vers un compte [Azure Storage][azure_storage]. Le service Batch inclut la prise en charge intégrée de l’accès au stockage d’objets Blob Azure ; lorsque les tâches sont exécutées, celles-ci peuvent télécharger les fichiers en question vers les [nœuds de calcul](#compute-node).

2. Chargez le **fichiers d’application** que les tâches exécuteront. Ces fichiers peuvent être des fichiers binaires ou des scripts, accompagnés de leurs dépendances, et sont exécutés par les tâches dans vos travaux. Vos tâches peuvent télécharger ces fichiers à partir de votre compte Azure Storage ; vous pouvez également utiliser la fonctionnalité [packages d’application](#application-packages) de Batch pour le déploiement et la gestion des applications.

3. Créez un [pool](#pool) de nœuds de calcul. Lors de la création d’un pool, vous devez spécifier le nombre de nœuds de calcul du pool, leur taille ainsi que le système d’exploitation. Pour chaque exécution de tâche dans votre travail, cette dernière est exécutée sur l’un des nœuds du pool.

4. Créez un [travail](#job). Un travail gère une collection de tâches. Vous associez chaque travail à un pool spécifique, dans lequel s’exécuteront les tâches du travail concerné.

5. Ajoutez des [tâches](#task) au travail. Chaque tâche exécute l’application ou le script téléchargés afin de traiter les fichiers de données téléchargés à partir de votre compte de stockage. Lorsque chaque tâche est terminée, celle-ci peut télécharger sa sortie vers Azure Storage.

6. Surveillez la progression du travail et récupérez la sortie de la tâche à partir d’Azure Storage.

Les sections suivantes portent sur ces points ainsi que sur les autres ressources de Batch qui permettront l’exécution de votre scénario de calcul distribué.

> [AZURE.NOTE] Un [compte Batch](batch-account-create-portal.md) vous est nécessaire pour utiliser le service Batch. En outre, pratiquement toutes les solutions utilisent un compte [Azure Storage][azure_storage] pour le stockage et la récupération de fichiers. Le service Batch prend actuellement en charge uniquement le type de compte de stockage à **usage général**, comme décrit à l’étape 5 de la section [Créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) dans l’article [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).

## Ressources du service Batch

Certaines des ressources suivantes (comptes, nœuds de calcul, pools, travaux et tâches) sont requises par toutes les solutions qui utilisent le service Batch. D’autres, telles que les planifications de travaux et les packages d’applications, sont utiles, mais facultatives.

- [Compte](#account)
- [Nœud de calcul](#compute-node)
- [Pool](#pool)
- [Travail](#job)

  - [Planifications de travaux](#scheduled-jobs)

- [Tâche](#task)

  - [Tâche de démarrage](#start-task)
  - [Tâche du gestionnaire de travaux](#job-manager-task)
  - [Tâches de préparation et lancement](#job-preparation-and-release-tasks)
  - [Tâche multi-instance (MPI)](#multi-instance-tasks)
  - [Dépendances de la tâche](#task-dependencies)

- [Packages d’applications](#application-packages)

## Compte

Un compte Batch est une entité identifiée de façon unique au sein du service Batch. Tout le traitement s’effectue via un compte Batch. Lorsque vous effectuez des opérations avec le service Batch, vous avez besoin du nom et d’une des clés du compte. Vous pouvez [créer un compte Azure Batch à l’aide du portail Azure](batch-account-create-portal.md).

## Nœud de calcul

Un nœud de calcul est une machine virtuelle Azure dédiée au traitement d’une partie de la charge de travail de votre application. La taille d’un nœud détermine le nombre de cœurs du processeur, la capacité de mémoire et la taille du système de fichiers local qui lui est allouée. Vous pouvez créer des pools de nœuds Windows ou Linux à l’aide d’images d’Azure Cloud Services ou du Marketplace de machines virtuelles. Pour plus d’informations sur ces options, voir la section suivante [Pool](#pool).

Les nœuds peuvent exécuter tout fichier exécutable ou script pris en charge par l’environnement de système d’exploitation du nœud. Cela inclut les scripts *.exe, *.cmd, *.bat et PowerShell pour Windows, les fichiers binaires, ainsi que les scripts shell et Python pour Linux.

Tous les nœuds de calcul Batch incluent également les éléments suivants :

- Une [structure de dossiers](#files-and-directories) standard et des [variables d’environnement](#environment-settings-for-tasks) associées pouvant être référencées par les tâches.
- Paramètres de **pare-feu** configurés pour le contrôle de l’accès.
- Un [accès distant](#connecting-to-compute-nodes) aux nœuds Windows (protocole RDP [Remote Desktop Protocol]) et Linux (Secure Shell [SSH]).

> [AZURE.NOTE] La prise en charge de Linux dans Azure Batch se trouve actuellement en version préliminaire. Pour plus d’informations, voir [Configurer des nœuds de calcul Linux dans des pools Azure Batch](batch-linux-nodes.md).

## Pool

Un pool est une collection de nœuds sur lesquels votre application s’exécute. Vous pouvez créer le pool manuellement. Il peut également être créé automatiquement par le service Batch lorsque vous spécifiez le travail à accomplir. Vous pouvez créer et gérer un pool qui répond aux besoins en ressources de votre application. Un pool ne peut être utilisé que par le compte Batch dans lequel il a été créé. Un compte Batch peut avoir plusieurs pools.

Les pools Azure Batch sont créés en parallèle à la plate-forme de calcul Azure principale. Ils permettent d’effectuer des allocations à grande échelle, l’installation d’applications, la distribution de données, des analyses de fonctionnement et des ajustements flexibles du nombre de nœuds de calcul au sein d’un pool ([mise à l’échelle](#scaling-compute-resources)).

Chaque nœud ajouté à un pool se voit attribuer un nom unique et l’adresse IP. Lorsqu’un nœud est supprimé d’un pool, toutes les modifications apportées au système d’exploitation ou aux fichiers sont perdues, et son nom et son adresse IP sont libérés pour une utilisation ultérieure. Lorsqu’un nœud quitte un pool, sa durée de vie est terminée.

Lorsque vous créez un pool, vous pouvez spécifier les attributs suivants :

- **Système d’exploitation** et **version** de nœud de calcul

	Deux options s’offrent à vous lorsque vous sélectionnez un système d’exploitation pour les nœuds du pool : **Configuration de la machine virtuelle** et **Configuration de Cloud Services**.

	La **Configuration de la machine virtuelle** fournit des images Linux et Windows pour les nœuds de calcul à partir du [Marketplace de machines virtuelles Azure][vm_marketplace]. Lorsque vous créez un pool contenant des nœuds de la Configuration de la machine virtuelle, vous devez spécifier la taille des nœuds ainsi que la **référence de l’image de la machine virtuelle** et la **référence de l’agent de nœud** du Batch à installer sur les nœuds. Pour plus d’informations sur la spécification des propriétés de pool, voir [Configurer des nœuds de calcul Linux dans des pools Azure Batch](batch-linux-nodes.md).

	La **configuration des services cloud** fournit *uniquement* des nœuds de calcul Windows. Les systèmes d’exploitation disponibles pour les pools de configuration des services coud sont répertoriés dans [Versions du SE invité et matrice de compatibilité du Kit de développement logiciel (SDK) Azure](../cloud-services/cloud-services-guestos-update-matrix.md). Lorsque vous créez un pool qui contient des nœuds Cloud Services, vous devez spécifier uniquement la taille du nœud et sa *famille de systèmes d’exploitation*. Lorsque vous créez des pools de nœuds de calcul Windows, vous utilisez le plus souvent les services Cloud Services.

    - La *famille de système d’exploitation* détermine également les versions de .NET qui sont installées avec le système d’exploitation.
	- Comme avec les rôles de travail dans Cloud Services, vous pouvez spécifier une *Version du système d’exploitation* (pour plus d’informations sur les rôles de travail, consultez la section [En savoir plus sur Cloud Services](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) de l’article [Vue d’ensemble de Cloud Services](../cloud-services/cloud-services-choose-me.md)).
    - Comme avec les rôles de travail, nous vous recommandons de spécifier `*` comme *version du système d’exploitation* afin que les nœuds soient automatiquement mis à niveau et qu’aucun travail supplémentaire ne soit requis pour gérer ces nouvelles versions. La principale raison de sélectionner une version de système d’exploitation spécifique est d’assurer la compatibilité des applications, ce qui permet aux tests de compatibilité descendante d’être réalisés avant d’autoriser la mise à jour de la version. Une fois validée, la *version du système d’exploitation* du pool peut être mise à jour et la nouvelle image du système d’exploitation peut être installée. Toutes les tâches en cours d’exécution seront interrompues et remises en file d’attente.

- **Taille des nœuds**

	Les tailles des nœuds de calcul de la **configuration des services cloud** sont répertoriées dans [Tailles de services cloud](../cloud-services/cloud-services-sizes-specs.md). Le service Batch prend en charge l’ensemble des tailles de Cloud Services, à l’exception de `ExtraSmall`.

	Les tailles de nœud de calcul de la **configuration de machine virtuelle** sont répertoriées dans [Tailles des machines virtuelles dans Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) et [Tailles des machines virtuelles dans Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Le service Batch prend en charge l’ensemble des tailles de machine virtuelle Azure, à l’exception de `STANDARD_A0` et de celles comprises dans Premium Storage (série `STANDARD_GS`, `STANDARD_DS`, et `STANDARD_DSV2`).

	Lorsque vous sélectionnez une taille de nœud, vous devez prendre en compte les caractéristiques et les exigences de la ou des applications qui vont être exécutées sur le nœud. La taille du nœud est en général sélectionnée en supposant qu’une tâche s’exécutera sur un nœud à la fois. Lorsque des aspects tels que la nature multithread de l’application et le volume de mémoire qu’elle utilise sont pris en compte, vous pouvez déterminer la taille de nœud la mieux adaptée et la plus rentable. Plusieurs tâches et, par conséquent, plusieurs instances d’application peuvent [s’exécuter en parallèle](batch-parallel-node-tasks.md), auquel cas vous choisirez généralement un nœud plus volumineux. Pour plus d’informations, consultez la section suivante « Stratégie de planification de tâches ».

	Tous les nœuds du pool ont la même taille. Si vous utilisez des applications dont la configuration requise et/ou les niveaux de charge sont différents, vous devez utiliser des pools distincts.

- **Nombre cible de nœuds**

	Il s’agit du nombre de nœuds de calcul que vous souhaitez déployer dans le pool. On parle de *cible*, car dans certaines situations, votre pool ne pourra peut-être pas atteindre le nombre de nœuds souhaité. Un pool peut ne pas atteindre le nombre souhaité de nœuds s’il atteint le [quota de cœurs](batch-quota-limit.md#batch-account-quotas) de votre compte Batch, ou si une formule de mise à l’échelle automatique que vous avez appliquée au pool limite le nombre maximal de nœuds (voir la section « Stratégie de mise à l’échelle » ci-après).

- **Stratégie de mise à l’échelle**

	En plus de spécifier un nombre statique de nœuds, vous pouvez écrire et appliquer une [formule de mise à l’échelle automatique](#scaling-compute-resources) à un pool. Le service Batch évalue régulièrement la formule et ajuste le nombre de nœuds dans le pool en fonction de différents paramètres de pool, de travail et de tâche que vous pouvez spécifier.

- **Stratégie de planification de tâches**

	L’option de configuration [Nombre maximal de tâches par nœud](batch-parallel-node-tasks.md) détermine le nombre maximal de tâches qui peuvent être exécutées en parallèle sur chaque nœud de calcul au sein du pool.

	La configuration par défaut prévoit d’exécuter une tâche sur un nœud à la fois, mais dans certains scénarios, il est parfois préférable que plusieurs tâches soient exécutées simultanément sur un nœud. Consultez [l’exemple de scénario](batch-parallel-node-tasks.md#example-scenario) de l’article sur les [tâches de nœud simultanées](batch-parallel-node-tasks.md) pour découvrir comment bénéficier de plusieurs tâches par nœud.

	Vous pouvez également spécifier un *type de remplissage* qui détermine si l’option Batch répartit les tâches uniformément sur tous les nœuds dans un pool, ou remplit chaque nœud avec le nombre maximal de tâches avant d’affecter des tâches à un autre nœud.

- **État de communication** des nœuds de calcul

	Dans la plupart des scénarios, les tâches fonctionnent indépendamment et n’ont pas besoin de communiquer les unes avec les autres. Il existe néanmoins des applications dans lesquelles les tâches doivent communiquer (comme dans les [scénarios impliquant des applications MPI](batch-mpi.md)).

	Vous pouvez configurer un pool pour permettre la communication entre les nœuds qu’il contient (**communication entre les nœuds**). Lorsque la communication entre les nœuds est activée, les nœuds des pools Configuration de Cloud Services peuvent communiquer entre eux sur les ports supérieurs à 1100, et les pools Configuration de la machine virtuelle ne limitent pas le trafic sur les ports.

	Notez que l’activation de la communication entre les nœuds affecte également le placement des nœuds au sein des clusters et peut limiter le nombre maximal de nœuds dans un pool en raison des restrictions de déploiement. Si votre application ne nécessite pas de communication entre les nœuds, le service Batch peut éventuellement allouer au pool un grand nombre de nœuds issus de différents centres de données et clusters pour accroître la puissance de traitement parallèle.

- **Tâche de démarrage** pour les nœuds de calcul

	La *tâche de démarrage* facultative s’exécute sur chacun des nœuds rejoignant le pool, ainsi qu’à chaque redémarrage ou réinitialisation d’un nœud. La tâche de démarrage est particulièrement utile pour la préparation des nœuds de calcul à l’exécution de tâches, telles que l’installation des applications que vos tâches exécutent sur les nœuds de calcul.

- **Packages d’applications**

	Vous pouvez spécifier des [packages d’applications](#application-packages) à déployer sur les nœuds de calcul dans le pool. Les packages d’applications permettent un déploiement simplifié et le contrôle de version des applications exécutées par vos tâches. Les packages d’applications que vous spécifiez pour un pool sont installés sur chaque nœud qui rejoint le pool, et à chaque fois qu’un nœud est redémarré ou réinitialisé.

- **Configuration réseau**

	Vous pouvez spécifier l’ID d’un [réseau virtuel (VNet)](../virtual-network/virtual-networks-overview.md) Azure dans lequel les nœuds de calcul du pool doivent être créés. Les conditions pour la spécification d’un réseau virtuel pour votre pool sont décrites à la section [Ajout d’un pool à un compte][vnet] de la référence de l’API REST Batch.

> [AZURE.IMPORTANT] Tous les comptes Batch ont un **quota** par défaut qui limite le nombre de **cœurs** (et par conséquent, celui des nœuds de calcul) dans un compte Batch. Pour en savoir plus sur les quotas par défaut et obtenir des instructions sur comment [augmenter un quota](batch-quota-limit.md#increase-a-quota) (par exemple, le nombre maximal de cœurs dans votre compte Batch), consultez l’article [Quotas et limites pour le service Azure Batch](batch-quota-limit.md). Si vous vous demandez pourquoi votre pool n’atteint pas plus de X nœuds, ce quota de cœurs en est peut-être la raison.

## Travail

Un travail est une collection de tâches. Il gère la façon dont le calcul est effectué par ses tâches sur les nœuds de calcul d’un pool.

- Le travail spécifie le **pool** sur lequel le travail sera exécuté. Vous pouvez créer un nouveau pool pour chaque tâche, ou utiliser un pool pour plusieurs travaux. Vous pouvez créer un pool pour chaque travail associé à une planification de travail, ou pour tous les travaux associés à une planification de travail.

- Une **priorité de travail** facultative peut être spécifiée. Quand un travail dont la priorité est supérieure à celle des autres travaux en cours est envoyé, les tâches du travail dont la priorité est la plus élevée sont insérées dans la file d’attente devant les tâches des travaux de priorité inférieure. Les tâches de priorité inférieure qui sont déjà en cours d’exécution ne sont pas reportées.

- Vous pouvez utiliser des **contraintes** de travail pour spécifier certaines limites pour vos travaux :

	Vous pouvez définir une **durée maximale** pour les travaux ; si la durée d’exécution du travail est supérieure à la durée maximale spécifiée, le travail et toutes les tâches qui lui sont associées seront terminés.

	Le service Batch peut détecter, puis relancer des tâches ayant échoué. Vous pouvez spécifier le **nombre maximal de tentatives de tâche** en tant que contrainte, et indiquer notamment si une tâche doit *toujours* être systématiquement relancée ou ne *jamais* l’être. Lorsqu’une tâche est relancée, cela signifie qu’elle est remise en file d’attente afin d’être réexécutée.

- Votre application cliente peut ajouter des tâches à un travail. Vous pouvez également spécifier une [tâche du gestionnaire de travaux](#job-manager-task). Une tâche de ce type contient les informations nécessaires à la création des tâches requises pour un travail et elle s’exécute sur l’un des nœuds de calcul du pool. La tâche du gestionnaire de travaux est gérée spécifiquement par Batch : elle est mise en file d’attente dès que le travail est créé et elle est redémarrée en cas d’échec. Une tâche du gestionnaire de travaux est *requise* pour les travaux créés dans le cadre d’une [planification de travail](#scheduled-jobs), car il s’agit du seul moyen de définir les tâches avant que le travail ne soit instancié.

- Par défaut, l’état des tâches est maintenu comme actif lorsque toutes les tâches du travail sont terminées. Vous pouvez modifier cette configuration pour terminer automatiquement le travail lorsque toutes les tâches afférentes ont été accomplies. Définissez la propriété **onAllTasksComplete** ([OnAllTasksComplete][net_onalltaskscomplete] dans Batch .NET) sur *terminatejob* pour terminer automatiquement le travail lorsque l’état de toutes les tâches afférentes est défini comme terminé.

	Notez que le service Batch considère les travaux ne présentant *aucune* tâche comme des travaux dont toutes les tâches sont terminées. C’est la raison pour laquelle cette option est généralement utilisée avec une [tâche de gestionnaire de travaux](#job-manager-task). Si vous souhaitez définir l’arrêt automatique d’un travail sans utiliser de gestionnaire de travaux, définissez initialement la propriété **onAllTasksComplete** d’un nouveau travail sur *noaction*, puis sur *terminatejob* seulement une fois que vous avez terminé d’ajouter des tâches au travail.

### Priorité de travail

Vous pouvez établir une priorité pour les travaux que vous créez dans Batch. Le service Batch utilise les valeurs de priorité du travail pour déterminer l’ordre de planification du travail dans un compte (à ne pas confondre avec le [travail planifié](#scheduled-jobs)). Les valeurs de priorité sont comprises entre -1000 et 1000, -1000 étant la priorité la plus basse et 1000 la plus élevée. Vous pouvez mettre à jour la priorité d’un travail à l’aide de l’opération [Mettre à jour les propriétés d’un travail][rest_update_job] \(Batch REST) ou en modifiant la propriété [CloudJob.Priority][net_cloudjob_priority] \(Batch .NET).

Dans un même compte, les travaux de priorité supérieure sont prioritaires en termes de planification sur les travaux de priorité inférieure. Un travail à priorité supérieure dans un compte n’est pas prioritaire en termes de planification sur un autre travail à priorité inférieure dans un autre compte.

La planification de travail entre pools est indépendante. Entre des pools différents, un travail à priorité supérieure n’est pas systématiquement planifié en premier, si le pool auquel il est associé n’a pas suffisamment de nœuds inactifs. Dans le même pool, les travaux avec le même niveau de priorité ont autant de chance d’être planifiés.

### Scheduled jobs

Les [planifications de travaux][rest_job_schedules] vous permettent de créer des travaux périodiques au sein du service Batch. Une planification de travail indique le moment où exécuter des tâches et les spécifications des travaux à exécuter. Vous pouvez spécifier la durée de la planification (la durée proprement dite et l’entrée en vigueur de la planification) et la fréquence à laquelle des travaux doivent être créés.

## Task

Une tâche est une unité de calcul associée à un travail. Elle s’exécute sur un nœud. Les tâches sont affectées à un nœud afin d’être exécutées ou sont mises en file d’attente jusqu’à ce qu’un nœud soit disponible. Une tâche exécute un ou plusieurs programmes ou scripts sur un nœud de calcul afin d’effectuer le travail nécessaire.

Lorsque vous créez une tâche, vous pouvez spécifier les éléments suivants :

- La **ligne de commande** de la tâche. Il s’agit de la ligne de commande qui exécute votre application ou script sur le nœud de calcul.

	Il est important de noter que la ligne de commande ne s’exécute pas réellement sous un interpréteur de commandes. Elle ne peut donc pas tirer parti en mode natif des fonctionnalités de l’interpréteur de commandes, comme l’expansion de [variables d’environnement](#environment-settings-for-tasks) (qui inclut le `PATH`). Pour tirer parti de ces fonctionnalités, vous devez appeler l’interpréteur de commandes dans la ligne de commande, par exemple, en lançant `cmd.exe` sur les nœuds Windows ou `/bin/sh` sur Linux :

	`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

	`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

	Si vos tâches doivent exécuter une application ou un script hors du `PATH` du nœud ou référencer des variables d’environnement, appelez l’interpréteur de commandes explicitement dans la ligne de commande de la tâche.

- **Fichiers de ressources** contenant les données à traiter. Ces fichiers sont automatiquement copiés sur le nœud depuis le stockage d’objets blob dans un compte Azure Storage **à usage général** avant l’exécution de la ligne de commande de la tâche. Pour plus d’informations, consultez les sections [Tâche de démarrage](#start-task) et [Fichiers et répertoires](#files-and-directories).

- Les **variables d’environnement** requises par l’application. Pour plus d’informations, consultez la section [Paramètres d’environnement des tâches](#environment-settings-for-tasks).

- Les **contraintes** sous lesquelles la tâche doit s’exécuter. Par exemple, la durée maximale pendant laquelle la tâche est autorisée à s’exécuter, le nombre maximal de nouvelles tentatives en cas d’échec de la tâche, ainsi que la durée maximale pendant laquelle les fichiers du répertoire de travail de la tâche sont conservés.

- **Packages d’applications** à déployer sur le nœud de calcul sur lequel l’exécution de la tâche est planifiée. Les [packages d’applications](#application-packages) permettent le déploiement simplifié et le contrôle de version des applications exécutées par vos tâches. Les packages d’applications au niveau des tâches sont particulièrement utiles dans les environnements de pool partagé, où différentes tâches sont exécutées sur un même pool et le pool n’est pas supprimé lorsqu’un travail est terminé. Si votre travail présente moins de tâches que le pool ne contient de nœuds, les packages d’applications au niveau des tâches peuvent réduire le transfert de données, votre application n’étant déployée que sur les nœuds exécutant les tâches.

Outre les tâches que vous pouvez définir pour effectuer des calculs sur un nœud, les tâches spéciales suivantes sont également fournies par le service Batch :

- [Tâche de démarrage](#start-task)
- [Tâche du gestionnaire de travaux](#job-manager-task)
- [Tâches de préparation et lancement](#job-preparation-and-release-tasks)
- [Tâches multi-instances (MPI)](#multi-instance-tasks)
- [Dépendances de la tâche](#task-dependencies)

### Tâche de démarrage

En associant une **tâche de démarrage** à un pool, vous pouvez préparer l’environnement d’exploitation de ses nœuds. Par exemple, vous pouvez effectuer des actions telles que l’installation des applications que vos tâches exécuteront et le démarrage des processus en arrière-plan. La tâche de démarrage s’exécute à chaque démarrage d’un nœud tant qu’il reste dans le pool, notamment lorsque le nœud est ajouté au pool en premier lieu, redémarré ou réinitialisé.

Le principal avantage de la tâche de démarrage est qu’elle peut contenir toutes les informations nécessaires pour configurer un nœud de calcul et installer les applications requises pour l’exécution de la tâche. Par conséquent, pour augmenter le nombre de nœuds dans un pool, il suffit de spécifier un nouveau nombre de nœuds cibles. Batch dispose déjà de toutes les informations nécessaires pour configurer les nouveaux nœuds et les préparer à accepter des tâches.

Comme avec n’importe quelle tâche Azure Batch, vous pouvez spécifier une liste de **fichiers de ressources** dans [Azure Storage][azure_storage], en plus d’une **ligne de commande** à exécuter. Batch copie d’abord les fichiers de ressources sur le nœud à partir d’Azure Storage, puis exécute la ligne de commande. Pour une tâche de démarrage de pool, la liste des fichiers contient généralement l’application de la tâche et ses dépendances.

Toutefois, elle peut également inclure des données de référence qui doivent être utilisées par toutes les tâches exécutées sur le nœud de calcul. Par exemple, la ligne de commande d’une tâche de démarrage peut effectuer une opération `robocopy` afin de copier les fichiers d’application (qui ont été spécifiés comme fichiers de ressources et téléchargés vers le nœud) à partir du [répertoire de travail](#files-and-directories) de la tâche de démarrage vers le [dossier partagé](#files-and-directories), puis exécuter un MSI ou `setup.exe`.

> [AZURE.IMPORTANT] Le service Batch prend actuellement en charge *uniquement* le type de compte de stockage à **usage général**, comme décrit à l’étape 5 de la section [Créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) dans l’article [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md). Vos tâches Batch (y compris les tâches standard, de démarrage, de préparation des travaux et de validation des travaux) doivent spécifier des fichiers de ressources se trouvant *uniquement* dans les comptes de stockage à **usage général**.

Il est généralement préférable pour le service Batch d’attendre que la tâche de démarrage soit terminée avant de considérer que le nœud est prêt à recevoir des tâches, mais vous pouvez configurer ce paramètre.

Si une tâche de démarrage échoue sur un nœud du pool, l’état du nœud est mis à jour pour refléter l’échec et le nœud n’est plus disponible pour les tâches à affecter. Une tâche de démarrage peut échouer en cas de problème de copie des fichiers de ressources depuis le stockage, ou si le processus exécuté par sa ligne de commande retourne un code de sortie différent de zéro.

Si vous ajoutez ou mettez à jour la tâche de démarrage pour un pool *existant*, vous devez redémarrer ses nœuds de calcul pour la tâche de démarrage à appliquer aux nœuds.

### Tâche du gestionnaire de travaux

Vous utilisez généralement une **tâche du gestionnaire de travaux** pour contrôler et/ou surveiller l’exécution des travaux, par exemple, pour créer et soumettre les tâches d’un travail, identifier les autres tâches à exécuter et déterminer lorsque le travail est terminé. Toutefois, une tâche du gestionnaire de travaux n’est pas limitée à ces activités. C’est une tâche à part entière qui peut effectuer toutes les actions requises pour le travail. Par exemple, une tâche du gestionnaire de travaux peut télécharger un fichier spécifié en tant que paramètre, en analyser le contenu et soumettre des tâches supplémentaires en fonction de ce contenu.

Une tâche du gestionnaire de travaux est lancée avant toutes les autres tâches. Elle fournit les fonctionnalités suivantes :

- Elle est automatiquement soumise en tant que tâche par le service Batch lorsque le travail est créé.

- Elle est planifiée pour s’exécuter avant les autres tâches d’un projet.

- Le nœud associé est le dernier à être supprimé d’un pool lorsque la taille de ce dernier diminue.

- Son arrêt peut être lié à l'arrêt de toutes les tâches du travail.

- Une tâche de gestionnaire de travaux reçoit la priorité la plus élevée lorsqu’elle doit être redémarrée. Si aucun nœud inactif n’est disponible, le service Batch peut mettre fin à une tâche en cours d’exécution dans le pool pour libérer de l’espace pour l’exécution de la tâche du gestionnaire de travaux.

- Une tâche du gestionnaire de travaux associée à un travail n’a pas la priorité sur les tâches d’autres travaux. Parmi les travaux, seules les priorités au niveau du travail sont observées.

### Tâches de préparation et lancement

Batch fournit des tâches de préparation des travaux pour la configuration de l’exécution des travaux préliminaire. Les tâches de validation des travaux sont destinées à la maintenance ou au nettoyage postérieurs aux travaux.

- **Tâche de préparation de travail** : une tâche de préparation de travail s’exécute sur tous les nœuds de calcul qui sont planifiés pour exécuter les tâches avant l’exécution des autres tâches du travail. Vous pouvez utiliser une tâche de préparation du travail pour copier les données partagées par toutes les tâches, mais propres au travail, par exemple.
- **Tâche de validation de travail** : lorsqu’un travail est terminé, une tâche de validation de travail s’exécute sur chaque nœud du pool ayant exécuté au moins une tâche. Vous pouvez utiliser une tâche de validation de travail pour supprimer les données copiées par la tâche de préparation de travail, ou pour compresser et charger des données de journaux de diagnostic, par exemple.

Les tâches de préparation et de validation de travail vous permettent de spécifier une ligne de commande à exécuter lorsque la tâche est appelée. Elles offrent des fonctionnalités telles que le téléchargement de fichiers, une exécution élevée, des variables d’environnement personnalisées, une durée d’exécution maximale, le nombre de tentatives et la durée de rétention des fichiers.

Pour plus d’informations sur les tâches de préparation de travail et de validation, consultez [Exécuter les tâches de préparation de travail et de validation sur les nœuds de calcul Azure Batch](batch-job-prep-release.md).

### Tâche multi-instance

Une [tâche multi-instance](batch-mpi.md) est une tâche configurée pour s’exécuter simultanément sur plusieurs nœuds de calcul. Avec des tâches multi-instances, vous pouvez permettre des scénarios de calcul haute performance qui requièrent un groupe de nœuds de calcul alloués ensemble pour traiter une seule et même charge de travail (comme MPI).

Pour une présentation détaillée de l’exécution des travaux MPI dans Batch à l’aide de la bibliothèque .NET Batch, consultez l’article [Utiliser les tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](batch-mpi.md).

### Dépendances de la tâche

La [dépendance entre tâches](batch-task-dependencies.md), comme son nom l’indique, vous permet de préciser que l’exécution d’une tâche dépend de l’achèvement préalable d’autres tâches. Cette fonctionnalité prend en charge des situations dans lesquelles une tâche « en aval » consomme la sortie d’une tâche « en amont », ou lorsqu’une tâche en amont effectue une initialisation requise par une tâche en aval. Pour utiliser cette fonctionnalité, vous devez d’abord activer les dépendances de tâche sur la tâche Batch. Ensuite, pour chaque tâche qui dépend d’une autre (ou de plusieurs autres), vous devez spécifier les tâches dont elle dépend.

Avec l’interdépendance des tâches, vous pouvez configurer des scénarios suivants :

* *taskB* dépend de *taskA* (l’exécution de *taskB* ne commence pas tant que celle de *taskA* n’est pas terminée).
* *taskC* dépend de *taskA* et de *taskB*.
* *taskD* dépend d’une plage de tâches, notamment des tâches *1* à *10*, avant de pouvoir s’exécuter.

Consultez [Task dependencies in Azure Batch](batch-task-dependencies.md) (Dépendances dans Azure Batch) et l’exemple de code [TaskDependencies][github_sample_taskdeps] dans le référentiel GitHub [azure-batch-samples][github_samples] pour plus d’informations détaillées sur cette fonctionnalité.

## Paramètres d'environnement des tâches

Chaque tâche qui s’exécute dans un travail Batch a accès aux variables d’environnement définies à la fois par le service Batch (définies par le service, comme indiqué dans le tableau suivant) et par des variables d’environnement personnalisées que vous pouvez définir pour les tâches. Les applications et les scripts exécutés par les tâches sur les nœuds ont accès à ces variables d’environnement pendant leur exécution.

Vous pouvez définir des variables d’environnement personnalisées au niveau de la tâche ou du travail en remplissant la propriété de *paramètres d’environnement* pour ces entités. Par exemple, consultez l’opération [Ajouter une tâche à un travail][rest_add_task] \(API REST Batch) ou les propriétés [CloudTask.EnvironmentSettings][net_cloudtask_env] et [CloudJob.CommonEnvironmentSettings][net_job_env] dans Batch .NET.

L’application cliente ou le service peuvent obtenir des variables d’environnement d’une tâche, à la fois définies par le service et personnalisées, à l’aide de l’opération [Obtenir des informations sur une tâche][rest_get_task_info] \(Batch REST) ou en accédant à la propriété [CloudTask.EnvironmentSettings][net_cloudtask_env] \(Batch .NET). Les processus qui s’exécutent sur un nœud de calcul peuvent accéder à ces dernières ainsi qu’à d’autres variables d’environnement sur le nœud, par exemple à l’aide de la syntaxe classique `%VARIABLE_NAME%` (Windows) ou la syntaxe `$VARIABLE_NAME` (Linux).

Les variables d’environnement suivantes sont définies par le service Batch et sont accessibles par vos tâches :

| Nom de variable d'environnement | Description |
|---------------------------------|--------------------------------------------------------------------------|
| `AZ_BATCH_ACCOUNT_NAME` | Nom du compte auquel la tâche appartient. |
| `AZ_BATCH_JOB_ID` | ID du travail auquel la tâche appartient. |
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

>[AZURE.IMPORTANT] Ces variables d’environnement sont uniquement disponibles dans le contexte de **l’utilisateur de la tâche** ; il s’agit du compte d’utilisateur sur le nœud sur lequel une tâche est exécutée. Vous ne les voyez *pas* si vous vous [connectez à distance](#connecting-to-compute-nodes) à un nœud de calcul via RDP (Remote Desktop Protocol) ou SSH (Secure Shell), et dressez la liste des variables d’environnement. Cela est dû au fait que le compte d’utilisateur qui est utilisé pour la connexion à distance n’est pas le même que le compte utilisé par la tâche.

## Fichiers et répertoires

Chaque tâche possède un *répertoire de travail* sous lequel elle crée zéro ou plusieurs fichiers et répertoires. Ce répertoire de travail peut être utilisé pour stocker le programme exécuté par la tâche, les données qu’il traite et la sortie du traitement qu’il effectue. Tous les fichiers et répertoires d’une tâche sont détenus par l’utilisateur de la tâche.

Le service Batch expose une partie du système de fichiers sur un nœud en tant que *répertoire racine*. Les tâches peuvent accéder au répertoire racine en référençant la variable d’environnement `AZ_BATCH_NODE_ROOT_DIR`. Pour plus d’informations sur l’utilisation de variables d’environnement, consultez la section [Paramètres d’environnement des tâches](#environment-settings-for-tasks).

Le répertoire racine contient la structure de répertoires suivante :

![Structure de répertoire du nœud de calcul][1]

- **Partagé** : ce répertoire fournit un accès en lecture/écriture à *toutes* les tâches qui s’exécutent sur un nœud. Les tâches qui s’exécutent sur le nœud peuvent créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire. Les tâches peuvent accéder à ce répertoire en référençant la variable d’environnement `AZ_BATCH_NODE_SHARED_DIR`.

- **Démarrage** : ce répertoire est utilisé par une tâche de démarrage en tant que répertoire de travail. Tous les fichiers téléchargés vers le nœud par la tâche de démarrage sont stockés ici. La tâche de démarrage peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire. Les tâches peuvent accéder à ce répertoire en référençant la variable d’environnement `AZ_BATCH_NODE_STARTUP_DIR`.

- **Tâches** : un répertoire est créé pour chaque tâche qui s’exécute sur le nœud. Il est accessible en référençant la variable d’environnement `AZ_BATCH_TASK_DIR`.

	Dans chaque répertoire de la tâche, le service Batch crée un répertoire de travail (`wd`) dont le chemin unique est spécifié par la variable d’environnement `AZ_BATCH_TASK_WORKING_DIR`. Ce répertoire fournit un accès en lecture/écriture à la tâche. La tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire. Ce répertoire est conservé en fonction de la contrainte *RetentionTime* spécifiée pour la tâche.

	`stdout.txt` et `stderr.txt` : ces fichiers sont écrits dans le dossier des tâches lors de l’exécution de la tâche.

>[AZURE.IMPORTANT] Lorsqu’un nœud est supprimé du pool, *tous* les fichiers stockés sur ce nœud sont supprimés.

## Packages d’applications

La fonctionnalité [packages d’application](batch-application-packages.md) facilite la gestion et le déploiement d’applications sur les nœuds de calcul dans vos pools. Vous pouvez facilement charger et gérer plusieurs versions des applications exécutées par vos tâches, notamment leurs fichiers binaires et leurs fichiers de prise en charge. Vous pouvez ensuite déployer automatiquement une ou plusieurs de ces applications sur les nœuds de calcul dans le pool.

Vous pouvez spécifier les packages d’applications au niveau du pool et des tâches. Lorsque vous spécifiez des packages d’applications au niveau du pool, l’application est déployée sur tous les nœuds du pool. Lorsque vous spécifiez des packages d’applications au niveau des tâches, l’application est déployée uniquement sur les nœuds programmés pour exécuter au moins une des tâches du travail, juste avant l’exécution de la ligne de commande de la tâche.

Batch gère les détails de l’utilisation d’Azure Storage en arrière-plan pour stocker vos packages d’applications et les déployer sur les nœuds de calcul, de manière à permettre de simplifier votre code et vos frais de gestion.

Pour en savoir plus sur la fonctionnalité packages d’application, consultez [Déploiement d’applications avec des packages d’applications Azure Batch](batch-application-packages.md).

>[AZURE.NOTE] Si vous ajoutez des packages de d’applications au niveau du pool à un pool *existant*, vous devez redémarrer ses nœuds de calcul afin que les packages d’applications soient déployés sur les nœuds.

## Durée de vie de nœud de pool et de calcul

Lorsque vous créez votre solution Azure Batch, vous devez prendre une décision de conception relative à la procédure et au moment de la création des pools, et à la durée pendant laquelle les nœuds de calcul de ces pools restent disponibles.

D’un côté, vous pouvez créer un pool pour chaque travail au moment de sa soumission, et supprimer ses nœuds dès la fin de l’exécution des tâches. Cela permet d’optimiser l’utilisation, car les nœuds ne sont alloués que lorsqu’ils sont absolument nécessaires, et ils s’arrêtent dès qu’ils deviennent inactifs. Cela signifie que le travail doit attendre que les nœuds soient alloués, mais il est important de noter que les tâches seront planifiées sur les nœuds dès qu’elles seront individuellement disponibles, allouées, et que cette tâche de démarrage sera terminée. Batch n’attend *pas* que tous les nœuds d’un pool soient disponibles avant d’affecter des tâches. Cela garantit l’utilisation maximale de tous les nœuds disponibles.

À l’opposé, si la priorité absolue consiste à démarrer immédiatement des travaux, vous pouvez créer un pool avant l’heure et mettre ses nœuds à disposition avant la soumission des travaux. Dans ce scénario, les tâches de travail peuvent démarrer immédiatement, mais les nœuds peuvent rester inactifs en attendant les tâches à affecter.

Une approche combinée est généralement utilisée pour la gestion d’une variable, hormis une charge en cours. Vous pouvez disposer d’un pool auquel plusieurs travaux sont soumis, mais vous pouvez augmenter ou réduire le nombre de nœuds en fonction de la charge de travail (consultez [Mise à l’échelle des ressources de calcul](#scaling-compute-resources) dans la section suivante). Vous pouvez procéder en réaction, en fonction de la charge actuelle, ou en amont, si la charge peut être prédite.

## Mise à l’échelle des ressources de calcul

Avec la [mise à l’échelle automatique](batch-automatic-scaling.md), le service Batch peut ajuster de manière dynamique le nombre de nœuds de calcul d’un pool en fonction de la charge de travail actuelle et de l’utilisation des ressources de votre scénario de calcul. Cela vous permet de réduire le coût global d’exécution de votre application en utilisant uniquement les ressources dont vous avez besoin et en libérant les autres.

Pour activer la mise à l’échelle automatique, écrivez une [formule de mise à l’échelle automatique](batch-automatic-scaling.md#automatic-scaling-formulas) et associez-la à un pool. Le service Batch utilise la formule pour déterminer le nombre cible de nœuds dans le pool pour le prochain intervalle de mise à l’échelle (intervalle que vous pouvez configurer). Vous pouvez spécifier les paramètres de mise à l’échelle automatique pour un pool lorsque vous le créez ou activez la mise à l’échelle sur un pool ultérieurement. Vous pouvez également mettre à jour les paramètres de mise à l’échelle sur un pool compatible avec la mise à l’échelle.

Par exemple, il se peut qu’un travail exige que vous envoyiez un grand nombre de tâches devant être exécutées. Vous pouvez attribuer au pool une formule de mise à l’échelle qui règle le nombre de nœuds du pool en fonction du nombre actuel de tâches en file d’attente et du degré d’achèvement des tâches dans le travail. Le service Batch évalue la formule régulièrement et redimensionne le pool en fonction de la charge de travail (ajout de nœuds en présence de plusieurs tâches en file d’attente, et suppression de nœuds en cas d’absence de tâches en file d’attente ou en cours d’exécution) et d’autres paramètres de formule.

Une formule de mise à l’échelle peut être basée sur les mesures suivantes :

- Les **mesures temporelles** sont basées sur les statistiques collectées toutes les cinq minutes dans le nombre d’heures spécifié.

- Les **mesures de ressources** sont basées sur l’utilisation du processeur, de la bande passante et de la mémoire, et sur le nombre de nœuds.

- Les **mesures de tâches** sont basées sur l’état de la tâche, tel que *Active* (en file d’attente), *En cours d’exécution* ou *Terminée*.

Lorsque la mise à l’échelle automatique diminue le nombre de nœuds de calcul dans un pool, vous devez réfléchir aux méthodes pour gérer les tâches s’exécutant au moment de cette opération de réduction. Pour ce faire, Batch fournit une *option de désallocation de nœud* que vous pouvez inclure dans vos formules. Par exemple, vous pouvez spécifier d’arrêter immédiatement les tâches en cours, de les arrêter immédiatement, puis les remettre en file d’attente pour une exécution sur un autre nœud, ou les autoriser à terminer avant la suppression du nœud du pool.

Pour plus d’informations sur la mise à l’échelle automatique d’une application, consultez la section [Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch](batch-automatic-scaling.md).

> [AZURE.TIP] Pour optimiser l’utilisation des ressources de calcul, définissez à zéro le nombre cible de nœuds à la fin d’un travail tout en autorisant les tâches en cours à s’achever.

## Sécurité avec certificats

En principe, vous devez utiliser des certificats lorsque vous chiffrez ou déchiffrez des informations sensibles pour les tâches, par exemple, la clé d’un [compte Azure Storage][azure_storage]. Pour ce faire, vous pouvez installer des certificats sur les nœuds. Les secrets chiffrés sont transmis aux tâches dans les paramètres de ligne de commande ou incorporés dans l’une des ressources et les certificats installés peuvent être utilisés pour les déchiffrer.

Pour ajouter un certificat à un compte Batch, utilisez l’opération [Ajouter le certificat][rest_add_cert] \(Batch REST) ou la méthode [CertificateOperations.CreateCertificate][net_create_cert] \(Batch .NET) pour ajouter un certificat à un compte Batch. Vous pouvez ensuite associer le certificat à un pool existant ou nouveau. Lorsqu’un certificat est associé à un pool, le service Batch installe le certificat sur chaque nœud du pool. Le service Batch installe les certificats appropriés au démarrage du nœud, avant de lancer une tâche quelconque (notamment les tâches de démarrage et celles du gestionnaire de travaux).

Si vous ajoutez des certificats à un pool *existant*, vous devez redémarrer ses nœuds de calcul afin que les certificats soient appliqués aux nœuds.

## Gestion des erreurs

Il peut s’avérer utile de gérer les échecs de tâche et d’application au sein de votre solution Batch.

### Gestion des échecs de tâche
Les échecs de tâche peuvent être classés suivant les catégories suivantes :

- **Échecs de planification**

	Si le transfert des fichiers qui sont spécifiés pour une tâche échoue pour une raison quelconque, une « erreur de planification » est définie pour la tâche.

	Des erreurs de planification peuvent se produire si des fichiers de ressources de la tâche ont été déplacés, si le compte Storage n’est plus disponible ou si un autre problème est survenu qui a empêché la copie des fichiers sur le nœud.

- **Échecs d’application**

	Le processus spécifié par la ligne de commande de la tâche peut également échouer. Il est considéré comme ayant échoué lorsqu’un code de sortie différent de zéro est renvoyé par le processus exécuté par la tâche (voir *Codes de sortie de tâche* dans la section ci-après).

	Pour les échecs d’application, vous pouvez configurer Batch pour relancer automatiquement la tâche autant de fois que vous l’aurez spécifié.

- **Échecs de contrainte**

	Vous pouvez définir une contrainte qui spécifie la durée maximale d’exécution d’un travail ou d’une tâche, le *maxWallClockTime*. Cela peut s’avérer utile lorsqu’il s’agit de terminer les tâches « suspendues ».

	Lorsque la durée maximale est dépassée, la tâche est marquée comme *terminée*, mais le code de sortie est défini sur `0xC000013A`, et le champ *schedulingError* est marqué comme `{ category:"ServerError", code="TaskEnded"}`.

### Débogage des échecs d’application

- `stderr` et `stdout`

	Pendant l’exécution, une application peut produire des diagnostics qui vous permettent de résoudre les problèmes. Comme mentionné dans la section plus haut [Fichiers et répertoires](#files-and-directories), le service Batch écrit des sorties et des sorties d’erreur standard dans des fichiers `stdout.txt` et `stderr.txt` du répertoire de tâche sur le nœud de calcul. Vous pouvez utiliser le portail Azure ou l’un des Kits de développement logiciel (SDK) Batch pour télécharger ces fichiers. Par exemple, en utilisant [ComputeNode.GetNodeFile][net_getfile_node] et [CloudTask.GetNodeFile][net_getfile_task] dans la bibliothèque .NET Batch, vous pouvez récupérer ces fichiers et d’autres à des fins de dépannage.

- **Codes de sortie de tâche**

	Comme mentionné précédemment, une tâche est marquée comme ayant échoué par le service Batch si le processus exécuté par la tâche retourne un code de sortie différent de zéro. Lorsqu’une tâche exécute un processus, Batch remplit la propriété de code de sortie de la tâche avec le *code de retour du processus*. Il est important de noter que le code de sortie d’une tâche n’est **pas** déterminé par le service Batch, mais par le processus proprement dit ou le système d’exploitation sur lequel le processus est exécuté.

### Prise en compte des échecs ou des interruptions de tâche

Les tâches peuvent parfois échouer ou être interrompues. L’application de la tâche elle-même peut échouer, le nœud sur lequel elle s’exécute peut être redémarré, voire supprimé du pool pendant le redimensionnement de ce dernier si la stratégie de désallocation du pool définie prévoit la suppression immédiate des nœuds, sans attendre la fin des tâches. Dans tous les cas, la tâche peut être automatiquement remise en file d’attente par Batch pour être exécutée sur un autre nœud.

Un problème intermittent peut également provoquer la suspension d’une tâche ou ralentir son exécution. Vous pouvez définir la durée d’exécution maximale d’une tâche. Si elle est dépassée, Batch interrompt l’application de la tâche.

### Se connecter aux nœuds de calcul

Vous pouvez effectuer des actions supplémentaires de débogage et de résolution des problèmes en vous connectant à un nœud de calcul à distance. Vous pouvez utiliser le portail Azure pour télécharger un fichier RDP pour les nœuds Windows et obtenir des informations de connexion SSH pour les nœuds Linux. Vous pouvez également effectuer cette opération à l’aide d’API Batch, par exemple [Batch .NET][net_rdpfile] ou [Python Batch](batch-linux-nodes.md#connect-to-linux-nodes).

>[AZURE.IMPORTANT] Pour vous connecter à un nœud via RDP ou SSH, vous devez d’abord créer un utilisateur sur le nœud. Pour ce faire, vous pouvez utiliser le portail Azure, [ajouter un compte d’utilisateur à un nœud][rest_create_user] à l’aide de l’API REST Batch, appeler la méthode [ComputeNode.CreateComputeNodeUser][net_create_user] dans Batch .NET ou appeler la méthode [add\_user][py_add_user] dans le module Python de Batch.

### Résolution des problèmes de « mauvais » nœuds de calcul

Quand certaines de vos tâches échouent, votre application cliente Batch ou un service peut examiner les métadonnées des tâches en échec pour identifier un nœud présentant un dysfonctionnement. Chaque nœud d’un pool se voit attribuer un ID unique et le nœud sur lequel s’exécute une tâche est inclus dans les métadonnées de la tâche. Une fois que vous avez identifié le nœud présentant un problème, vous pouvez effectuer les actions suivantes :

- **Redémarrer le nœud** ([REST][rest_reboot] | [.NET][net_reboot])

	Le fait de redémarrer le nœud peut parfois résoudre des problèmes latents comme des processus bloqués ou défaillants. Notez que si votre pool utilise une tâche de démarrage ou si votre travail utilise une tâche de préparation, ces deux éléments s’exécuteront au redémarrage du nœud.

- **Réinitialiser le nœud** ([REST][rest_reimage] | [.NET][net_reimage])

	Cette opération réinstalle le système d’exploitation sur le nœud. Comme avec le redémarrage d’un nœud, les tâches de démarrage et celles de préparation d’un travail sont relancées une fois le nœud réinitialisé.

- **Supprimer le nœud du pool** ([REST][rest_remove] | [.NET][net_remove])

	Il est parfois nécessaire de supprimer entièrement le nœud à partir du pool.

- **Désactiver la planification des tâches sur le nœud** ([REST][rest_offline] | [.NET][net_offline])

	Cette opération est efficace puisqu’elle place le nœud « hors connexion ». Ainsi, aucune tâche ultérieure ne peut lui être assignée. Toutefois, le nœud est autorisé à poursuivre son exécution et à rester dans le pool. Cela vous permet d’effectuer une recherche approfondie sur la cause des échecs sans perdre les données de la tâche en échec et sans que le nœud n’occasionne d’autres échecs de tâche. Par exemple, vous pouvez désactiver la planification des tâches sur le nœud, puis vous [connecter à distance](#connecting-to-compute-nodes) pour examiner les journaux des événements de ce nœud ou encore résoudre d’autres problèmes. Après avoir terminé votre recherche, vous pouvez remettre le nœud en ligne en activant la planification des tâches ([REST][rest_online], [.NET][net_online]), ou effectuer l’une des actions indiquées précédemment.

> [AZURE.IMPORTANT] Pour chaque action décrite dans cette section (redémarrer, réinitialiser, supprimer, et désactiver la planification des tâches), vous pouvez indiquer la manière dont les tâches en cours d’exécution sur le nœud sont gérées lorsque vous effectuez l’action. Par exemple, lorsque vous désactivez la planification des tâches sur un nœud à l’aide de la bibliothèque cliente .NET Batch, vous pouvez spécifier une valeur d’énumération [DisableComputeNodeSchedulingOption][net_offline_option]. Celle-ci sert à préciser s’il faut **interrompre** les tâches en cours d’exécution, les **remettre en file d’attente** pour les planifier sur d’autres nœuds ou finaliser les tâches en cours avant d’exécuter l’action (**TaskCompletion**).

## Étapes suivantes

- Passez en revue un exemple d’application Batch détaillée dans [Prise en main de la bibliothèque Azure Batch pour .NET.](batch-dotnet-get-started.md). Il existe également une [version Python](batch-python-tutorial.md) du didacticiel qui exécute une charge de travail sur des nœuds de calcul Linux.

- Téléchargez et créez l’exemple de projet [Batch Explorer][github_batchexplorer] à utiliser lors du développement de vos solutions Batch. En utilisant l’explorateur Batch, vous pouvez effectuer ce qui suit, et bien plus encore :
  - Analysez et manipulez les pools, les travaux et les tâches au sein de votre compte Batch
  - Téléchargez `stdout.txt`, `stderr.txt` et d’autres fichiers à partir des nœuds
  - Créez des utilisateurs sur les nœuds et téléchargez les fichiers RDP pour la connexion à distance

- Découvrez comment [créer des pools de nœuds de calcul Linux](batch-linux-nodes.md).

- Consultez le [forum Azure Batch][batch_forum] sur MSDN. Le forum est parfait pour poser des questions, que vous soyez débutant ou expert dans l’utilisation du service Batch.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

<!---HONumber=AcomDC_0831_2016-->