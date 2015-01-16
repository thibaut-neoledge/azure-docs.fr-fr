
<properties title="API basics for Azure Batch" pageTitle="Concepts de base concernant les API dans Azure Batch" description="Concepts permettant de présenter les API et les services par lots d'Azure Batch aux développeurs" metaKeywords="" services="batch" solutions="" documentationCenter=".NET" authors="yidingz, karran.batta" videoId="" scriptId="" manager="timlt" />

<tags ms.service="batch" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="big-compute" ms.date="10/27/2014" ms.author="yidingz, karran.batta" />


<!--The next line, with one pound sign at the beginning, is the page title--> 
# Concepts de base concernant les API dans Azure Batch

Le service Azure Batch fournit une infrastructure de planification des travaux pour le calcul évolutif et distribué. Il gère un ensemble de machines virtuelles qui sont situées dans différents centres de données et clusters dans Azure. Le service Batch effectue un calcul distribué en exécutant un ou plusieurs programmes de manière planifiée ou à la demande, pour une exécution à un moment donné sur une collection spécifique de ces machines virtuelles. Il gère ces machines virtuelles pour exécuter les tâches de calcul selon les besoins en ressources, les spécifications et les contraintes que vous fournissez.

En utilisant le service Batch, vous pouvez éliminer la nécessité d'écrire du code pour la mise en file d'attente, la planification, l'allocation et la gestion des ressources de calcul. Cela vous permet de vous concentrer sur l'application spécifique, sans vous soucier de la complexité de la gestion des ressources et de la planification des travaux dans la plateforme sous-jacente. Cela permet également au service Batch d'optimiser l'emplacement de ces travaux, ainsi que leur accès aux données qu'ils ont besoin de traiter.

Voici quelques scénarios que vous pouvez activer en utilisant le service Batch :

- traitement parallèle qui effectue beaucoup de calculs ;

- nettoyage quotidien des fichiers ;

- traitement par lots.

Pour en savoir plus sur le service Batch, consultez les sections suivantes :

- [Ressources du service Batch](#resource)

- [Flux de travail du service Batch](#workflow)

- [Fichiers et répertoires](#file)

- [Mise à l'échelle des applications](#scaling)

- [Certificats pour les applications](#cert)

- [Priorité de la planification](#scheduling)

- [Paramètres d'environnement des tâches](#environment)

## <a name="resource"></a> Ressources du service Batch

Lorsque vous utilisez le service Batch, vous profitez des ressources suivantes :

- [Compte](#account)

- [Machine virtuelle de tâche](#taskvm)

- [Pool](#pool)

- [Élément de travail](#workitem)

- [Job](#job)

- [Tâche](#task)

### <a name="account"></a>Compte

Un compte Batch est une entité identifiée de façon unique au sein du service Batch. Tout le traitement s'effectue via un compte Batch. Lorsque vous effectuez des opérations avec le service Batch, vous avez besoin du nom et de la clé du compte. Actuellement, vous devez contacter l'équipe Azure Batch pour créer un compte. Une fois le compte créé, une clé vous est fournie.

### <a name="taskvm"></a>Machine virtuelle de tâche

Une machine virtuelle de tâche est une machine virtuelle Azure dédiée à une charge de travail spécifique pour votre application. La taille d'une machine virtuelle de tâche détermine le nombre de cœurs du processeur, la capacité de mémoire et la taille du système de fichiers local qui lui est allouée. Une machine virtuelle de tâche peut être une machine virtuelle de petite, grande ou très grande taille, comme décrit dans [Tailles de machines virtuelles et services cloud pour Windows Azure](http://msdn.microsoft.com/fr-fr/library/dn197896.aspx).

Les types de programme qu'une machine virtuelle de tâche peut exécuter incluent les fichiers exécutables (.exe), les fichiers de commandes (.cmd et .bat) et les fichiers de script. Une machine virtuelle de tâche a également les attributs suivants :

- dossiers de systèmes de fichiers spécifiques d'une tâche et partagés ;

- fichiers stdout.txt et stderr.txt écrits dans le dossier spécifique d'une tâche ;

- variables d'environnement pour le traitement ;

- paramètres de pare-feu configurés pour le contrôle de l'accès.

### <a name="pool"></a>Pool

Un pool est une collection de machines virtuelles de tâche sur lesquelles votre application s'exécute. Vous pouvez créer le pool ou laisser le service Batch le créer automatiquement lorsque vous spécifiez le travail à accomplir. Vous pouvez créer et gérer un pool qui répond aux besoins de votre application. Un pool peut être utilisé uniquement par le compte Batch dans lequel il a été créé. Un compte Batch peut avoir plusieurs pools.

Chaque machine virtuelle de tâche ajoutée à un pool se voit attribuer un nom unique et l'adresse IP associée. Lorsqu'une machine virtuelle de tâche est supprimée d'un pool, elle perd les modifications apportées au système d'exploitation, tous ses fichiers locaux, son nom et son adresse IP. Lorsqu'une machine virtuelle de tâche quitte un pool, sa durée de vie est terminée.

Vous pouvez configurer un pool pour permettre la communication entre les machines virtuelles de tâche qu'il contient. Si la communication intra-pool est demandée pour un pool, le service Batch autorise les ports supérieurs à 1100 sur chaque machine virtuelle de tâche du pool. Chaque machine virtuelle de tâche du pool est configurée pour autoriser ou restreindre les connexions entrantes à cette plage de ports et uniquement à partir d'autres machines virtuelles de tâche du pool. Si votre application ne nécessite pas la communication entre des machines virtuelles de tâche, le service Batch peut éventuellement allouer un grand nombre de machines virtuelles de tâche de différents centres de données ou clusters au pool pour permettre un traitement plus parallèle.

Lorsque vous créez un pool, vous pouvez spécifier les attributs suivants :

- taille des machines virtuelles de tâche du pool ;

- famille de systèmes d'exploitation et version exécutée sur les machines virtuelles de tâche ;

- nombre cible de machines virtuelles de tâche devant être disponibles pour le pool ;

- stratégie de mise à l'échelle du pool ;

- état de communication des machines virtuelles de tâche du pool ;

- tâche de démarrage des machines virtuelles de tâche du pool.

Lorsque vous créez un pool, vous pouvez spécifier le compte de stockage auquel le pool doit être associé. Le service Batch alloue au compte de stockage spécifié des machines virtuelles de tâche provenant de centres de données dotés des meilleures capacités en matière de bande passante et de connectivité réseau. Les charges de travail bénéficient ainsi d'un accès plus efficace aux données.

### <a name="workitem"></a>Élément de travail

Un élément de travail spécifie comment le calcul est effectué sur les machines virtuelles de tâche d'un pool. Il inclut les éléments de configuration suivants :

1.Programme qui effectue le traitement sur la machine virtuelle de tâche.

2.Paramètres de ligne de commande du programme.

3.Priorité du calcul.

4.Planification du calcul, qui inclut la définition de la récurrence ou non du calcul.

Un élément de travail est toujours attribué à un pool et ce dernier peut déjà exister ou être créé automatiquement lors de la création de l'élément de travail.

### <a name="job"></a>Job

Un travail est une instance en cours d'exécution d'un élément de travail et se compose d'une collection de tâches. Le service Batch instancie un travail en fonction de la configuration de l'élément de travail. Le travail utilise les machines virtuelles de tâche du pool associé à l'élément de travail.

### <a name="task"></a>Tâche

Une tâche est une unité de calcul associée à un travail et exécutée sur une machine virtuelle de tâche. Elle utilise les ressources suivantes :

- Programme spécifié dans l'élément de travail.

- Fichiers de ressources qui contiennent les données à traiter. Ces fichiers sont automatiquement copiés sur la machine virtuelle de tâche depuis le stockage d'objets blob. Pour plus d'informations, consultez la section Fichiers et répertoires.

- Paramètres d'environnement requis par le programme. Pour plus d'informations, consultez la section Paramètres d'environnement des tâches.

- Contraintes dans lesquelles le calcul doit se produire. Par exemple, la durée maximale pendant laquelle la tâche est autorisée à s'exécuter, le nombre maximal de nouvelles tentatives en cas d'échec de la tâche, ainsi que la durée maximale pendant laquelle les fichiers du répertoire de travail sont conservés.

Outre les tâches que vous pouvez définir pour effectuer des calculs sur une machine virtuelle de tâche, vous pouvez utiliser les tâches spéciales suivantes fournies par le service Batch :

- [Tâche de démarrage](#starttask)

- [Tâche du gestionnaire de travaux](#jobmanagertask)

#### <a name="starttask"></a>Tâche de démarrage

Vous pouvez configurer le système d'exploitation des machines virtuelles de tâche d'un pool en associant une tâche de démarrage au pool. Une tâche de démarrage peut effectuer certaines actions, dont l'installation du logiciel et le démarrage des processus en arrière-plan. Elle s'exécute chaque fois qu'une machine virtuelle de tâche démarre pendant sa durée de présence dans le pool.

Une tâche de démarrage est définie en ajoutant une section XML dans le corps de demande de l'opération Ajouter un pool. L'exemple suivant montre une définition de base d'une tâche de démarrage :

	{
		"commandLine":"mypoolsetup.exe",
		"resourceFiles":
		[
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"mypoolsetup.exe"
			},
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp2.exe"
			}
		],
		"maxTaskRetryCount":0
	}


#### <a name="jobmanagertask"></a>Tâche du gestionnaire de travaux

Une tâche du gestionnaire de travaux est lancée avant toutes les autres tâches. La tâche du gestionnaire de travaux offre les avantages suivants :

- Elle est automatiquement créée par le service Batch lorsque le travail est créé.

- Elle est planifiée avant les autres tâches du travail.

- La machine virtuelle de tâche associée est la dernière à être supprimée d'un pool lorsque la taille de ce dernier diminue.

- Elle dispose de la priorité la plus élevée lorsqu'elle doit être redémarrée. Si une machine virtuelle de tâche inactive n'est pas disponible, le service Batch peut mettre fin à une tâche en cours d'exécution dans le pool pour lui laisser la place de s'exécuter.

- Son arrêt peut être lié à l'arrêt de toutes les tâches du travail.

Une tâche du gestionnaire de travaux associée à un travail n'a pas la priorité sur les tâches des autres travaux. Parmi les travaux, seules les priorités au niveau du travail sont observées. Une tâche du gestionnaire de travaux est définie en ajoutant une section XML dans le corps de demande de l'opération Ajouter un élément de travail. L'exemple suivant montre une définition de base d'une tâche du gestionnaire de travaux :

	{
		"name":"jmTask",
		"commandLine":"myapp1.exe",
		"resourceFiles":
		[
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp1.exe"
			},
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp2.exe"
			}
		],
		"taskConstraints":
		{
			"maxWallClockTime":"PT1H",
			"maxTaskRetryCount":0,
			"retentionTime":"PT1H"
		},
		"killJobOnCompletion":true,
		"runElevated":false,
		"runExclusive":true
	}




## <a name="workflow"></a>Flux de travail du service Batch

Vous avez besoin d'un compte Batch pour utiliser le service Batch et vous utilisez plusieurs ressources du service pour planifier le calcul. Utilisez le flux de travail de base suivant lorsque vous créez un scénario de calcul distribué avec le service Batch :

1.Téléchargez les fichiers que vous souhaitez utiliser dans votre scénario de calcul distribué dans un compte de stockage Azure. Ces fichiers doivent être situés dans le compte de stockage afin que le service Batch puisse y accéder. Ce dernier les charge sur une machine virtuelle de tâche lorsque la tâche s'exécute.

2.Téléchargez les fichiers binaires dépendants dans le compte de stockage, notamment le programme exécuté par la tâche et les assemblys dépendants. Ces fichiers doivent également être accessibles à partir du stockage et sont chargés dans la machine virtuelle de tâche.

3.Créez un pool de machines virtuelles de tâche. Vous pouvez attribuer la taille de la machine virtuelle de tâche à utiliser lors de la création du pool. Lorsqu'une tâche s'exécute, elle reçoit une machine virtuelle de tâche à partir de ce pool.

4.Créez un élément de travail. Un travail est créé automatiquement lorsque vous créez un élément de travail. Ce dernier vous permet de gérer un travail constitué de tâches.

5.Ajoutez des tâches à l'élément de travail. Chaque tâche utilise le programme téléchargé pour traiter les informations à partir d'un fichier téléchargé.

6.Analysez les résultats de la sortie.

## <a name="files"></a>Fichiers et répertoires

Chaque tâche possède un répertoire de travail dans lequel elle crée zéro ou plusieurs répertoires et fichiers pour stocker le programme qu'elle exécute, les données qu'elle traite et la sortie du traitement qu'elle effectue. Ces fichiers et répertoires sont ensuite disponibles pour une utilisation par d'autres tâches pendant l'exécution d'un travail. L'ensemble des tâches, fichiers et répertoires d'une machine virtuelle de tâche sont la propriété d'un seul compte d'utilisateur.

Le service Batch expose une partie du système de fichiers sur une machine virtuelle de tâche en tant que répertoire racine. Le répertoire racine de la machine virtuelle de tâche est disponible pour une tâche via la variable d'environnement WATASK\_TVM\_ROOT\_DIR. Pour plus d'informations sur l'utilisation de variables d'environnement, consultez la section Paramètres d'environnement des tâches.

Le répertoire racine contient les sous-répertoires suivants :

- **Tâches** - Cet emplacement est celui où tous les fichiers qui appartiennent à des tâches qui s'exécutent sur la machine virtuelle de tâche sont stockés. Pour chaque tâche, le service Batch crée un répertoire de travail avec le chemin d'accès unique au format %WATASK\_TVM\_ROOT\_DIR%/tasks/workitemName/jobName/taskName/. Ce répertoire fournit un accès en lecture/écriture à la tâche. La tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire, et ce dernier est conservé en fonction de la contrainte RetentionTime spécifiée pour la tâche.

- **Partagé** - Cet emplacement est un répertoire partagé pour toutes les tâches situées dans le compte. Dans la machine virtuelle de tâche, le répertoire partagé est %WATASK\_TVM\_ROOT\_DIR%/shared. Ce répertoire fournit un accès en lecture/écriture à la tâche. La tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire.

- **Démarrer** - Cet emplacement est utilisé par une tâche de démarrage comme répertoire de travail. Tous les fichiers téléchargés par le service Batch pour lancer la tâche de démarrage sont également stockés dans ce répertoire. Dans la machine virtuelle de tâche, le répertoire de démarrage est %WATASK\_TVM\_ROOT\_DIR%/start. La tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire, et ce dernier est utilisable par les tâches de démarrage pour configurer le système d'exploitation.

Lorsqu'une machine virtuelle de tâche est supprimée du pool, tous les fichiers stockés dans celle-ci sont supprimés.

## <a name="scaling"></a>Mise à l'échelle des applications

Votre application peut facilement être mise à l'échelle (dans un sens ou dans l'autre) automatiquement pour accueillir le calcul dont vous avez besoin. Vous pouvez ajuster dynamiquement le nombre de machines virtuelles de tâche d'un pool en fonction de la charge de travail actuelle et des statistiques sur l'utilisation des ressources. Vous pouvez également optimiser le coût total de l'exécution de votre application en la configurant à l'échelle automatiquement. Vous pouvez spécifier les paramètres de mise à l'échelle d'un pool lorsqu'il est créé, et mettre à jour la configuration à tout moment.

Spécifiez la mise à l'échelle automatique d'une application à l'aide d'un jeu de formules de mise à l'échelle. Les formules peuvent servir à déterminer le nombre de machines virtuelles de tâche qui se trouvent dans le pool pendant le prochain intervalle de mise à l'échelle. Par exemple, vous devez envoyer un grand nombre de tâches qui doivent être planifiées sur un pool. Vous pouvez attribuer une formule de mise à l'échelle au pool qui spécifie la taille du pool selon le nombre actuel de tâches en attente et la vitesse d'exécution des tâches. Le service Batch évalue la formule régulièrement et redimensionne le pool en fonction de la charge de travail.

Une formule peut être basée sur les mesures suivantes :

- **Mesures temporelles** - Celles-ci sont basées sur les statistiques collectées toutes les cinq minutes dans le nombre d'heures spécifié.

- **Mesures de ressources** - Celles-ci sont basées sur l'utilisation du processeur, de la bande passante et de la mémoire, et sur le nombre de machines virtuelles de tâche.

- **Mesures de tâches** - Celles-ci sont basées sur l'état des tâches (Actif, En attente et Terminé).

Pour plus d'informations sur la mise à l'échelle automatique d'une application, consultez la section Configuration de la mise à l'échelle automatique de machines virtuelles de tâche.

## <a name="cert"></a>Certificats pour les applications

En général, vous devez utiliser des certificats lorsque vous chiffrez des informations confidentielles. Des certificats peuvent être installés sur les machines virtuelles de tâche. Les secrets chiffrés sont transmis aux tâches dans les paramètres de ligne de commande ou incorporés dans l'une des ressources et les certificats installés peuvent être utilisés pour les déchiffrer. Un exemple d'informations secrètes est la clé d'un compte de stockage.

Vous utilisez l'opération Ajouter un certificat pour ajouter un certificat à un compte Batch. Vous pouvez ensuite associer le certificat à un pool existant ou nouveau. Lorsqu'un certificat est associé à un pool, le service Batch installe le certificat sur chaque machine virtuelle de tâche du pool. Le service Batch installe les certificats appropriés au démarrage de la machine virtuelle de tâche, avant de lancer toutes les tâches, y compris les tâches de démarrage et celles du gestionnaire de travaux.

## <a name="scheduling"></a>Priorité de la planification

Lorsque vous créez un élément de travail, vous pouvez lui attribuer une priorité. Chaque travail de l'élément de travail est créé avec cette priorité. Le service Batch utilise les valeurs de priorité du travail pour déterminer l'ordre de planification des travaux dans un compte. Les valeurs de priorité peuvent être comprises entre -1000 et 1000, -1000 étant la priorité la plus basse et 1000 la plus élevée. Vous pouvez mettre à jour la priorité d'un travail à l'aide de l'opération UpdateJob.

Dans le même compte, les tâches à la priorité plus élevée ont la priorité en termes de planification sur les tâches avec une priorité plus faible. Un travail avec une priorité plus élevée dans un compte n'a pas de priorité de planification sur un autre travail avec une valeur de priorité inférieure dans un autre compte.

La planification des travaux dans différents pools est indépendante. Entre les différents pools, il n'est pas systématique qu'un travail avec une priorité plus élevée soit planifié en premier, si son pool n'a pas suffisamment de machines virtuelles de tâche inactives. Dans le même pool, les travaux avec le même niveau de priorité ont autant de chance d'être planifiés.

## <a name="environment"></a>Paramètres d'environnement des tâches

Vous pouvez spécifier des paramètres d'environnement qui peuvent être utilisés dans le contexte d'une tâche. Les paramètres d'environnement d'une tâche de démarrage et de tâches en cours d'exécution sous un travail sont définis en ajoutant une section XML dans le corps de demande des opérations Ajouter une tâche ou Mettre à jour une tâche.

L'exemple suivant montre la définition d'un paramètre d'environnement :

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
  WATASK_PORTRANGE_LOW
  WATASK_PORTRANGE_HIGH
</td>
<td>
  Plage de ports (incluse) que la tâche peut utiliser pour la communication intra-pool lorsque cette dernière est activée.
</td>
</table>


**Remarque** 

Vous ne pouvez pas remplacer ces variables définies par le système.

Vous pouvez récupérer la valeur des paramètres d'environnement à l'aide de l'opération Obtenir une tâche.
