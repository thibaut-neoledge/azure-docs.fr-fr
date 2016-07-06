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
	ms.date="06/29/2016"
	ms.author="marsma"/>

# Présentation des fonctionnalités du service Batch pour les développeurs

Dans cette vue d’ensemble des principaux composants du service Azure Batch, nous aborderons les fonctionnalités du service Batch qui permettent aux développeurs de créer des solutions de calcul parallèles à grande échelle.

Si vous développez une application ou un service de calcul distribués, qui émettent des appels d’API [Batch REST][batch_rest_api] directs, ou si vous utilisez l’un des [Kits de développement logiciel (SDK)](batch-technical-overview.md#batch-development-apis), vous utiliserez la plupart des ressources et des fonctionnalités présentées ci-dessous.

> [AZURE.TIP] Pour une présentation plus poussée du service Batch, consultez les [Notions de base d’Azure Batch](batch-technical-overview.md).

## Flux de travail du service Batch

Le flux de travail de haut niveau suivant est caractéristique de la plupart des applications et services utilisant le service Batch pour le traitement des charges de travail parallèles :

1. Chargez les **fichiers de données** que vous souhaitez traiter vers un compte [Azure Storage][azure_storage]. Le service Batch inclut la prise en charge intégrée de l’accès au stockage d’objets Blob Azure ; lorsque les tâches sont exécutées, celles-ci peuvent télécharger les fichiers en question vers les [nœuds de calcul](#compute-node).

2. Chargez le **fichiers d’application** que les tâches exécuteront. Ces fichiers peuvent être des fichiers binaires ou des scripts, accompagnés de leurs dépendances, et sont exécutés par les tâches dans vos travaux. Ces fichiers peuvent être récupérés à partir de votre compte Azure Storage, puis téléchargés vers les nœuds de calcul par les tâches ; vous pouvez également utiliser les [packages d’application](#application-packages) de Batch pour le déploiement et la gestion des applications.

3. Créer un [Pool](#pool) de nœuds de calcul. Lors de la création d’un pool, vous devez spécifier le nombre de nœuds de calcul pour le pool, leur taille ainsi que leur système d’exploitation. Pour chaque exécution de tâche dans votre travail, cette dernière est exécutée sur l’un des nœuds du pool.

4. Création d’un [Travail](#job). Un travail gère une collection de tâches ; chaque travail est associé à un pool spécifique, dans lequel s’exécuteront les tâches du travail concerné.

5. Ajoutez des [Tâches](#task) au travail. Chaque tâche exécute l’application ou le script téléchargés afin de traiter les fichiers de données téléchargés à partir de votre compte de stockage. Lorsque chaque tâche est terminée, celle-ci peut télécharger sa sortie vers Azure Storage.

6. Surveillez la progression du travail et récupérez la sortie de la tâche à partir d’Azure Storage.

Dans les sections qui suivent, vous allez apprendre chacune des ressources mentionnées dans le flux de travail ci-dessus, ainsi que de nombreuses autres fonctionnalités de traitement par lots qui activeront votre scénario de calcul.

> [AZURE.NOTE] Vous aurez besoin d’un [compte Batch](batch-account-create-portal.md) pour utiliser le service de traitement par lots et presque toutes les solutions utiliseront un compte [Azure Storage][azure_storage] pour le stockage et la récupération des fichiers. Le service Batch prend actuellement en charge uniquement le type de compte de stockage **à usage général**, comme décrit à l’étape 5, [Créez un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account), de l’article [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).

## Composants de service Batch

Certaines des ressources suivantes (compte, nœud de calcul, pool, travail, tâche) sont requises par toutes les solutions utilisant le service Batch. D’autres, comme les planifications des tâches et les packages d’application sont des tâches utiles, mais facultatives.

- [Compte](#account)
- [Nœud de calcul](#compute-node)
- [Pool](#pool)
- [Travail](#job)

  - [Planifications de travaux](#scheduled-jobs)

- [Tâche](#task)

  - [Tâche de démarrage](#start-task)
  - [Tâche du gestionnaire de travaux](#job-manager-task)
  - [Tâches de préparation et lancement](#job-preparation-and-release-tasks)
  - [Tâches multi-instances (MPI)](#multi-instance-tasks)
  - [Dépendances de la tâche](#task-dependencies)

- [Packages d’applications](#application-packages)

## Compte

Un compte Batch est une entité identifiée de façon unique au sein du service Batch. Tout le traitement s’effectue via un compte Batch. Lorsque vous effectuez des opérations avec le service Batch, vous avez besoin du nom et d’une des clés du compte. Vous pouvez [créer et gérer un compte Azure Batch dans le portail Azure](batch-account-create-portal.md).

## Nœud de calcul

Un nœud de calcul est une machine virtuelle Azure dédiée au traitement d’une partie de la charge de travail de votre application. La taille d’un nœud détermine le nombre de cœurs du processeur, la capacité de mémoire et la taille du système de fichiers local qui lui est allouée. Pour créer des pools de nœuds Windows ou Linux à l’aide d’images de Cloud Services ou du Marketplace de machines virtuelles, consultez la section [Pool](#pool) ci-dessous.

Les nœuds peuvent exécuter tout fichier exécutable ou script pris en charge par l’environnement de système d’exploitation du nœud. Cela inclut les scripts *.exe, *.cmd, *.bat et PowerShell pour Windows et les fichiers binaires ainsi que les scripts shell et Python pour Linux.

Tous les nœuds de calcul Batch incluent également les éléments suivants :

- Une [structure de dossiers](#files-and-directories) standard et des [variables d’environnement](#environment-settings-for-tasks) pouvant être référencées par les tâches.
- Paramètres de **pare-feu** configurés pour le contrôle de l’accès.
- Un [accès à distance](#connecting-to-compute-nodes) aux nœuds Windows (RDP) et Linux (SSH).

> [AZURE.NOTE] La prise en charge de Linux dans Azure Batch se trouve actuellement en version préliminaire. Pour plus d’informations, voir [Configurer des nœuds de calcul Linux dans des pools Azure Batch](batch-linux-nodes.md).

## Pool

Un pool est une collection de nœuds sur lesquels votre application s’exécute. Vous pouvez créer le pool manuellement ou laisser le service Batch le créer automatiquement lorsque vous spécifiez le travail à accomplir. Vous pouvez créer et gérer un pool qui répond aux besoins de votre application en matière de ressources, et des pools peuvent être utilisés uniquement par le compte Batch dans lequel ils ont été créés. Un compte Batch peut avoir plusieurs pools.

Les pools Azure Batch sont créés sur la plateforme de calcul principale Azure ; les pools Batch permettent l’affectation à grande échelle, l’installation d’applications et de données, le transfert de données, l’analyse de l’état, ainsi que le réglage flexible du nombre de nœuds de calcul avec un pool ([mise à l’échelle](#scaling-compute-resources)).

Chaque nœud ajouté à un pool se voit attribuer un nom unique et l’adresse IP. Lorsqu’un nœud est supprimé d’un pool, toutes les modifications apportées au système d’exploitation ou aux fichiers sont perdues, et son nom et l’adresse IP sont libérés pour une utilisation ultérieure. Lorsqu’un nœud quitte un pool, sa durée de vie est terminée.

Lorsque vous créez un pool, vous pouvez spécifier les attributs suivants :

- **Système d’exploitation** et **version** de nœud de calcul

	Deux options s’offrent à vous lors de la sélection d’un système d’exploitation pour les nœuds du pool : **Configuration de la machine virtuelle** et **Configuration des services cloud**.

	La **Configuration de la machine virtuelle** fournit des images Linux et Windows pour les nœuds de calcul à partir du [Marketplace de machines virtuelles Azure][vm_marketplace]. Lorsque vous créez un pool contenant des nœuds de la Configuration de la machine virtuelle, vous devez spécifier la taille des nœuds ainsi que la **référence de l’image de la machine virtuelle** et la référence de **l’agent de nœud** du Batch devant être installés sur les nœuds. Pour plus d’informations sur la spécification des propriétés de pool, voir [Configurer des nœuds de calcul Linux dans des pools Azure Batch](batch-linux-nodes.md).

	La **configuration des services cloud** fournit *uniquement* des nœuds de calcul Windows. Les systèmes d’exploitation disponibles pour les pools de configuration des services coud sont répertoriés dans [Versions du SE invité et matrice de compatibilité du Kit de développement logiciel (SDK) Azure](../cloud-services/cloud-services-guestos-update-matrix.md). Lorsque vous créez un pool contenant des nœuds Cloud Services, vous devez uniquement spécifier la taille du nœud et sa *famille de systèmes d’exploitation*. Lors de la création de pools de nœuds de calcul Windows, les services Cloud Services sont le plus couramment utilisés.

    - La *famille de système d’exploitation* détermine également les versions de .NET qui sont installées avec le système d’exploitation.
	- Comme avec les rôles de travail dans Cloud Services, une *Version du système d’exploitation* peut être spécifiée (pour plus d’informations sur les rôles de travail, consultez la section [Connaître les services de cloud](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) dans la [Vue d’ensemble de Cloud Services](../cloud-services/cloud-services-choose-me.md)).
    - Comme avec les rôles de travail, il est recommandé de spécifier `*` comme *version du système d’exploitation* afin que les nœuds soient automatiquement mis à niveau et qu’aucun travail supplémentaire ne soit requis pour gérer ces nouvelles versions. La principale raison pour sélectionner une version de système d’exploitation spécifique est d’assurer la compatibilité avec l’application, permettant aux tests de compatibilité descendante d’être réalisés avant d’autoriser la mise à jour de la version. Une fois validée, la *version du système d’exploitation* du pool peut être mise à jour et la nouvelle image du système d’exploitation peut être installée. Toutes les tâches en cours d’exécution seront interrompues et remises en file d’attente.

- **Taille des nœuds**

	Les tailles des nœuds de calcul de la **configuration des services cloud** sont répertoriées dans [Tailles de services cloud](../cloud-services/cloud-services-sizes-specs.md). Le service Batch prend en charge l’ensemble des tailles de Cloud Services, à l’exception de `ExtraSmall`.

	Les tailles de nœud de calcul de la **configuration de machine virtuelle** sont répertoriées dans [Tailles des machines virtuelles dans Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) et [Tailles des machines virtuelles dans Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Le service Batch prend en charge l’ensemble des tailles de machine virtuelle Azure, à l’exception de `STANDARD_A0` et de celles comprises dans Premium Storage (série `STANDARD_GS`, `STANDARD_DS`, et `STANDARD_DSV2`).

	Lorsque vous sélectionnez une taille de nœud, vous devez prendre en compte les caractéristiques et les exigences de la ou des applications qui vont être exécutées sur le nœud. La taille du nœud est en général sélectionnée en supposant qu’une tâche s’exécutera sur un nœud à la fois. Lorsqu’on prend en compte des aspects tels que la nature multithread de l’application et le volume de mémoire qu’elle utilise, vous pouvez déterminer la taille de nœud la mieux adaptée et la plus rentable. Plusieurs tâches et, par conséquent, plusieurs instances d’application peuvent [s’exécuter en parallèle](batch-parallel-node-tasks.md), auquel cas vous devrez choisir un nœud plus volumineux. Pour plus d’informations, consultez la section « Stratégie de planification de tâches » ci-dessous.

	Tous les nœuds du pool ont la même taille. Si différentes applications doivent être exécutées avec des exigences système et/ou des niveaux de charge différents, plusieurs pools devront alors être utilisés.

- **Nombre cible de nœuds**

	Il s’agit du nombre de nœuds de calcul que vous souhaitez déployer dans le pool. On parle de *cible* car, dans certaines situations, votre pool ne pourra pas atteindre le nombre de nœuds souhaité. Un pool peut ne pas atteindre le nombre souhaité de nœuds pour de multiples raisons : votre compte Batch peut avoir atteint son [quota principal](batch-quota-limit.md#batch-account-quotas) ou une formule de mise à l’échelle automatique a été appliquée au pool, limitant ainsi le nombre maximal de nœuds (voir *Stratégie de mise à l’échelle* ci-dessous).

- **Stratégie de mise à l’échelle**

	En plus de spécifier un nombre statique de nœuds, vous pouvez écrire et appliquer une [formule de mise à l’échelle automatique](#scaling-compute-resources) à un pool. Le service Batch évaluera régulièrement la formule et ajuste le nombre de nœuds dans le pool en fonction de différents paramètres de pool, de travail et de tâche que vous pouvez spécifier.

- **Stratégie de planification de tâches**

	L’option de configuration [tâches maximales par nœud](batch-parallel-node-tasks.md) détermine le nombre maximal de tâches qui peuvent être exécutées en parallèle sur chaque nœud de calcul au sein du pool. La configuration par défaut prévoit d’exécuter une tâche sur un nœud à la fois, mais dans certains scénarios, il est parfois préférable que plusieurs tâches soient exécutées simultanément sur un nœud. Consultez [l’exemple de scénario](batch-parallel-node-tasks.md#example-scenario) de notre article sur les [tâches de nœud simultanées](batch-parallel-node-tasks.md) pour découvrir comment bénéficier de plusieurs tâches par nœud.

	Vous pouvez également spécifier un *type de remplissage* qui détermine si l’option Batch répartit les tâches uniformément sur tous les nœuds dans un pool, ou remplit chaque nœud avec le nombre maximal de tâches avant d’affecter des tâches à un autre nœud.

- **État de communication** des nœuds de calcul

	Dans la plupart des scénarios, les tâches fonctionnent indépendamment les unes des autres et n’ont pas besoin de communiquer entre elles, mais il existe certaines applications dans lesquelles des tâches doivent communiquer (telles que les [scénarios MPI](batch-mpi.md)).

	Vous pouvez configurer un pool pour permettre la communication entre les nœuds qu’il contient (**communication entre les nœuds**). Lorsque la communication entre les nœuds est activée, les nœuds des pools de la configuration des services cloud peuvent communiquer entre eux sur les ports supérieurs à 1100 et les pools de la configuration de machine virtuelle ne limitent pas le trafic sur les ports.

	Notez que l’activation de la communication entre les nœuds affecte le placement des nœuds au sein des clusters et peut également limiter le nombre maximal de nœuds dans un pool en raison des restrictions de déploiement. Si votre application ne nécessite pas la communication entre nœuds, le service Batch peut éventuellement allouer au pool un grand nombre de nœuds issus de différents centres de données ou clusters pour accroître la puissance de traitement parallèle.

- **Tâche de démarrage** pour les nœuds de calcul

	La *tâche de démarrage* s’exécute sur chacun des nœuds rejoignant le pool, ainsi qu’à chaque redémarrage ou réinitialisation d’un nœud. La tâche de démarrage est particulièrement utile pour la préparation des nœuds de calcul à l’exécution de tâches, telles que l’installation des applications que vos tâches exécuteront.

> [AZURE.IMPORTANT] Tous les comptes Batch ont un **quota** par défaut qui limite le nombre de **cœurs** (et par conséquent, celui des nœuds de calcul) dans un compte Batch. Pour en savoir plus sur les quotas par défaut et obtenir des instructions sur comment [augmenter un quota](batch-quota-limit.md#increase-a-quota) (par exemple, le nombre maximal de cœurs dans votre compte Batch), consultez l’article [Quotas et limites pour le service Azure Batch](batch-quota-limit.md). Si vous vous demandez pourquoi votre pool n’atteint pas plus de X nœuds, ce quota de cœurs peut en être la raison.

## Job

Un travail est une collecte de données et gère comment le calcul est effectué par ses tâches sur les nœuds de calcul d’un pool.

- Le travail spécifie le **pool** sur lequel le travail sera exécuté. Vous pouvez créer un nouveau pool pour chaque tâche, ou utiliser un pool pour plusieurs travaux. Vous pouvez créer un pool pour chaque travail associé à une planification de travail, ou pour tous les travaux associés à une planification de travail.
- Une **priorité de travail** facultative peut être spécifiée. Quand un travail dont la priorité est plus élevée que celle des autres travaux en cours d’exécution est envoyé, les tâches du travail dont la priorité est la plus élevée sont insérées dans la file d’attente, devant les tâches des travaux de priorité inférieure. Les tâches de priorité inférieure qui sont déjà en cours d’exécution ne seront pas annulées.
- Les **contraintes** de travail peuvent spécifier certaines limites pour vos travaux :

	Vous pouvez définir une **durée maximale** pour les travaux ; si la durée d’exécution du travail est supérieure à la durée maximale spécifiée, le travail et toutes les tâches qui lui sont associées seront terminés.

	Le service Batch peut détecter, puis relancer des tâches ayant échoué. Le **nombre maximal de tentatives de tâche** peut être spécifié comme une contrainte, et indiquer notamment si une tâche doit *toujours* être systématiquement relancée ou ne *jamais* l’être. Lorsqu’une tâche est relancée, cela signifie qu’elle est remise en file d’attente afin d’être réexécutée.

- Les tâches peuvent être ajoutées à un travail par votre application cliente, ou une [Tâche du gestionnaire de travaux](#job-manager-task) peut être spécifiée. Une tâche de gestionnaire de travaux contient les informations nécessaires à la création des tâches requises pour un travail, avec la tâche de gestionnaire de travaux qui s’exécute sur l’un des nœuds de calcul du pool. La tâche du gestionnaire de travaux est gérée spécifiquement par Batch : elle est remise en file d’attente dès que le travail est créé et elles sont relancées lorsqu’elle échoue. Une tâche de gestionnaire de travaux est *requise* pour les travaux créés dans le cadre d’une [planification de travail](#scheduled-jobs), car il s’agit du seul moyen de définir les tâches avant que le travail ne soit instancié.

### Priorité de travail

Vous pouvez établir une priorité pour les travaux que vous créez dans Batch. Le service Batch utilise les valeurs de priorité du travail pour déterminer l’ordre de planification du travail dans un compte (à ne pas confondre avec le [travail planifié](#scheduled-jobs)). Les valeurs de priorité sont comprises entre -1000 et 1000, -1000 étant la priorité la plus basse et 1000 la plus élevée. Vous pouvez mettre à jour la priorité d’un travail à l’aide de l’opération [Mettre à jour les propriétés d’un travail][rest_update_job] \(Batch REST) ou en modifiant la propriété [CloudJob.Priority][net_cloudjob_priority] \(Batch .NET).

Dans le même compte, les tâches à la priorité plus élevée ont la priorité en termes de planification sur les tâches avec une priorité plus faible. Un travail avec une priorité plus élevée dans un compte n'a pas de priorité de planification sur un autre travail avec une valeur de priorité inférieure dans un autre compte.

La planification de travail entre pools est indépendante. Entre des pools différents, un travail avec une priorité plus élevée n’est pas systématiquement planifié en premier, si le pool auquel il est associé n’a pas suffisamment de nœuds inactifs. Dans le même pool, les travaux avec le même niveau de priorité ont autant de chance d’être planifiés.

### Scheduled jobs

Les [planifications de travaux][rest_job_schedules] vous permettent de créer des travaux périodiques au sein du service Batch. Une planification de travail indique le moment où exécuter des tâches et les spécifications des travaux à exécuter. Une planification de travail permet la spécification de la durée de la planification (la durée de vie et l’entrée en vigueur de la planification) et la fréquence à laquelle des travaux doivent être créés.

## Task

Une tâche est une unité de calcul associée à un travail et exécutée sur un nœud. Les tâches sont affectées à un nœud afin d’être exécutées ou sont mises en file d’attente jusqu’à ce qu’un nœud soit disponible. Une tâche exécute un ou plusieurs programmes ou scripts sur un nœud de calcul afin d’effectuer le travail nécessaire.

Lorsque vous créez une tâche, vous pouvez spécifier les éléments suivants :

- La **ligne de commande** de la tâche. Il s’agit de la ligne de commande qui exécute votre application ou script sur le nœud de calcul.

	Il est important de noter que cette ligne de commande n’est pas exécutée dans un interpréteur de commandes et ne tire donc pas parti des fonctionnalités de l’interpréteur, telles que l’expansion de la [variable d’environnement](#environment-settings-for-tasks) (cela inclut `PATH`). Pour tirer parti de ces fonctionnalités, vous devez **appeler l’interface de ligne de commande dans la ligne de commande**. Par exemple, en lançant `cmd.exe` sur les nœuds Windows ou `/bin/sh` sur Linux :

	`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

	`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

	Si vos tâches doivent exécuter une application ou un script hors du `PATH` du nœud ou référencer des variables d’environnement, appelez l’interpréteur de commandes explicitement dans la ligne de commande de la tâche.

- **Fichiers de ressources** contenant les données à traiter. Ces fichiers sont automatiquement copiés sur le nœud depuis le stockage d’objets blob dans un compte Azure Storage **à usage général** avant l’exécution de la ligne de commande de la tâche. Pour plus d’informations, consultez la section [Tâche de démarrage](#start-task) et [Fichiers et répertoires](#files-and-directories) ci-dessous.

- Les **variables d’environnement** requises par l’application. Pour plus d’informations, consultez la section [Paramètres d’environnement des tâches](#environment-settings-for-tasks) ci-dessous.

- Les **contraintes** sous lesquelles la tâche doit s’exécuter. Par exemple, la durée maximale pendant laquelle la tâche est autorisée à s’exécuter, le nombre maximal de nouvelles tentatives en cas d’échec de la tâche, ainsi que la durée maximale pendant laquelle les fichiers du répertoire de travail sont conservés.

Outre les tâches que vous pouvez définir pour effectuer des calculs sur un nœud, les tâches spéciales suivantes sont également fournies par le service Batch :

- [Tâche de démarrage](#start-task)
- [Tâche du gestionnaire de travaux](#job-manager-task)
- [Tâches de préparation et lancement](#job-preparation-and-release-tasks)
- [Tâches multi-instances (MPI)](#multi-instance-tasks)
- [Dépendances de la tâche](#task-dependencies)

#### Tâche de démarrage

En associant une **tâche de démarrage** avec un pool, vous pouvez préparer l’environnement d’exploitation de ses nœuds en effectuant des actions telles que l’installation des applications que vos tâches exécuteront et le démarrage des processus en arrière-plan. Elle s’exécute chaque fois qu’un nœud démarre pendant sa durée de présence dans le pool, et notamment lorsque le nœud est ajouté au pool en premier lieu, redémarré ou réinitialisé.

Le principal avantage de la tâche de démarrage est qu’elle peut contenir toutes les informations nécessaires pour configurer un nœud de calcul et installer les applications nécessaires à l’exécution de la tâche. Par conséquent, pour augmenter le nombre de nœuds dans un pool, il suffit de spécifier un nombre de nœuds cibles. Batch dispose déjà de toutes les informations nécessaires pour configurer les nouveaux nœuds et les préparer à accepter des tâches.

Comme avec n’importe quelle tâche Azure Batch, une liste de **fichiers de ressources** peut être spécifiée dans [Azure Storage][azure_storage], en plus d’une **ligne de commande** à exécuter. Batch copiera d’abord les fichiers de ressources vers le nœud à partir d’Azure Storage, puis exécutera la ligne de commande. Pour une tâche de démarrage du pool, la liste des fichiers contient généralement l’application de la tâche ainsi que ses dépendances, mais elle peut également inclure des données de référence qui seront utilisées par toutes les tâches qui s’exécutent sur les nœuds de calcul. Par exemple, la ligne de commande d’une tâche de démarrage peut effectuer une opération `robocopy` afin de copier les fichiers d’application (qui ont été spécifiés en tant que fichiers de ressources et téléchargés vers le nœud) à partir du [répertoire de travail](#files-and-directories) de la tâche de démarrage vers le [dossier partagé](#files-and-directories), puis exécuter un MSI ou `setup.exe`.

> [AZURE.IMPORTANT] Le service Batch prend actuellement en charge *uniquement* le type de compte de stockage à **usage général**, comme décrit à l’étape 5, [Créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account), de l’article [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md). Vos tâches Batch (y compris les tâches standard, de démarrage, de préparation des travaux et de validation des travaux) doivent spécifier des fichiers de ressources se trouvant *uniquement* dans les comptes de stockage à **usage général**.

Il est généralement préférable pour le service Batch d’attendre que la tâche de démarrage soit terminée avant de considérer que le nœud est prêt à recevoir des tâches, bien que ceci soit configurable.

Si une tâche de démarrage échoue sur un nœud du pool, l’état du nœud est mis à jour pour refléter l’échec et le nœud n’est plus disponible pour les tâches à affecter. Une tâche de démarrage peut échouer en cas de problème de copie des fichiers de ressources depuis le stockage, ou si le processus exécuté par sa ligne de commande retourne un code de sortie différent de zéro.

#### Tâche du gestionnaire de travaux

Une **tâche de gestionnaire de tâche** est généralement pour le contrôle et/ou la surveillance de l’exécution du travail. Par exemple, la création et l’envoi des d’un travail, la détermination d’autres tâches à exécuter et la détermination du fonctionnement est complète. Une tâche de gestionnaire de travaux n’est pas limitée à ces activités ; il s’agit d’une tâche à part entière qui peut exécuter n’importe quelle action requise pour le travail. Par exemple, une tâche de gestionnaire de tâche peut télécharger un fichier spécifié en tant que paramètre, analyser le contenu de ce fichier et envoyer des tâches supplémentaires en fonction de son contenu.

Une tâche de gestionnaire de tâche est démarrée avant toutes les autres tâches et offre les caractéristiques suivantes :

- Elle est automatiquement soumise en tant que tâche par le service Batch lorsque le travail est créé.

- Elle est planifiée pour s’exécuter avant les autres tâches d’un projet.

- Le nœud associé est le dernier à être supprimé d’un pool lorsque la taille de ce dernier diminue.

- Son arrêt peut être lié à l'arrêt de toutes les tâches du travail.

- La tâche de gestionnaire de travaux reçoit la priorité la plus élevée lorsqu’elle doit être redémarrée. Si aucun nœud inactif n’est disponible, le service Batch peut mettre fin à une tâche en cours d’exécution dans le pool pour lui laisser la place de s’exécuter.

- Une tâche du gestionnaire de travaux associée à un travail n’a pas la priorité sur les tâches d’autres travaux. Parmi les travaux, seules les priorités au niveau du travail sont observées.

#### Tâches de préparation et lancement

Batch fournit la tâche de préparation de travail pour le programme d’installation de l’exécution du travail préliminaire et la tâche de version de maintenance postérieure au travail ou de nettoyage.

- **Tâche de préparation de travail** : la tâche de préparation de travail s’exécute sur tous les nœuds de calcul pour exécuter les tâches avant que les autres tâches du travail soient exécutées. Utilisez la tâche de préparation de travail pour copier des données partagées par toutes les tâches, mais unique au travail, par exemple.
- **Tâche de validation de travail** : lorsqu’un travail est terminé, la tâche de validation s’exécute sur chaque nœud dans le pool ayant exécuté au moins une tâche. Utilisez la tâche de validation pour supprimer les données copiées par la tâche de préparation de travail, ou compresser et télécharger des données de journaux de diagnostic, par exemple.

Les tâches de préparation et de validation vous permettent de spécifier une ligne de commande à exécuter lorsque la tâche est appelée et offrent des fonctionnalités telles que le téléchargement de fichiers, l’exécution élevée, les variables d’environnement personnalisées, la durée maximale d’exécution, le nombre de nouvelles tentatives et la période de rétention de fichier.

Pour plus d’informations sur les tâches de préparation de travail et de validation, consultez [Exécuter les tâches de préparation de travail et de validation sur les nœuds de calcul Azure Batch](batch-job-prep-release.md).

#### Tâches multi-instances

Une [tâche multi-instance](batch-mpi.md) est une tâche configurée pour s’exécuter simultanément sur plusieurs nœuds de calcul. Avec des tâches multi-instances, vous pouvez activer des scénarios de calcul haute performance, comme MPI (Message Passing Interface), qui requièrent un groupe de nœuds de calcul alloués ensemble pour traiter une seule et même charge de travail.

Pour une présentation détaillée de l’exécution des travaux MPI dans Batch à l’aide de la bibliothèque .NET de Batch, consultez l’article [Utiliser des tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](batch-mpi.md).

#### Dépendances de la tâche

La [dépendance entre tâches](batch-task-dependencies.md), comme son nom l’indique, vous permet de préciser que l’exécution d’une tâche dépend de l’achèvement préalable d’autres tâches. Cette fonctionnalité prend en charge des situations dans lesquelles une tâche « en aval » consomme le résultat d’une sortie d’une tâche « en amont », ou lorsqu’une tâche en amont effectue une initialisation requise par une tâche en aval. Pour utiliser cette fonctionnalité, vous devez d’abord activer les dépendances de tâche sur la tâche Batch. Ensuite, pour chaque tâche qui dépend d’une autre (ou de plusieurs autres), vous devez spécifier les tâches dont elle dépend.

Avec l’interdépendance des tâches, vous pouvez configurer des scénarios suivants :

* *taskB* dépend de *taskA* (*taskB* ne commence pas tant que l’exécution de *taskA* n’est pas terminée)
* *taskC* dépend de *taskA* et de *taskB*
* *taskD* dépend d’une suite de tâches, notamment les tâches *1* à *10*, avant de pouvoir s’exécuter

Consultez [Task dependencies in Azure Batch](batch-task-dependencies.md) (Dépendances dans Azure Batch) et l’exemple de code [TaskDependencies][github_sample_taskdeps] dans le référentiel GitHub [azure-batch-samples][github_samples] pour plus d’informations détaillées sur cette fonctionnalité.

## Paramètres d'environnement des tâches

Chaque tâche qui s’exécute dans un travail Batch a accès aux variables d’environnement définies à la fois par le service Batch (définie par le service, consultez le tableau ci-dessous) et par des variables d’environnement personnalisées pouvant être configurées pour les tâches. Les applications et les scripts exécutés par des tâches sur les nœuds ont accès à ces variables d’environnement pendant leur exécution.

Vous pouvez définir des variables d’environnement personnalisées au niveau de la tâche ou du travail en remplissant la propriété de *paramètres d’environnement* pour ces entités. Par exemple, consultez l’opération [Ajouter une tâche à un travail][rest_add_task] \(API Batch REST) ou les propriétés [CloudTask.EnvironmentSettings][net_cloudtask_env] et [CloudJob.CommonEnvironmentSettings][net_job_env] dans Batch .NET.

L’application cliente ou le service peuvent obtenir des variables d’environnement d’une tâche, à la fois définies par le service et personnalisées, à l’aide de l’opération [Obtenir des informations sur une tâche][rest_get_task_info] \(Batch REST) ou en accédant à la propriété [CloudTask.EnvironmentSettings][net_cloudtask_env] \(Batch .NET). Les processus qui s’exécutent sur un nœud de calcul peuvent accéder à ces dernières ainsi qu’à d’autres variables d’environnement sur le nœud, par exemple à l’aide de la syntaxe classique `%VARIABLE_NAME%` (Windows) ou la syntaxe `$VARIABLE_NAME` (Linux).

Les variables d’environnement suivantes sont définies par le service Batch et sont accessibles par vos tâches :

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

>[AZURE.IMPORTANT] Ces variables d’environnement sont uniquement disponibles pour **l’utilisateur de la tâche** ; il s’agit du compte d’utilisateur du nœud sur lequel une tâche est exécutée. Vous ne verrez **pas** ces éléments si vous vous [connectez à distance](#connecting-to-compute-nodes) à un nœud de calcul via RDP ou SSH et répertoriez les variables d’environnement, car le compte d’utilisateur utilisé pour la connexion à distance n’est pas le même que le compte utilisé par la tâche.

## Fichiers et répertoires

Chaque tâche possède un *répertoire de travail* sous lequel elle crée zéro ou plusieurs fichiers et répertoires. Ce répertoire de travail peut être utilisé pour stocker le programme exécuté par la tâche, les données qu’il traite et la sortie du traitement qu’il effectue. Tous les fichiers et répertoires d’une tâche sont détenus par l’utilisateur de la tâche.

Le service Batch expose une partie du système de fichiers sur un nœud en tant que répertoire racine. Le répertoire racine est disponible pour une tâche par le biais d’un accès à la variable d’environnement `AZ_BATCH_NODE_ROOT_DIR`. Pour plus d’informations sur l’utilisation de variables d’environnement, consultez la section [Paramètres d’environnement des tâches](#environment-settings-for-tasks).

Le répertoire racine contient la structure de répertoires suivante :

![Structure de répertoire du nœud de calcul][1]

- **Partagé** : ce répertoire fournit l’accès en lecture/écriture à *toutes* les tâches qui s’exécutent sur un nœud. Les tâches qui s’exécutent sur le nœud peuvent créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire. Les tâches peuvent accéder au répertoire en référençant la variable d’environnement `AZ_BATCH_NODE_SHARED_DIR`.

- **Démarrage** - Ce répertoire est utilisé par une tâche de démarrage en tant que répertoire de travail. Tous les fichiers téléchargés vers le nœud par la tâche de démarrage sont stockés ici. La tâche de démarrage peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire. Les tâches peuvent accéder au répertoire en référençant la variable d’environnement `AZ_BATCH_NODE_STARTUP_DIR`.

- **Tâches** - Un répertoire est créé pour chaque tâche qui s’exécute sur le nœud, accessible en référençant la variable d’environnement `AZ_BATCH_TASK_DIR`.

	Dans chaque répertoire de la tâche, le service Batch crée un répertoire de travail (`wd`) dont le chemin unique est spécifié par la variable d’environnement `AZ_BATCH_TASK_WORKING_DIR`. Ce répertoire fournit un accès en lecture/écriture à la tâche. La tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire, et ce dernier est conservé en fonction de la contrainte *RetentionTime* spécifiée pour la tâche.

	`stdout.txt` et `stderr.txt` - Ces fichiers sont écrits dans le dossier des tâches lors de l’exécution de la tâche.

>[AZURE.IMPORTANT] Lorsqu’un nœud est supprimé du pool, *tous* les fichiers stockés dans le nœud sont supprimés.

## Packages d’applications

La fonctionnalité [packages d’application](batch-application-packages.md) offre une gestion facile et permet le déploiement d’applications sur les nœuds de calcul dans vos pools. Avec les packages d’applications, vous pouvez facilement télécharger et gérer plusieurs versions des applications exécutées par vos tâches, et notamment les fichiers binaires et fichiers de prise en charge, puis déployer automatiquement une ou plusieurs de ces applications sur les nœuds de calcul dans le pool.

Batch gère les détails de l’utilisation de stockage Azure en arrière-plan pour stocker et déployer en toute sécurité vos packages d’application pour le calcul des nœuds, donc votre code et vos frais de gestion peuvent être simplifiés.

Pour en savoir plus sur la fonctionnalité du package d’application, consultez [Déploiement de l’application avec des packages d’applications Azure Batch](batch-application-packages.md).

## Durée de vie de nœud de pool et de calcul

Lorsque vous créez votre solution Azure Batch, une décision de conception doit être prise quant à la procédure et le moment de la création, et la durée sur laquelle les nœuds de calcul de ces pools restent disponibles.

D’un côté, un pool peut être créé pour chaque travail au moment de l’envoi, et ses nœuds peuvent être supprimés dès lors que les tâches cessent de s’exécuter. Ceci permet d’optimiser l’utilisation puisque les nœuds ne sont alloués que lorsque cela est absolument nécessaire et qu’ils s’arrêtent dès qu’ils deviennent inactifs. Cela signifie que le travail doit attendre que les nœuds soient alloués, mais il est important de noter que les tâches seront planifiées sur les nœuds dès qu’elles seront individuellement disponibles, allouées, et que cette tâche de démarrage sera terminée. Batch *n’* attend pas, par exemple, que tous les nœuds d’un pool soient disponibles avant d’affecter des tâches, car cela entraînerait une faible utilisation des nœuds disponibles.

À l’autre extrémité du spectre, si la priorité absolue consiste à démarrer immédiatement une tâche, un pool peut être créé avant l’heure et ses nœuds seront mis à disposition avant l’envoi de travaux. Dans ce scénario, les tâches du travail peuvent démarrer immédiatement, mais les nœuds peuvent rester inactifs en attendant les tâches à affecter.

Une approche combinée, généralement utilisée pour la gestion de la charge variable, mais continue, consiste à disposer d’un pool auquel plusieurs travaux sont soumis, mais à mettre à l’échelle le nombre de nœuds (dans un sens ou dans l’autre) en fonction de la charge de travail (voir [Mise à l’échelle des ressources de calcul](#scaling-compute-resources) ci-dessous). Cela peut être fait en réaction, en fonction de la charge actuelle, de façon proactive, si la charge peut être prévue.

## Mise à l’échelle des ressources de calcul

Avec la [mise à l’échelle automatique](batch-automatic-scaling.md), le service Batch peut ajuster de manière dynamique le nombre de nœuds de calcul d’un pool en fonction de la charge de travail actuelle et de l’utilisation des ressources de votre scénario de calcul. Cela vous permet de réduire le coût global d’exécution de votre application en utilisant uniquement les ressources dont vous avez besoin et en libérant les autres.

Pour activer la mise à l’échelle automatique, vous devez écrire une [formule de mise à l’échelle automatique](batch-automatic-scaling.md#automatic-scaling-formulas) et l’associer avec un pool. Le service Batch utilise la formule pour déterminer le nombre cible de nœuds dans le pool pour le prochain intervalle de mise à l’échelle (intervalle que vous pouvez configurer). Vous pouvez spécifier les paramètres de mise à l’échelle automatique pour un pool lorsque vous le créez ou activez la mise à l’échelle sur un pool ultérieurement. Vous pouvez également mettre à jour les paramètres de mise à l’échelle sur un pool compatible avec la mise à l’échelle.

Par exemple, il se peut qu’un travail exige que vous envoyiez un grand nombre de tâches devant être exécutées. Vous pouvez attribuer au pool une formule de mise à l’échelle qui règle le nombre de nœuds du pool en fonction du nombre actuel de tâches dans la file d’attente et du degré d’achèvement des tâches dans le travail. Le service Batch évalue la formule régulièrement et redimensionne le pool en fonction de la charge de travail (ajoute des nœuds en présence de plusieurs tâches dans la file d’attente ; supprime des nœuds en cas d’absence de tâches dans la file d’attente ou en cours d’exécution) et d’autres paramètres de formule.

Une formule de mise à l’échelle peut être basée sur les mesures suivantes :

- **Mesures temporelles** – Celles-ci sont basées sur les statistiques collectées toutes les cinq minutes dans le nombre d'heures spécifié.

- **Mesures de ressources** – Celles-ci sont basées sur l’utilisation du processeur, de la bande passante et de la mémoire, et sur le nombre de nœuds.

- **Métriques de tâche** – Basées sur l’état de la tâche, tel que *Active* (en attente), *En cours d’exécution*, ou *Terminée*.

Lorsque la mise à l’échelle automatique diminue le nombre de nœuds de calcul dans un pool, vous devez réfléchir aux méthodes pour gérer les tâches s’exécutant au moment de cette opération de réduction. Pour ce faire, Batch fournit une *option de désallocation de nœud* que vous pouvez inclure dans vos formules. Par exemple, vous pouvez spécifier d’arrêter immédiatement les tâches en cours, de les arrêter immédiatement, puis les remettre en file d’attente pour une exécution sur un autre nœud, ou les autoriser à terminer avant la suppression du nœud du pool.

Pour plus d’informations sur la mise à l’échelle automatique d’une application, consultez la section [Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch](batch-automatic-scaling.md).

> [AZURE.TIP] Pour optimiser l’utilisation des ressources de calcul, définissez à zéro le nombre cible de nœuds à la fin d’un travail tout en autorisant les tâches en cours à s’achever.

## Sécurité avec certificats

Vous devez en principe utiliser des certificats lors du chiffrement ou du déchiffrement des informations sensibles pour les tâches, par exemple, la clé d’un [compte de stockage Azure][azure_storage]. Pour prendre en charge ces opérations, il est possible d’installer des certificats sur les nœuds. Les secrets chiffrés sont transmis aux tâches dans les paramètres de ligne de commande ou incorporés dans l’une des ressources et les certificats installés peuvent être utilisés pour les déchiffrer.

Pour ajouter un certificat à un compte Batch, utilisez l’opération [Ajouter le certificat][rest_add_cert] \(Batch REST) ou la méthode [CertificateOperations.CreateCertificate][net_create_cert] \(Batch .NET) pour ajouter un certificat à un compte Batch. Vous pouvez ensuite associer le certificat à un pool existant ou nouveau. Lorsqu’un certificat est associé à un pool, le service Batch installe le certificat sur chaque nœud du pool. Le service Batch installe les certificats appropriés au démarrage du nœud, avant de lancer une tâche quelconque (notamment les tâches de démarrage et celles du gestionnaire de travaux).

## Gestion des erreurs

Il peut s’avérer utile de gérer les échecs de tâche et application au sein de votre solution Batch.

### Gestion des échecs de tâche
Les échecs de tâche peuvent être classés suivant les catégories suivantes :

- **Échecs de planification**

	Si le transfert de fichiers spécifié pour une tâche échoue pour une raison quelconque, une « erreur de planification » est définie pour la tâche.

	Les causes d’erreur de planification peuvent être dues au fait que les fichiers de ressources de la tâche ont été déplacés, que le compte de stockage est indisponible ou au fait qu’un autre problème est survenu et a empêché la copie des fichiers vers le nœud.

- **Échecs d’application**

	Le processus de la tâche spécifié par la ligne de commande peut également échouer. Le processus est considéré comme ayant échoué lorsqu’un code de sortie différent de zéro est renvoyé par le processus exécuté par la tâche (voir *Codes de sortie de tâche* ci-dessous).

	Pour les échecs d’application, il est possible de configurer Batch pour relancer automatiquement la tâche autant de fois que l’utilisateur l’aura spécifié.

- **Échecs de contrainte**

	Une contrainte peut être définie, qui spécifie la durée maximale d’exécution d’un travail ou d’une tâche, le *maxWallClockTime*. Cela peut s’avérer utile lorsqu’il s’agit de terminer les tâches « suspendues ».

	Lorsque le nombre maximal de tentatives est atteint, la tâche est marquée comme *terminée*, mais le code de sortie est défini sur le `0xC000013A`, et le champ *schedulingError* est marqué comme `{ category:"ServerError", code="TaskEnded"}`.

### Débogage des échecs d’application

- `stderr` et `stdout`

	Pendant l’exécution, une application peut produire des diagnostics pouvant être utilisés pour résoudre les problèmes. Comme mentionné dans la section [Fichiers et répertoires](#files-and-directories), le service Batch écrit des sorties et des sorties d’erreur standard dans des fichiers `stdout.txt` et `stderr.txt` se trouvant dans le répertoire de tâche sur le nœud de calcul. Vous pouvez utiliser le portail Azure ou l’un des Kits de développement logiciel (SDK) de Batch pour télécharger ces fichiers. Par exemple, en utilisant [ComputeNode.GetNodeFile][net_getfile_node] et [CloudTask.GetNodeFile][net_getfile_task] dans la bibliothèque Batch .NET, vous pouvez récupérer ces fichiers et d’autres à des fins de dépannage.

- **Codes de sortie de tâche**

	Comme mentionné ci-dessus, une tâche est marquée comme ayant échoué par le service Batch si le processus exécuté par la tâche retourne un code de sortie différent de zéro. Lorsqu’une tâche exécute un processus, Batch remplit la propriété de code de sortie de la tâche avec le *code de retour du processus*. Il est important de noter que le code de sortie d’une tâche n’est **pas** déterminé par le service Batch, mais par le processus lui-même ou le système d’exploitation sur lequel le processus est exécuté.

### Prise en compte des échecs ou des interruptions de tâche

Les tâches peuvent parfois échouer ou être interrompues. L’application de la tâche elle-même peut échouer, le nœud sur lequel elle s’exécute peut être redémarré, voire retiré du pool pendant le redimensionnement de ce dernier si la stratégie de désallocation définie prévoit la suppression immédiate des nœuds, sans attendre la fin des tâches. Dans tous les cas, la tâche peut être automatiquement remise en file d’attente par Batch pour exécution sur un autre nœud.

Un problème intermittent peut également provoquer la suspension d’une tâche ou ralentir son exécution. Une durée maximale d’exécution de la tâche peut être définie, et, en cas de dépassement, Batch interrompt l’application de la tâche.

### Se connecter aux nœuds de calcul

Des débogages et diagnostics étendus peuvent être effectués en se connectant à un nœud de calcul à distance. Vous pouvez utiliser le portail Azure pour télécharger un fichier RDP (Remote Desktop) pour les nœuds Windows et obtenir des informations de connexion SSH pour les nœuds Linux. Vous pouvez également effectuer cette opération à l’aide des API Batch, telles que [Batch .NET][net_rdpfile] ou [Python Batch](batch-linux-nodes.md#connect-to-linux-nodes).

>[AZURE.IMPORTANT] Pour vous connecter à un nœud via RDP ou SSH, vous devez d’abord créer un utilisateur sur le nœud. Pour ce faire, utilisez le portail Azure pour [Ajouter un compte d’utilisateur à un nœud][rest_create_user] à l’aide de l’API Batch REST, appeler la méthode [ComputeNode.CreateComputeNodeUser][net_create_user] dans Batch .NET, ou appeler la méthode [add\_user][py_add_user] dans le module Python de Batch.

### Résolution des problèmes de « mauvais » nœuds de calcul

Quand certaines de vos tâches échouent, votre application cliente Batch ou un service peut examiner les métadonnées des tâches en échec pour identifier un nœud présentant un dysfonctionnement. Chaque nœud d’un pool se voit attribuer un ID unique et le nœud sur lequel s’exécute une tâche est inclus dans les métadonnées de la tâche. Une fois que vous avez identifié un « nœud problème », vous pouvez effectuer les actions suivantes :

- **Redémarrer le nœud** ([REST][rest_reboot] | [.NET][net_reboot])

	Le fait de redémarrer le nœud peut parfois résoudre des problèmes latents comme des processus bloqués ou défaillants. Notez que si votre pool utilise une tâche de démarrage ou si votre travail utilise une tâche de préparation, ces deux éléments s’exécuteront au redémarrage du nœud.

- **Réinitialiser le nœud** ([REST][rest_reimage] | [.NET][net_reimage])

	Cette opération réinstalle le système d’exploitation sur le nœud. Comme avec le redémarrage d’un nœud, les tâches de démarrage et celles de préparation d’un travail sont relancées une fois le nœud réinitialisé.

- **Supprimer le nœud du pool** ([REST][rest_remove] | [.NET][net_remove])

	Il est parfois nécessaire de supprimer entièrement le nœud à partir du pool.

- **Désactiver la planification des tâches sur le nœud** ([REST][rest_offline] | [.NET][net_offline])

	Cette opération est efficace puisqu’elle place le nœud « hors connexion ». Ainsi, aucune tâche ultérieure ne peut lui être assignée. Toutefois, le nœud est autorisé à poursuivre son exécution et à rester dans le pool. Cela vous permet de faire une recherche approfondie sur la cause des échecs sans perdre les données de la tâche en échec et sans que le nœud n’occasionne d’autres échecs de tâche supplémentaires. Par exemple, vous pouvez désactiver la planification des tâches sur le nœud, puis vous [connecter à distance](#connecting-to-compute-nodes) pour examiner les journaux des événements de ce nœud ou encore résoudre d’autres problèmes. Une fois que vous avez terminé votre recherche, vous pouvez remettre le nœud en ligne en activant la planification des tâches ([REST][rest_online], [.NET][net_online]), ou effectuer l’une des actions ci-dessus.

> [AZURE.IMPORTANT] Pour chaque action mentionnée ci-dessus (redémarrer, réinitialiser, supprimer, désactiver la planification des tâches), vous pouvez indiquer la manière dont les tâches en cours d’exécution sur le nœud sont gérées lorsque vous effectuez l’action. Par exemple, lorsque vous désactivez la planification des tâches sur un nœud avec la bibliothèque cliente Batch.NET, vous pouvez indiquer une valeur d’énumération [DisableComputeNodeSchedulingOption][net_offline_option]. Celle-ci sert à préciser s’il faut **interrompre** les tâches en cours d’exécution, les **remettre en file d’attente** pour les planifier sur d’autres nœuds ou finaliser les tâches en cours avant d’exécuter l’action (**TaskCompletion**).

## Étapes suivantes

- Passez en revue un exemple d’application Batch détaillée dans [Prise en main de la bibliothèque Azure Batch pour .NET.](batch-dotnet-get-started.md). Il existe également une [version Python](batch-python-tutorial.md) du didacticiel qui exécute une charge de travail sur des nœuds de calcul Linux.

- Découvrez comment [créer des pools de nœuds de calcul Linux](batch-linux-nodes.md).

- Consultez le [forum Azure Batch][batch_forum] sur MSDN. Le forum est parfait pour poser des questions, que vous soyez débutant ou expert dans l’utilisation du service Batch.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
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
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx

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

<!---HONumber=AcomDC_0629_2016-->