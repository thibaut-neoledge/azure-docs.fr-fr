<properties 
	pageTitle="Concepts de base concernant les API dans Azure Batch" 
	description="Concepts permettant de présenter les API et les services par lots d'Azure Batch aux développeurs" 
	services="batch" 
	documentationCenter=".net" 
	authors="yidingzhou" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="batch" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-compute" 
	ms.date="03/19/2015" 
	ms.author="yidingz"/>

<!--The next line, with one pound sign at the beginning, is the page title-->
# Concepts de base concernant les API dans Azure Batch

Le service Azure Batch fournit une infrastructure de planification des travaux pour le calcul évolutif et distribué. Il gère un ensemble de machines virtuelles qui sont situées dans différents centres de données et clusters dans Azure. Le service Batch effectue un calcul distribué en exécutant un ou plusieurs programmes de manière planifiée ou à la demande, pour une exécution à un moment donné sur une collection spécifique de ces machines virtuelles. Il gère ces machines virtuelles pour exécuter les tâches de calcul selon les besoins en ressources, les spécifications et les contraintes que vous fournissez.

En utilisant le service Batch, vous pouvez éliminer la nécessité d'écrire du code pour la mise en file d'attente, la planification, l'allocation et la gestion des ressources de calcul. Cela vous permet de vous concentrer sur l'application spécifique, sans vous soucier de la complexité de la gestion des ressources et de la planification des travaux dans la plateforme sous-jacente. Cela permet également au service Batch d'optimiser l'emplacement de ces travaux, ainsi que leur accès aux données qu'ils ont besoin de traiter.

Voici quelques scénarios que vous pouvez activer en utilisant le service Batch :

- traitement parallèle qui effectue beaucoup de calculs ;

- nettoyage quotidien des fichiers ;

- traitement par lots.

## <a name="resource"></a> Ressources du service Batch

Lorsque vous utilisez le service Batch, vous profitez des ressources suivantes :

- [Compte](#account)

- [Machine virtuelle de tâche](#taskvm)

- [Pool](#pool)

- [Élément de travail](#workitem)

- [Travail](#job)

- [Tâche](#task)

	- [Tâche de démarrage](#starttask)
	
	- [Tâche du gestionnaire de travaux](#jobmanagertask)

### <a name="account"></a>Compte

Un compte Batch est une entité identifiée de façon unique au sein du service Batch. Tout le traitement s'effectue via un compte Batch. Lorsque vous effectuez des opérations avec le service Batch, vous avez besoin du nom et de la clé du compte. Pour créer un compte Batch, reportez-vous à la section Compte Batch de la [vue d'ensemble d’Azure Batch][].


### <a name="taskvm"></a>Machine virtuelle de tâche

Une machine virtuelle de tâche est une machine virtuelle Azure dédiée à une charge de travail spécifique pour votre application. La taille d'une machine virtuelle de tâche détermine le nombre de cœurs du processeur, la capacité de mémoire et la taille du système de fichiers local qui lui est allouée. Une machine virtuelle de tâche peut être une machine virtuelle de petite, grande ou très grande taille, comme décrit dans la rubrique [Tailles des machines virtuelles et services cloud pour Windows Azure](http://msdn.microsoft.com/library/dn197896.aspx).

Les types de programme qu'une machine virtuelle de tâche peut exécuter incluent les fichiers exécutables (.exe), les fichiers de commandes (.cmd et .bat) et les fichiers de script. Une machine virtuelle de tâche a également les attributs suivants :

- Dossiers de systèmes de fichiers partagés et spécifiques d'une tâche. Une structure des dossiers et des variables d’environnement sont créées sur chaque machine virtuelle du pool. La structure des dossiers suivante est créée avec un dossier «partagé » pour les applications et des données partagées entre les tâches, ainsi qu’un dossier pour chacune d’entre elles.

![][1]

- fichiers stdout.txt et stderr.txt écrits dans le dossier spécifique d'une tâche ;

- variables d'environnement pour le traitement ;

- paramètres de pare-feu configurés pour le contrôle de l'accès.

>Accès aux machines virtuelles
>
>Si l'accès à une machine virtuelle est requis, pour le débogage par exemple, le fichier RDP peut être obtenu et ensuite utilisé pour accéder à la machine virtuelle via le Bureau à distance.


### <a name="pool"></a>Pool

Un pool est une collection de machines virtuelles de tâche sur lesquelles votre application s'exécute. Vous pouvez créer le pool ou laisser le service Batch le créer automatiquement lorsque vous spécifiez le travail à accomplir. Vous pouvez créer et gérer un pool qui répond aux besoins de votre application. Un pool peut être utilisé uniquement par le compte Batch dans lequel il a été créé. Un compte Batch peut avoir plusieurs pools.

Pools Azure Batch créés sur la plate-forme de calcul principale Azure ; les pools Batch permettent l’allocation à grande échelle, l’installation d’applications et de données, le transfert de données, l’analyse de l’état d’intégrité et la mise à l’échelle flexible des machines virtuelles.

Chaque machine virtuelle de tâche ajoutée à un pool se voit attribuer un nom unique et l'adresse IP associée. Lorsqu'une machine virtuelle de tâche est supprimée d'un pool, elle perd les modifications apportées au système d'exploitation, tous ses fichiers locaux, son nom et son adresse IP. Quand une machine virtuelle de tâche quitte un pool, sa durée de vie est terminée.

Vous pouvez configurer un pool pour permettre la communication entre les machines virtuelles de tâche qu'il contient. Si la communication intra-pool est demandée pour un pool, le service Batch autorise les ports supérieurs à 1100 sur chaque machine virtuelle de tâche du pool. Chaque machine virtuelle de tâche du pool est configurée pour autoriser ou restreindre les connexions entrantes à cette plage de ports et uniquement à partir d'autres machines virtuelles de tâche du pool. Si votre application ne nécessite pas la communication entre des machines virtuelles de tâche, le service Batch peut éventuellement allouer un grand nombre de machines virtuelles de tâche de différents centres de données ou clusters au pool pour permettre un traitement plus parallèle.

Lorsque vous créez un pool, vous pouvez spécifier les attributs suivants :

- La **taille des machines virtuelles** dans le pool.
	- La taille appropriée de la machine virtuelle doit être définie en fonction des caractéristiques et des spécifications de l'application ou des applications qui vont être utilisées sur la machine virtuelle. La taille d’une machine virtuelle est généralement déterminée en partant du principe que les tâches sont exécutées les unes à la suite des autres. Une taille plus appropriée et plus rentable vous est, par exemple, proposée lorsqu’une application est multithread et que celle-ci requiert une mémoire plus ou moins importante. Il est possible que plusieurs tâches soient attribuées et que plusieurs instances d’application s’exécutent en parallèle. Dans ce cas, une machine virtuelle plus volumineuse est généralement choisie – voir ci-dessous le « nombre maximum de tâches autorisées par machine virtuelle ». 
	- Toutes les machines virtuelles du pool doivent avoir la même taille. Si différentes applications doivent être exécutées avec la même configuration système requise et/ou avec une charge différente, plusieurs pools devront alors être créés.
	- Toutes les tailles de machine virtuelle du service cloud peuvent être configurées pour un pool, hormis A0.

- La famille de système d'exploitation et la version qui s'exécute sur les machines virtuelles.
	- Comme avec les rôles de travail, la famille de système d’exploitation et la version de système d’exploitation peuvent être configurées.
	- La famille de système d’exploitation détermine également les versions de .NET qui sont installées avec le système d'exploitation.
	- Comme avec les rôles de travail, il est recommandé d’utiliser « * » pour que la version du système d’exploitation installée sur la machine virtuelle soit automatiquement mise à niveau et qu’aucune tâche supplémentaire ne soit requise pour gérer ces nouvelles versions. Une version spécifique de système d'exploitation est généralement utilisée pour assurer la compatibilité des applications, et permettre aux tests de compatibilité descendante d’être réalisés avant d'autoriser la mise à jour de la version. Une fois validée, la version du système d'exploitation du pool peut être mise à jour et la nouvelle image du système d'exploitation peut également être installée – toutes les tâches en cours d'exécution seront interrompues et remises en file d'attente.

- Nombre cible de machines virtuelles qui doivent être disponibles pour le pool.

- stratégie de mise à l'échelle du pool ; Outre le nombre de machines virtuelles, vous pouvez également spécifier une formule de mise à l'échelle pour chaque pool. Le service Batch exécutera la formule pour modifier le nombre de machines virtuelles utilisées en fonction des statistiques du pool et de l’élément de travail.

- Configuration de la planification
	- Une tâche peut, par défaut, s’exécuter à tout moment sur une machine virtuelle du pool, mais il est parfois préférable que plusieurs tâches soient exécutées en même temps sur une machine virtuelle. Vous pouvez, par exemple, augmenter l'utilisation d’une machine virtuelle si une application requiert davantage d’E/S ; l’exécution de plusieurs applications optimisera l'utilisation du processeur. Il vous est également possible de réduire le nombre de machines virtuelles présentes dans le pool, mais ceci risque de réduire le nombre de copies de données requises pour les groupes de données de référence de grande taille. Pour une taille d’application A1, une taille A4 pourrait être utilisée, de même qu’une configuration permettant aux utilisateurs d’exécuter jusqu'à 8 tâches simultanément, consommant chacune un cœur.
	- La configuration « tâches maximales par machine virtuelle » détermine le nombre maximal de tâches qui peuvent être exécutées en parallèle.
	- Une « stratégie de remplissage » peut également être spécifiée et déterminer si Batch remplit d’abord les machines virtuelles ou si les tâches sont réparties sur toutes les machines virtuelles.
 
- L'état de communication des ordinateurs virtuels dans le pool.
 	- Les tâches s’exécutent généralement de manière indépendante et elles n’ont pas besoin de communiquer entre elles, mais ceci n’est pas toujours le cas pour certaines applications (notamment les applications utilisant MPI).
	- Une configuration peut vous permettre de découvrir si la machine virtuelle peut communiquer, et elle peut être utilisée pour configurer l'infrastructure sous-jacente du réseau et avoir un impact sur le placement des machines virtuelles.

- tâche de démarrage des machines virtuelles de tâche du pool.

Lorsque vous créez un pool, vous pouvez spécifier le compte de stockage auquel le pool doit être associé. Le service Batch alloue au compte de stockage spécifié des machines virtuelles de tâche provenant de centres de données dotés des meilleures capacités en matière de bande passante et de connectivité réseau. Les charges de travail bénéficient ainsi d'un accès plus efficace aux données.

### <a name="workitem"></a>Élément de travail

Un élément de travail spécifie comment le calcul est effectué sur les machines virtuelles de tâche d'un pool.

- Un élément de travail peut disposer d’un ou plusieurs travaux qui lui sont associés. Une planification facultative peut être spécifiée pour un élément de travail. Dans ce cas, une tâche est créée pour chaque occurrence de la planification. Si aucune planification n'est spécifiée, pour le travail à la demande, un travail est alors créé immédiatement.
- L'élément de travail spécifie le pool sur lequel le travail sera exécuté. Le pool peut être un pool déjà créé, qui est utilisé par de nombreux éléments de travail, mais il peut également être créé pour chaque travail associé à l'élément de travail ou pour toutes les tâches associées à l'élément de travail.
- Une priorité facultative peut être spécifiée. Lorsqu'un élément de travail est envoyé avec une priorité plus élevée que les autres éléments de travail en cours d'exécution, les tâches des éléments de travail de priorité plus élevée sont mises en file d'attente, devant les tâches des éléments de travail de priorité inférieure. Les tâches de priorité inférieure qui sont déjà en cours d'exécution ne seront pas annulées.
- Des contraintes peuvent être spécifiées et appliquées aux travaux qui leur sont associés.
	- Une durée maximale peut être définie pour ces tâches. Si la durée d’exécution des travaux est supérieure à la durée maximale spécifiée, le travail et toutes les tâches qui lui sont associées seront terminés.
	- Azure Batch peut détecter les tâches qui échouent et les relancer. Le nombre maximal de tentatives de tâche peut être spécifié, par défaut, comme une contrainte, et indiquer notamment qu'une tâche doit toujours être relancée (ou bien qu’elle ne doit jamais l’être). Lorsqu’une tâche est relancée, cela signifie qu’elle est remise en file d'attente et qu’elle sera de nouveau exécutée.
- Les tâches qui doivent être exécutées pour l'élément de travail peuvent être spécifiées par le client de la même manière que l'élément de travail a été créé, mais une tâche de gestionnaire de travaux peut également être spécifiée. Une tâche de gestionnaire de travaux utilise l'API Batch et contient le code permettant de créer les tâches requises pour un travail avec la tâche qui s’exécute sur une des machines virtuelles du pool. Les tâches de gestionnaire de travaux sont gérées essentiellement par Batch : elles sont mises en file d'attente dès que le travail est créé et elles sont relancées lorsqu’elles échouent pour une raison quelconque. Un gestionnaire de travaux est requis pour les éléments de travail disposant d’une planification puisqu’il est le seul moyen permettant de définir les tâches avant que le travail soit instancié.

### <a name="job"></a>Travail

Un travail est une instance en cours d'exécution d'un élément de travail et se compose d'une collection de tâches. Le service Batch instancie un travail en fonction de la configuration de l'élément de travail. Le travail utilise les machines virtuelles de tâche du pool associé à l'élément de travail.

### <a name="task"></a>Tâche

Une tâche est une unité de calcul associée à un travail et exécutée sur une machine virtuelle de tâche. Les tâches sont affectées à une machine virtuelle dans le but d’être exécutées ou mises en file d'attente jusqu'à ce qu'une machine virtuelle soit disponible. Elle utilise les ressources suivantes :

- Programme spécifié dans l'élément de travail.

- Fichiers de ressources qui contiennent les données à traiter. Ces fichiers sont automatiquement copiés sur la machine virtuelle de tâche depuis le stockage d'objets blob. Pour plus d'informations, consultez la section Fichiers et répertoires.

- Paramètres d'environnement requis par le programme. Pour plus d'informations, consultez la section Paramètres d'environnement des tâches.

- Contraintes dans lesquelles le calcul doit se produire. Par exemple, la durée maximale pendant laquelle la tâche est autorisée à s'exécuter, le nombre maximal de nouvelles tentatives en cas d'échec de la tâche, ainsi que la durée maximale pendant laquelle les fichiers du répertoire de travail sont conservés.

Outre les tâches que vous pouvez définir pour effectuer des calculs sur une machine virtuelle de tâche, vous pouvez utiliser les tâches spéciales suivantes fournies par le service Batch :

- [Tâche de démarrage](#starttask)

- [Tâche du gestionnaire de travaux](#jobmanagertask)

#### <a name="starttask"></a>Tâche de démarrage

Vous pouvez configurer le système d'exploitation des machines virtuelles d'un pool en associant une tâche de démarrage au pool. Une tâche de démarrage peut effectuer certaines actions, dont l'installation du logiciel et le démarrage des processus en arrière-plan. Elle s'exécute chaque fois qu'une machine virtuelle démarre pendant sa durée de présence dans le pool.

Comme avec n'importe quelle tâche Batch, une liste de fichiers peut être spécifiée dans le stockage Azure, en plus d'une ligne de commande qui est exécutée par Batch. Azure Batch copiera les fichiers du stockage Azure et exécutera ensuite la ligne de commande. Pour une tâche de démarrage du pool, la liste des fichiers contient généralement les fichiers des applications ou un package, mais elle peut également inclure des données de référence qui seront utilisées par toutes les tâches qui s’exécutent sur les machines virtuelles du pool. La ligne de commande peut exécuter des scripts PowerShell ou bien Robocopy pour copier, par exemple, les fichiers d'application dans le dossier « partagé », et également exécuter un MSI.

Bien que ceci soit configurable, il est généralement préférable pour Batch d’attendre que la tâche de démarrage s'exécute, pour que la machine virtuelle soit prête à être affectée.

Si une tâche de démarrage échoue pour une machine virtuelle du pool, l'état de la machine virtuelle est mis à jour pour refléter l'échec et la machine virtuelle ne sera pas disponible pour les tâches qui doivent être affectées. Une tâche de démarrage peut échouer si un problème de copie de fichiers est spécifié pour la tâche de démarrage ou que le processus de tâche de démarrage renvoie une valeur différente de zéro.

Le fait que toutes les informations nécessaires à la configuration des machines virtuelles et à l’installation des applications soient déclarées signifie qu’il est aussi simple d’augmenter le nombre de machines virtuelles dans un pool que de spécifier le nouveau nombre requis ; Batch dispose de toutes les informations lui permettant de configurer les machines virtuelles et de les préparer à accepter des tâches.

Une tâche de démarrage est définie lors de l’ajout d’une section JSON au corps de la demande pour l'opération Ajouter un pool. L'exemple suivant montre une définition de base d'une tâche de démarrage :

	{
		“commandLine”:”mypoolsetup.exe”,
		“resourceFiles”:
		[
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”mypoolsetup.exe”
			},
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp2.exe”
			}
		],
		“maxTaskRetryCount”:0
	}

Une interface C# ressemble à ceci :

	ICloudPool pool = pm.CreatePool(poolName, targetDedicated: 3, vmSize: "small", osFamily: "3");
	pool.StartTask = new StartTask();
	pool.StartTask.CommandLine = "mypoolsetup.exe";
	pool.StartTask.ResourceFiles = new List<IResourceFile>();
	pool.StartTask.ResourceFiles.Add(new ResourceFile("http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d", "mypoolsetup.exe"));
	pool.Commit();


#### <a name="jobmanagertask"></a>Tâche du gestionnaire de travaux

Une tâche du gestionnaire de travaux est lancée avant toutes les autres tâches. La tâche du gestionnaire de travaux offre les avantages suivants :

- Elle est automatiquement créée par le service Batch lorsque le travail est créé.

- Elle est planifiée avant les autres tâches du travail.

- La machine virtuelle de tâche associée est la dernière à être supprimée d'un pool lorsque la taille de ce dernier diminue.

- Elle dispose de la priorité la plus élevée lorsqu'elle doit être redémarrée. Si une machine virtuelle de tâche inactive n'est pas disponible, le service Batch peut mettre fin à une tâche en cours d'exécution dans le pool pour lui laisser la place de s'exécuter.

- Son arrêt peut être lié à l'arrêt de toutes les tâches du travail.

Une tâche du gestionnaire de travaux associée à un travail n'a pas la priorité sur les tâches des autres travaux. Parmi les travaux, seules les priorités au niveau du travail sont observées. Une tâche du gestionnaire de travaux est définie en ajoutant une section XML dans le corps de demande de l'opération Ajouter un élément de travail. L'exemple suivant montre une définition de base d'une tâche du gestionnaire de travaux :

	{
		“name”:”jmTask”,
		“commandLine”:”myapp1.exe”,
		“resourceFiles”:
		[
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp1.exe”
			},
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp2.exe”
			}
		],
		“taskConstraints”:
		{
			“maxWallClockTime”:”PT1H”,
			“maxTaskRetryCount”:0,
			“retentionTime”:”PT1H”
		},
		“killJobOnCompletion”:true,
		“runElevated”:false,
		“runExclusive”:true
	}




## <a name="workflow"></a>Flux de travail du service Batch

Vous avez besoin d'un compte Batch pour utiliser le service Batch et vous utilisez plusieurs ressources du service pour planifier le calcul. Utilisez le flux de travail de base suivant lorsque vous créez un scénario de calcul distribué avec le service Batch :

1. Téléchargez les fichiers que vous souhaitez utiliser dans votre scénario de calcul distribué dans un compte de stockage Azure. Ils doivent être situés dans le compte de stockage afin que le service Batch puisse y accéder. Ce dernier les charge sur une machine virtuelle de tâche quand la tâche s'exécute.

2. Téléchargez les fichiers binaires dépendants dans le compte de stockage. notamment le programme exécuté par la tâche et les assemblys dépendants. Ces fichiers doivent également être accessibles à partir du stockage et sont chargés dans la machine virtuelle de tâche.

3. Créez un pool de machines virtuelles de tâche. Vous pouvez attribuer la taille de la machine virtuelle de tâche à utiliser lors de la création du pool. Quand une tâche s'exécute, elle reçoit une machine virtuelle de tâche à partir de ce pool.

4. Créez un élément de travail. Un travail est créé automatiquement lorsque vous créez un élément de travail. Un élément de travail vous permet de gérer un travail constitué de tâches.

5. Ajoutez des tâches à l'élément de travail. Chaque tâche utilise le programme téléchargé pour traiter les informations à partir d'un fichier téléchargé.

6. Analysez les résultats de la sortie.

## <a name="files"></a>Fichiers et répertoires

Chaque tâche possède un répertoire de travail dans lequel elle crée zéro ou plusieurs répertoires et fichiers pour stocker le programme qu'elle exécute, les données qu'elle traite et la sortie du traitement qu'elle effectue. Ces fichiers et répertoires sont ensuite disponibles pour une utilisation par d'autres tâches pendant l'exécution d'un travail. L'ensemble des tâches, fichiers et répertoires d'une machine virtuelle de tâche sont la propriété d'un seul compte d'utilisateur.

Le service Batch expose une partie du système de fichiers sur une machine virtuelle de tâche en tant que répertoire racine. Le répertoire racine de la machine virtuelle de tâche est disponible pour une tâche via la variable d'environnement WATASK_TVM_ROOT_DIR. Pour plus d'informations sur l'utilisation de variables d'environnement, consultez la section Paramètres d'environnement des tâches.

Le répertoire racine contient les sous-répertoires suivants :

- **Tâches** – Cet emplacement est celui où tous les fichiers qui appartiennent à des tâches qui s'exécutent sur la machine virtuelle de tâche sont stockés. Pour chaque tâche, le service Batch crée un répertoire de travail avec le chemin d'accès unique au format %WATASK_TVM_ROOT_DIR%/tasks/workitemName/jobName/taskName/. Ce répertoire fournit un accès en lecture/écriture à la tâche. La tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire, et ce dernier est conservé en fonction de la contrainte RetentionTime spécifiée pour la tâche.

- **Partagé** – Cet emplacement est un répertoire partagé pour toutes les tâches situées dans le compte. Dans la machine virtuelle de tâche, le répertoire partagé est %WATASK_TVM_ROOT_DIR%/shared. Ce répertoire fournit un accès en lecture/écriture à la tâche. La tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire.

- **Démarrer** – Cet emplacement est utilisé par une tâche de démarrage comme répertoire de travail. Tous les fichiers téléchargés par le service Batch pour lancer la tâche de démarrage sont également stockés dans ce répertoire. Dans la machine virtuelle de tâche, le répertoire de démarrage est %WATASK_TVM_ROOT_DIR%/start. La tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire, et ce dernier est utilisable par les tâches de démarrage pour configurer le système d'exploitation.

Lorsqu'une machine virtuelle de tâche est supprimée du pool, tous les fichiers stockés dans celle-ci sont supprimés.

## <a name="lifetime"></a>Pool et durée de vie des machines virtuelles

Avant de démarrer la conception, il est tout d’abord nécessaire de déterminer la date de création des pools et la période durant laquelle les machines virtuelles resteront disponibles.

Un pool peut être créé pour chaque travail envoyé et les machines virtuelles peuvent être supprimées dès lors que les tâches cesseront de s’exécuter. Ceci permet d’optimiser l'utilisation puisque les machines virtuelles ne sont allouées que lorsque cela est absolument nécessaire et qu’elles s’arrêtent dès qu'elles deviennent inactives. Cela signifie que la tâche doit attendre que les machines virtuelles soient allouées, même s'il est important de noter que les tâches seront planifiées sur les machines virtuelles dès qu'elles seront individuellement disponibles, allouées et que cette tâche de démarrage sera terminée ; Batch n'attend pas, par exemple, que toutes les machines virtuelles d’un pool soient disponibles, car cela entraînerait une faible utilisation.

Si la priorité est de lancer immédiatement les travaux, un pool doit alors être créé et des machines virtuelles doivent être disponibles avant l'envoi de la tâche. Les tâches peuvent être immédiatement lancées, mais il se peut qu’en fonction de la charge, la machine virtuelle soit inactive en attendant les tâches projet.

Lorsqu’une quantité variable de charge est en cours, l’utilisateur doit disposer d'un pool vers lequel plusieurs travaux sont envoyés, mais également augmenter ou réduire le nombre de machines virtuelles en fonction de la charge. Ceci peut être effectué de manière réactive ou proactive lorsque la charge peut être prédite.

## <a name="scaling"></a>Mise à l'échelle des applications

Votre application peut facilement être mise à l'échelle (dans un sens ou dans l'autre) automatiquement pour accueillir le calcul dont vous avez besoin. Vous pouvez ajuster dynamiquement le nombre de machines virtuelles de tâche d'un pool en fonction de la charge de travail actuelle et des statistiques sur l'utilisation des ressources. Vous pouvez également optimiser le coût total de l'exécution de votre application en la configurant à l'échelle automatiquement. Vous pouvez spécifier les paramètres de mise à l'échelle d'un pool lorsqu'il est créé, et mettre à jour la configuration à tout moment.

Lors d’une diminution du nombre de machines virtuelles, les tâches s’exécutant sur la machine virtuelle doivent être prises en compte. Une stratégie de désallocation est spécifiée et détermine si les tâches en cours d'exécution doivent être interrompues pour supprimer immédiatement la machine virtuelle ou si les tâches peuvent être terminées avant que la machine virtuelle soit supprimée. Définir le nombre cible de machines virtuelles sur zéro à la fin d'un travail, mais permettre à des tâches en cours d’être achevées, optimisera l'utilisation.

Spécifiez la mise à l'échelle automatique d'une application à l'aide d'un jeu de formules de mise à l'échelle. Les formules peuvent servir à déterminer le nombre de machines virtuelles de tâche qui se trouvent dans le pool pendant le prochain intervalle de mise à l'échelle. Par exemple, vous devez envoyer un grand nombre de tâches qui doivent être planifiées sur un pool. Vous pouvez attribuer une formule de mise à l'échelle au pool qui spécifie la taille du pool selon le nombre actuel de tâches en attente et la vitesse d'exécution des tâches. Le service Batch évalue la formule régulièrement et redimensionne le pool en fonction de la charge de travail.

Une formule peut être basée sur les mesures suivantes :

- **Mesures temporelles** – Celles-ci sont basées sur les statistiques collectées toutes les cinq minutes dans le nombre d'heures spécifié.

- **Mesures de ressources** – Celles-ci sont basées sur l'utilisation du processeur, de la bande passante et de la mémoire, et sur le nombre de machines virtuelles de tâche.

- **Mesures de tâches** – Celles-ci sont basées sur l'état des tâches (Actif, En attente et Terminé).

Pour plus d'informations sur la mise à l'échelle automatique d'une application, consultez la section Configuration de la mise à l'échelle automatique de machines virtuelles de tâche.

>Supprimer des machines virtuelles
>
>Bien que ceci soit rarement nécessaire, il est possible de spécifier des machines virtuelles pour qu’elles soient supprimées d’un pool. Si une machine virtuelle paraît peu fiable, celle-ci peut être supprimée.

## <a name="cert"></a>Certificats pour les applications

En général, vous devez utiliser des certificats lorsque vous chiffrez des informations confidentielles. Des certificats peuvent être installés sur les machines virtuelles de tâche. Les secrets chiffrés sont transmis aux tâches dans les paramètres de ligne de commande ou incorporés dans l'une des ressources et les certificats installés peuvent être utilisés pour les déchiffrer. Un exemple d'informations secrètes est la clé d'un compte de stockage.

Vous utilisez l'opération Ajouter un certificat pour ajouter un certificat à un compte Batch. Vous pouvez ensuite associer le certificat à un pool existant ou nouveau. Lorsqu'un certificat est associé à un pool, le service Batch installe le certificat sur chaque machine virtuelle de tâche du pool. Le service Batch installe les certificats appropriés au démarrage de la machine virtuelle de tâche, avant de lancer toutes les tâches, y compris les tâches de démarrage et celles du gestionnaire de travaux.

## <a name="scheduling"></a>Priorité de la planification

Lorsque vous créez un élément de travail, vous pouvez lui attribuer une priorité. Chaque travail de l'élément de travail est créé avec cette priorité. Le service Batch utilise les valeurs de priorité du travail pour déterminer l'ordre de planification des travaux dans un compte. Les valeurs de priorité peuvent être comprises entre -1000 et 1000, -1000 étant la priorité la plus basse et 1000 la plus élevée. Vous pouvez mettre à jour la priorité d'un travail à l'aide de l'opération UpdateJob.

Dans le même compte, les tâches à la priorité plus élevée ont la priorité en termes de planification sur les tâches avec une priorité plus faible. Un travail avec une priorité plus élevée dans un compte n'a pas de priorité de planification sur un autre travail avec une valeur de priorité inférieure dans un autre compte.

La planification des travaux dans différents pools est indépendante. Entre les différents pools, il n'est pas systématique qu'un travail avec une priorité plus élevée soit planifié en premier, si son pool n'a pas suffisamment de machines virtuelles de tâche inactives. Dans le même pool, les travaux avec le même niveau de priorité ont autant de chance d'être planifiés.

## <a name="environment"></a>Paramètres d'environnement des tâches

Vous pouvez spécifier des paramètres d'environnement qui peuvent être utilisés dans le contexte d'une tâche. Les paramètres d'environnement d'une tâche de démarrage et de tâches en cours d'exécution sous un travail sont définis en ajoutant une section XML dans le corps de demande des opérations Ajouter une tâche ou Mettre à jour une tâche.

L'exemple suivant montre la définition d'un paramètre d'environnement :

Pour chaque tâche planifiée sous un travail, un ensemble spécifique de variables d'environnement est défini par le service Batch. Le tableau suivant répertorie les variables d'environnement définies par le service Batch pour toutes les tâches.

<table>
  <tr>
   <th>Nom de variable d'environnement
   </th>
   <th>
   Description
   </th>
  </tr>
 <tr>
  <td>
  WATASK_ ACCOUNT_NAME
  </td>
  <td>
  Nom du compte auquel appartient la tâche.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_WORKITEM_NAME
  </td>
  <td >Nom de l'élément de travail auquel appartient la tâche.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_JOB_NAME
  </td>
  <td >Nom du travail auquel appartient la tâche.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TASK_NAME
  </td>
  <td >Nom de la tâche en cours.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_POOL_NAME
  </td>
  <td >Nom du pool sur lequel la tâche s'exécute.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TVM_NAME
  </td>
  <td >Nom de la machine virtuelle de tâche sur laquelle la tâche s'exécute.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TVM_ROOT_DIR
  </td>
  <td >Chemin d'accès complet du répertoire racine de la machine virtuelle de tâche.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_PORTRANGE_LOW WATASK_PORTRANGE_HIGH
</td>
<td>
  Plage de ports (incluse) que la tâche peut utiliser pour la communication intra-pool lorsque cette dernière est activée.
</td>
</table>

**Remarque :**

Vous ne pouvez pas remplacer ces variables définies par le système.

Vous pouvez récupérer la valeur des paramètres d'environnement à l'aide de l'opération Obtenir une tâche.

## <a name="errorhandling"></a>Gestion des erreurs

###Gestion des erreurs de tâche
Les échecs de tâche peuvent être classés dans les catégories suivantes :

- Échecs de planification :
	- Si des fichiers sont spécifiés pour la tâche, la copie d'un ou plusieurs des fichiers peut échouer. Les fichiers ont peut-être été déplacés, le compte de stockage n'est peut-être plus disponible, etc.
	- Une « erreur de planification » est définie pour la tâche dans ce cas.
- Échecs d’application :
	- Le processus de la tâche spécifié par la ligne de commande peut également échouer. Le processus est considéré comme ayant échoué lorsqu'un code de sortie différent de zéro est renvoyé.
	- Pour les échecs d'application, il est possible de configurer Batch pour relancer automatiquement la tâche autant de fois que l’utilisateur l’aura spécifié. 
- Échecs de contrainte :
	- Une contrainte peut être spécifiée pour fixer le nombre maximal de tentatives d'exécution d'un travail ou d’une tâche. Celle-ci peut être utile pour mettre fin à une tâche qui a été suspendue.
	- Lorsque ce nombre maximal de tentatives est atteint, la tâche est marquée comme terminée, mais le code de sortie sera marqué comme `0xC000013A` et le champ schedulingError sera marqué comme `{ category:“ServerError”, code=“TaskEnded”}`.

###Débogage des échecs d'application

Une application peut produire des diagnostics qui peuvent être utilisés pour résoudre les problèmes. Les applications écriront souvent des informations dans les fichiers stdout et stderr ou fourniront leur résultat dans des fichiers personnalisés. Une API est, dans ce cas, fournie pour obtenir des fichiers, en spécifiant la tâche ou la machine virtuelle.

Il est également possible de se connecter aux machines virtuelles du pool. Une API renvoie le fichier RDP d’une machine virtuelle, qui peut ensuite être utilisée pour se connecter à la machine virtuelle.

###Gestion des échecs et des problèmes de tâches

Des tâches peuvent échouer ou être interrompues pour plusieurs raisons. L'application de la tâche peut également échouer, la machine virtuelle sur laquelle la tâche s’exécute peut alors être relancée ou supprimée par un redimensionnement du pool d’après la stratégie de désallocation qui a été définie pour supprimer immédiatement la machine virtuelle sans attendre que la tâche se termine. Dans tous les cas, la tâche peut être automatiquement remise en file d'attente par Batch et exécutée sur une autre machine virtuelle.

Un problème intermittent peut également provoquer la suspension d’une tâche ou ralentir son exécution. La durée maximale pendant laquelle la tâche est autorisée à s’exécuter peut être définie par l’utilisateur, mais lorsque celle-ci est dépassée, Batch interrompt l'application de la tâche. La remise en file d’attente automatique n'est pas possible dans ce cas, mais ce dernier peut être détecté par le client qui peut, quant à lui, soumettre une nouvelle tâche.

###Gestion des « mauvaises » machines virtuelles

Chaque machine virtuelle du pool se voit attribuer un nom unique et la machine virtuelle sur laquelle s'exécute une tâche est incluse dans les métadonnées de la tâche. Si une machine virtuelle provoque l'échec des tâches pour une raison quelconque, ceci peut être déterminé par le client et la machine virtuelle suspecte est supprimée du pool. Si une tâche a été exécutée sur la machine virtuelle qui a été supprimée, celle-ci est automatiquement remise en file d'attente et exécutée sur une autre machine virtuelle.


<!--Image references-->
[1]: ./media/batch-api-basics/batch-api-basics-01.png

[vue d'ensemble d’Azure Batch]: batch-technical-overview.md

<!---HONumber=July15_HO4-->