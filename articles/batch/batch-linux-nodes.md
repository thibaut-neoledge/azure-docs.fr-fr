---
title: Nœuds Linux dans des pools Azure Batch | Microsoft Docs
description: Découvrez comment traiter vos charges de travail de calcul parallèles sur des pools de machines virtuelles Linux dans Azure Batch.
services: batch
documentationcenter: python
author: mmacy
manager: timlt
editor: ''

ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: na
ms.date: 09/08/2016
ms.author: marsma

---
# Configurer des nœuds de calcul Linux dans des pools Azure Batch
Vous pouvez utiliser Azure Batch pour exécuter des charges de travail de calcul parallèles sur les machines virtuelles Linux et Windows. Cet article explique comment créer des pools de nœuds de calcul Linux dans le service Batch à l’aide de bibliothèques clientes [Batch Python][py_batch_package] et [Batch .NET][api_net].

> [!NOTE]
> [Application packages](batch-application-packages.md) ne sont actuellement pas pris en charge sur les nœuds de calcul Linux.
> 
> 

## Configuration de la machine virtuelle
Lorsque vous créez un pool de nœuds de calcul dans Azure Batch, vous avez deux options pour sélectionner la taille du nœud et le système d’exploitation : configuration des services cloud et configuration de la machine virtuelle.

La **configuration des services cloud** fournit *uniquement* des nœuds de calcul Windows. Les tailles de nœud de calcul disponibles sont répertoriées dans [Tailles de services cloud](../cloud-services/cloud-services-sizes-specs.md), et les systèmes d’exploitation disponibles sont répertoriés dans [Versions du SE invité et matrice de compatibilité du Kit de développement logiciel (SDK) Azure](../cloud-services/cloud-services-guestos-update-matrix.md). Lorsque vous créez un pool contenant des nœuds Azure Cloud Services, vous devez uniquement spécifier la taille du nœud et sa « famille de systèmes d’exploitation », que vous trouverez dans les articles dont il est question ci-dessus. Pour les pools de nœuds de calcul Windows, les services Cloud Services sont le plus couramment utilisés.

La **Configuration de la machine virtuelle** fournit des images Linux et Windows pour les nœuds de calcul. Les tailles de nœud de calcul disponibles sont répertoriées dans [Tailles des machines virtuelles dans Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) et [Tailles des machines virtuelles dans Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Lorsque vous créez un pool contenant des nœuds de la Configuration de la machine virtuelle, vous devez spécifier la taille des nœuds ainsi que la référence de l’image de la machine virtuelle et la référence de l’agent de nœud du Batch à installer sur les nœuds.

### Référence de l’image de la machine virtuelle
Le service Batch utilise des [jeux de mise à l’échelle de machine virtuelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pour fournir des nœuds de calcul Linux. Les images de système d’exploitation pour ces machines virtuelles sont fournies par [Azure Marketplace][vm_marketplace]. Lorsque vous configurez une référence d’image de machine virtuelle, vous spécifiez les propriétés d’une image de machine virtuelle Azure Marketplace. Vous avez besoin des propriétés suivantes lorsque vous créez une référence d’image de machine virtuelle :

| **Propriétés de référence d’image** | **Exemple** |
| --- | --- |
| Publisher |Canonical |
| Offer |UbuntuServer |
| SKU |14\.04.4-LTS |
| Version |le plus récent |

> [!TIP]
> Vous trouverez plus d’informations sur ces propriétés et sur la manière de répertorier des images Marketplace dans [Parcourir et sélectionner des images de machines virtuelles Linux dans Azure avec l’interface CLI ou PowerShell](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Notez que toutes les images Marketplace ne sont pas compatibles avec Batch pour le moment. Pour plus d’informations, consultez la rubrique [Référence de l’agent de nœud](#node-agent-sku) ci-dessous.
> 
> 

### Référence de l’agent de nœud
L’agent de nœud de Batch est un programme qui s’exécute sur chaque nœud dans le pool et fournit l’interface de commande et de contrôle entre le nœud et le service Batch. Il existe différentes implémentations de l’agent de nœud pour différents systèmes d’exploitation, connues sous le nom de références. Essentiellement, lorsque vous créez une configuration de machine virtuelle, vous spécifiez d’abord la référence de l’image de la machine virtuelle, puis spécifiez l’agent de nœud à installer sur l’image. En règle générale, chaque référence d’agent de nœud est compatible avec plusieurs images de machine virtuelle. Voici quelques exemples de références d’agent de nœud :

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> Toutes les images de machine virtuelle disponibles sur Marketplace ne sont pas compatibles avec les agents de nœud Batch actuellement disponibles. Vous devez utiliser les Kits de développement logiciel (SDK) Batch pour répertorier les références d’agent de nœud disponibles ainsi que les images de machine virtuelle avec lesquelles ils sont compatibles. Pour plus d’informations, consultez la [liste des images de machine virtuelle](#list-of-virtual-machine-images) fournie dans la suite de cet article.
> 
> 

## Créer un pool Linux : Batch Python
L’extrait de code suivant illustre un exemple d’utilisation de la [bibliothèque cliente Microsoft Azure Batch pour Python][py_batch_package] pour créer un pool de nœuds de calcul de serveur Ubuntu. Vous trouverez la documentation de référence pour le module Batch Python à la page [azure.batch package ][py_batch_docs] dans Lire la documentation.

Cet extrait de code crée explicitement un paramètre [ImageReference][py_imagereference] et spécifie chacune de ses propriétés (éditeur, offre, référence, version). Dans un code de production, nous vous recommandons toutefois d’utiliser la méthode [list\_node\_agent\_skus][py_list_skus] pour déterminer et sélectionner les combinaisons disponibles de références d’image et de nœuds d’agent lors de l’exécution.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Comme nous l’avons indiqué, il est recommandé d’utiliser la méthode [list\_node\_agent\_skus][py_list_skus] \(au lieu de créer explicitement le paramètre [ImageReference][py_imagereference]) afin de sélectionner de manière dynamique une combinaison d’image Marketplace/agent de nœud actuellement prise en charge. L’extrait de code Python suivant illustre l’utilisation de cette méthode.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## Créer un pool Linux : Batch .NET
L’extrait de code suivant illustre un exemple d’utilisation de la bibliothèque cliente [Batch .NET][nuget_batch_net] pour créer un pool de nœuds de calcul de serveur Ubuntu. Vous pouvez trouver la [documentation de référence sur les Batch .NET][api_net] sur MSDN.

L’extrait de code suivant utilise la méthode [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] pour sélectionner des combinaisons d’images Marketplace et de références d’agent de nœud actuellement pris en charge dans la liste. Cette technique est souhaitable car la liste des combinaisons prises en charge peut changer de temps à autre. En règle générale, les combinaisons prises en charge sont ajoutées.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.SkuId.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

Bien que l’extrait de code ci-dessus utilise la méthode [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] pour répertorier et sélectionner des combinaisons d’images et de références d’agent de nœud prises en charge de manière dynamique (recommandé), vous pouvez également configurer explicitement un paramètre [ImageReference][net_imagereference] \:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## Liste des images de machine virtuelle
Le tableau suivant répertorie les images de machine virtuelle Marketplace compatibles avec les agents de nœud Batch au moment où cet article a été mis à jour. Il est important de noter que cette liste n’est pas définitive, car des images et des agents de nœud peuvent être ajoutés ou supprimés à tout moment. Nous recommandons l’utilisation de [list\_node\_agent\_skus][py_list_skus] \(Python) et [ListNodeAgentSkus][net_list_skus] \(Batch .NET) par vos services et applications Batch pour déterminer et sélectionner parmi les références actuellement disponibles.

> [!WARNING]
> La liste suivante peut changer à tout moment. Utilisez toujours les méthodes de **création d’une liste de références d’agents de nœud** disponibles dans les API Batch pour répertorier, puis sélectionner parmi les machines virtuelles et les références d’agent de nœud compatibles lorsque vous exécutez vos travaux Batch.
> 
> 

| **Publisher** | **Offer** | **Référence d’image** | **Version** | **ID de référence de l’agent de nœud** |
| --- | --- | --- | --- | --- |
| Canonical |UbuntuServer |14\.04.0-LTS |le plus récent |batch.node.ubuntu 14.04 |
| Canonical |UbuntuServer |14\.04.1-LTS |le plus récent |batch.node.ubuntu 14.04 |
| Canonical |UbuntuServer |14\.04.2-LTS |le plus récent |batch.node.ubuntu 14.04 |
| Canonical |UbuntuServer |14\.04.3-LTS |le plus récent |batch.node.ubuntu 14.04 |
| Canonical |UbuntuServer |14\.04.4-LTS |le plus récent |batch.node.ubuntu 14.04 |
| Canonical |UbuntuServer |14\.04.5-LTS |le plus récent |batch.node.ubuntu 14.04 |
| Canonical |UbuntuServer |16\.04.0-LTS |le plus récent |batch.node.ubuntu 16.04 |
| Credativ |Debian |8 |le plus récent |batch.node.debian 8 |
| OpenLogic |CentOS |7\.0 |le plus récent |batch.node.centos 7 |
| OpenLogic |CentOS |7\.1 |le plus récent |batch.node.centos 7 |
| OpenLogic |CentOS-HPC |7\.1 |le plus récent |batch.node.centos 7 |
| OpenLogic |CentOS |7,2 |le plus récent |batch.node.centos 7 |
| Oracle |Oracle-Linux |7\.0 |le plus récent |batch.node.centos 7 |
| SUSE |openSUSE |13\.2 |le plus récent |batch.node.opensuse 13.2 |
| SUSE |openSUSE-Leap |42\.1 |le plus récent |batch.node.opensuse 42.1 |
| SUSE |SLES-HPC |12 |le plus récent |batch.node.opensuse 42.1 |
| SUSE |SLES |12-SP1 |le plus récent |batch.node.opensuse 42.1 |
| microsoft-ads |standard-data-science-vm |standard-data-science-vm |le plus récent |batch.node.windows amd64 |
| microsoft-ads |linux-data-science-vm |linuxdsvm |le plus récent |batch.node.centos 7 |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |le plus récent |batch.node.windows amd64 |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |le plus récent |batch.node.windows amd64 |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |le plus récent |batch.node.windows amd64 |
| MicrosoftWindowsServer |WindowsServer |Windows Server Technical Preview |le plus récent |batch.node.windows amd64 |

## Se connecter à des nœuds Linux
Pendant le développement ou lors de la résolution des problèmes, il peut s’avérer nécessaire de se connecter aux nœuds de votre pool. Contrairement aux nœuds de calcul Windows, vous ne pouvez pas utiliser le protocole RDP (Remote Desktop Protocol) pour se connecter à des nœuds Linux. Au lieu de cela, le service Batch autorise l’accès SSH sur chaque nœud de connexion à distance.

L’extrait de code Python suivant crée un utilisateur sur chaque nœud d’un pool, nécessaire à la connexion à distance. Il imprime ensuite les informations de connexion SSH pour chaque nœud.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Voici un exemple de sortie du code ci-dessus pour un pool contenant quatre nœuds Linux :

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Notez qu’au lieu d’un mot de passe, vous pouvez spécifier une clé publique SSH lorsque vous créez un utilisateur sur un nœud. Dans le kit de développement logiciel (SDK) Python, vous devez utiliser le paramètre **ssh\_public\_key** sur [ComputeNodeUser][py_computenodeuser]. Dans .NET, vous devez utiliser la propriété [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key].

## Tarification
Azure Batch est basé sur la technologie d’Azure Cloud Services et des machines virtuelles Azure. Le service Batch lui-même est proposé gratuitement, ce qui signifie que vous payez uniquement les ressources de calcul utilisées par vos solutions Batch. Si vous sélectionnez **Configuration des services cloud**, vous serez facturé en fonction de la [tarification des services cloud][cloud_services_pricing]. Si vous sélectionnez la **Configuration de la machine virtuelle**, vous serez facturé en fonction de la [tarification des machines virtuelles][vm_pricing].

## Étapes suivantes
### Didacticiel Python Batch
Pour accéder à un didacticiel plus détaillé sur l’utilisation de Batch avec Python, consultez la page [Prise en main du client Python Azure Batch](batch-python-tutorial.md). Son [exemple de code][github_samples_pyclient] associé comprend une fonction d’assistance, `get_vm_config_for_distro`, qui affiche une autre technique de configuration de machines virtuelles.

### Exemples de code Batch Python
Consultez les autres [exemples de code Python][github_samples_py] du référentiel [azure-batch-samples][github_samples] sur GitHub. Ce dernier présente plusieurs scripts illustrant l’exécution d’opérations Batch communes telles que la création de pools, de travaux et de tâches. Le fichier [Lisez-moi][github_py_readme] qui accompagne les exemples de code Python contient des informations sur l’installation des packages nécessaires.

### Forum Azure Batch
Le [Forum Azure Batch][forum] sur MSDN est l’endroit idéal pour discuter de Batch et poser des questions sur le service. Consultez le forum pour obtenir des publications « permanentes » utiles et publiez les questions que vous vous posez pendant la création de vos solutions Batch.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/fr-FR/home?forum=azurebatch
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/

<!---HONumber=AcomDC_0914_2016-->