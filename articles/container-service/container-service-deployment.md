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

Azure Container Service assure le déploiement rapide des principales solutions de mise en cluster et d’orchestration de containers open source. En utilisant, Azure Container Service, vous pouvez déployer les clusters Marathon Mesos et Docker Swarm à l’aide des modèles Azure Resource Manager ou du portail Azure. Vous déployez ces clusters au moyen de jeux de mise à l’échelle de machines virtuelles Azure et les clusters tirent parti des offres de mise en réseau et de stockage Azure. Pour accéder à Azure Container Service, vous devez disposer d’un abonnement Azure. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935) dès aujourd’hui.

Ce document vous guide lors du déploiement d’un cluster Azure Container Service à l’aide du [portail Azure](#creating-a-service-using-the-azure-portal), de l’[interface de ligne de commande (CLI) Azure](#creating-a-service-using-the-azure-cli) et du [module Azure PowerShell](#creating-a-service-using-powershell).

## Création d’un service à l’aide du portail Azure

Sélectionnez l’un des modèles GitHub suivants pour déployer un cluster Mesos ou Docker Swarm. Notez que ces deux modèles sont identiques, à l’exception de la sélection de l’orchestrateur par défaut.

* [Modèle Mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Modèle Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Lorsque vous sélectionnez le bouton **Déployer dans Azure** sur la page du modèle, le portail Azure s’ouvre et affiche un formulaire qui ressemble à ce qui suit : <br />

![Créer un déploiement à l’aide du formulaire](media/create-mesos-params.png) <br />

Renseignez le formulaire en suivant les indications fournies et cliquez sur **OK** lorsque vous avez terminé. <br />

Champ | Description
----------------|-----------
DNSNAMEPREFIX | Il doit s’agir d’une valeur unique au monde. Ce paramètre servira à créer des noms DNS pour chacun des éléments clés du service. Vous trouverez plus d’informations plus loin dans cet article.
AGENTCOUNT | Nombre de machines virtuelles qui seront créées dans le jeu de mise à l’échelle de l’agent Azure Container Service.
AGENTVMSIZE | Taille des machines virtuelles de l’agent. Veillez à sélectionner une taille fournissant suffisamment de ressources pour héberger vos conteneurs les plus volumineux.
ADMINUSERNAME | Nom d’utilisateur qui sera utilisé pour un compte sur chaque machine virtuelle et chaque jeu de mise à l’échelle des machines virtuelles dans le cluster Azure Container Service.
ORCHESTRATORTYPE| Type Orchestrator à utiliser dans votre cluster Azure Container Service.
MASTERCOUNT | Nombre de machines virtuelles à configurer en tant que serveurs principaux de votre cluster. Vous pouvez n’en sélectionner qu’une, mais cette valeur n’apportera aucune résilience à votre cluster et est recommandée uniquement à des fins de test. Nous vous recommandons d’en choisir 3 ou 5 pour un cluster de production.
SSHRSAPUBLICKEY | Vous devez utiliser SSH pour l’authentification sur les machines virtuelles. Ce champ vous permet d’ajouter votre clé publique. Il est très important de bien coller la valeur de votre clé dans cette zone. Certains éditeurs insèrent des sauts de ligne dans le contenu, ce qui a pour effet de corrompre la clé. Vérifiez que votre clé ne contient aucun saut de ligne et qu’elle inclut le préfixe « ssh-rsa » et le suffixe « nom\_d’utilisateur@domaine ». Vous devriez obtenir quelque chose du type : « ssh rsa AAAAB3Nz... SNIPPEDCONTENT... UcyupgH azureuser@linuxvm' ». Si vous devez créer une clé SSH, suivez les instructions fournies pour [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) et [Linux](../virtual-machines/virtual-machines-linux-ssh-from-linux.md) sur le site de documentation Azure.

Une fois que vous avez défini les valeurs appropriées pour vos paramètres, cliquez sur **OK**. Indiquez ensuite un nom de groupe de ressources et sélectionnez une région, puis lisez et acceptez les conditions juridiques.

> [AZURE.NOTE] Dans la version préliminaire, Azure Container Service n’est pas facturé. Seuls les frais de calcul standard, comme les frais de la machine virtuelle, du stockage et de la mise en réseau, sont facturés.

![Sélectionner un groupe de ressources](media/resourcegroup.png)

Pour finir, sélectionnez **Créer**. Revenez à votre tableau de bord. En supposant que vous n’avez pas décoché la case **Épingler au tableau de bord** dans le panneau de déploiement, vous obtiendrez une vignette animée semblable à ce qui suit :

![Vignette du modèle de déploiement](media/deploy.png)

Il ne vous reste plus maintenant qu’à attendre que le cluster soit créé. Une fois que le cluster est créé, vous verrez des panneaux qui montrent les ressources qui composent le cluster Azure Container Service.

![Finished](media/final.png)

## Création d’un service à l’aide de l’interface de ligne de commande (CLI) Azure

Pour créer une instance d’Azure Container Service à l’aide de l’interface de ligne de commande (CLI), vous avez besoin d’un abonnement Azure. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935) dès aujourd’hui. Vous devez également avoir installé et configuré la CLI Azure.

Sélectionnez l’un des modèles GitHub suivants pour déployer un cluster Mesos ou Docker Swarm. Notez que ces deux modèles sont identiques, à l’exception de la sélection de l’orchestrateur par défaut.

* [Modèle Mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Modèle Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Assurez-vous que la CLI Azure a bien été liée à un abonnement Azure. Pour ce faire, vous pouvez utiliser la commande suivante :

```bash
Azure account show
```
Si aucun compte Azure n’est retourné, utilisez la commande suivante pour connecter la CLI à Azure.

```bash
azure login -u user@domain.com
```

Ensuite, configurez les outils de la CLI Azure pour qu’ils utilisent les modèles Azure Resource Manager.

```bash
azure config mode arm
```

Si vous souhaitez créer votre cluster dans un nouveau groupe de ressources, vous devez d’abord le créer. Utilisez la commande suivante, où `GROUP_NAME` est le nom du groupe de ressources que vous souhaitez créer et `REGION` la région dans laquelle vous souhaitez créer le groupe de ressources :

```bash
azure group create GROUP_NAME REGION
```

Une fois le groupe de ressources créé, vous pouvez créer votre cluster à l’aide de la commande suivante, où :

- **RESOURCE\_GROUP** est le nom du groupe de ressources que vous souhaitez utiliser pour ce service.
- **DEPLOYMENT\_NAME** est le nom de ce déploiement.
- **TEMPLATE\_URI** est l’emplacement du fichier de déploiement. Notez qu’il doit s’agir du fichier RAW, et *non* d’un pointeur vers l’interface utilisateur de GitHub. Pour trouver cette URL, sélectionnez le fichier azuredeploy.json dans GitHub et cliquez sur le bouton **RAW**.

> [AZURE.NOTE] Lorsque vous exécutez cette commande, l’interpréteur de commandes vous invite à saisir les valeurs des paramètres de déploiement.

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```

### Indication des paramètres du modèle

Cette version de la commande vous oblige à définir les paramètres de manière interactive. Si vous souhaitez fournir des paramètres, comme une chaîne au format JSON, vous pouvez le faire à l’aide du commutateur `-p`. Par exemple :

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

Vous pouvez également fournir un fichier de paramètres au format JSON à l’aide du commutateur `-e` :

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
 ```

Pour voir un exemple de fichier de paramètres nommé `azuredeploy.parameters.json`, recherchez-le avec les modèles Azure Container Service dans GitHub.

## Création d’un service à l’aide de PowerShell

Vous pouvez également déployer un cluster Azure Container Service avec PowerShell. Ce document est basé sur la version 1.0 du [module Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/).

Sélectionnez l’un des modèles suivants pour déployer un cluster Mesos ou Docker Swarm. Notez que ces deux modèles sont identiques, à l’exception de la sélection de l’orchestrateur par défaut.

* [Modèle Mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Modèle Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Avant de créer un cluster dans votre abonnement Azure, vérifiez que votre session PowerShell a bien été connectée à Azure. Pour cela, utilisez la commande `Get-AzureRMSubscription` :

```powershell
Get-AzureRmSubscription
```

Si vous avez besoin de vous connecter à Azure, utilisez la commande `Login-AzureRMAccount` :

```powershell
Login-AzureRmAccount
```

Si vous déployez vers un nouveau groupe de ressources, vous devez d’abord le créer. Pour créer un groupe de ressources, utilisez la commande `New-AzureRmResourceGroup` en spécifiant un nom de groupe de ressources et une région de destination :

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Une fois le groupe de ressources créé, vous pouvez créer votre cluster à l’aide de la commande suivante. L’URI du modèle souhaité doit être spécifié pour le paramètre `-TemplateUri`. Lorsque vous exécutez cette commande, PowerShell vous invite à saisir les valeurs des paramètres de déploiement.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```

### Indication des paramètres du modèle

Si vous êtes familiarisé avec PowerShell, vous savez que vous pouvez parcourir les paramètres disponibles pour une applet de commande en utilisant le signe moins (-) puis en appuyant sur la touche TAB. Cette fonctionnalité fonctionne également avec les paramètres que vous définissez dans votre modèle. Dès que vous entrez le nom du modèle, l’applet de commande récupère le modèle, analyse ses paramètres et ajoute les paramètres du modèle à la commande de façon dynamique. Il est ainsi beaucoup plus facile de spécifier les valeurs des paramètres du modèle. En outre, si vous oubliez une valeur obligatoire, PowerShell vous invite à spécifier cette valeur.

Voici l’intégralité de la commande contenant les paramètres. Vous pouvez renseigner vos propres valeurs pour les noms des ressources.

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## Étapes suivantes

À présent que vous disposez d’un cluster opérationnel, consultez les articles suivants pour obtenir des informations supplémentaires relatives à la connexion et à la gestion du cluster.

- [Connexion à un cluster Azure Container Service](./container-service-connect.md)
- [Travail avec Azure Container Service et Mesos](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0413_2016-->