<properties linkid="batch-technical-overview" urlDisplayName="" pageTitle="Vue d'ensemble technique d'Azure Batch" metaKeywords="" description="En savoir plus sur les concepts, les workflows et les scénarios du service Azure Batch" metaCanonical="" services="batch" documentationCenter="" title="Azure Batch technical overview" authors="danlep" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="batch" ms.workload="big-compute" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/27/2014" ms.author="danlep" />


#Vue d'ensemble technique d'Azure Batch
Azure Batch vous permet d'exécuter des applications HPC (calculs complexes) et parallèles à grande échelle dans le cloud en fournissant la planification des travaux et la mise à l'échelle automatique des ressources de calcul en tant que service de plateforme. En utilisant le Kit de développement logiciel (SDK) Batch et le service Batch, vous pouvez configurer des charges de travail par lots de manière à ce qu'elles s'exécutent à la demande ou selon une planification sur une collection gérée de machines virtuelles, sans avoir à vous soucier de la complexité de la planification des travaux et de la gestion des ressources dans la plateforme sous-jacente.
 
>[WACOM.NOTE]Le service Batch est disponible en version préliminaire. Pour l'utiliser, vous avez besoin d'un compte Azure et vous devez l'activer sur votre abonnement. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Création d'un compte Azure](http://www.windowsazure.com/fr-fr/develop/php/tutorials/create-a-windows-azure-account/). 


Cet article offre une vue d'ensemble constituée des sections suivantes :
 
* [Cas d'utilisation](#BKMK_Scenarios)
* [Scénarios pour les développeurs](#BKMK_Approaches)
* [Concepts Batch](#BKMK_Entities)
* [Flux de travail d'éléments de travail](#BKMK_Workflow_workitems)
* [Flux de travail pour publier et exécuter des applications](#BKMK_Workflow_cloudappss)
* [Ressources supplémentaires](#BKMK_Resources)

<h2 id="BKMK_Scenarios">Cas d'utilisation</h2>

Le service Batch fait appel à l'élasticité et à l'échelle du cloud pour vous aider dans le cadre du *traitement par lots* ou du *calcul par lots* et de l'exécution de gros volumes de tâches similaires par programme. Un programme de ligne de commande ou un script accepte un ensemble de fichiers de données comme entrée, traite les données dans une série de tâches et produit un ensemble de fichiers de sortie. Les fichiers de sortie peuvent constituer le résultat final ou une étape intermédiaire dans un flux de travail plus volumineux.       
 
Le calcul par lots est un modèle courant pour les organisations qui traitent, transforment et analysent de grandes quantités de données, de manière planifiée ou à la demande. Cela inclut le traitement de fin de cycle tel que la création de rapports sur les risques quotidiens auxquels doit faire face une banque ou une paie qui doit être effectuée sur la base d'une planification. Cela comprend également les applications d'ingénierie, scientifiques et d'entreprise à grande échelle qui ont généralement besoin des outils et ressources d'une grille ou d'un cluster de calcul. Il s'agit d'applications HPC traditionnelles telles que les simulations de dynamiques de fluides, ainsi que des charges de travail spécialisées dans les champs, comme la conception automobile, l'exploration des sources de pétrole et de gaz, la recherche médicale et la création de contenu numérique. 
 
Le service Batch fonctionne bien avec des applications ou charges de travail intrinsèquement parallèles, qui se prêtent à l'exécution de tâches en parallèle sur plusieurs ordinateurs, comme des machines virtuelles de calcul gérées par le service Batch. Voir la Figure 1.  

![Parallel tasks][parallel]

**Figure 1. Tâches parallèles exécutées sur plusieurs ordinateurs**

En voici quelques exemples :

* Modélisation de risques financiers 
* Rendu et traitement d'images
* Encodage et transcodage multimédia
* Analyse de séquence génétique
* Test de logiciels

Vous pouvez également utiliser le service Batch pour effectuer des calculs parallèles avec une étape de réduction à la fin et d'autres charges de travail parallèles non cycliques plus compliquées. 

>[WACOM.NOTE]La version préliminaire du service Batch ne prend pas actuellement en charge les applications MPI (Message Passing Interface).

<h2 id="BKMK_Approaches">Scénarios pour les développeurs</h2>

Les API REST du service Batch prennent en charge deux scénarios de développement pour vous aider à configurer et à exécuter vos charges de travail par lots avec le service Batch :
 
1. **Distribution de calculs comme éléments de travail** - Utilisez les API *Batch* pour créer et gérer un pool flexible de machines virtuelles de calcul et spécifier les éléments de travail qui y sont exécutés. Cela vous donne un contrôle total sur les ressources et exige que le client gère le pipeline d'exécution des tâches (par exemple, avec un gestionnaire de flux de travail ou un planificateur meta), ce qui peut être implémenté à l'aide des API REST du service Batch, ou éventuellement d'une fonctionnalité de gestionnaire de travaux de l'élément de travail. Au lieu de configurer un cluster de calcul ou d'écrire du code pour mettre en file d'attente et planifier vos projets, vous automatisez la planification des travaux de calcul et mettez à l'échelle un pool de machines virtuelles de calcul pour les exécuter. Dans le cadre de la spécification d'éléments de travail, vous définissez toutes les dépendances et le déplacement des fichiers d'entrée et de sortie. 

2. **Publication et exécution d'applications avec le service Batch** - Les API *Batch Apps* fournissent un niveau d'abstraction supérieur et un pipeline d'exécution de travaux hébergé par le service Batch. Batch Apps permet de créer une charge de travail par lots en tant que service dans le cloud à partir d'une application qui s'exécute actuellement sur les postes de travail clients ou un cluster de calcul. Batch Apps vous permet d'encapsuler des fichiers exécutables et binaires existants et de les publier de manière à s'exécuter sur des machines virtuelles regroupées dans un pool, que le service Batch crée et gère en arrière-plan. L'infrastructure Batch Apps gère le déplacement des fichiers d'entrée et de sortie, l'exécution et la gestion des travaux, ainsi que la persistance des données. Batch Apps vous permet également de modéliser des tâches pour le mode de partitionnement des données et pour plusieurs étapes d'un travail. Sont inclus une API REST et le portail Batch Apps, accessible à partir du portail de gestion Azure et qui vous permet de surveiller les travaux que vous avez envoyés.


<h2 id="BKMK_Entities">Concepts Batch</h2>

Les sections suivantes résument les concepts clés pour l'utilisation des API et du service Batch. Pour plus d'informations, consultez la page [Concepts de base concernant les API dans Azure Batch](http://azure.microsoft.com/fr-fr/documentation/articles/batch-api-basics). 

* [Compte Batch](#BKMK_Account)
* [Machines virtuelles de tâche et pools](#BKMK_TVM)
* [Éléments de travail, travaux et tâches](#BKMK_Workitem)
* [Fichiers et répertoires](#BKMK_Files)

<h3 id="BKMK_Account">Compte Batch</h3>
Vous avez besoin d'un **compte Batch** unique pour utiliser le service Batch. Lorsque vous effectuez des opérations d'API avec le service Batch, vous authentifiez vos demandes avec l'URL du compte et une clé d'accès associée. Vous avez également besoin d'un compte Batch pour accéder au portail Batch Apps.
 
Vous pouvez créer un compte Batch et gérer des clés d'accès pour le compte dans le portail de gestion Azure.

Pour créer un compte Batch :

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur **Nouveau**, **Calcul**, **Service Batch**, puis sur **Création rapide**.
![Create a Batch account][account_portal]

3. Entrez les informations suivantes :

	* Dans **Nom du compte**, entrez un nom unique à utiliser dans l'URL du compte Batch.
	* Dans **Région**, sélectionnez une région dans laquelle le service Batch est disponible.
	* Si vous avez plusieurs abonnements, dans **Abonnement**, sélectionnez un abonnement disponible qui sera facturé pour votre utilisation du service Batch.

 
<h3 id="BKMK_TVM">Machines virtuelles de tâche et pools</h3>

Une **machine virtuelle de tâche** est une machine virtuelle Azure que le service Batch consacre à l'exécution d'une charge de travail (tâche) spécifique pour votre application : par exemple un fichier exécutable (.exe) ou, dans le cas de Batch Apps, un ou plusieurs programmes à partir d'une image d'application. Contrairement à une machine virtuelle Azure classique, vous ne configurez et ne gérez pas une machine virtuelle de tâche directement ; au lieu de cela, le service Batch crée et gère les machines virtuelles de tâche comme un **pool** de ressources de calcul configurées à l'identique, comme le montre la Figure 2. 

![Pool and TVMs][TVM_pool]

**Figure 2. Pool de machines virtuelles de tâche**

Si vous utilisez les API Batch, vous pouvez créer un pool directement ou configurer le service Batch pour en créer un automatiquement lorsque vous spécifiez le travail à effectuer. Si vous utilisez l'API Batch Apps, un pool est créé automatiquement lorsque vous exécutez votre application Batch sur le cloud.


Voici les attributs d'un pool :

* La [taille](http://msdn.microsoft.com/library/azure/dn197896.aspx) des machines virtuelles de tâche 
* Le système d'exploitation qui s'exécute sur les machines virtuelles de tâche
* Le nombre maximal de machines virtuelles de tâche
* Une stratégie de mise à l'échelle pour le pool : formule basée sur l'utilisation de ressources et la charge de travail actuelles, et qui ajuste dynamiquement le nombre de machines virtuelles de tâche qui traitent les tâches
* Si les ports de pare-feu sont activés sur les machines virtuelles de tâche pour permettre la communication intra-pool
* Un certificat installé sur la machine virtuelle de tâche, par exemple, pour authentifier l'accès à un compte de stockage
* Une tâche de démarrage pour les machines virtuelles de tâche, pour les opérations uniques telles que l'installation d'applications ou le téléchargement des données utilisées par les tâches

>[WACOM.NOTE]Actuellement, une machine virtuelle de tâche peut uniquement s'exécuter sur les systèmes d'exploitation Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 R2 SP1.

Un pool peut être utilisé uniquement par le compte Batch dans lequel il a été créé. Un compte Batch peut avoir plusieurs pools.

Chaque machine virtuelle de tâche ajoutée à un pool se voit attribuer un nom unique et l'adresse IP associée. Lorsqu'une machine virtuelle de tâche est supprimée d'un pool, elle perd les modifications apportées au système d'exploitation, ses fichiers locaux, son nom et son adresse IP. Lorsqu'une machine virtuelle de tâche quitte un pool, sa durée de vie est terminée.


<h3 id="BKMK_Workitem">Éléments de travail, travaux et tâches</h3>

Un **élément de travail** est un modèle qui spécifie le mode d'exécution d'une application sur les machines virtuelles de tâche d'un pool. Un **travail** est une instance planifiée d'un élément de travail qui peut se produire une ou plusieurs fois. Un travail est une collection de **tâches**. La Figure 3 illustre les relations de base.    
 
![Work item, job, and tasks][job_task]

**Figure 3. Élément de travail, travail et tâches**

En fonction des API que vous utilisez pour développer avec le service Batch, vous devez gérer plus ou moins de détails sur les éléments de travail, les travaux et les tâches.

* Si vous développez une application avec les API Batch de niveau inférieur, vous devez définir par programme tous les éléments de travail, travaux et tâches que le service Batch exécute et configurer les pools de machines virtuelles de tâche qui exécutent les tâches.

* Si vous intégrez une application cliente en utilisant les outils et API Batch Apps, vous pouvez utiliser des composants qui fractionnent un travail en tâches automatiquement, traitent les tâches et fusionnent les résultats des tâches individuelles dans les résultats finaux du travail. Lorsque vous envoyez la charge de travail au service Batch, l'infrastructure Batch Apps gère les travaux et exécute les tâches sur les ressources de calcul sous-jacentes. 



<h3 id="BKMK_Files">Fichiers et répertoires</h3>

Un fichier est un élément de données utilisé comme entrée d'une tâche de travail. Une tâche peut avoir un nombre quelconque de fichiers d'entrée associés (un, aucun ou plusieurs). Le même fichier peut être utilisé dans plusieurs tâches. Par exemple, pour les tâches d'un travail de rendu vidéo, il peut y avoir des textures et des modèles couramment utilisés. Pour les tâches d'un travail d'analyse de données, les fichiers peuvent être un jeu d'observations ou de mesures.

Chaque tâche possède un répertoire de travail dans lequel elle crée les répertoires et fichiers nécessaires pour stocker le programme qu'elle exécute, les données d'entrée qu'elle traite et la sortie du traitement qu'elle effectue. Ces fichiers et répertoires sont disponibles pour une utilisation par d'autres tâches pendant l'exécution d'un travail. L'ensemble des tâches, fichiers et répertoires d'une machine virtuelle de tâche sont la propriété d'un seul compte d'utilisateur.

Là encore, en fonction des API que vous utilisez avec le service Batch, vous devez gérer plus ou moins de détails sur les emplacements et le déplacement des fichiers d'entrée et de sortie pour vos travaux et tâches. Si vous développez avec les API Batch de niveau inférieur, vous spécifiez explicitement ces dépendances et les déplacements de fichiers. Avec Batch Apps, l'infrastructure gère la plupart de ces détails pour vous. 

<h2 id="BKMK_Workflow_workitems">Flux de travail d'éléments de travail</h2>
La Figure 4 illustre un flux de travail classique pour distribuer des calculs à un pool de machines virtuelles de tâche à l'aide de l'API Batch.

![Work items workflow][work_item_workflow]

**Figure 4. Flux de travail distribuant des éléments de travail aux machines virtuelles regroupées dans un pool**

1.	Téléchargez les fichiers d'entrée (comme données ou images sources) requis pour un travail dans un compte de stockage Azure. Ces fichiers doivent être situés dans le compte de stockage afin que le service Batch puisse y accéder. Ce dernier les charge sur une machine virtuelle de tâche lorsque la tâche s'exécute.
2.	Téléchargez les fichiers binaires dépendants dans le compte de stockage, notamment le programme exécuté par la tâche et les assemblys dépendants. Ces fichiers doivent également être accessibles à partir du stockage et sont chargés dans la machine virtuelle de tâche.
3.	Créez un pool de machines virtuelles de tâche, en spécifiant la taille des machines virtuelles, le système d'exploitation et d'autres propriétés. Lorsqu'une tâche s'exécute, elle reçoit une machine virtuelle de tâche à partir de ce pool.
4.	Créez un élément de travail. Un travail est automatiquement créé lorsque vous créez un élément de travail. Un élément de travail vous permet de gérer un travail constitué de tâches.
5.	Ajoutez des tâches au travail. Chaque tâche utilise le programme téléchargé pour traiter les informations à partir d'un fichier téléchargé.
6.	Exécutez l'application et surveillez les résultats de la sortie.

<h2 id="BKMK_Workflow_cloudapps">Flux de travail pour publier et exécuter des applications </h2>
La Figure 5 illustre un flux de travail de base pour publier une application à l'aide de l'API Batch Apps, puis envoyer des travaux à l'application activée par le service Batch.

![Application publishing workflow][app_pub_workflow]

**Figure 5. Flux de travail de publication et d'exécution d'une application avec Batch Apps**

1.	Préparez une **image d'application**. Il s'agit d'un fichier .zip contenant les fichiers exécutables d'application existants et les fichiers de prise en charge requis. Il peut s'agir des mêmes exécutables que ceux que vous exécutez dans une batterie de serveurs ou un cluster classique.
2.	Créez un fichier .zip de l'**assembly cloud** qui appellera et répartira vos charges de travail vers le service Batch. Il contient deux composants disponibles via le Kit de développement logiciel (SDK) :

	a. **Outil de fractionnement du travail** - Celui-ci décompose un travail en tâches pouvant être traitées de manière indépendante. Par exemple, dans un scénario d'animation, l'outil de fractionnement du travail prend un travail de rendu vidéo et le décompose en images individuelles.

	b. **Processeur de tâches** - Celui-ci appelle l'application exécutable pour une tâche donnée. Par exemple, dans un scénario d'animation, le processeur de tâches appelle un programme de rendu pour restituer l'image unique spécifiée par la tâche. 

3.	Utilisez les outils de développement ou les API Batch Apps pour télécharger les fichiers .zip préparés lors des deux étapes précédentes dans un compte de stockage Azure. Ces fichiers doivent être situés dans le compte de stockage afin que le service Batch puisse y accéder. Cela est généralement effectué une seule fois par application, par un administrateur de service.
4.	Fournissez un moyen d'envoyer des travaux au service d'application activé dans Azure. Il peut s'agir d'un plug-in dans l'interface utilisateur de votre application, un portail web ou un service sans assistance au sein de votre système principal. Des exemples sont disponibles avec le Kit de développement logiciel (SDK) pour illustrer les diverses options. 

	Pour exécuter un travail :

	a. Téléchargez les fichiers d'entrée (comme données ou images sources) spécifiques du travail de l'utilisateur. Ils doivent être situés dans le compte de stockage afin que le service Batch puisse y accéder.

	b. Envoyez un travail avec les paramètres requis et la liste de fichiers.
	
	c. Surveillez la progression du travail à l'aide des API ou du portail Batch Apps.
	
	d. Téléchargez les sorties.
	
<h2 id="BKMK_Resources">Ressources supplémentaires</h2>

* [Prise en main de la bibliothèque Azure Batch pour .NET](http://azure.microsoft.com/fr-fr/documentation/articles/batch-dotnet-get-started/)
* [Référence de l'API REST Azure Batch](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [Référence de l'API REST Azure Batch Apps](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[TVM_pool]: ./media/batch-technical-overview/TVM_pool.png
[job_task]: ./media/batch-technical-overview/job_task.png
[account_portal]: ./media/batch-technical-overview/account_portal.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png
