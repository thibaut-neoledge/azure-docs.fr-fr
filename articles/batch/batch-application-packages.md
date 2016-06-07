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
	ms.date="05/20/2016"
	ms.author="marsma" />

# Déploiement d’applications avec des packages d’applications Azure Batch

La fonctionnalité packages d’applications d’Azure Batch offre une gestion facile et permet le déploiement d’applications sur les nœuds de calcul dans votre pool. Avec les packages d’applications, vous pouvez télécharger et gérer plusieurs versions des applications exécutées par vos tâches, et notamment les fichiers binaires et fichiers de prise en charge, puis déployer automatiquement une ou plusieurs de ces applications sur les nœuds de calcul dans le pool.

Dans cet article, vous allez apprendre à télécharger et à gérer des packages d’applications à l’aide du portail Azure, puis à les installer sur les nœuds de calcul d’un pool à l’aide de la bibliothèque [Batch .NET][api_net].

> [AZURE.NOTE] La fonctionnalité de packages d’applications décrite ici remplace la fonctionnalité « Batch Apps » disponible dans les versions précédentes du service.

## Configuration requise des packages d’applications

La fonctionnalité de packages d’applications abordée dans cet article est compatible *uniquement* avec les pools Batch créés après le 10 mars 2016. Les packages d’applications ne seront pas déployés sur les nœuds de calcul dans des pools créés avant cette date.

Cette fonctionnalité a été introduite dans l’[API REST Batch][api_rest] version 2015-12-01.2.2 et la version 3.1.0 de la bibliothèque [Batch .NET][api_net] correspondante. Nous vous recommandons de toujours utiliser la dernière version de l’API lorsque vous utilisez Batch.

> [AZURE.IMPORTANT] Actuellement, les packages d’applications ne sont pris en charge que par les pools créés avec **CloudServiceConfiguration**. Vous ne pouvez pas utiliser de packages d’applications dans des pools créés avec des images VirtualMachineConfiguration. Pour plus d’informations sur les deux configurations, consultez la section [Configuration de la machine virtuelle](batch-linux-nodes.md#virtual-machine-configuration) dans [Configurer des nœuds de calcul Linux dans des pools Azure Batch](batch-linux-nodes.md).

## Concernant les applications et les packages d’applications

Dans Azure Batch, une **application** fait référence à un jeu de versions binaires qui peut être téléchargé automatiquement sur les nœuds de calcul dans votre pool. Un **package d’application** fait référence à un *ensemble spécifique* de ces fichiers binaires et représente une *version* donnée de l’application.

![Diagramme détaillé sur les applications et les packages d’applications][1]

### Applications

Une application dans Batch contient un ou plusieurs packages d’applications. Elle spécifie les options de configuration de l’application, comme la version par défaut du package d’application à installer sur les nœuds de calcul, et si ses packages peuvent être mis à jour ou supprimés.

### Packages d’applications

Un package d’application est un fichier ZIP contenant les fichiers binaires de l’application et les fichiers de prise en charge requis pour l’exécution par vos tâches. Chaque package d’application représente une version spécifique de l’application. Lorsque vous créez un pool dans le service Batch, vous pouvez spécifier une ou plusieurs de ces applications et (éventuellement) une version, et les packages d’applications correspondants seront automatiquement téléchargés et extraits sur chaque nœud lorsque celui-ci rejoint le pool.

> [AZURE.IMPORTANT] Il existe des restrictions concernant le nombre d’applications et de packages d’applications dans un compte Batch, ainsi que sur la taille maximale des packages d’applications. Consultez la page [Quotas et limites pour le service Azure Batch](batch-quota-limit.md) pour obtenir plus d’informations sur ces limites.

### Avantages des packages d’applications

Les packages d’applications peuvent simplifier le code dans votre solution Batch, mais aussi réduire les coûts requis par la gestion des applications exécutées par vos tâches.

Avec les packages d’applications, la tâche de démarrage de votre pool ne doit pas nécessairement spécifier une longue liste de fichiers de ressources individuels à installer sur les nœuds. Vous n’êtes pas obligé de gérer manuellement plusieurs versions de ces fichiers dans Azure Storage ou sur vos nœuds. Et inutile de vous soucier de la génération d’[URL SAP](../storage/storage-dotnet-shared-access-signature-part-1.md) pour fournir l’accès aux fichiers dans votre compte Azure Storage.

Batch gère les détails de l’utilisation d’Azure Storage en arrière-plan pour stocker et déployer les packages d’applications pour le calcul des nœuds, donc votre code et vos frais de gestion peuvent être simplifiés.

## Téléchargement et gestion des applications

À l’aide du portail Azure, vous pouvez ajouter, mettre à jour et supprimer des packages d’applications et configurer les versions par défaut pour chaque application.

Dans les sections suivantes, nous aborderons tout d’abord l’association d’un compte de stockage avec votre compte Batch, puis nous examinerons les fonctionnalités de gestion de package disponibles sur le portail Azure. Ensuite, vous allez apprendre à déployer ces packages sur des nœuds de calcul à l’aide de la bibliothèque [Batch .NET][api_net].

### Liaison d’un compte de stockage

Pour utiliser les packages d’applications, vous devez d’abord lier un compte Azure Storage à votre compte Batch. Si vous n’avez pas encore configuré de compte de stockage pour votre compte Batch, le portail Azure affiche un avertissement la première fois que vous cliquez sur la vignette *Applications* dans le panneau du compte Batch.

> [AZURE.IMPORTANT] Le service Batch prend actuellement en charge *uniquement* le type de compte de stockage à **usage général**, comme décrit à l’étape 5, [Créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account), de l’article [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md). Quand vous liez un compte Azure Storage à votre compte Batch, liez *uniquement* un compte de stockage à **usage général**.

![Avertissement Aucun compte de stockage configuré dans le portail Azure][9]

Le service Batch utilise le compte de stockage associé pour le stockage et la récupération des packages d’applications. Après avoir lié les deux comptes, Batch peut déployer automatiquement les packages stockés dans le compte de stockage lié sur vos nœuds de calcul. Cliquez sur **Paramètres du compte de stockage** sur le panneau *Avertissement*, puis **Compte de stockage** sur le panneau *Compte de stockage* pour lier un compte de stockage à votre compte Batch.

![Panneau Sélectionner un compte de stockage dans le portail Azure][10]

Nous vous recommandons de créer un compte de stockage *spécifiquement* pour une utilisation avec votre compte Batch et de le sélectionner ici. Pour obtenir des détails sur la création d’un compte de stockage, consultez « Créer un compte de stockage » sous [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md). Une fois que vous avez créé un compte de stockage, vous pouvez le lier à votre compte Batch à l’aide du panneau *Compte de stockage*.

> [AZURE.WARNING] Étant donné que Batch stocke vos packages d’applications à l’aide d’Azure Storage, vous êtes [facturé comme d’habitude][storage_pricing] pour les données d’objets blob de blocs. Veillez à prendre en compte la taille et le nombre de vos packages d’applications et à supprimer régulièrement les packages obsolètes pour réduire les coûts.

### Affichage des applications en cours

Pour afficher les applications dans votre compte Batch, cliquez sur la vignette **Applications** dans le panneau du compte Batch.

![Mosaïque Applications][2]

Cette opération ouvre le panneau *Applications* :

![Liste des applications][3]

Le panneau *Applications* affiche l’ID de chaque application dans votre compte, ainsi que les propriétés suivantes :

* **Packages** : le nombre de versions associées à cette application.
* **Version par défaut** : si vous ne spécifiez pas de version au moment de la définition de l’application pour un pool, cette version est installée. Ce paramètre est facultatif.
* **Autoriser les mises à jour** : si cette valeur est définie sur *Non*, les mises à jour et les suppressions de package sont désactivées pour l’application. Seules de nouvelles versions du package d’application peuvent être ajoutées. La valeur par défaut est *Oui*.

### Affichage des détails de l’application

Cliquez sur une application dans le panneau *Applications* pour afficher le panneau des détails pour cette application.

![Détails de l’application][4]

Dans le panneau des détails de l’application, vous pouvez configurer les paramètres suivants pour votre application.

* **Autoriser les mises à jour** : permet de spécifier si ses packages d’applications peuvent être mis à jour ou supprimés (voir « Mise à jour ou suppression d’un package d’application » ci-dessous).
* **Version par défaut** : permet de spécifier un package d’application par défaut à déployer sur les nœuds de calcul.
* **Nom d’affichage** : il s’agit d’un nom « convivial » que votre solution Batch peut utiliser pendant l’affichage d’informations sur l’application, comme dans l’interface utilisateur d’un service que vous fournissez à vos clients via Batch.

### Ajout d’une application

Pour créer une nouvelle application, ajoutez un package d’application à l’aide d’un ID d’application nouveau et unique. Le premier package d’application que vous ajoutez à l’aide du nouvel ID d’application crée également la nouvelle application.

Cliquez sur **Ajouter** sur le panneau *Applications* pour ouvrir le panneau *Nouvelle application*.

![Panneau Nouvelle application dans le portail Azure][5]

Le panneau *Nouvelle application* contient les champs suivants pour spécifier les paramètres de votre nouvelle application et du package d’application.

**ID d’application**

Spécifie l’ID de votre nouvelle application, qui est soumis aux règles de validation des ID Azure Batch standard :

* Peut contenir n’importe quelle combinaison de caractères alphanumériques, y compris des tirets et des traits de soulignement.
* Ne peut pas contenir plus de 64 caractères.
* Doit être unique dans le compte Batch.
* Préservation et non-respect de la casse.

**Version**

Spécifie la version du package d’application que vous téléchargez. Les chaînes de version sont soumises aux règles de validation suivantes :

* Peut contenir n’importe quelle combinaison de caractères alphanumériques, y compris des tirets, des traits de soulignement et des points.
* Ne peut pas contenir plus de 64 caractères.
* Doit être unique dans l’application.
* Préservation et non-respect de la casse.

**Package d’application**

Spécifie le fichier ZIP contenant les fichiers binaires de l’application et les fichiers de prise en charge requis pour l’exécution de l’application. Cliquez sur la zone de texte **Sélectionner un fichier** ou sur l’icône de dossier pour rechercher et sélectionner un fichier ZIP contenant les fichiers de votre application.

Une fois que vous avez sélectionné un fichier, cliquez sur **OK** pour commencer le chargement sur Azure Storage. Lorsque l’opération de téléchargement est terminée, vous êtes averti et le panneau se ferme. Notez que, selon la taille du fichier que vous téléchargez et la vitesse de votre connexion réseau, cette opération peut prendre du temps.

> [AZURE.WARNING] Ne fermez pas le panneau *Nouvelle application* avant que l’opération de chargement soit terminée. Cela interrompt le processus de téléchargement.

### Ajout d’un package d’application

Pour ajouter une nouvelle version de package d’application pour une application existante, sélectionnez une application dans le panneau *Applications*, cliquez sur **Packages**, puis cliquez sur **Ajouter** pour afficher le panneau *Ajouter un package*.

![Panneau Ajouter un package d’application dans le portail Azure][8]

Comme vous pouvez le voir, les champs correspondent à ceux du panneau *Nouvelle application*, à l’exception de la zone de texte « ID d’application » qui est désactivée. Comme précédemment, spécifiez la **Version** pour votre nouveau package, accédez au fichier ZIP de votre **package d’application**, puis cliquez sur **OK** pour charger le package.

### Mise à jour ou suppression d’un package d’application

Pour mettre à jour ou supprimer un package d’application, ouvrez le panneau des détails de l’application, cliquez sur **Packages** pour afficher le panneau *Packages*, cliquez sur les **points de suspension** dans la ligne du package d’application que vous souhaitez modifier, puis sélectionnez l’action que vous souhaitez effectuer.

![Mettre à jour ou supprimer un package dans le portail Azure][7]

**Mettre à jour**

Quand vous cliquez sur **Mettre à jour**, le panneau *Mettre à jour le package* s’affiche. Ce panneau est similaire au panneau *Nouveau package d’application* ; toutefois, seul le champ de sélection du package est activé, ce qui vous permet de spécifier un nouveau fichier ZIP à charger.

![Panneau Mettre à jour un package dans le portail Azure][11]

**Supprimer**

Quand vous cliquez sur **Supprimer**, vous êtes invité à confirmer la suppression de la version du package. Batch supprime le package dans Azure Storage. Si vous supprimez la version par défaut d’une application, le paramètre de version par défaut est supprimé de l’application.

![Supprimer l’application][12]

## Installation d’applications sur des nœuds de calcul

Maintenant que nous avons couvert le téléchargement et la gestion des packages d’applications à l’aide du portail Azure, nous sommes prêts à discuter de leur déploiement sur les nœuds de calcul et de leur exécution avec des tâches Batch.

Pour installer un package d’application sur les nœuds de calcul dans un pool, indiquez au moins une *référence* de package d’application pour le pool. Pour ce faire, dans Batch .NET, ajoutez une ou plusieurs classes [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] à la création du pool ou à un pool existant.

La classe [ApplicationPackageReference][net_pkgref] spécifie un ID et la version d’une application à installer sur les nœuds de calcul d’un pool.

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

## Exécution des applications installées

Quand chaque nœud de calcul rejoint un pool (ou est redémarré ou réinitialisé), les packages que vous avez spécifiés sont téléchargés et extraits vers un répertoire nommé au sein de `AZ_BATCH_ROOT_DIR` sur le nœud. Batch crée également une variable d’environnement pour les lignes de commande de votre tâche à utiliser lors de l’appel des fichiers binaires applicatifs ; cette variable se conforme au schéma d’affectation de noms suivant :

`AZ_BATCH_APP_PACKAGE_appid#version`

Par exemple, si vous spécifiez que la version 2.7 de l’application *blender* doit être installée, vos tâches peuvent accéder à ses fichiers binaires en faisant référence à la variable d’environnement suivante dans la ligne de commande :

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Si votre application spécifie une version par défaut, vous pouvez référencer la variable d’environnement sans le suffixe de la chaîne de version. Par exemple, si vous aviez spécifié la version 2.7 par défaut pour l’application *blender* au sein du portail Azure, vos tâches peuvent faire référence à la variable d’environnement suivante :

`AZ_BATCH_APP_PACKAGE_BLENDER`

L’extrait de code suivant montre comment une tâche peut être configurée quand une version par défaut a été spécifiée pour l’application *blender*.

```csharp
string taskId = "blendertask01";
string commandLine = @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -my -command -args";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Consultez la rubrique « Paramètres d’environnement des tâches » dans [Aperçu des fonctionnalités Batch](batch-api-basics.md) pour plus d’informations sur les paramètres d’environnement du nœud de calcul.

## Mise à jour des packages d’applications d’un pool

Si un pool existant a déjà été configuré avec un package d’application, vous pouvez spécifier un nouveau package pour le pool. Si vous spécifiez une nouvelle référence de package pour un pool, les points suivants s’appliquent :

* Tous les nouveaux nœuds formant le pool installeront le package nouvellement spécifié, ainsi que n’importe quel nœud existant qui est redémarré ou reconfiguré.
* Les nœuds de calcul qui sont déjà dans le pool quand vous mettez à jour les références du package n’installent pas automatiquement le nouveau package d’application ; ils doivent être redémarrés ou réinitialisés pour recevoir le nouveau package.
* Quand un nouveau package est déployé, les variables d’environnement créées reflètent les nouvelles références du package d’application.

Dans cet exemple, le pool existant dispose de la version 2.7 de l’application *blender* configurée comme l’une de ses classes [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]. Pour mettre à jour les nœuds du pool avec la version 2.76b, spécifiez une nouvelle classe [ApplicationPackageReference][net_pkgref] avec la nouvelle version, puis validez la modification.

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

La nouvelle version (version 2.76b) étant configurée, elle sera déployée sur les *nouveaux* nœuds qui rejoignent le pool. Pour installer la version 2.76b sur les nœuds qui sont *déjà* dans le pool, redémarrez-les (ou réinitialisez-les). Notez que les nœuds redémarrés conservent les fichiers des déploiements précédents du package.

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

Avec les packages d’applications, vous pouvez plus facilement fournir à vos clients la possibilité de sélectionner les applications pour leurs tâches et spécifier la version exacte à utiliser, lors du traitement de tâches avec votre service Batch. Vous pouvez également fournir à vos clients la possibilité de télécharger et d’effectuer le suivi de leurs propres applications dans votre service.

## Étapes suivantes

* L’[API REST Batch][api_rest] prend également en charge l’utilisation de packages d’applications. Par exemple, consultez l’élément [applicationPackageReferences][rest_add_pool_with_packages] dans [Ajouter un pool à un compte][rest_add_pool] pour spécifier les packages à installer à l’aide de l’API REST. Consultez la page [Applications][rest_applications] pour plus de détails sur l’obtention d’informations sur l’application à l’aide de l’API REST Batch.

* Découvrez comment [gérer les quotas et les comptes Azure Batch avec Batch Management .NET](batch-management-dotnet.md) par programmation. La bibliothèque [Batch Management .NET][api_net_mgmt] peut activer les fonctionnalités de création et de suppression de compte pour votre application ou service Batch.

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
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
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

<!---HONumber=AcomDC_0525_2016-->