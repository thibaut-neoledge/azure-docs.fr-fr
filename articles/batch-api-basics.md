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

## <a name="resource"></a> Ressources du service de traitement par lots

Lorsque vous utilisez le service Batch, vous profitez des ressources suivantes :

- [Compte](#account)

- [Machine virtuelle](#taskvm)

- [Pool](#pool)

- [Élément de travail](#workitem)

- [Travail](#job)

- [Tâche](#task)

	- [Démarrer la tâche](#starttask)
	
	- [ManagerTask de travail](#jobmanagertask)

### <a name="account"></a>Compte

Un compte Batch est une entité identifiée de façon unique au sein du service Batch. Tout le traitement s'effectue via un compte Batch. Lorsque vous effectuez des opérations avec le service Batch, vous avez besoin du nom et de la clé du compte. Pour créer un compte de traitement par lots, reportez-vous à la section compte de traitement de [vue d'ensemble du lot Azure][].


### <a name="taskvm"></a>Machine virtuelle

Une machine virtuelle de tâche est une machine virtuelle Azure dédiée à une charge de travail spécifique pour votre application. La taille d'une machine virtuelle de tâche détermine le nombre de cœurs du processeur, la capacité de mémoire et la taille du système de fichiers local qui lui est allouée. Une TVM peut être une machine virtuelle extra large, grande ou petite comme décrit dans [Machine virtuelle et les tailles de Service Cloud pour Azure](http://msdn.microsoft.com/library/dn197896.aspx).

Les types de programme qu'une machine virtuelle de tâche peut exécuter incluent les fichiers exécutables (.exe), les fichiers de commandes (.cmd et .bat) et les fichiers de script. Une machine virtuelle de tâche a également les attributs suivants :

- Dossiers de système de fichiers qui sont spécifiques aux tâches et partagées. Un variables d'environnement et de la structure de dossier sont créés sur chaque ordinateur virtuel du pool. La structure de dossiers suivante est créée avec un dossier « partagé » pour les applications et les données partagées entre les tâches, plus un dossier pour chaque tâche.

![][1]

- fichiers stdout.txt et stderr.txt écrits dans le dossier spécifique d'une tâche ;

- variables d'environnement pour le traitement ;

- paramètres de pare-feu configurés pour le contrôle de l'accès.

>Accès aux machines virtuelles
>
>Si l'accès à une machine virtuelle est requis pour le débogage par exemple, le fichier RDP peut être obtenu, qui peut ensuite être utilisé pour accéder à la machine virtuelle via le Bureau à distance.


### <a name="pool"></a>Pool

Un pool est une collection de machines virtuelles de tâche sur lesquelles votre application s'exécute. Vous pouvez créer le pool ou laisser le service Batch le créer automatiquement lorsque vous spécifiez le travail à accomplir. Vous pouvez créer et gérer un pool qui répond aux besoins de votre application. Un pool peut être utilisé uniquement par le compte Batch dans lequel il a été créé. Un compte Batch peut avoir plusieurs pools.

Azure pools par lots basées sur la plate-forme principale de calcul Azure ; Pools de lot fournissent allocation à grande échelle, application et installation de données, le déplacement des données, analyse du fonctionnement et évolution flexible de l'ordinateur virtuel.

Chaque machine virtuelle de tâche ajoutée à un pool se voit attribuer un nom unique et l'adresse IP associée. Lorsqu'une machine virtuelle de tâche est supprimée d'un pool, elle perd les modifications apportées au système d'exploitation, tous ses fichiers locaux, son nom et son adresse IP. Quand une machine virtuelle de tâche quitte un pool, sa durée de vie est terminée.

Vous pouvez configurer un pool pour permettre la communication entre les machines virtuelles de tâche qu'il contient. Si la communication intra-pool est demandée pour un pool, le service Batch autorise les ports supérieurs à 1100 sur chaque machine virtuelle de tâche du pool. Chaque machine virtuelle de tâche du pool est configurée pour autoriser ou restreindre les connexions entrantes à cette plage de ports et uniquement à partir d'autres machines virtuelles de tâche du pool. Si votre application ne nécessite pas la communication entre des machines virtuelles de tâche, le service Batch peut éventuellement allouer un grand nombre de machines virtuelles de tâche de différents centres de données ou clusters au pool pour permettre un traitement plus parallèle.

Lorsque vous créez un pool, vous pouvez spécifier les attributs suivants :

- Le **taille des machines virtuelles** dans le pool.
	- La taille appropriée de la machine virtuelle doit être choisi, selon les caractéristiques et les spécifications de l'application ou les applications qui vont être utilisées sur l'ordinateur virtuel. Normalement la taille de la machine virtuelle est récupérée en supposant qu'une tâche s'exécute à la fois sur l'ordinateur virtuel ; par exemple, si l'application est multithread et quelle mémoire que requise détermine la taille de machine virtuelle plus appropriée et rentable. Il est possible d'avoir plusieurs tâches affectées et plusieurs instances de l'application en cours d'exécution en parallèle, auquel cas une machine virtuelle supérieure est généralement choisi – voir ci-dessous sur « nombre maximum de tâches par ordinateur virtuel ». 
	- Tous les la machine virtuelle dans un pool doit être de la même taille. Si différentes applications doivent être exécutées avec la même configuration système requise et/ou avec charge différents pools distincts doivent être créés.
	- Toutes les tailles de machine virtuelle de service cloud peuvent être configurés pour un pool, à l'exception de A0.

- La famille de systèmes d'exploitation et la version qui s'exécute sur les machines virtuelles.
	- Comme avec les rôles de travail, la famille de systèmes d'exploitation et la Version du système d'exploitation peuvent être configuré.
	- La famille de systèmes d'exploitation détermine également les versions de .NET sont installées avec le système d'exploitation.
	- Comme avec les rôles de travail, pour la Version du système d'exploitation qu'il est recommandé que « * » est utilisé afin que sont la machine virtuelle automatiquement mis à niveau et il n'a aucune tâche nécessaire pour répondre aux nouvelles versions. Cas d'usage principal pour récupérer une version de système d'exploitation spécifique est d'assurer le maintien de la compatibilité des applications, en permettant aux tests à réaliser avant d'autoriser la version mise à jour de compatibilité descendante. Une fois validée, la version du système d'exploitation pour le pool peut être mis à jour et la nouvelle image du système d'exploitation installé – toutes les tâches en cours d'exécution seront interrompue et nouveau en file d'attente.

- Nombre d'ordinateurs virtuels qui doivent être disponibles pour le pool cible.

- stratégie de mise à l'échelle du pool ; Outre le nombre de machines virtuelles, vous pouvez également spécifier une formule de mise à l'échelle pour chaque pool. Service de traitement par lots s'exécute la formule pour ajuster le nombre de machines virtuelles basées sur les statistiques de pool et élément de travail.

- Configuration de la planification
	- La configuration par défaut est une tâche à exécuter sur un pool de machines virtuelles à tout moment, mais il existe des scénarios où il est avantageux d'avoir plusieurs tâches exécuter en même temps sur un ordinateur virtuel. Un exemple consiste à augmenter l'utilisation des ordinateurs virtuels si une application doit attendre les e/s ; avoir plusieurs applications à exécuter augmentera l'utilisation du processeur. Un autre exemple consiste à réduire le nombre de l'ordinateur virtuel dans la liste. Cela risque de réduire la quantité de copies de données requis pour les jeux de données de référence de grande taille. Si un A1 serait la taille correcte de l'application, puis un A4 peut être choisi et la configuration définie pour exécuter jusqu'à 8 tâches simultanément, chaque utilisation d'un cœur.
	- La configuration « max tâches par ordinateur virtuel » détermine le nombre maximal de tâches qui peuvent être exécutés en parallèle.
	- Un « stratégie de remplissage » peut également être spécifié qui détermine si le lot remplit première la machine virtuelle ou si les tâches sont répartis sur tous les VM.
 
- L'état de communication des ordinateurs virtuels dans le pool.
 	- Dans une grande partie des scénarios tâches fonctionnent indépendamment et n'avez pas besoin de communiquer avec d'autres tâches, mais il existe certaines applications où les tâches communiqueront (par exemple, les applications à l'aide de MPI).
	- Il existe de configuration qui contrôle si la machine virtuelle sera en mesure de communiquer, qui est utilisée pour configurer le placement d'infrastructure et impacts de réseau sous-jacente de la machine virtuelle.

- tâche de démarrage des machines virtuelles de tâche du pool.

Lorsque vous créez un pool, vous pouvez spécifier le compte de stockage auquel le pool doit être associé. Le service Batch alloue au compte de stockage spécifié des machines virtuelles de tâche provenant de centres de données dotés des meilleures capacités en matière de bande passante et de connectivité réseau. Les charges de travail bénéficient ainsi d'un accès plus efficace aux données.

### <a name="workitem"></a>Élément de travail

Un élément de travail spécifie comment le calcul est effectué sur les machines virtuelles de tâche d'un pool.

- Un élément de travail peut avoir un ou plusieurs travaux lui est associé. Une planification facultative peut être spécifiée pour un élément de travail dans ce cas, une tâche est créée pour chaque occurrence de la planification. Si aucune planification n'est spécifiée, pour le travail à la demande, un travail est créé immédiatement.
- L'élément de travail spécifie le pool sur lequel le travail sera exécuté. Le pool peut être un existant, déjà créé un pool qui est utilisé par nombreux éléments de travail, mais un pool peut également être créé pour chaque tâche associée à l'élément de travail ou pour toutes les tâches associées à l'élément de travail.
- Une priorité facultative peut être spécifiée. Lorsqu'un élément de travail est envoyé avec une priorité plus élevée que les autres éléments de travail en cours d'exécution, les tâches priorité plus élevées insérés dans la file d'attente avant les tâches d'élément de travail priorité inférieures. Tâches de faible priorité sont déjà en cours d'exécution ne sera pas devancées.
- Contraintes peuvent être spécifiées pour le travail associé ou les travaux qui sera appliqué.
	- Une fois wallclock maximale peut être définie pour les tâches. Si les travaux s'exécute plus longtemps que la durée de wallclock maximale spécifiée, le travail et toutes les tâches associées seront terminées.
	- Lot Azure peut détecter les tâches qui échouent et les tâches de nouvelle tentative. Nombre maximal de tentatives de tâche peut être spécifié comme une contrainte, notamment spécifiant qu'une tâche est toujours retentée ou jamais une nouvelle tentative. Nouvelle tentative de tâches d'un signifie que la tâche est de nouveau en file d'attente et s'exécute de nouveau.
- Tâches à exécuter pour l'élément de travail peuvent être spécifiés par le client de la même manière que l'élément de travail a été créé, mais une tâche Job Manager peut également être spécifiée. Une tâche de gestionnaire utilise l'API de lot et contient le code pour créer les tâches requises pour un travail avec la tâche en cours d'exécution sur un de du pool d'ordinateurs virtuels. Les tâches de gestionnaire est gérée par lot : il est en file d'attente dès que le travail est créé et est redémarré si elle échoue pour une raison quelconque. Un gestionnaire de travaux est requis pour les éléments de travail avec une planification associée comme c'est le seul moyen de définir les tâches avant l'instanciation de travail.

### <a name="job"></a>Travail

Un travail est une instance en cours d'exécution d'un élément de travail et se compose d'une collection de tâches. Le service Batch instancie un travail en fonction de la configuration de l'élément de travail. Le travail utilise les machines virtuelles de tâche du pool associé à l'élément de travail.

### <a name="task"></a>Tâche

Une tâche est une unité de calcul associée à un travail et exécutée sur une machine virtuelle de tâche. Les tâches sont affectées à une machine virtuelle pour l'exécution ou en file d'attente jusqu'à ce qu'une machine virtuelle devient disponible. Elle utilise les ressources suivantes :

- Programme spécifié dans l'élément de travail.

- Fichiers de ressources qui contiennent les données à traiter. Ces fichiers sont automatiquement copiés sur la machine virtuelle de tâche depuis le stockage d'objets blob. Pour plus d'informations, consultez la section Fichiers et répertoires.

- Paramètres d'environnement requis par le programme. Pour plus d'informations, consultez la section Paramètres d'environnement des tâches.

- Contraintes dans lesquelles le calcul doit se produire. Par exemple, la durée maximale pendant laquelle la tâche est autorisée à s'exécuter, le nombre maximal de nouvelles tentatives en cas d'échec de la tâche, ainsi que la durée maximale pendant laquelle les fichiers du répertoire de travail sont conservés.

Outre les tâches que vous pouvez définir pour effectuer des calculs sur une machine virtuelle de tâche, vous pouvez utiliser les tâches spéciales suivantes fournies par le service Batch :

- [Démarrer la tâche](#starttask)

- [Le gestionnaire tâche](#jobmanagertask)

#### <a name="starttask"></a>Démarrer la tâche

Vous pouvez configurer le système d'exploitation des ordinateurs virtuels dans un pool en associant une tâche de démarrage du pool. Une tâche de démarrage peut effectuer certaines actions, dont l'installation du logiciel et le démarrage des processus en arrière-plan. Cette tâche s'exécute chaque fois qu'une machine virtuelle commence pour tant qu'il reste dans le pool.

Comme avec n'importe quelle tâche de traitement par lots, une liste de fichiers dans le stockage Azure peut spécifiée en plus d'une ligne de commande est exécutée par lots. Lot Azure sera d'abord copier les fichiers du stockage Azure, puis exécutez la ligne de commande. Pour une tâche de démarrage pool, la liste des fichiers contient généralement les fichiers des applications ou le package, mais elle pourrait également inclure des données de référence qui seront utilisées par toutes les tâches en cours d'exécution sur le pool VM. La ligne de commande peut exécuter de script PowerShell ou de robocopy, par exemple, pour copier les fichiers d'application dans le dossier « partagé » ; Il peut également exécuter un MSI.

Normalement, il est souhaitable pour attendre la tâche de démarrage pour s'exécuter, envisagez la machine virtuelle prête à être affectés à des tâches du lot, mais il est configurable.

Si une tâche de démarrage échoue pour un pool d'ordinateurs virtuels, puis l'état de la machine virtuelle est mise à jour pour refléter l'échec et l'ordinateur virtuel ne sera pas disponible pour les tâches à affecter. Une tâche de démarrage peut échouer si un problème de copie des fichiers spécifiés pour la tâche de démarrage ou le processus de tâche de démarrage renvoie différente de zéro.

Le fait que toutes les informations nécessaires pour configurer la machine virtuelle et installer des applications sont déclarées signifie que l'augmentation du nombre de machines virtuelles dans un pool est aussi simple en spécifiant le nombre requis de nouveau ; Traitement par lots a toutes les informations nécessaires pour configurer la machine virtuelle et préparez-vous à accepter les tâches.

Une tâche de démarrage est définie en ajoutant une section JSON pour le corps de la demande pour l'opération Ajouter un Pool. L'exemple suivant montre une définition de base d'une tâche de démarrage :

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

Une interface c# ressemble à ceci :

	ICloudPool pool = pm.CreatePool(poolName, targetDedicated: 3, vmSize: "small", osFamily: "3");
	pool.StartTask = new StartTask();
	pool.StartTask.CommandLine = "mypoolsetup.exe";
	pool.StartTask.ResourceFiles = new List<IResourceFile>();
	pool.StartTask.ResourceFiles.Add(new ResourceFile("http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d", "mypoolsetup.exe"));
	pool.Commit();


#### <a name="jobmanagertask"></a>Le gestionnaire tâche

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




## <a name="workflow"></a>Flux de travail du service de traitement par lots

Vous avez besoin d'un compte Batch pour utiliser le service Batch et vous utilisez plusieurs ressources du service pour planifier le calcul. Utilisez le flux de travail de base suivant lorsque vous créez un scénario de calcul distribué avec le service Batch :

1. Téléchargez les fichiers que vous souhaitez utiliser dans votre scénario à un compte de stockage Windows Azure. Ils doivent être situés dans le compte de stockage afin que le service Batch puisse y accéder. Ce dernier les charge sur une machine virtuelle de tâche quand la tâche s'exécute.

2. Téléchargez les fichiers binaires dépendants au compte de stockage. notamment le programme exécuté par la tâche et les assemblys dépendants. Ces fichiers doivent également être accessibles à partir du stockage et sont chargés dans la machine virtuelle de tâche.

3 créer un pool de TVMs. Vous pouvez attribuer la taille de la machine virtuelle de tâche à utiliser lors de la création du pool. Quand une tâche s'exécute, elle reçoit une machine virtuelle de tâche à partir de ce pool.

4 créer un élément de travail. Un travail est créé automatiquement lorsque vous créez un élément de travail. Un élément de travail vous permet de gérer un travail constitué de tâches.

5 ajouter des tâches à l'élément de travail. Chaque tâche utilise le programme téléchargé pour traiter les informations à partir d'un fichier téléchargé.

6 contrôler les résultats de la sortie.

## <a name="files"></a>Fichiers et répertoires

Chaque tâche possède un répertoire de travail dans lequel elle crée zéro ou plusieurs répertoires et fichiers pour stocker le programme qu'elle exécute, les données qu'elle traite et la sortie du traitement qu'elle effectue. Ces fichiers et répertoires sont ensuite disponibles pour une utilisation par d'autres tâches pendant l'exécution d'un travail. L'ensemble des tâches, fichiers et répertoires d'une machine virtuelle de tâche sont la propriété d'un seul compte d'utilisateur.

Le service Batch expose une partie du système de fichiers sur une machine virtuelle de tâche en tant que répertoire racine. Le répertoire racine de la TVM est disponible pour une tâche via la variable d'environnement WATASK_TVM_ROOT_DIR. Pour plus d'informations sur l'utilisation de variables d'environnement, consultez la section Paramètres d'environnement des tâches.

Le répertoire racine contient les sous-répertoires suivants :

- **Tâches** – cet emplacement est où tous les fichiers sont stockés qui appartiennent à des tâches qui s'exécutent sur la TVM. Pour chaque tâche, le service de traitement par lots crée un répertoire de travail avec le chemin d'accès unique sous la forme % WATASK_TVM_ROOT_DIR%/tasks/workitemName/jobName/taskName/. Ce répertoire fournit un accès en lecture/écriture à la tâche. La tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire, et ce dernier est conservé en fonction de la contrainte RetentionTime spécifiée pour la tâche.

- **Shared** – cet emplacement est un répertoire partagé pour toutes les tâches sous le compte. Sur la TVM, le répertoire partagé est % WATASK_TVM_ROOT_DIR%/shared. Ce répertoire fournit un accès en lecture/écriture à la tâche. La tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire.

- **Démarrer** – cet emplacement est utilisé par une tâche de démarrage comme répertoire de travail. Tous les fichiers téléchargés par le service Batch pour lancer la tâche de démarrage sont également stockés dans ce répertoire. Sur la TVM, le répertoire de démarrage est % WATASK_TVM_ROOT_DIR%/start. La tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire, et ce dernier est utilisable par les tâches de démarrage pour configurer le système d'exploitation.

Lorsqu'une machine virtuelle de tâche est supprimée du pool, tous les fichiers stockés dans celle-ci sont supprimés.

## <a name="lifetime"></a>Pool et la durée de vie de machine virtuelle

Une décision de conception fondamental est lorsque les pools sont créés et la durée pendant laquelle l'ordinateur virtuel restent disponibles.

Une approche consiste à un pool peut être créé pour chaque tâche lorsque le travail est envoyé et la machine virtuelle supprimées lorsque des tâches de fin d'exécution. Ceci permettra de maximiser l'utilisation la machine virtuelle est allouée uniquement lorsque cela est absolument nécessaire et arrêt dès qu'elles deviennent inactives. Cela signifie que la tâche doit attendre pour la machine virtuelle à allouer, même s'il est important de noter que les tâches seront planifiées pour l'ordinateur virtuel dès qu'elles sont disponibles individuellement, alloué et cette tâche est terminée ; par exemple, lot n'attend pas jusqu'à ce que tous les ordinateurs virtuels dans un pool sont disponibles comme qui entraînerait une faible utilisation.

Si des travaux de démarrer son exécution immédiatement est la priorité un pool doit être créé et de machine virtuelle disponible avant l'envoi de la tâche. Les tâches peuvent démarrer immédiatement, mais l'ordinateur virtuel peut être inactif en attendant les tâches de projet, en fonction de la charge.

Un commun motif pour lorsqu'il existe une quantité variable de charge en cours doit disposer d'un pool à laquelle plusieurs travaux sont envoyés, mais l'échelle vers le haut ou vers le bas le nombre de machines virtuelles en fonction de la charge ; Cela peut être effectué de manière réactive ou de manière proactive si la charge peut être prédite.

## <a name="scaling"></a>Mise à l'échelle des applications

Votre application peut facilement être mise à l'échelle (dans un sens ou dans l'autre) automatiquement pour accueillir le calcul dont vous avez besoin. Vous pouvez ajuster dynamiquement le nombre de machines virtuelles de tâche d'un pool en fonction de la charge de travail actuelle et des statistiques sur l'utilisation des ressources. Vous pouvez également optimiser le coût total de l'exécution de votre application en la configurant à l'échelle automatiquement. Vous pouvez spécifier les paramètres de mise à l'échelle d'un pool lorsqu'il est créé, et mettre à jour la configuration à tout moment.

Une diminution du nombre de machines virtuelles, peuvent être des tâches en cours d'exécution sur l'ordinateur virtuel qui doivent être pris en compte. Une stratégie de désallocation est spécifiée qui détermine si les tâches en cours d'exécution sont arrêtés pour supprimer l'ordinateur virtuel immédiatement ou si les tâches sont autorisées à se terminer avant que la machine virtuelle est supprimée. Définissant le nombre cible de la machine virtuelle à zéro à la fin d'un travail, mais permettant d'exécuter les tâches à terminer, optimiser le taux d'utilisation.

Spécifiez la mise à l'échelle automatique d'une application à l'aide d'un jeu de formules de mise à l'échelle. Les formules peuvent servir à déterminer le nombre de machines virtuelles de tâche qui se trouvent dans le pool pendant le prochain intervalle de mise à l'échelle. Par exemple, vous devez envoyer un grand nombre de tâches qui doivent être planifiées sur un pool. Vous pouvez attribuer une formule de mise à l'échelle au pool qui spécifie la taille du pool selon le nombre actuel de tâches en attente et la vitesse d'exécution des tâches. Le service Batch évalue la formule régulièrement et redimensionne le pool en fonction de la charge de travail.

Une formule peut être basée sur les mesures suivantes :

- **Métriques temporelles** – selon les statistiques collectées toutes les cinq minutes dans le nombre d'heures spécifié.

- **Métriques de ressource** – basée sur l'utilisation de l'UC, l'utilisation de la bande passante, utilisation de la mémoire et nombre TVMs.

- **Tâches métriques** – basé sur l'état des tâches, telles que les actifs, en attente et terminées.

Pour plus d'informations sur la mise à l'échelle automatique d'une application, consultez la section Configuration de la mise à l'échelle automatique de machines virtuelles de tâche.

>Supprimer l'ordinateur virtuel
>
>Il n'est pas souvent nécessaire, mais il est possible de spécifier des ordinateurs virtuels pour supprimer un pool de. S'il existe un ordinateur virtuel qui semble être moins fiable il pourrait être supprimé, par exemple.

## <a name="cert"></a>Certificats pour les applications

En général, vous devez utiliser des certificats lorsque vous chiffrez des informations confidentielles. Des certificats peuvent être installés sur les machines virtuelles de tâche. Les secrets chiffrés sont transmis aux tâches dans les paramètres de ligne de commande ou incorporés dans l'une des ressources et les certificats installés peuvent être utilisés pour les déchiffrer. Un exemple d'informations secrètes est la clé d'un compte de stockage.

Vous utilisez l'opération Ajouter un certificat pour ajouter un certificat à un compte Batch. Vous pouvez ensuite associer le certificat à un pool existant ou nouveau. Lorsqu'un certificat est associé à un pool, le service Batch installe le certificat sur chaque machine virtuelle de tâche du pool. Le service Batch installe les certificats appropriés au démarrage de la machine virtuelle de tâche, avant de lancer toutes les tâches, y compris les tâches de démarrage et celles du gestionnaire de travaux.

## <a name="scheduling"></a>La priorité de planification

Lorsque vous créez un élément de travail, vous pouvez lui attribuer une priorité. Chaque travail de l'élément de travail est créé avec cette priorité. Le service Batch utilise les valeurs de priorité du travail pour déterminer l'ordre de planification des travaux dans un compte. Les valeurs de priorité peuvent être comprises entre -1000 et 1000, -1000 étant la priorité la plus basse et 1000 la plus élevée. Vous pouvez mettre à jour la priorité d'un travail à l'aide de l'opération UpdateJob.

Dans le même compte, les tâches à la priorité plus élevée ont la priorité en termes de planification sur les tâches avec une priorité plus faible. Un travail avec une priorité plus élevée dans un compte n'a pas de priorité de planification sur un autre travail avec une valeur de priorité inférieure dans un autre compte.

La planification des travaux dans différents pools est indépendante. Entre les différents pools, il n'est pas systématique qu'un travail avec une priorité plus élevée soit planifié en premier, si son pool n'a pas suffisamment de machines virtuelles de tâche inactives. Dans le même pool, les travaux avec le même niveau de priorité ont autant de chance d'être planifiés.

## <a name="environment"></a>Paramètres d'environnement pour les tâches

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
Échecs de tâches se répartissent dans les catégories suivantes :

- Échecs de planification :
	- Si les fichiers sont spécifiés pour la tâche, la copie d'un ou plusieurs des fichiers peut échouer. Il est possible que les fichiers ont déplacé, le compte de stockage n'est plus disponible, etc..
	- Une planification « erreur » est définie pour la tâche dans ce cas.
- Échecs d'application :
	- Le processus de tâche spécifié par la ligne de commande peut également échouer. Le processus est considéré comme ayant échoué lorsqu'un code de sortie différent de zéro est retourné.
	- Pour les défaillances d'application, il est possible de configurer le traitement par lots pour réessayer d'effectuer automatiquement la tâche jusqu'à un nombre spécifié de fois. 
- Échecs de contrainte :
	- Une contrainte peut être spécifiée pour la quantité maximale d'exécution d'un travail ou une tâche pour. Le peut être utile pour mettre fin à une tâche qui a été suspendu.
	- Lorsque le délai a été dépassé alors la tâche est marquée comme terminée, mais la volonté de code de sortie est marqué comme `0xC000013A` et champ schedulingError est marqué comme `{ category:“ServerError”, code=“TaskEnded”}`.

###Débogage des échecs d'Application

Une application peut produire des diagnostics qui peut être utilisé pour résoudre les problèmes. Souvent les applications écrit les informations dans stdout et stderr fichiers ou la sortie vers les fichiers personnalisés. Dans ce cas une API est fournie pour obtenir des fichiers, en spécifiant la tâche ou la machine virtuelle.

Il est également possible de connecter du pool d'ordinateurs virtuels. Une API renvoie le fichier RDP pour une machine virtuelle, qui peut ensuite être utilisée pour se connecter à la machine virtuelle.

###Des échecs de tâches et des problèmes de restauration

Tâches peuvent échouer ou être interrompues pour plusieurs raisons. L'application de la tâche elle-même peut échouer, obtient du redémarrage de l'ordinateur virtuel sur lequel la tâche est en cours d'exécution ou l'ordinateur virtuel est supprimé par un redimensionnement pool avec la stratégie désallocation pour supprimer l'ordinateur virtuel immédiatement sans attendre la fin de la tâche. Dans tous les cas, la tâche peut être automatiquement ré-file d'attente par lot et s'exécuter sur une autre machine virtuelle.

Il est également possible qu'un problème intermittent provoquer une tâche de blocage ou est trop longue à exécuter. La durée d'exécution maximale peut être définie pour une tâche et si lot dépassé interrompt l'application de la tâche. Actuellement, nouveau queuing automatique n'est pas possible dans ce cas, mais le cas peuvent être détecté par le client qui peut envoyer une nouvelle tâche.

###Restauration de l'ordinateur virtuel « Mauvais »

Chaque ordinateur virtuel dans un pool est donné un nom unique et l'ordinateur virtuel sur lequel s'exécute une tâche inclus dans les métadonnées de tâche. Dans le cas où il existe un ordinateur virtuel qui provoque l'échec des tâches pour une raison quelconque, cela peut être déterminé par le client et le suspect que machine virtuelle est supprimée du pool. Si une tâche a été exécuté sur l'ordinateur virtuel qui a été supprimé, puis il est automatiquement ré-en file d'attente et exécutée sur une autre machine virtuelle.


<!--Image references-->
[1]: ./media/batch-api-basics/batch-api-basics-01.png

[vue d'ensemble du lot Azure]: batch-technical-overview.md

<!---HONumber=GIT-SubDir-->