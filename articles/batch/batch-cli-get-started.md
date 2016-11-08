---
title: Prise en main de l’interface de ligne de commande Azure Batch | Microsoft Docs
description: Obtenez une brève introduction aux commandes Batch dans l’interface de ligne de commande Azure pour la gestion des ressources du service Azure Batch.
services: batch
documentationcenter: ''
author: mmacy
manager: timlt
editor: ''

ms.service: batch
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 09/30/2016
ms.author: marsma

---
# Prise en main de l’interface de ligne de commande Azure Batch
L’interface de ligne de commande Azure (Azure CLI) multiplateforme vous permet de gérer vos comptes et ressources Batch comme les pools, les travaux et les tâches dans des interfaces de commande Linux, Mac et Windows. Avec l’interface de ligne de commande Azure, vous pouvez effectuer nombre des tâches (et écrire leur script) que vous réalisez avec les API Batch, le portail Azure et les applets de commande PowerShell pour Batch.

Cet article est basé sur l’interface de ligne de commande Azure version 0.10.5.

## Composants requis
* [Installer l’interface de ligne de commande Microsoft Azure](../xplat-cli-install.md)
* [Connectez l’interface de ligne de commande Azure à votre abonnement Azure](../xplat-cli-connect.md)
* Basculez en **mode Resource Manager **: `azure config mode arm`

> [!TIP]
> Nous vous recommandons de mettre à jour fréquemment votre installation d’interface de ligne de commande Azure pour tirer parti des améliorations et des mises à jour de service.
> 
> 

## Aide relative aux commandes
Vous pouvez afficher le texte d’aide de toutes les commandes dans l’interface de ligne de commande Azure en ajoutant `-h` comme seule option après la commande. Par exemple :

* Pour obtenir l’aide de la commande `azure`, entrez : `azure -h`
* Pour obtenir la liste de toutes les commandes Batch dans l’interface de ligne de commande, utilisez : `azure batch -h`
* Pour obtenir de l’aide sur la création d’un compte Batch, entrez : `azure batch account create -h`

En cas de doute, utilisez l’option de ligne de commande `-h` pour obtenir de l’aide sur toute commande de l’interface de ligne de commande Azure.

## Création d’un compte Batch
Usage :

    azure batch account create [options] <name>

Exemple :

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

Crée un compte Batch avec les paramètres spécifiés. Vous devez spécifier au moins un emplacement, un groupe de ressources et un nom de compte. Si vous ne disposez pas déjà d’un groupe de ressources, créez-en un en exécutant `azure group create`, puis spécifiez l’une des régions Azure (par exemple, « États-Unis de l’Ouest ») pour l’option `--location`. Par exemple :

    azure group create --name "resgroup001" --location "West US"

> [!NOTE]
> Le nom du compte Batch doit être unique dans la région Azure dans laquelle le compte est créé. Il doit comporter 3 à 24 caractères, lesquels ne peuvent être que des caractères alphanumériques minuscules. Vous ne pouvez pas utiliser de caractères spéciaux tels que `-` ou `_` dans les noms de compte Batch.
> 
> 

### Compte de stockage lié (stockage automatique)
Vous pouvez (si vous le souhaitez) lier un compte de stockage **à usage général** à votre compte Batch lorsque vous le créez. La fonctionnalité des [packages d’application](batch-application-packages.md) de Batch utilise le stockage d’objets blob dans un compte de stockage général lié, comme la bibliothèque [Batch File Conventions .NET](batch-task-output.md). Ces fonctionnalités facultatives vous aideront à déployer les applications exécutées par vos tâches Batch et à conserver les données qu’elles produisent.

Pour lier un compte de stockage Azure existant à un nouveau compte Batch lorsque vous le créez, spécifiez l’option `--autostorage-account-id`. Cette option nécessite l’ID de ressource complet du compte de stockage.

Tout d’abord, affichez les détails de votre compte de stockage :

    azure storage account show --resource-group "resgroup001" "storageaccount001"

Utilisez ensuite la valeur **Url** pour l’option `--autostorage-account-id`. La valeur Url commence par « /subscriptions/ » et contient votre ID d’abonnement et le chemin d’accès à la ressource du compte de stockage :

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## Suppression d’un compte Batch
Usage :

    azure batch account delete [options] <name>

Exemple :

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

Supprime le compte Batch spécifié. À l’invite, confirmez que vous souhaitez supprimer le compte (l’exécution de la suppression du compte peut prendre un certain temps).

## Gérer les touches d’accès rapide au compte
Vous avez besoin d’une touche d’accès rapide pour [créer et modifier des ressources](#create-and-modify-batch-resources) dans votre compte Batch.

### Répertorier les touches d’accès rapide
Usage :

    azure batch account keys list [options] <name>

Exemple :

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

Répertorie les clés du compte Batch donné.

### Générer une nouvelle clé d'accès
Usage :

    azure batch account keys renew [options] --<primary|secondary> <name>

Exemple :

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

Régénère la clé spécifiée pour le compte Batch donné.

## Créer et modifier les ressources Batch
L’interface de ligne de commande Azure vous permet de créer, lire, mettre à jour et supprimer (CRUD) des ressources Batch telles que des pools, des nœuds de calcul, des travaux et des tâches. Pour effectuer ces opérations CRUD, vous avez besoin du nom de votre compte Batch, d’une touche d’accès rapide et d’un point de terminaison. Vous pouvez spécifier ces derniers avec les options `-a`, `-k` et `-u`, ou définir les [variables d’environnement](#credential-environment-variables) utilisées automatiquement par l’interface de ligne de commande (si renseignées).

### Variables d’environnement des informations d’identification
Vous pouvez définir les variables d’environnement `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY` et `AZURE_BATCH_ENDPOINT` au lieu de spécifier les options `-a`, `-k` et `-u` sur la ligne de commande de chaque commande exécutée. L’interface de ligne de commande Batch utilise ces variables (si elles sont définies) afin que vous puissiez omettre les options `-a`, `-k` et `-u`. Le reste de cet article part du principe que ces variables d’environnement sont utilisées.

> [!TIP]
> Dressez la liste de vos clés avec `azure batch account keys list`, et affichez le point terminaison du compte avec `azure batch account show`.
> 
> 

### Fichiers JSON
Lorsque vous créez des ressources Batch comme des pools et des travaux, vous pouvez spécifier un fichier JSON contenant la configuration de la nouvelle ressource au lieu de transmettre ses paramètres en tant qu’options de ligne de commande. Par exemple :

`azure batch pool create my_batch_pool.json`

Si vous pouvez effectuer de nombreuses opérations de création de ressources en utilisant uniquement des options de ligne de commande, certaines fonctionnalités requièrent un fichier au format JSON contenant les détails des ressources. Par exemple, vous devez utiliser un fichier JSON si vous souhaitez spécifier des fichiers de ressources pour une tâche de démarrage.

Pour trouver le fichier JSON requis pour créer une ressource, reportez-vous à l’article [Référence de l’API REST Batch][rest_api] sur MSDN. Chaque rubrique « Ajouter un *type de ressource* » contient un exemple de fichier JSON pour créer la ressource, que vous pouvez utiliser comme modèle pour vos fichiers JSON. Par exemple, vous trouverez un fichier JSON pour la création de pools dans l’article [Add a pool to an account (Ajouter un pool à un compte)][rest_add_pool].

> [!NOTE]
> Si vous spécifiez un fichier JSON lorsque vous créez une ressource, tous les autres paramètres que vous spécifiez sur la ligne de commande de cette ressource sont ignorés.
> 
> 

## Créer un pool
Usage :

    azure batch pool create [options] [json-file]

Exemple (configuration de la machine virtuelle) :

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

Exemple (configuration des services cloud) :

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Crée un pool de nœuds de calcul dans le service Batch.

Comme indiqué dans l’article [Aperçu des fonctionnalités Batch](batch-api-basics.md#pool), deux options s’offrent à vous lorsque vous sélectionnez un système d’exploitation pour les nœuds du pool : **Configuration de la machine virtuelle** et **Configuration des services cloud**. Utilisez les options `--image-*` pour créer des pools Configuration de la machine virtuelle, et `--os-family` pour créer des pools Configuration des services cloud. Vous ne pouvez pas spécifier à la fois les options `--os-family` et `--image-*`.

Vous pouvez spécifier des [packages d’application](batch-application-packages.md) de pool et la ligne de commande pour une [tâche de démarrage](batch-api-basics.md#start-task). Toutefois, pour spécifier des fichiers de ressources pour la tâche de démarrage, vous devez plutôt utiliser un [fichier JSON](#json-files).

Supprimez un pool avec :

    azure batch pool delete [pool-id]

> [!TIP]
> Recherchez dans la [liste des images de machine virtuelle](batch-linux-nodes.md#list-of-virtual-machine-images) les valeurs appropriées pour les options `--image-*`.
> 
> 

## Création d’un travail
Usage :

    azure batch job create [options] [json-file]

Exemple :

    azure batch job create --id "job001" --pool-id "pool001"

Ajoute un travail au compte Batch et spécifie le pool sur lequel ses tâches s’exécutent.

Supprimez un travail avec :

    azure batch job delete [job-id]

## Dresser la liste des pools, des travaux, des tâches et des autres ressources
Chaque type de ressource Batch prend en charge une commande `list` qui interroge votre compte Batch et répertorie les ressources de ce type. Par exemple, vous pouvez répertorier les pools de votre compte et les tâches d’un travail :

    azure batch pool list
    azure batch task list --job-id "job001"

### Listage efficace des ressources
Pour des interrogations plus rapides, vous pouvez spécifier des options de clause **select**, **filter** et **expand** pour les opérations `list`. Utilisez ces options pour limiter la quantité de données retournées par le service Batch. Comme tout le filtrage se produit côté serveur, seules les données qui vous intéressent sont transmises. Utilisez ces clauses pour économiser la bande passante (et par conséquent gagner du temps) lorsque vous effectuez des opérations de liste.

Par exemple, cette commande ne retourne que les pools dont les ID commencent par « renderTask » :

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

L’interface de ligne de commande Batch prend en charge les trois clauses gérées par le service Batch :

* `--select-clause [select-clause]` Retourne un sous-ensemble de propriétés pour chaque entité.
* `--filter-clause [filter-clause]` Retourne uniquement les entités qui correspondent à l’expression OData spécifiée.
* `--expand-clause [expand-clause]` Obtient les informations d’entité dans un seul appel REST sous-jacent. Pour le moment, la clause expand ne prend en charge que la propriété `stats`.

Pour en savoir plus sur les trois clauses et sur leur utilisation pour interroger des listes, consultez l’article [Interroger efficacement le service Azure Batch](batch-efficient-list-queries.md).

## Gestion des packages d’application
Les packages d’application permettent de déployer facilement des applications vers les nœuds de calcul dans vos pools. Avec l’interface de ligne de commande Azure, vous pouvez charger des packages d’application, gérer les versions des packages et supprimer des packages.

Pour créer une application et ajouter une version de package :

**Créez** une application :

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**Ajoutez** un package d’application :

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**Activez** le package :

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

Définissez la **version par défaut** pour l’application :

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### Déployer un package d’application
Vous pouvez spécifier un ou plusieurs packages d’application pour le déploiement lorsque vous créez un pool. Lorsque vous spécifiez un package lors de la création du pool, il est déployé sur chaque nœud lorsque le nœud rejoint le pool. Les packages sont également déployés lorsqu’un nœud est redémarré ou réinitialisé.

Spécifiez l’option `--app-package-ref` lors de la création d’un pool pour déployer un package d’application sur les nœuds du pool dès qu’ils rejoignent le pool. L’option `--app-package-ref` accepte une liste délimitée par des points-virgules des ID d’application à déployer sur les nœuds de calcul.

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

Lorsque vous créez un pool à l’aide des options de ligne de commande, vous ne pouvez pas pour le moment spécifier *quelle* version de package d’application déployer sur les nœuds de calcul, par exemple « 1.10-beta3 ». Par conséquent, vous devez spécifier une version par défaut de l’application avec `azure batch application set [options] --default-version <version-id>` avant de créer le pool (voir la section précédente). Vous pouvez toutefois spécifier une version de package pour le pool si vous utilisez un [fichier JSON](#json-files) à la place des options de ligne de commande lorsque vous créez le pool.

Pour plus d’informations sur les packages d’application, consultez [Déploiement d’applications avec des packages d’applications Azure Batch](batch-application-packages.md).

> [!IMPORTANT]
> Pour utiliser les packages d’application, vous devez commencer par [lier un compte de stockage Azure](#linked-storage-account-autostorage) à votre compte Batch.
> 
> 

### Mise à jour des packages d’applications d’un pool
Pour mettre à jour les applications affectées à un pool existant, exécutez la commande `azure batch pool set` avec l’option `--app-package-ref` :

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

Pour déployer le nouveau package d’application sur des nœuds de calcul se trouvant déjà dans un pool existant, vous devez redémarrer ou réinitialiser ces nœuds :

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

> [!TIP]
> Vous pouvez obtenir une liste des nœuds d’un pool, ainsi que leurs ID de nœud, avec `azure batch node list`.
> 
> 

Gardez à l’esprit que vous devez déjà avoir configuré l’application avec une version par défaut avant le déploiement (`azure batch application set [options] --default-version <version-id>`).

## Conseils de dépannage
Cette section est destinée à vous fournir les ressources qu’il convient d’utiliser pour résoudre les problèmes liés à l’interface de ligne de commande Azure. Elle ne résoudra pas nécessairement tous les problèmes, mais elle peut vous aider à en cerner la cause et à vous orienter vers les ressources utiles.

* Utilisez `-h` pour obtenir le **texte d’aide** d’une commande d’interface de ligne de commande.
* Utilisez `-v` et `-vv` pour afficher une sortie de commande **détaillée** ; l’option `-vv` permet d’obtenir une sortie plus détaillée, et affiche les requêtes et réponses REST réelles. Ces commutateurs sont pratiques pour afficher une sortie complète des erreurs.
* L’option `--json` permet d’afficher la **sortie de commande au format JSON**. Par exemple, `azure batch pool show "pool001" --json` affiche les propriétés de pool001 au format JSON. Vous pouvez ensuite copier et modifier cette sortie pour l’utiliser dans un `--json-file` (voir la section [Fichiers JSON](#json-files) plus haut dans cet article).
* Le [forum Batch sur MSDN][batch_forum] est une ressource très utile, et est surveillé attentivement par les membres de l’équipe Batch. Veillez à y publier vos questions si vous rencontrez des problèmes ou si vous avez besoin d’aide pour une opération spécifique.
* Pour le moment, toutes les opérations sur les ressources Batch ne pas prises en charge par l’interface de ligne de commande Batch. Par exemple, vous ne pouvez pas spécifier actuellement une *version* de package d’application pour un pool, mais seulement l’ID du package. Dans ce cas, vous devez peut-être fournir un `--json-file` pour votre commande au lieu d’utiliser les options de ligne de commande. Veillez à ce que la version de l’interface de ligne de commande soit toujours à jour afin de bénéficier des améliorations ultérieures.

## Étapes suivantes
* Consultez l’article [Déploiement d’applications avec des packages d’applications Azure Batch](batch-application-packages.md) pour découvrir comment utiliser cette fonctionnalité pour gérer et déployer les applications que vous exécutez sur les nœuds de calcul Batch.
* Consultez l’article [Interroger efficacement le service Batch](batch-efficient-list-queries.md) pour en savoir plus sur la réduction du nombre d’éléments et le type d’informations retournées pour les requêtes envoyées au service Batch.

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/fr-FR/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx

<!---HONumber=AcomDC_1005_2016-->