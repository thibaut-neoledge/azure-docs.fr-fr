<properties
   pageTitle="Déploiement d’un cluster Azure Container Service | Microsoft Azure"
   description="Déployez un cluster Azure Container Service à l’aide du portail Azure, de l’interface de ligne de commande Azure ou de PowerShell."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, conteneurs, micro-services, Mesos, Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>
   
# Déploiement d’un cluster Azure Container Service

Azure Container Service assure le déploiement rapide des principales solutions de mise en cluster et d’orchestration de containers open source. L’utilisation d’Azure Container Service permet de déployer les clusters Marathon Mesos et Docker Swarm à l’aide des modèles Azure Resource Manager ou du portail. Ces clusters sont déployés au moyen de jeux de mise à l’échelle de machines virtuelles Azure et tirent parti des offres de mise en réseau et de stockage Azure. Pour accéder à Azure Container Service, vous devez disposer d’un abonnement Azure. Si ce n’est pas le cas, inscrivez-vous dès aujourd’hui pour un [essai gratuit](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Ce document vous guide tout au long du déploiement d’un cluster Azure Container Service à l’aide du [portail Azure](#creating-a-service-using-the-azure-portal), de l’[interface de ligne de commande Azure](#creating-a-service-using-the-azure-cli) et du [module Azure PowerShell](#creating-a-service-using-powershell).
   
## Création d’un service à l’aide du portail Azure
 
Sélectionnez l’un des modèles suivants pour déployer un cluster Mesos ou Docker Swarm. **Remarque** : ces deux modèles sont identiques, à l’exception de la sélection de l’orchestrateur par défaut.
 
* Mesos : [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm : [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)
 
Chaque page du modèle comporte un bouton de déploiement vers Azure qui permet d’accéder au formulaire suivant du portail Azure. <br />

![Créer un déploiement](media/create-mesos-params.png) <br />

Renseignez le formulaire en suivant les indications fournies et cliquez sur OK lorsque vous avez terminé. <br />

Champ | Description
----------------|-----------
DNSNAMEPREFIX | Il doit s’agir d’une valeur unique au monde. Ce paramètre servira à créer des noms DNS pour chacun des éléments clés du service. Pour plus d’informations, voir ci-dessous.
AGENTCOUNT | Nombre de machines virtuelles qui seront créées dans le jeu de mise à l’échelle de l’agent ACS.
AGENTVMSIZE | Spécifie la taille des machines virtuelles de l’agent. Veillez à sélectionner une taille fournissant suffisamment de ressources pour héberger vos conteneurs les plus volumineux.
ADMINUSERNAME | Nom d’utilisateur qui sera utilisé pour un compte sur chaque machine virtuelle et chaque jeu de mise à l’échelle des machines virtuelles dans le cluster ACS.
ORCHESTRATORTYPE| Sélectionnez l’orchestrateur que vous souhaitez utiliser dans votre cluster ACS.
MASTERCOUNT | Nombre de machines virtuelles à configurer en tant que serveurs principaux de votre cluster. Vous pouvez n’en sélectionner qu’une, mais cette valeur n’apportera aucune résilience à votre cluster et est recommandée uniquement à des fins de test. Pour un cluster de production, il est recommandé d’en sélectionner 3 ou 5. 
SSHRSAPUBLICKEY | Vous devez utiliser SSH pour l’authentification sur les machines virtuelles. Ce champ vous permet d’ajouter votre clé publique. Il est très important de bien coller la valeur de votre clé dans cette zone. Certains éditeurs insèrent des sauts de ligne dans le contenu, ce qui a pour effet de corrompre la clé. Vérifiez que votre clé ne contient aucun saut de ligne et qu’elle inclut le préfixe « ssh-rsa » et le suffixe « nom\_d’utilisateur@domaine ». Vous devriez obtenir quelque chose du type : « ssh rsa AAAAB3Nz... SNIPPEDCONTENT... UcyupgH azureuser@linuxvm' ». Si vous devez créer une clé SSH, suivez les instructions fournies pour [Windows](../virtual-machines/virtual-machines-windows-use-ssh-key.md) et [Linux](../virtual-machines/virtual-machines-linux-use-ssh-key.md) sur le site de documentation Azure.
  
Une fois que vous avez défini les valeurs appropriées pour vos paramètres, cliquez sur OK. Indiquez ensuite un nom de groupe de ressources et sélectionnez une région, puis lisez et acceptez les conditions juridiques.

> Dans la version préliminaire, Azure Container Service est fourni gratuitement ; seuls les frais de traitement standard (machine virtuelle, stockage, réseau, etc.) sont appliqués.
 
![Sélectionner un groupe de ressources](media/resourcegroup.png)
 
Pour finir, cliquez sur « Créer ». Vous êtes redirigé vers votre tableau de bord. En supposant que vous n’avez pas décoché la case « Épingler au tableau de bord » dans le panneau de déploiement, vous obtiendrez une vignette animée semblable à ce qui suit :

![déploiement](media/deploy.png)
 
Il ne vous reste plus maintenant qu’à attendre que le cluster soit créé. Une fois le processus terminé, vous pourrez accéder à des panneaux décrivant les ressources qui composent le cluster ACS.
 
![Finished](media/final.png)

## Création d’un service à l’aide de l’interface de ligne de commande Azure

Pour créer une instance d’Azure Container Service à l’aide de l’interface de ligne de commande (CLI), vous avez besoin d’un abonnement Azure. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un essai gratuit dès aujourd’hui. Vous devez également avoir installé et configuré la CLI Azure.
 
Sélectionnez l’un des modèles suivants pour déployer un cluster Mesos ou Docker Swarm. **Remarque** : ces deux modèles sont identiques, à l’exception de la sélection de l’orchestrateur par défaut.
 
* Mesos : [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm : [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)
 
Assurez-vous que la CLI Azure a bien été liée à un abonnement Azure. Pour ce faire, exécutez la commande suivante :

```bash
Azure account show
```
Si aucun compte Azure n’est retourné, utilisez la commande suivante pour connecter la CLI à Azure.
 
```bash
azure login -u user@domain.com
```

Veillez ensuite à configurer les outils de la CLI Azure pour qu’ils utilisent les modèles Azure Resource Manager.
 
```bash
azure config mode arm
```
 
Si vous souhaitez créer votre cluster dans un nouveau groupe de ressources, vous devez d’abord le créer. Utilisez cette commande, où `GROUP_NAME` est le nom du groupe de ressources que vous souhaitez créer et `REGION` la région dans laquelle vous souhaitez créer le groupe de ressources.
 
```bash
azure group create GROUP_NAME REGION
```
 
Une fois le groupe de ressources créé, vous pouvez créer votre cluster à l’aide de la commande suivante, où :

- **RESOURCE\_GROUP** est le nom du groupe de ressources que vous souhaitez utiliser pour ce service.
- **DEPLOYMENT\_NAME** est le nom de ce déploiement.
- **TEMPLATE\_URI** est l’emplacement du fichier de déploiement. **Remarque** : il doit s’agir du fichier RAW, et non d’un pointeur vers l’interface utilisateur de GitHub. Pour trouver cette URL, sélectionnez le fichier azuredeploy.json dans GitHub et cliquez sur le bouton RAW :

> Remarque : lorsque vous exécutez cette commande, l’interpréteur de commandes vous invite à saisir les valeurs des paramètres de déploiement.
 
```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```
 
### Indication des paramètres du modèle
 
Cette version de la commande oblige l’utilisateur à définir les paramètres de manière interactive. Si vous souhaitez fournir des paramètres en tant que chaîne au format json, vous pouvez le faire à l’aide du commutateur `-p`. Par exemple :
 
 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

Sinon, vous pouvez fournir un fichier de paramètres au format json à l’aide du commutateur `-e` :

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON'
 ```
 
Vous pouvez accéder à un exemple de fichier de paramètres nommé `azuredeploy.parameters.json` avec les modèles ACS dans GitHub.
 
## Création d’un service à l’aide de PowerShell

Il est également possible de déployer un cluster ACS avec PowerShell. Ce document est basé sur les versions 1.0 et supérieures du [module Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/).

Sélectionnez l’un des modèles suivants pour déployer un cluster Mesos ou Docker Swarm. **Remarque** : ces deux modèles sont identiques, à l’exception de la sélection de l’orchestrateur par défaut.
 
* Mesos : [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm : [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)

Avant de créer un cluster dans votre abonnement Azure, vérifiez que votre session PowerShell a bien été ouverte dans Azure. Pour ce faire, vous pouvez exécuter la commande `Get-AzureRMSubscription`.

```powershell
Get-AzureRmSubscription
```

Si vous avez besoin de vous connecter à Azure, utilisez la commande `Login-AzureRMAccount`.

```powershell
Login-AzureRmAccount
```
 
Si vous souhaitez effectuer le déploiement vers un nouveau groupe de ressources, vous devez d’abord le créer. Pour créer un groupe de ressources, utilisez la commande `New-AzureRmResourceGroup` en spécifiant un nom de groupe de ressources et une région de destination.

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Une fois le groupe de ressources créé, vous pouvez créer votre cluster à l’aide de la commande suivante. L’URI du modèle souhaité doit être spécifié pour le paramètre `-TemplateUri`. Lorsque vous exécutez cette commande, PowerShell vous invite à saisir les valeurs des paramètres de déploiement.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```
 
### Provide template parameters
 
If you are familiar with PowerShell, you know that you can cycle through the available parameters for a cmdlet by typing a minus sign (-) and then pressing the TAB key. This same functionality also works with parameters that you define in your template. As soon as you type the template name, the cmdlet fetches the template, parses the parameters, and adds the template parameters to the command dynamically. This makes it very easy to specify the template parameter values. And, if you forget a required parameter value, PowerShell prompts you for the value.
 
Below is the full command with parameters included. You can provide your own values for the names of the resources.

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE\_GROUP\_NAME-TemplateURI TEMPLATE\_URI -adminuser value1 -adminpassword value2 ....
```
 
## Étapes suivantes
 
À présent que vous disposez d’un cluster opérationnel, consultez les documents suivants pour obtenir des informations supplémentaires relatives à la connexion et à la gestion du cluster.
 
- [Connexion au cluster ACS](./container-service-connect.md)
- [Utilisation d’ACS et de Mesos](./container-service-mesos-marathon-rest.md)

 

<!-----HONumber=AcomDC_0218_2016-->