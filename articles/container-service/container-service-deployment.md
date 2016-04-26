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

Azure Container Service assure le déploiement rapide des principales solutions de mise en cluster et d’orchestration de containers open source. En utilisant Azure Container Service, vous pouvez déployer les clusters DC/OS et Docker Swarm à l’aide des modèles Azure Resource Manager ou du portail Azure. Vous déployez ces clusters au moyen de jeux de mise à l’échelle de machines virtuelles Azure et les clusters tirent parti des offres de mise en réseau et de stockage Azure. Pour accéder à Azure Container Service, vous devez disposer d’un abonnement Azure. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935) dès aujourd’hui.

Ce document vous guide lors du déploiement d’un cluster Azure Container Service à l’aide du [portail Azure](#creating-a-service-using-the-azure-portal), de l’[interface de ligne de commande (CLI) Azure](#creating-a-service-using-the-azure-cli) et du [module Azure PowerShell](#creating-a-service-using-powershell).

## Création d’un service à l’aide du portail Azure

Connectez-vous au portail Azure, sélectionnez Nouveau et recherchez **Azure Container Service** sur l’Azure Marketplace.

![Créer un déploiement](media/acs-portal1.png) <br />

Sélectionnez **Azure Container Service** puis cliquez sur **Créer**.

![Créer un déploiement](media/acs-portal2.png) <br />

Entrez les informations suivantes :

- Nom d’utilisateur : le nom qui sera utilisé pour un compte sur chaque machine virtuelle et chaque jeu de mise à l’échelle des machines virtuelles dans le cluster Azure Container Service.
- Abonnement : sélectionnez un abonnement Azure.
- Groupe de ressources : sélectionnez un groupe de ressources existant ou créez-en un.
- Emplacement : sélectionnez une région Azure pour le déploiement d’Azure Container Service.
- Clé publique SSH : ajoutez la clé publique qui sera utilisée pour l'authentification sur les machines virtuelles d’Azure Container Service. Vous devez absolument vérifier que cette clé ne contient aucun saut de ligne et qu’elle inclut le préfixe « ssh-rsa » et le suffixe « nom\_d’utilisateur@domaine ». Elle doit présenter le format suivant, « **ssh-rsa AAAAB3Nz...SNIPPEDCONTENT...UcyupgH azureuser@linuxvm**». Pour obtenir des conseils sur la création de clés SSH, consultez les articles pour [Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) et [Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/).

Cliquez sur **OK** lorsque vous souhaitez continuer.

![Créer un déploiement](media/acs-portal3.png) <br />

Sélectionnez un type d'orchestration. Les options sont :

- DC/OS : déploie un cluster DC/OS.
- Swarm : déploie un cluster Docker Swarm.

Cliquez sur **OK** lorsque vous souhaitez continuer.

![Créer un déploiement](media/acs-portal4.png) <br />

Entrez les informations suivantes :

- Nombre de maîtres : quantité de maîtres dans le cluster.
- Nombre d'agents : pour Docker Swarm, ce sera le nombre initial d'agents dans le jeu de mise à l'échelle de l'agent. Pour DC/OS, ce sera le nombre initial d’agents dans un jeu de mise à l’échelle privé. Dé plus, une mise à l’échelle publique est créée avec un nombre prédéterminé d’agents. Le nombre d'agents dans ce jeu de mise à l’échelle public repose sur le nombre de maîtres créés dans le cluster, 1 agent public pour 1 maître et 2 agents publics pour 3 ou 5 maîtres.
- Taille de la machine virtuelle de l’agent : la taille des machines virtuelles des agents.
- Préfixe DNS : un nom unique dans le monde qui sera utilisé comme préfixe pour les éléments clés des noms de domaine pleinement qualifiés pour le service. 

Cliquez sur **OK** lorsque vous souhaitez continuer.

![Créer un déploiement](media/acs-portal5.png) <br />

Cliquez sur **OK** une fois la validation du service terminée.

![Créer un déploiement](media/acs-portal6.png) <br />

Cliquez sur **Créer** pour démarrer le processus de déploiement.

![Créer un déploiement](media/acs-portal7.png) <br />

Si vous avez choisi d'épingler le déploiement au portail Azure, l'état du déploiement peut être consulté.

![Créer un déploiement](media/acs-portal8.png) <br />

Lorsque le déploiement est terminé, le cluster Azure Container Service est prêt à l’emploi.

## Création d’un service à l’aide de l’interface de ligne de commande (CLI) Azure

Pour créer une instance d’Azure Container Service à l’aide de l’interface de ligne de commande (CLI), vous avez besoin d’un abonnement Azure. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935) dès aujourd’hui. Vous devez également avoir installé et configuré la CLI Azure.

Sélectionnez l’un des modèles GitHub suivants pour déployer un cluster DC/OS ou Docker Swarm. Notez que ces deux modèles sont identiques, à l’exception de la sélection de l’orchestrateur par défaut.

* [Modèle DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Modèle Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Assurez-vous que la CLI Azure a bien été liée à un abonnement Azure. Pour ce faire, vous pouvez utiliser la commande suivante :

```bash
azure account show
```
Si aucun compte Azure n’est retourné, utilisez la commande suivante pour connecter la CLI à Azure.

```bash
azure login -u user@domain.com
```

Ensuite, configurez les outils de la CLI Azure pour qu’ils utilisent les modèles Azure Resource Manager.

```bash
azure config mode arm
```

Créez un groupe de ressources Azure et un cluster Container Service avec la commande suivante, où :

- **RESOURCE\_GROUP** est le nom du groupe de ressources que vous souhaitez utiliser pour ce service.
- **LOCATION** correspond à la région Azure dans laquelle le groupe de ressources et le déploiement d’Azure Container Service seront créés.
- **TEMPLATE\_URI** est l’emplacement du fichier de déploiement. **Remarque** : il doit s’agir du fichier RAW, et non d’un pointeur vers l’interface utilisateur de GitHub. Pour trouver cette URL, sélectionnez le fichier azuredeploy.json dans GitHub et cliquez sur le bouton RAW :

> Remarque : lorsque vous exécutez cette commande, l’interpréteur de commandes vous invite à saisir les valeurs des paramètres de déploiement.
 
```bash
# sample deployment

azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### Indication des paramètres du modèle

Cette version de la commande vous oblige à définir les paramètres de manière interactive. Si vous souhaitez fournir des paramètres, comme une chaîne au format JSON, vous pouvez le faire à l’aide du commutateur `-p`. Par exemple :

 ```bash
 # sample deployment

azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

Vous pouvez également fournir un fichier de paramètres au format JSON à l’aide du commutateur `-e` :

 ```bash
 # sample deployment

azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
 ```

Pour voir un exemple de fichier de paramètres nommé `azuredeploy.parameters.json`, recherchez-le avec les modèles Azure Container Service dans GitHub.

## Création d’un service à l’aide de PowerShell

Vous pouvez également déployer un cluster Azure Container Service avec PowerShell. Ce document est basé sur la version 1.0 du [module Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/).

Sélectionnez l’un des modèles suivants pour déployer un cluster DC/OS ou Docker Swarm. Notez que ces deux modèles sont identiques, à l’exception de la sélection de l’orchestrateur par défaut.

* [Modèle DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
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
# sample deployment

New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```

### Indication des paramètres du modèle

Si vous êtes familiarisé avec PowerShell, vous savez que vous pouvez parcourir les paramètres disponibles pour une applet de commande en utilisant le signe moins (-) puis en appuyant sur la touche TAB. Cette fonctionnalité fonctionne également avec les paramètres que vous définissez dans votre modèle. Dès que vous entrez le nom du modèle, l’applet de commande récupère le modèle, analyse ses paramètres et ajoute les paramètres du modèle à la commande de façon dynamique. Il est ainsi beaucoup plus facile de spécifier les valeurs des paramètres du modèle. En outre, si vous oubliez une valeur obligatoire, PowerShell vous invite à spécifier cette valeur.

Voici l’intégralité de la commande contenant les paramètres. Vous pouvez renseigner vos propres valeurs pour les noms des ressources.

```powershell
# sample deployment

New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## Étapes suivantes
 
À présent que vous disposez d’un cluster opérationnel, consultez les documents suivants pour obtenir des informations supplémentaires relatives à la connexion et à la gestion du cluster.
 
[Connexion avec un cluster Azure Container Service](./container-service-connect.md) [Utilisation d’Azure Container Service et de DC/OS ](./container-service-mesos-marathon-rest.md) [Utilisation avec Azure Container Service et Docker Swarm](./container-service-docker-swarm.md)

<!---HONumber=AcomDC_0420_2016-->