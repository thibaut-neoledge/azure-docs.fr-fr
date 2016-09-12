<properties
	pageTitle="Installation et gestion facilitées des applications dans Azure Batch | Microsoft Azure"
	description="Utilisez la fonctionnalité de packages d’applications d’Azure Batch pour gérer facilement plusieurs applications et versions pour l’installation sur des nœuds de calcul Batch."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="08/25/2016"
	ms.author="marsma" />

# Déploiement d’applications avec des packages d’applications Azure Batch

La fonctionnalité de packages d’application d’Azure Batch permet de gérer facilement les applications de tâche et leur déploiement sur les nœuds de calcul dans votre pool. Grâce aux packages d’application, vous pouvez charger et gérer plusieurs versions des applications que vos tâches exécutent, notamment leurs fichiers de prise en charge. Vous pouvez ensuite déployer automatiquement une ou plusieurs de ces applications sur les nœuds de calcul de votre pool.

Dans cet article, vous allez apprendre à charger et gérer des packages d’application à l’aide du Portail Azure. Vous apprendrez ensuite à les installer sur les nœuds de calcul d’un pool au moyen de la bibliothèque [Batch .NET][api_net].

> [AZURE.NOTE] La fonctionnalité de packages d’applications décrite ici remplace la fonctionnalité « Batch Apps » disponible dans les versions précédentes du service.

## Configuration requise des packages d’application

Pour utiliser les packages d’application, vous devez commencer par [lier un compte de stockage Azure](#link-a-storage-account) à votre compte Batch.

La fonctionnalité de packages d’application abordée dans cet article est *uniquement* compatible avec les pools Batch créés après le 10 mars 2016. Les packages d’applications ne seront pas déployés sur les nœuds de calcul dans des pools créés avant cette date.

Cette fonctionnalité a été introduite dans la version 2015-12-01.2.2 de [l’API REST Batch][api_rest] et dans la version 3.1.0 de la bibliothèque [Batch .NET][api_net] correspondante. Nous vous recommandons de toujours utiliser la dernière version de l’API lorsque vous utilisez Batch.

> [AZURE.IMPORTANT] Pour l’instant, seuls les pools *CloudServiceConfiguration* prennent en charge les packages d’application. Vous ne pouvez pas utiliser de packages d’application dans des pools créés à l’aide d’images VirtualMachineConfiguration. Pour plus d’informations sur les deux configurations, voir la section [Configuration de la machine virtuelle](batch-linux-nodes.md#virtual-machine-configuration) de l’article [Configurer des nœuds de calcul Linux dans des pools Azure Batch](batch-linux-nodes.md).

## Concernant les applications et les packages d’applications

Dans Azure Batch, une *application* fait référence à un jeu de versions de fichiers binaires automatiquement téléchargeable sur les nœuds de calcul de votre pool. Un *package d’application* désigne un *jeu spécifique* de ces fichiers binaires et représente une *version* donnée de l’application.

![Diagramme détaillé sur les applications et les packages d’applications][1]

### Applications

Dans Batch, une application contient un ou plusieurs packages d’application et spécifie les options de configuration de l’application. Par exemple, une application peut indiquer la version par défaut du package d’application à installer sur les nœuds de calcul, et préciser si ses packages peuvent être mis à jour ou supprimés.

### Packages d’applications

Un package d’application est un fichier .zip contenant les fichiers binaires de l’application et les fichiers de prise en charge qui sont requis pour l’exécution par vos tâches. Chaque package d’application représente une version spécifique de l’application.

Vous pouvez spécifier les packages d’applications au niveau du pool et des tâches. Lorsque vous créez un pool ou une tâche, vous pouvez spécifier un ou plusieurs de ces packages et, le cas échéant, une version.

* **Les packages d’application du pool** sont déployés sur *tous* les nœuds dans le pool. Les applications sont déployées lorsqu’un nœud rejoint un pool, et lorsqu’il est redémarré ou réinitialisé.

    Les packages d’application de pool sont appropriés lorsque tous les nœuds dans un pool exécutent les tâches d’un travail. Vous pouvez spécifier un ou plusieurs packages d’application lorsque vous créez un pool, et vous pouvez ajouter ou mettre à jour les packages d’un pool existant. Si vous mettez à jour les packages d’application d’un pool existant, vous devez redémarrer ses nœuds pour installer le nouveau package.

* **Les packages d’application de tâche** sont déployés uniquement sur un nœud de calcul programmé pour exécuter une tâche, juste avant d’exécuter la ligne de commande de la tâche. Si le package d’application spécifié et la version sont déjà sur le nœud, il n’est pas redéployé et le package existant est utilisé.

    Les packages d’application de tâche sont utiles dans les environnements de pool partagé, où différentes travaux sont exécutés sur un même pool et le pool n’est pas supprimé lorsqu’un travail est terminé. Si votre travail présente moins de tâches que le pool ne contient de nœuds, les packages d’applications au niveau des tâches peuvent réduire le transfert de données, votre application n’étant déployée que sur les nœuds exécutant les tâches.

    Les autres scénarios pouvant tirer parti des packages d’application de tâche sont les travaux utilisant une application particulièrement importante, mais uniquement pour un petit nombre de tâches. Par exemple, une étape de prétraitement ou une tâche de fusion, où l’application de prétraitement ou de fusion est lourde.

> [AZURE.IMPORTANT] Il existe des restrictions concernant le nombre d’applications et de packages d’applications dans un compte Batch, ainsi que sur la taille maximale des packages d’applications. Pour plus d’informations sur ces limites, voir [Quotas et limites pour le service Azure Batch](batch-quota-limit.md).

### Avantages des packages d’applications

Les packages d’application peuvent simplifier le code de votre solution Batch et alléger les coûts requis par la gestion des applications exécutées par vos tâches.

La tâche de démarrage de votre pool ne doit pas nécessairement spécifier une longue liste de fichiers de ressources individuels à installer sur les nœuds. Vous n’êtes pas obligé de gérer manuellement plusieurs versions de vos fichiers dans le stockage Azure ni sur vos nœuds. En outre, inutile de vous soucier de la génération [d’URL SAP](../storage/storage-dotnet-shared-access-signature-part-1.md) pour fournir l’accès aux fichiers dans votre compte de stockage. Batch fonctionne en arrière-plan avec le stockage Azure pour stocker des packages d’application et les déployer sur les nœuds de calcul.

## Téléchargement et gestion des applications

Vous pouvez utiliser le [portail Azure][portal] ou la bibliothèque [Batch Management .NET](batch-management-dotnet.md) pour gérer les packages d’application dans votre compte Batch. Dans les sections suivantes, nous allons commencer par lier un compte de stockage, puis nous aborderons l’ajout d’applications et de packages et leur gestion avec le portail.

### Liaison d’un compte de stockage

Pour utiliser les packages d’application, vous devez commencer par lier un compte Azure Storage à votre compte Batch. Si vous n’avez pas encore configuré de compte de stockage pour votre compte Batch, le portail Azure affiche un avertissement la première fois que vous cliquez sur la vignette **Applications** du panneau **Compte Batch**.

> [AZURE.IMPORTANT] Le service Batch ne prend actuellement en charge *que* le type de compte de stockage à **usage général**, comme décrit à l’étape 5, [Créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account), dans [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md). Quand vous liez un compte de stockage Azure à votre compte Batch, liez *uniquement* un compte de stockage **à usage général**.

![Avertissement Aucun compte de stockage configuré dans le portail Azure][9]

Le service Batch utilise le compte de stockage associé pour le stockage et la récupération des packages d’applications. Une fois que vous avez lié les deux comptes, Batch peut déployer automatiquement les packages stockés dans le compte de stockage lié sur vos nœuds de calcul. Dans le panneau **Avertissement**, cliquez sur **Paramètres du compte de stockage**, puis cliquez sur **Compte de stockage** dans le panneau **Compte de stockage** pour lier un compte de stockage à votre compte Batch.

![Panneau Sélectionner un compte de stockage dans le portail Azure][10]

Nous vous recommandons de créer un compte de stockage *spécifiquement* destiné à être utilisé avec votre compte Batch et de le sélectionner ici. Pour plus d’informations sur la création d’un compte de stockage, voir la section « Créer un compte de stockage » de l’article [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md). Après avoir créé un compte de stockage, vous pouvez le lier à votre compte Batch à l’aide du panneau **Compte de stockage**.

> [AZURE.WARNING] Étant donné que Batch utilise le stockage Azure pour stocker vos packages d’application, vous êtes [facturé comme d’habitude][storage_pricing] pour les données d’objet blob de blocs. Veillez à prendre en compte la taille et le nombre de vos packages d’applications et à supprimer régulièrement les packages obsolètes pour réduire les coûts.

### Affichage des applications en cours

Pour visualiser les applications dans votre compte Batch, cliquez sur la vignette **Applications** du panneau **Compte Batch**.

![Mosaïque Applications][2]

Cette opération ouvre le panneau **Applications** :

![Liste des applications][3]

Le panneau **Applications** affiche l’ID de chaque application dans votre compte et les propriétés suivantes :

* **Packages** : le nombre de versions associées à cette application.
* **Version par défaut** : la version qui sera installée si vous ne spécifiez pas de version lorsque vous définissez l’application pour un pool. Ce paramètre est facultatif.
* **Autoriser les mises à jour** : la valeur spécifiant si les mises à jour, les suppressions et les ajouts de packages sont autorisés. Si ce paramètre présente la valeur **Non**, les mises à jour et les suppressions de packages sont désactivées pour l’application. Seules les nouvelles versions de package d’application pourront être ajoutées. La valeur par défaut de ce paramètre est **Oui**.

### Affichage des détails de l’application

Cliquez sur une application du panneau **Applications** pour ouvrir le panneau contenant les détails de cette application.

![Détails de l’application][4]

Dans le panneau des détails de l’application, vous pouvez configurer les paramètres suivants pour votre application.

* **Autoriser les mises à jour** : permet de spécifier si ses packages d’application peuvent être mis à jour ou supprimés. Voir la section « Mettre à jour ou supprimer un package d’application » dans la suite de cet article.
* **Version par défaut** : permet de spécifier un package d’application par défaut à déployer sur les nœuds de calcul.
* **Nom d’affichage** : permet de spécifier un nom « convivial » que votre solution Batch peut utiliser pendant l’affichage d’informations sur l’application, comme dans l’interface utilisateur d’un service que vous offrez à vos clients par le biais de Batch.

### Ajout d’une application

Pour créer une application, ajoutez un package d’application et spécifiez un nouvel ID d’application unique. Le premier package d’application que vous ajoutez avec le nouvel ID d’application créera également l’application.

Dans le panneau **Applications**, cliquez sur **Ajouter** pour ouvrir le panneau **Nouvelle application**.

![Panneau Nouvelle application dans le portail Azure][5]

Le panneau **Nouvelle application** contient les champs ci-après pour vous permettre de spécifier les paramètres de la nouvelle application et du nouveau package d’application.

**ID d’application**

Ce champ spécifie l’ID de votre nouvelle application, qui est soumis aux règles de validation des ID Azure Batch standard :

* Peut contenir n’importe quelle combinaison de caractères alphanumériques, y compris des tirets et des traits de soulignement.
* Ne peut pas contenir plus de 64 caractères.
* Doit être unique dans le compte Batch.
* Préserve et ne respecte pas la casse.

**Version**

Spécifie la version du package d’application que vous téléchargez. Les chaînes de version sont soumises aux règles de validation suivantes :

* Peut contenir n’importe quelle combinaison de caractères alphanumériques, y compris des tirets, des traits de soulignement et des points.
* Ne peut pas contenir plus de 64 caractères.
* Doit être unique dans l’application.
* Préservation et non-respect de la casse.

**Package d’application**

Ce champ spécifie le fichier .zip contenant les fichiers binaires de l’application et les fichiers de prise en charge qui sont requis pour l’exécution de l’application. Cliquez sur la zone **Sélectionner un fichier** ou sur l’icône de dossier pour rechercher et sélectionner un fichier .zip contenant les fichiers de votre application.

Après avoir sélectionné un fichier, cliquez sur **OK** pour commencer le chargement dans le stockage Azure. Lorsque l’opération de chargement est terminée, vous en êtes averti et le panneau se ferme. Selon la taille du fichier que vous chargez et la vitesse de votre connexion réseau, cette opération peut prendre un certain temps.

> [AZURE.WARNING] Ne fermez pas le panneau **Nouvelle application** avant que l’opération de chargement soit terminée. La fermeture du panneau met fin au processus de chargement.

### Ajout d’un package d’application

Pour ajouter une nouvelle version de package d’application pour une application existante, sélectionnez une application dans le panneau **Applications**, cliquez sur **Packages**, puis cliquez sur **Ajouter** pour ouvrir le panneau **Ajouter un package**.

![Panneau Ajouter un package d’application dans le portail Azure][8]

Comme vous pouvez le voir, les champs correspondent à ceux du panneau **Nouvelle application**, mais la zone **ID d’application** est désactivée. Comme vous l’avez fait pour la nouvelle application, spécifiez la **version** de votre nouveau package, accédez au fichier .zip de votre **package d’application**, puis cliquez sur **OK** pour charger le package.

### Mettre à jour ou supprimer un package d’application

Pour mettre à jour ou supprimer un package d’application existant, ouvrez le panneau des détails de l’application, cliquez sur **Packages** pour ouvrir le panneau **Packages**, cliquez sur les **points de suspension** dans la ligne du package d’application que vous voulez modifier, puis sélectionnez l’action à exécuter.

![Mettre à jour ou supprimer un package dans le portail Azure][7]

**Mettre à jour**

Quand vous cliquez sur **Mettre à jour**, le panneau *Mettre à jour le package* s’affiche. Ce panneau est semblable au panneau *Nouveau package d’application* ; toutefois, seul le champ de sélection du package est activé, ce qui vous permet de spécifier un nouveau fichier ZIP à charger.

![Panneau Mettre à jour un package dans le portail Azure][11]

**Supprimer**

Lorsque vous cliquez sur **Supprimer**, vous êtes invité à confirmer la suppression de la version du package, puis Batch supprime le package du stockage Azure. Si vous supprimez la version par défaut d’une application, le paramètre **Version par défaut** est supprimé de l’application.

![Supprimer l’application][12]

## Installation d’applications sur des nœuds de calcul

Maintenant que vous avez vu comment gérer les packages d’application avec le portail Azure, nous pouvons discuter de leur déploiement sur les nœuds de calcul et leur exécution avec les tâches Batch.

### Installation des packages d’application de pool

Pour installer un package d’application sur les nœuds de calcul d’un pool, spécifiez au moins une *référence* de package d’application pour le pool. Les packages d’applications que vous spécifiez pour un pool sont installés sur chaque nœud de calcul lorsque ce nœud rejoint le pool, et lorsque le nœud est redémarré ou réinitialisé.

Dans Batch .NET, spécifiez une ou plusieurs propriétés [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] lorsque vous créez un pool ou pour un pool existant. La classe [ApplicationPackageReference][net_pkgref] spécifie un ID et la version d’une application à installer sur les nœuds de calcul d’un pool.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

>[AZURE.IMPORTANT] Si un déploiement de package d’application échoue pour une raison quelconque, le service Batch marque le nœud comme [inutilisable][net_nodestate], et aucune tâche ne sera planifiée sur ce nœud. Dans ce cas, vous devez **redémarrer** le nœud pour relancer le déploiement du package. Le fait de redémarrer le nœud réactive également la planification des tâches sur le nœud.

### Installation des packages d’application de tâche

À l’instar de l’installation sur un pool, vous spécifiez les *références* de package d’application pour une tâche. Lorsqu’une tâche est planifiée pour s’exécuter sur un nœud, le package est téléchargé et extrait juste avant que la ligne de commande de la tâche soit exécutée. Si une version et un package spécifiés sont déjà installés sur le nœud, le package n’est pas téléchargé et le package existant est utilisé.

Pour installer un package d’application de tâche, configurer la propriété [CloudTask][net_cloudtask].[ApplicationPackageReferences][net_cloudtask_pkgref] de tâche :

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## Exécution des applications installées

Les packages que vous avez spécifiés pour un pool ou une tâche sont téléchargés et extraits dans un répertoire nommé au sein du `AZ_BATCH_ROOT_DIR` du nœud. Batch crée également une variable d’environnement qui contient le chemin d’accès au répertoire nommé. Vos lignes de commande de tâche utilisent cette variable d’environnement lors du référencement de l’application sur le nœud. La réponse est au format suivant :

`AZ_BATCH_APP_PACKAGE_APPLICATIONID#version`

`APPLICATIONID` et `version` sont des valeurs qui correspondent à la version de l’application et du package que vous avez spécifiées pour le déploiement. Par exemple, si vous avez spécifié que la version 2.7 de l’application *blender* doit être installée, vos lignes de commande de tâche utiliseraient cette variable d’environnement pour accéder à ses fichiers :

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Si vous spécifiez une version par défaut pour une application, vous pouvez omettre le suffixe de version. Par exemple, si vous définissez « 2.7 » comme version par défaut pour l’application *blender*, vos tâches peuvent référencer la variable d’environnement suivante et exécuter la version 2.7 :

`AZ_BATCH_APP_PACKAGE_BLENDER`

L’extrait de code suivant montre un exemple de ligne de commande de tâche qui lance la version par défaut de l’application *blender* :

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Pour plus d’informations sur les paramètres d’environnement de nœud de calcul, voir la section [Paramètres d’environnement des tâches](batch-api-basics.md#environment-settings-for-tasks) de l’article [Présentation des fonctionnalités du service Batch pour les développeurs](batch-api-basics.md).

## Mise à jour des packages d’applications d’un pool

Si un pool existant a déjà été configuré avec un package d’application, vous pouvez spécifier un nouveau package pour le pool. Si vous spécifiez une nouvelle référence de package pour un pool, les points suivants s’appliquent :

* Tous les nouveaux nœuds rejoignant le pool, ainsi que n’importe quel nœud existant redémarré ou réinitialisé, installeront le package nouvellement spécifié.
* Les nœuds de calcul qui sont déjà dans le pool lorsque vous mettez à jour les références du package n’installent pas automatiquement le nouveau package d’application. Ces nœuds de calcul doivent être redémarrés ou réinitialisés pour recevoir le nouveau package.
* Lorsqu’un nouveau package est déployé, les variables d’environnement créées reflètent les références du nouveau package d’application.

Dans cet exemple, le pool existant comporte la version 2.7 de l’application *blender* configurée comme l’une de ses propriétés [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]. Pour mettre à jour les nœuds du pool avec la version 2.76b, spécifiez une nouvelle classe [ApplicationPackageReference][net_pkgref] avec la nouvelle version, puis validez la modification.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Maintenant que la nouvelle version (version 2.76b) a été configurée, elle sera déployée sur tous les *nouveaux* nœuds qui rejoignent le pool. Pour installer la version 2.76b sur des nœuds qui figurent *déjà* dans le pool, redémarrez ou réinitialisez ces derniers. Notez que les nœuds redémarrés conservent les fichiers des déploiements précédents du package.

## Liste des applications dans un compte Batch

Vous pouvez répertorier les applications et leurs packages dans un compte Batch à l’aide de la méthode [ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries].

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## Conclusion

Grâce aux packages d’application, vous pouvez aider vos clients à sélectionner les applications pour leurs travaux et à spécifier la version exacte à utiliser lors du traitement de travaux avec votre service Batch. Vous pouvez également fournir à vos clients la possibilité de télécharger et d’effectuer le suivi de leurs propres applications dans votre service.

## Étapes suivantes

* [L’API REST Batch][api_rest] prend également en charge l’utilisation de packages d’application. Par exemple, pour plus d’informations sur la spécification des packages à installer à l’aide de l’API REST, voir l’élément [applicationPackageReferences][rest_add_pool_with_packages] de l’article [Ajouter un pool à un compte][rest_add_pool]. Pour plus de détails sur l’obtention d’informations sur l’application à l’aide de l’API REST Batch, voir [Applications][rest_applications].

* Découvrez comment [gérer les quotas et les comptes Azure Batch avec Batch Management .NET](batch-management-dotnet.md) par programme. La bibliothèque [Batch Management .NET][api_net_mgmt] peut activer les fonctionnalités de création et de suppression de compte pour votre application ou service Batch.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagramme détaillée des packages d’applications"
[2]: ./media/batch-application-packages/app_pkg_02.png "Mosaïque Applications dans le portail Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Panneau Application dans le portail Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Panneau Détails de l’application dans le portail Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Panneau Nouvelle application dans le portail Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Liste déroulante Mettre à jour ou supprimer des packages dans le portail Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Panneau Nouveau package d’application dans le portail Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Alerte Aucun compte de stockage lié"
[10]: ./media/batch-application-packages/app_pkg_10.png "Panneau Sélectionner un compte de stockage dans le portail Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Panneau Mettre à jour un package dans le portail Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Boîte de dialogue de confirmation de la suppression d’un package dans le portail Azure"

<!---HONumber=AcomDC_0831_2016-->