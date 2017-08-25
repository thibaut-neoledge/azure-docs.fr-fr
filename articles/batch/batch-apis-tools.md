---
title: "Utilisation des outils et API Azure Batch pour développer des solutions de traitement parallèle à grande échelle | Microsoft Docs"
description: "Découvrez les API et les outils disponibles pour développer des solutions avec le service Azure Batch."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: c8c76944f4a95d3c8181454a7103ea0a3022189a
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---


# <a name="overview-of-batch-apis-and-tools"></a>Vue d’ensemble des outils et API Batch

Le traitement des charges de travail parallèles avec Azure Batch est généralement effectué par programme en utilisant l’une des [API Batch](#batch-development-apis). Votre application cliente ou le service peut utiliser les API Batch pour communiquer avec le service Batch. Avec les API Batch, vous pouvez créer et gérer les pools de nœuds de calcul, des machines virtuelles ou services de cloud. Vous pouvez ensuite planifier des tâches à exécuter sur ces nœuds. 

Vous pouvez efficacement traiter des charges de travail à grande échelle pour votre organisation, ou fournir un service frontal à vos clients afin qu’ils puissent exécuter des travaux et des tâches, à la demande ou selon un calendrier, sur un nœud, des centaines de nœuds, voire des milliers. Vous pouvez également utiliser Azure Batch dans le cadre d’un plus grand workflow géré par des outils comme [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Si vous souhaitez mieux comprendre les fonctionnalités offertes par l’API Batch, consultez la page [Vue d’ensemble des fonctionnalités d’Azure Batch](batch-api-basics.md).
> 
> 

## <a name="azure-accounts-for-batch-development"></a>Comptes Azure pour le développement de Batch
Lorsque vous développez des solutions Batch, vous devrez utiliser les comptes suivants dans Microsoft Azure.

* **Compte et abonnement Azure** : si vous ne possédez pas encore d’abonnement Azure, vous pouvez activer les [avantages de votre abonnement MSDN][msdn_benefits] ou vous inscrire pour obtenir un [compte Azure gratuit][free_account]. Lorsque vous créez un compte, un abonnement par défaut est automatiquement créé.
* **Compte Batch** : les ressources Azure Batch, notamment les pools, les nœuds de calcul, les travaux et les tâches, sont associées à un compte Azure Batch. Lorsque votre application effectue une requête auprès du service Batch, il authentifie la requête en utilisant le nom du compte Azure Batch, l’URL du compte et une clé d’accès. Vous pouvez [créer un compte Azure Batch](batch-account-create-portal.md) à l’aide du portail Azure.
* **Compte de stockage** : Batch inclut la prise en charge intégrée de l’utilisation des fichiers dans [Azure Storage][azure_storage]. Presque tous les scénarios du service Batch font appel au stockage Blob Azure pour la préparation des programmes exécutés par vos tâches et les données qu’ils traitent ainsi que pour le stockage des données de sortie qu’elles génèrent. Pour créer un compte de stockage, consultez l’article [À propos des comptes de stockage Azure](../storage/common/storage-create-storage-account.md).

## <a name="batch-service-apis"></a>API du service Batch

Vos applications et services peuvent émettre des appels directs de l’API REST ou utiliser une ou plusieurs des bibliothèques clientes suivantes pour exécuter et gérer vos charges de travail Azure Batch.

| API | Informations de référence sur l'API | Télécharger | Didacticiel | Exemples de code | En savoir plus |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[MSDN][batch_rest] |N/A |- |- | [Versions prises en charge](https://docs.microsoft.com/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[Didacticiel](batch-dotnet-get-started.md) |[GitHub][api_sample_net] | [Notes de publication](http://aka.ms/batch-net-dataplane-changelog) |
| **Python Batch** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[Didacticiel](batch-python-tutorial.md)|[GitHub][api_sample_python] | [Lisez-moi](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- | [Lisez-moi](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Java Batch** |[github.io][api_java] |[Maven][api_java_jar] |- |[Lisez-moi][api_sample_java] | [Lisez-moi](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>API de Batch Management

Les API Azure Resource Manager pour le service Batch fournissent un accès par programme aux comptes Batch. À l’aide de ces API, vous pouvez gérer par programme les comptes Batch, les quotas et les packages d’applications.  

| API | Informations de référence sur l'API | Télécharger | Didacticiel | Exemples de code |
| --- | --- | --- | --- | --- |
| **REST Batch Resource Manager** |[docs.microsoft.com][api_rest_mgmt] |N/A |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **.NET Batch Resource Manager** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [Didacticiel](batch-management-dotnet.md) |[GitHub][api_sample_net] |

## <a name="batch-command-line-tools"></a>Outils en ligne de commande Batch

Ces outils en ligne de commande fournissent les mêmes fonctionnalités que les API des services Batch et Batch Management : 

* [Applets de commande PowerShell pour Batch][batch_ps] : les applets de commande Azure Batch contenues dans le module [Azure PowerShell](/powershell/azure/overview) vous permettent de gérer les ressources du service Batch avec PowerShell.
* [Azure CLI](/cli/azure/overview) : l’interface de ligne de commande Azure est un ensemble d’outils multiplateforme qui fournit des commandes d’environnement permettant d’interagir avec de nombreux services Azure, y compris les services Batch et Batch Management. Consultez la page [Gérer les ressources Batch avec Azure CLI](batch-cli-get-started.md) pour plus d’informations sur l’utilisation d’Azure CLI avec Batch.

## <a name="other-tools-for-application-development"></a>Autres outils pour le développement d’applications

Voici quelques outils supplémentaires qui peuvent être utiles pour générer et déboguer vos applications et services Batch :

* [Portail Azure][portal] : vous pouvez créer, surveiller et supprimer des pools, des travaux et des tâches dans les panneaux Batch du portail Azure. Vous pouvez consulter les informations d’état de ces éléments et d’autres ressources pendant que vous exécutez vos travaux, et même télécharger des fichiers depuis les nœuds de calcul de vos pools. Par exemple, vous pouvez télécharger une tâche ayant échoué `stderr.txt` lors de la résolution des problèmes. Vous pouvez également télécharger des fichiers du Bureau à distance (RDP), à utiliser pour vous connecter aux nœuds de calcul.
* [Azure Batch Explorer][batch_explorer] : Batch Explorer fournit une fonctionnalité de gestion des ressources Batch similaire à celle du portail Azure, mais dans une application cliente Windows Presentation Foundation (WPF) autonome. Il s’agit de l’un des exemples d’applications Batch .NET disponibles sur [GitHub][github_samples]. Vous pouvez le générer avec Visual Studio 2015 ou une version ultérieure et l’utiliser pour accéder aux ressources de votre compte Batch et les gérer pendant que vous développez et déboguez vos solutions Batch. Consultez les détails sur les travaux, les pools et les tâches, téléchargez des fichiers des nœuds de calcul et connectez-vous à distance aux nœuds à l’aide des fichiers du Bureau à distance (RDP) téléchargeables avec Batch Explorer.
* [Microsoft Azure Storage Explorer][storage_explorer] : bien qu’il ne s’agisse pas à proprement parler d’un outil Azure Batch, l’Explorateur de stockage est l’un de vos meilleurs alliés pendant le développement et le débogage de vos solutions Batch.

## <a name="additional-resources"></a>Ressources supplémentaires

- Pour en savoir plus sur la journalisation des événements à partir de votre application Batch, consultez [Consigner des événements pour l’analyse et l’évaluation de diagnostic des solutions Batch](batch-diagnostics.md). Pour en savoir plus sur les événements déclenchés par le service Batch, voir [Batch Analytics](batch-analytics.md).
- Pour plus d’informations sur les variables d’environnement des nœuds de calcul, consultez [Variables d’environnement de nœud de calcul Azure Batch](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Étapes suivantes

* Consultez la [Présentation des fonctionnalités du service Batch pour les développeurs](batch-api-basics.md). Elle contient des informations indispensables pour toute personne se préparant à l’utilisation de Batch. L’article contient des informations plus détaillées sur les ressources du service Batch telles que les pools, les nœuds, les travaux et les tâches, ainsi que sur les nombreuses fonctionnalités API que vous pouvez utiliser lors de la création de votre application Batch.
* [Prise en main de la bibliothèque Azure Batch pour .NET](batch-dotnet-get-started.md) pour apprendre à utiliser C# et la bibliothèque .NET Batch pour exécuter une charge de travail simple à l’aide d’un flux de travail Batch courant. Cet article servira de référence essentielle dans la découverte de l’utilisation du service Batch. Il existe également une [version Python](batch-python-tutorial.md) du didacticiel.
* Téléchargez les [exemples de code sur GitHub][github_samples] pour voir comment C# et Python peuvent interagir avec Batch pour planifier et traiter des exemples de charge de travail.
* Consultez le [parcours d’apprentissage de Batch][learning_path] pour avoir une idée des ressources dont vous allez disposer dans votre découverte du service Batch.


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/\rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/resourcemanager/azurerm.batch/v2.7.0/azurerm.batch
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

