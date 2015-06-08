<properties
	pageTitle="Vue d'ensemble technique d'Azure Batch"
	description="Découvrez les concepts, les flux de travail et les scénarios du service Azure Batch"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="danlep"/>


#Vue d'ensemble technique d'Azure Batch
Lot Azure vous permet d'exécuter à grande échelle parallèle et haute applications performance computing (HPC) dans le cloud. C'est un service de plate-forme qui fournit la planification des tâches et la mise à l'échelle d'une collection managée de machines virtuelles (VM) pour exécuter les travaux. En utilisant le service de traitement par lots, vous pouvez configurer des charges de travail par lots à exécuter dans Azure à la demande ou selon une planification, ne vous inquiétez pas sur la complexité de la planification des tâches et la gestion des machines virtuelles dans la plate-forme sous-jacente.

>[AZURE.NOTE]Le service Batch est disponible en version préliminaire. Pour utiliser le traitement par lots, vous avez besoin d'un compte Azure et vous avez besoin pour vous inscrire pour l'aperçu du lot. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page [Création d’un compte Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/).


## Cas d'utilisation

Lot utilise l'élasticité et l'échelle du cloud pour vous aider avec *le traitement par lots* ou *lot informatique* -exécute un grand nombre de tâches similaires pour obtenir des résultat souhaité. Un programme de ligne de commande ou un script accepte un ensemble de fichiers de données comme entrée, traite les données dans une série de tâches et produit un ensemble de fichiers de sortie. Les fichiers de sortie peuvent être le résultat final ou une étape intermédiaire dans un flux de travail volumineux.

Le calcul par lots est un modèle courant pour les organisations qui traitent, transforment et analysent de grandes quantités de données, de manière planifiée ou à la demande. Cela inclut le traitement de fin de cycle tel que la création de rapports sur les risques quotidiens auxquels doit faire face une banque ou une paie qui doit être effectuée sur la base d'une planification. Cela comprend également les applications d'ingénierie, scientifiques et d'entreprise à grande échelle qui ont généralement besoin des outils et ressources d'une grille ou d'un cluster de calcul. Il s'agit d'applications HPC traditionnelles telles que les simulations de dynamiques de fluides, ainsi que des charges de travail spécialisées dans les domaines tels que la conception automobile, l'exploration des sources de pétrole et de gaz, la recherche médicale et la création de contenu numérique.

Le service Batch fonctionne bien avec des applications ou charges de travail intrinsèquement parallèles, qui se prêtent à l'exécution de tâches en parallèle sur plusieurs ordinateurs, comme des machines virtuelles de calcul gérées par le service Batch. Voir la Figure 1.

![Tâches parallèles][parallel]

**Figure 1. Tâches parallèles exécutés sur plusieurs ordinateurs**

Voici quelques exemples :

* Modélisation de risques financiers
* Rendu et traitement d'images
* Encodage et transcodage multimédia
* Analyse de séquence génétique
* Test de logiciels

Vous pouvez également utiliser le service Batch pour effectuer des calculs parallèles avec une étape de réduction à la fin et d'autres charges de travail parallèles non cycliques plus compliquées.

>[AZURE.NOTE]La version préliminaire du service Batch ne prend pas actuellement en charge les applications MPI (Message Passing Interface).

## Scénarios pour les développeurs

Les API REST du service Batch prennent en charge deux scénarios de développement pour vous aider à configurer et à exécuter vos charges de travail par lots avec le service Batch :

1. **Distribuer des calculs tels que des éléments de travail** -utilisez le *lot* API pour créer et gérer un pool de machines virtuelles de calcul flexible et spécifier leur travail seront. Cela vous donne le contrôle total sur les ressources et exige que le client gérer le pipeline d'exécution tâche - par exemple, avec un gestionnaire de flux de travail ou d'un planificateur meta, ce qui peut être implémenté à l'aide de l'API REST de lot, ou éventuellement une fonctionnalité du Gestionnaire de travail de l'élément de travail. Au lieu de configurer un cluster de calcul ou d'écrire du code pour mettre en file d'attente et planifier vos projets, vous automatisez la planification des travaux de calcul et mettez à l'échelle un pool de machines virtuelles de calcul pour les exécuter. Dans le cadre de la spécification des éléments de travail, définissez toutes les dépendances et définissent le déplacement des fichiers d'entrée et de sortie.

2. **Publier et exécuter des applications avec le service de traitement** - le *lot applications* API fonctionnent à un niveau supérieur, pour vous aider à encapsuler une application existante s'il s'exécute comme un service sur un pool de machines virtuelles géré en arrière-plan par lot. L'application peut être une qui s'exécute aujourd'hui sur les stations de travail ou un cluster de calcul. L'infrastructure Batch Apps gère le déplacement des fichiers d'entrée et de sortie, l'exécution et la gestion des travaux, ainsi que la persistance des données. Batch Apps vous permet également de modéliser des tâches pour le mode de partitionnement des données et pour plusieurs étapes d'un travail. Sont inclus une API REST et le portail Batch Apps, accessible à partir du Portail de gestion Azure et qui vous permet de surveiller les travaux que vous avez envoyés.


## Concepts Batch

Les sections suivantes résument les concepts clés pour l'utilisation des API et du service Batch. Pour plus d'informations, consultez [principes fondamentaux d'API pour Azure lot](batch-api-basics.md).

* [Compte de traitement par lots](#BKMK_Account)
* [Tâche des ordinateurs virtuels et pools](#BKMK_TVM)
* [Éléments de travail, des projets et des tâches](#BKMK_Workitem)
* [Fichiers et répertoires](#BKMK_Files)

### <a id="BKMK_Account">Compte de traitement par lots</a>
Vous avez besoin d'une unique **lot compte** pour fonctionner avec le service de traitement par lots. Toutes les requêtes que vous apportez au service de traitement par lots doivent être authentifiés en utilisant le nom du compte et sa clé d'accès.

Vous pouvez créer un compte de traitement par lots et gérer des clés d'accès pour le compte dans le portail Azure ou avec le [applets de commande PowerShell de lot](batch-powershell-cmdlets-get-started.md).

Pour créer un compte de traitement par lots dans le portail :

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur **nouveau**, cliquez sur **Compute**, cliquez sur **lot Service**, puis cliquez sur **Création rapide**. ![Créer un compte de traitement par lots][account_portal]

3. Entrez les informations suivantes :

	* Dans **nom de compte**, entrez un nom unique à utiliser dans l'URL de compte de traitement par lots.
	* Dans **région**, sélectionnez une région dans laquelle le lot est disponible.
	* Si vous avez plusieurs abonnements, en **abonnement**, sélectionnez un abonnement disponible qui sera facturé pour l'utilisation du traitement par lots.


### <a id="BKMK_TVM">Tâche des ordinateurs virtuels et pools</a>

Un **machine virtuelle** (TVM) est une machine virtuelle Azure que le service de traitement par lots dédie un spécifique en cours d'exécution de tâches pour votre application : par exemple un fichier exécutable (.exe) ou dans le cas des applications de traitement par lots, un ou plusieurs programmes à partir d'une image de l'application. Contrairement à une machine virtuelle Azure typique, ne pas configurer ou gérer une TVM directement ; au lieu de cela, le service de traitement par lots crée et gère les TVMs comme un **pool** d'ordinateurs virtuels configurés de la même façon, comme illustré Figure 2.

![Pool et TVMs][TVM_pool]

**Figure 2. Un pool de TVMs**

Si vous utilisez les API de traitement par lots, vous pouvez créer un pool directement ou créer un automatiquement lorsque vous spécifiez le travail à effectuer. Si vous utilisez l'API Batch Apps, un pool est créé automatiquement quand vous exécutez votre application Batch compatible cloud.


Voici les attributs d'un pool :

* Un [taille](http://msdn.microsoft.com/library/azure/dn197896.aspx) pour les TVMs
* Le système d'exploitation qui s'exécute sur les machines virtuelles de tâche
* Le nombre maximal de machines virtuelles de tâche
* Une stratégie de mise à l'échelle pour le pool : formule basée sur l'utilisation de ressources et la charge de travail actuelles, et qui ajuste dynamiquement le nombre de machines virtuelles de tâche qui traitent les tâches
* Si les ports de pare-feu sont activés sur les machines virtuelles de tâche pour permettre la communication intra-pool
* Un certificat installé sur la machine virtuelle de tâche, par exemple, pour authentifier l'accès à un compte de stockage
* Une tâche de démarrage pour les machines virtuelles de tâche, pour les opérations uniques telles que l'installation d'applications ou le téléchargement des données utilisées par les tâches

>[AZURE.NOTE]Actuellement, une machine virtuelle de tâche peut uniquement s'exécuter sur les systèmes d'exploitation Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 R2 SP1.

Un pool peut être utilisé uniquement par le compte Batch dans lequel il a été créé. Un compte Batch peut avoir plusieurs pools.

Chaque TVM dans un pool est affectée un nom unique et une adresse IP associée. Lorsqu'une machine virtuelle de tâche est supprimée d'un pool, elle perd les modifications apportées au système d'exploitation, ses fichiers locaux, son nom et son adresse IP. Quand une machine virtuelle de tâche quitte un pool, sa durée de vie est terminée.


### <a id="BKMK_Workitem">Éléments de travail, des projets et des tâches</a>

Un **workitem** est un modèle qui spécifie comment une application s'exécutera sur TVMs dans un pool. Un **travail** est un élément de travail planifié et peut apparaître une seule fois ou se reproduit. Un travail est constitué d'une collection de **tâches**. La Figure 3 illustre les relations de base.

![Élément de travail, des travaux et des tâches][job_task]

**Figure 3. Un élément de travail, des travaux et des tâches**

En fonction de l'API que vous permet de développer avec le traitement par lots, vous devez gérer les détails plus ou moins sur les éléments de travail, des projets et des tâches.

* Si vous développez une application avec les API de traitement par lots, vous devez définir les éléments de travail, des projets et des tâches et de configurer les pools TVM qui exécutent les tâches par programme.

* Si vous intégrez une application cliente en utilisant les outils et API Batch Apps, vous pouvez utiliser des composants qui fractionnent un travail en tâches automatiquement, traitent les tâches et fusionnent les résultats des tâches individuelles dans les résultats finaux du travail. Lorsque vous envoyez la charge de travail pour le service de traitement par lots, l'infrastructure des applications de traitement par lots gère les travaux et exécute les tâches sur les TVMs.



### <a id="BKMK_Files">Fichiers et répertoires</a>

Un fichier est un élément de données utilisé comme entrée d'une tâche de travail. Une tâche peut avoir un nombre quelconque de fichiers d'entrée associés (un, aucun ou plusieurs). Le même fichier peut être utilisé dans plusieurs tâches. Par exemple, pour les tâches d'un travail de rendu vidéo, il peut y avoir des textures et des modèles couramment utilisés. Pour les tâches d'un travail d'analyse de données, les fichiers peuvent être un jeu d'observations ou de mesures.

Il existe un répertoire de travail pour chaque tâche qui contient les données d'entrée qu'il traite et les données de sortie qu'il crée. Ces fichiers et répertoires sont disponibles pour une utilisation par d'autres tâches pendant l'exécution d'un travail. L'ensemble des tâches, fichiers et répertoires d'une machine virtuelle de tâche sont la propriété d'un seul compte d'utilisateur.

Là encore, en fonction de l'API que vous utilisez avec le traitement par lots, vous devez gérer plus ou moins plus d'informations sur les fichiers d'entrée et de sortie pour vos projets et des tâches. Si vous développez avec les API de traitement par lots, spécifiez ces dépendances et les mouvements de fichier explicitement. Avec Batch Apps, l'infrastructure gère la plupart de ces détails pour vous.

## Workflow d'éléments de travail
Figure 4 vous montre comment soumettre une application à un pool dans lequel elle est distribuée pour le traitement. Il utilise l'API de traitement par lots.

![Workflow d'éléments de travail][work_item_workflow]

**Figure 4. Flux de travail pour distribuer des éléments de travail pour les ordinateurs de bureaux virtuels**

1.	Téléchargez les fichiers d'entrée (comme données ou images sources) nécessaires à un travail dans un compte de stockage Azure. Ils doivent être situés dans le compte de stockage afin que le service Batch puisse y accéder. Ce dernier les charge sur une machine virtuelle de tâche quand la tâche s'exécute.
2.	Téléchargez les fichiers binaires dépendants dans le compte de stockage, notamment le programme exécuté par la tâche et les assemblys dépendants. Ces fichiers doivent également être accessibles à partir du stockage et sont chargés dans la machine virtuelle de tâche.
3.	Créez un pool de machines virtuelles de tâche, en spécifiant la taille des machines virtuelles, leur système d'exploitation et d'autres propriétés. Quand une tâche s'exécute, elle reçoit une machine virtuelle de tâche à partir de ce pool.
4.	Créez un élément de travail. Un travail est automatiquement créé lorsque vous créez un élément de travail. Un élément de travail vous permet de gérer un travail constitué de tâches.
5.	Ajoutez des tâches au travail. Chaque tâche utilise le programme téléchargé pour traiter les informations à partir d'un fichier téléchargé.
6.	Exécutez l'application et surveillez les résultats de la sortie.

## Flux de travail pour publier et exécuter des applications
La Figure 5 illustre un flux de travail de base pour publier une application à l'aide de l'API Batch Apps, puis envoyer des travaux à l'application activée par le service Batch.

![Workflow de publication d'application][app_pub_workflow]

**Figure 5. Flux de travail pour publier et exécuter une application avec des applications de traitement par lots**

1.	Préparer un **image d'application** -un fichier .zip contenant vos fichiers exécutables d'application existant et les fichiers de prise en charge dont ils ont besoin. Il peut s'agir des mêmes exécutables que ceux que vous exécutez dans une batterie de serveurs ou un cluster classique.
2.	Créer un fichier zip de la **cloud assembly** qui appeler et distribuer vos charges de travail pour le service de traitement par lots. Il contient deux composants disponibles via le Kit de développement logiciel (SDK) :

	a. **Fractionnement du travail** – décompose un travail en tâches qui peuvent être traitées indépendamment. Par exemple, dans un scénario d'animation, l'outil de fractionnement du travail prend un travail de rendu vidéo et le décompose en images individuelles.

	b. **Processeur de tâches** – appelle l'application exécutable pour une tâche donnée. Par exemple, dans un scénario d'animation, le processeur de tâches appelle un programme de rendu pour restituer l'image unique spécifiée par la tâche.

3.	Utilisez les outils de développement ou les API Batch Apps pour télécharger les fichiers .zip préparés durant les deux étapes précédentes vers un compte de stockage Azure. Ils doivent être situés dans le compte de stockage afin que le service Batch puisse y accéder. Cela est généralement effectué une seule fois par application, par un administrateur de services fédérés.
4.	Fournissez un moyen d'envoyer des travaux au service d'application activé dans Azure. Il peut s'agir d'un plug-in dans l'interface utilisateur de votre application, un portail web ou un service sans assistance au sein de votre système principal. Des exemples sont disponibles avec le Kit de développement logiciel (SDK) pour illustrer les diverses options.

	Pour exécuter un travail :

	a. Téléchargez les fichiers d'entrée (comme données ou images sources) spécifiques du travail de l'utilisateur. Ils doivent être situés dans le compte de stockage afin que le service Batch puisse y accéder.

	b. Envoyez un travail avec les paramètres requis et la liste de fichiers.

	c. Surveillez la progression du travail à l'aide des API ou du portail Batch Apps.

	d. Téléchargez les sorties.

## Ressources supplémentaires

* [Commencez avec la bibliothèque de commandes Azure pour .NET](batch-dotnet-get-started.md)
* [Outils et bibliothèques de développement de lot azure](batch-development-libraries-tools.md)
* [Référence de l'API REST Azure lot](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [Référence de l'API REST lot Azure applications](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[TVM_pool]: ./media/batch-technical-overview/TVM_pool.png
[job_task]: ./media/batch-technical-overview/job_task.png
[account_portal]: ./media/batch-technical-overview/account_portal.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png

<!---HONumber=GIT-SubDir-->