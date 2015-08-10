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
	ms.topic="get-started-article"
	ms.date="07/13/2015"
	ms.author="danlep"/>


#Vue d'ensemble technique d'Azure Batch
Azure Batch vous permet d'exécuter des applications de calculs complexes parallèles à grande échelle efficacement dans le cloud. Il s’agit d’un service de plateforme qui fournit la planification des tâches et la mise à l'échelle automatique d'une collection gérée de machines virtuelles pour exécuter les tâches. En utilisant le service Batch, vous pouvez configurer des charges de travail par lots à exécuter dans Azure à la demande ou sur planification, sans vous préoccuper de la complexité de la configuration et de la gestion d’un cluster HPC, de machines virtuelles ou d’un planificateur de travaux.

>[AZURE.NOTE]Pour utiliser Batch, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page [Création d’un compte Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/).


## Cas d'utilisation

Batch utilise l'élasticité et l'échelle du cloud pour vous aider avec le *traitement par lots* ou le *calcul par lots* et l’exécution d’un grand nombre de tâches similaires afin d’obtenir les résultats souhaités. Un programme de ligne de commande ou un script accepte un ensemble de fichiers de données comme entrée, traite les données dans une série de tâches et produit un ensemble de fichiers de sortie. Les fichiers de sortie peuvent être le résultat final ou une étape intermédiaire dans un flux de travail plus important.

Le calcul par lots est un modèle courant pour les organisations qui traitent, transforment et analysent de grandes quantités de données, de manière planifiée ou à la demande. Cela inclut le traitement de fin de cycle tel que la création de rapports sur les risques quotidiens auxquels doit faire face une banque ou une paie qui doit être effectuée sur la base d'une planification. Cela comprend également les applications d'ingénierie, scientifiques et d'entreprise à grande échelle qui ont généralement besoin des outils et ressources d'une grille ou d'un cluster de calcul. Il s'agit d'applications HPC traditionnelles telles que les simulations de dynamiques de fluides, ainsi que des charges de travail spécialisées dans des domaines allant de la création de contenu numérique aux services financiers, en passant par la recherche en sciences de la vie.

Le service Batch fonctionne bien avec des applications ou charges de travail intrinsèquement parallèles, qui se prêtent à l'exécution de tâches en parallèle sur plusieurs ordinateurs, comme des machines virtuelles de calcul gérées par le service Batch. Voir la Figure 1.

![Tâches parallèles][parallel]

**Figure 1. Tâches parallèles exécutées sur plusieurs ordinateurs**

Voici quelques exemples :

* Modélisation de risques financiers
* Rendu et traitement d'images
* Encodage et transcodage multimédia
* Analyse de séquence génétique
* Test de logiciels

Vous pouvez également utiliser le service Batch pour effectuer des calculs parallèles avec une étape de réduction à la fin et d'autres charges de travail parallèles plus compliquées.

>[AZURE.NOTE]À ce stade, vous pouvez uniquement exécuter des charges de travail Windows Server sur Batch. De plus, Batch ne prend pas actuellement en charge les applications MPI (Message Passing Interface).

## Scénarios pour les développeurs

Batch prend en charge plusieurs scénarios de développement pour vous aider à configurer et à exécuter vos charges de travail parallèles à grande échelle avec le service Batch. Ces scénarios tirent parti des API pour créer et gérer les pools de machines virtuelles (les nœuds de calcul) et planifier les projets et les tâches qui s'y exécutent. Consultez [Concepts de base concernant les API dans Azure Batch](batch-api-basics.md) pour en savoir plus sur les concepts relatifs à Batch.

Des scénarios développeur Batch habituels sont présentés dans les sections suivantes.

### Montée en charge d’une charge de travail parallèle

Utilisez l'API Batch pour une montée en charge d’un travail intrinsèquement parallèle comme le rendu d’image sur un pool comportant jusqu’à plusieurs milliers de cœurs de calcul. Au lieu de configurer un cluster de calcul ou d'écrire du code pour mettre en file d'attente et planifier vos travaux et de déplacer les données d’entrée et de sortie nécessaires, vous automatisez la planification des grands travaux de calcul et mettez à l'échelle un pool de machines virtuelles de calcul pour les exécuter. Vous pouvez écrire des applications clientes ou frontales pour exécuter des travaux et des tâches à la demande, selon une planification ou dans le cadre d'un plus grand flux de travail, géré par des outils tels qu’[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

La Figure 2 présente un flux de travail simplifié pour soumettre une application à un pool Batch dans lequel elle est distribuée pour le traitement.

![Flux de travail des éléments de travail][work_item_workflow]

**Figure 2 : Montée en charge d’une charge de travail parallèle sur Batch**

1.	Téléchargez les fichiers d'entrée (comme données ou images sources) nécessaires à un travail dans un compte de stockage Azure. Ils doivent être situés dans le compte de stockage afin que le service Batch puisse y accéder. Le service Batch charge des fichiers sur des nœuds de calcul lors de l'exécution de tâches.
2.	Téléchargez les fichiers binaires dépendants dans le compte de stockage, notamment le programme exécuté par la tâche et les assemblys dépendants. Ces fichiers doivent également être accessibles à partir du stockage et sont chargés dans les nœuds de calcul.
3.	Créez un pool de nœuds de calcul, en spécifiant les propriétés comme la taille de leur machine virtuelle et le système d'exploitation qu’ils s'exécutent. Vous pouvez également définir l’augmentation et la diminution du nombre de nœuds dans le pool en réponse à la charge de travail. Quand une tâche s'exécute, elle reçoit un nœud à partir de ce pool.
4.	Définissez un travail à exécuter sur le pool.
5.	Ajoutez des tâches au travail. Chaque tâche utilise le programme téléchargé pour traiter les informations à partir d'un fichier téléchargé.
6.	Exécutez l'application et surveillez les résultats de la sortie.


### Activation du cloud pour une application nécessitant beaucoup de ressources

Vous pouvez utiliser l'API Batch Apps en version préliminaire pour encapsuler une application existante afin qu’elle s'exécute en tant que service sur un pool de nœuds de calcul, que Batch gère en arrière-plan. Il peut s’agir d’une application qui s'exécute aujourd'hui sur les stations de travail clientes ou d’un cluster de calcul. Vous pouvez développer le service pour permettre aux utilisateurs de décharger le pic de travail sur le cloud, ou exécuter leur travail entièrement dans le cloud. L'infrastructure Batch Apps gère le déplacement des fichiers d'entrée et de sortie, le fractionnement des travaux en tâches, le traitement des tâches et des travaux et la persistance des données.

>[AZURE.IMPORTANT]Azure propose uniquement l'API Batch Apps sous forme de version préliminaire. Utilisez-la uniquement pour les projets de test ou de validation technique. Les fonctionnalités clés de Batch Apps seront intégrées dans l’API Batch dans les prochaines versions du service.

La Figure 3 illustre un flux de travail de base pour publier une application à l'aide de l'API Batch Apps, puis permettre à un utilisateur d’envoyer des travaux à l'application.

![Flux de travail de publication de l'application][app_pub_workflow]

**Figure 3 : Flux de travail pour la publication et l’exécution d’une application avec Batch Apps**

1.	Préparation d’une **image d'application** : un fichier zip contenant les exécutables de votre application existante et les fichiers de support dont ils ont besoin. Il peut s'agir des mêmes exécutables que ceux que vous exécutez dans une batterie de serveurs ou un cluster classique.
2.	Créez un fichier zip de l’**assembly cloud** qui appellera et distribuera vos charges de travail pour le service Batch. Il contient deux composants :

	a. **Fractionnement du travail** : décompose un travail en tâches qui peuvent être traitées indépendamment. Par exemple, dans un scénario d'animation, l'outil de fractionnement du travail prend un travail de rendu vidéo et le décompose en images individuelles.

	b. **Processeur de tâches** : appelle l'exécutable de l’application pour une tâche donnée. Par exemple, dans un scénario d'animation, le processeur de tâches appelle un programme de rendu pour restituer l'image unique spécifiée par la tâche.

3.	Utilisez les outils de développement ou l’API Batch Apps pour télécharger les fichiers .zip préparés durant les deux étapes précédentes vers un compte de stockage Azure. Ils doivent être situés dans le compte de stockage afin que le service Batch puisse y accéder. Cela est généralement effectué une seule fois par application, par un administrateur de services fédérés.
4.	Fournissez un moyen d'envoyer des travaux au service d'application activé dans Azure. Il peut s'agir d'un plug-in dans l'interface utilisateur de votre application, un portail web ou un service sans assistance au sein de votre système principal.

	Pour exécuter un travail :

	a. Téléchargez les fichiers d'entrée (comme données ou images sources) spécifiques du travail de l'utilisateur. Ils doivent être situés dans le compte de stockage afin que le service Batch puisse y accéder.

	b. Envoyez un travail avec les paramètres requis et la liste de fichiers.

	c. Surveillez la progression du travail à l'aide des API ou du portail Batch Apps.



## <a id="BKMK_Account">Compte Batch</a>
Vous devez créer un ou plusieurs **comptes Batch** uniques à utiliser et développer avec le service Batch. Toutes les requêtes que vous présentez au service Batch doivent être authentifiées en utilisant le nom du compte et sa clé d'accès.

Vous pouvez créer un compte Batch et gérer des clés d'accès pour ce compte dans le portail Azure en version préliminaire ou à l’aide des [cmdlets Batch PowerShell](batch-powershell-cmdlets-get-started.md).

Pour créer un compte Batch dans le portail :

1. Connectez-vous à la [version préliminaire du portail Azure](https://portal.azure.com).

2. Cliquez sur **New** > **Compute** > **Marketplace** > **Everything** puis saisissez *Batch* dans la zone de recherche.

	![Batch dans Marketplace][marketplace_portal]

3. Cliquez sur **Batch Service** dans les résultats de la recherche, puis cliquez sur **Create**.

4. Dans le panneau **New Batch Account**, entrez les informations suivantes :

	a. Dans **Nom de compte**, saisissez un nom unique à utiliser dans l'URL du compte Batch.

	>[AZURE.NOTE]Le nom du compte Batch doit être unique dans Azure, contenir entre 3 et 24 caractères, et utiliser des minuscules et des chiffres uniquement.

	b. Cliquez sur **Resource group** pour sélectionner un groupe de ressources existant pour le compte, ou créez-en un.

	c. Si vous avez plusieurs abonnements, cliquez sur **Abonnement** pour sélectionner un abonnement disponible où créer le compte.

	d. Dans **Location**, sélectionnez une région Azure dans laquelle Batch est disponible.

	![Création d’un compte Batch][account_portal]

5. Cliquez sur **Create** pour terminer la création du compte.


Une fois le compte créé, il apparaît dans le portail et vous pouvez gérer les clés d’accès et d’autres paramètres. Par exemple, cliquez sur l'icône de clé pour gérer les clés d'accès.

![Clés de compte de Batch][account_keys]

## Ressources supplémentaires

* [Prise en main de la bibliothèque Azure Batch pour .NET](batch-dotnet-get-started.md)
* [Outils et bibliothèques de développement Azure Batch](batch-development-libraries-tools.md)
* [Référence de l'API REST Azure Batch](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [Référence de l'API REST d’Azure Batch Apps](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[marketplace_portal]: ./media/batch-technical-overview/marketplace_batch.PNG
[account_portal]: ./media/batch-technical-overview/batch_acct_portal.png
[account_keys]: ./media/batch-technical-overview/account_keys.PNG
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png

<!---HONumber=July15_HO5-->