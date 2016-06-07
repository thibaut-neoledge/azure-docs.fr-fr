
<properties
	pageTitle="Interface de ligne de commande Azure avec Resource Manager | Microsoft Azure"
	description="Utilisez l’interface de ligne de commande Azure pour déployer plusieurs ressources en tant que groupe de ressources."
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/20/2016"
	ms.author="danlep"/>

# Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager

> [AZURE.SELECTOR]
- [Interface de ligne de commande Azure](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)



Cet article présente des méthodes courantes pour créer et gérer des ressources Azure en utilisant l’interface de ligne de commande Azure en mode Azure Resource Manager.

>[AZURE.NOTE] Pour créer et gérer des ressources Azure sur la ligne de commande, vous devez disposer d’un abonnement Azure ([compte Azure gratuit ici](https://azure.microsoft.com/free/)). Vous devrez également [installer l’interface de ligne de commande Azure ](xplat-cli-install.md) et [vous connecter pour utiliser des ressources Azure associées à votre compte](xplat-cli-connect.md). Si ces opérations ont déjà été effectuées, vous pouvez dès à présent créer et gérer ces ressources.

## Ressources Azure

Utilisez Azure Resource Manager pour créer et gérer un groupe de _ressources_ (qui sont des entités gérées par l'utilisateur, comme une machine virtuelle, un serveur de bases de données, une base de données ou un site web) comme une seule unité logique ou un seul _groupe de ressources_.

L’un des avantages d’Azure Resource Manager réside dans le fait que vous pouvez créer vos ressources Azure de manière _déclarative_ : vous décrivez la structure et les relations d’un groupe de ressources pouvant être déployé dans des *modèles* JSON. Le modèle identifie les paramètres qui peuvent être renseignés en ligne lors de l’exécution d’une commande ou stockés dans un fichier de paramètres JSON (JavaScript Object Notation) distinct. Vous pouvez donc facilement créer de nouvelles ressources en utilisant le même modèle et en changeant simplement les paramètres. Par exemple, un modèle qui crée un site web a des paramètres pour le nom du site, la région du site web et d'autres paramètres courants.

Lorsqu'un modèle est utilisé pour modifier ou créer un groupe, un _déploiement_ est créé, avant d'être appliqué au groupe. Pour plus d'informations sur Azure Resource Manager, voir la [Présentation d'Azure Resource Manager](resource-group-overview.md).

Une fois le déploiement effectué, vous devez gérer les ressources individuelles impérativement sur la ligne de commande, comme dans le modèle de déploiement classique. Par exemple, utilisez les commandes CLI en mode Resource Manager pour démarrer, arrêter ou supprimer des ressources telles que les [machines virtuelles Azure Resource Manager](./virtual-machines/virtual-machines-linux-cli-deploy-templates.md).

## Authentification

Pour utiliser Azure Resource Manager avec l’interface de ligne de commande Azure, vous devez vous authentifier auprès de Microsoft Azure en utilisant la commande `azure login`, puis en spécifiant un compte géré par Azure Active Directory, qui peut être un compte professionnel ou scolaire (un compte d’organisation) ou un compte Microsoft. L'authentification avec un certificat installé à l'aide d'un fichier .publishsettings ne fonctionne pas dans ce mode.

Pour plus d'informations sur l'authentification auprès de Microsoft Azure, consultez la rubrique [Se connecter à un abonnement Azure à partir de l'interface de ligne de commande Azure](xplat-cli-connect.md).

>[AZURE.NOTE] Lorsque vous utilisez un compte géré par Azure Active Directory, vous pouvez également utiliser le contrôle d’accès en fonction du rôle (RBAC) Azure pour gérer l’accès aux ressources Azure et leur utilisation. Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md).

## Définir le mode Resource Manager

Étant donné que l’interface de ligne de commande n’est pas en mode Resource Manager par défaut, utilisez la commande suivante pour activer les commandes Resource Manager de l’interface de ligne de commande Azure.

	azure config mode arm

## Recherche d'emplacements

La plupart des commandes Azure Resource Manager ont besoin d’un emplacement valide pour créer ou trouver une ressource. Vous pouvez rechercher tous les emplacements disponibles pour les différentes ressources Azure à l'aide de la commande suivante.

	azure location list

Elle permet de répertorier les régions Azure disponibles, telles que « États-Unis de l’Ouest », « États-Unis de l’Est » et ainsi de suite. Pour plus d’informations sur les fournisseurs de ressources disponibles et les emplacements correspondants, utilisez la commande `azure provider list` suivie de la commande `azure provider show`. Par exemple, la commande suivante répertorie les emplacements du service de conteneur Azure :

    azure provider show Microsoft.ContainerService 

## Créer un groupe de ressources

Un groupe de ressources est un regroupement logique de réseaux, de stockage et d’autres ressources de calcul. Presque toutes les commandes en mode Resource Manager ont besoin d’un groupe de ressources. Vous pouvez créer un groupe de ressources dans la région États-Unis de l’Ouest, nommé par exemple _testRG_ à l’aide de la commande suivante.

	azure group create -n "testRG" -l "West US"

Vous allez déployer ce groupe de ressources *testRG* ultérieurement, lors de l’utilisation d’un modèle pour lancer une machine virtuelle Ubuntu. Une fois que vous avez créé un groupe de ressources, vous pouvez ajouter des ressources telles que des machines virtuelles et des réseaux ou du stockage.


## Utilisation de modèles de groupe de ressources

### Localisation et configuration d'un modèle de groupe de ressources

Quand vous utilisez des modèles, vous pouvez [créer vos propres modèles](resource-group-authoring-templates.md) ou utiliser l’un des [modèles de démarrage rapide](https://azure.microsoft.com/documentation/templates/), bénéficiant de la contribution de la communauté, également disponibles sur [GitHub](https://github.com/Azure/azure-quickstart-templates).

La création d’un modèle n’est pas abordée dans cet article. Pour commencer, utilisez le modèle _101-simple-vm-from-image_ disponible dans les [modèles de démarrage rapide](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux/). Par défaut, ceci crée une seule machine virtuelle Ubuntu 14.04.2-LTS dans un nouveau réseau virtuel avec un seul sous-réseau. Vous devez uniquement spécifier un groupe de ressources et les quelques paramètres suivants pour utiliser ce modèle :

* nom d’utilisateur administrateur de la machine virtuelle = `adminUsername` ;
* mot de passe = `adminPassword` ;
* nom de domaine de la machine virtuelle = `dnsLabelPrefix`.

>[AZURE.TIP] Ces étapes n'indiquent qu'une manière d'utiliser un modèle de machine virtuelle avec l'interface de ligne de commande Azure. Pour d'autres exemples, consultez la rubrique [Déploiement et gestion de machines virtuelles à l'aide des modèles Azure Resource Manager et de l'interface de ligne de commande Azure](./virtual-machines/virtual-machines-linux-cli-deploy-templates.md).

1. Suivez le lien « En savoir plus avec GitHub » pour télécharger les fichiers azuredeploy.json et azuredeploy.parameters.json depuis [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux) dans un dossier de travail sur votre ordinateur local. (Veillez à sélectionner le format _brut_ de chaque fichier dans GitHub.)

2. Ouvrez le fichier azuredeploy.parameters.json dans un éditeur de texte et entrez des valeurs de paramètre appropriées pour votre environnement (en laissant la valeur **ubuntuOSVersion** inchangée).

	```
			{
			  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
			  "contentVersion": "1.0.0.0",
			  "parameters": {
			    "adminUsername": {
			      "value": "azureUser"
			    },
			    "adminPassword": {
			      "value": "GEN-PASSWORD"
			    },
			    "dnsLabelPrefix": {
			      "value": "GEN-UNIQUE"
			    },
			    "ubuntuOSVersion": {
			      "value": "14.04.2-LTS"
			    }
			  }
			}

	```
3.  À présent que les paramètres de déploiement ont été modifiés, vous allez déployer la machine virtuelle Ubuntu dans le groupe de ressources *testRG* créé précédemment. Choisissez un nom pour le déploiement (dans cet exemple, *testRGDeploy*), puis utilisez la commande suivante pour le démarrer.

	```
	azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json testRG testRGDeploy
	```

	L'option `-e` spécifie le fichier azuredeploy.parameters.json que vous avez modifié à l'étape précédente. L’option `-f` spécifie le fichier de modèle azuredeploy.json.

	Cette commande renvoie la valeur OK une fois le déploiement chargé, mais avant qu'il ne soit appliqué aux ressources du groupe.

4. Pour vérifier le statut du déploiement, utilisez la commande suivante.

	```
	azure group deployment show testRG testRGDeploy
	```

	**ProvisioningState** indique le statut du déploiement.

	Si votre déploiement est exécuté avec succès, vous verrez une sortie similaire à celle de l'exemple suivant.

		azure-cli@0.8.0:/# azure group deployment show testRG testRGDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Succeeded
		data:    Timestamp          : 
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ---------------------
		data:    adminUsername          String        MyUserName
		data:    adminPassword          SecureString  undefined
		data:    dnsLabelPrefix    String        MyDomainName
		data:    ubuntuOSVersion        String        14.04.2-LTS
		info:    group deployment show command OK

	>[AZURE.NOTE] Si vous réalisez que votre configuration n'est pas correcte et que vous devez arrêter un déploiement long, utilisez la commande suivante.
	>
	> `azure group deployment stop testRG testRGDeploy`
	>
	> Si vous n'indiquez pas de nom de déploiement, un nom est créé automatiquement sur la base de celui du fichier de modèle. Il est renvoyé dans la sortie de la commande `azure group create`.

	Maintenant vous pouvez exécuter SSH dans la machine virtuelle à l'aide du nom de domaine spécifié. Lorsque vous vous connectez à la machine virtuelle, vous devez utiliser un nom de domaine complet sous la forme `<domainName>.<region>.cloudapp.azure.com`, tel que `MyDomainName.westus.cloudapp.azure.com`.

5. Pour afficher le groupe, utilisez la commande suivante.

		azure group show testRG

	Cette commande renvoie des informations sur les ressources du groupe. Si vous avez plusieurs groupes, utilisez la commande `azure group list` pour extraire une liste des noms de groupes et `azure group show` pour afficher les détails d'un groupe donné.

Vous pouvez également utiliser un modèle directement à partir de [GitHub](https://github.com/Azure/azure-quickstart-templates) au lieu d'en télécharger un sur votre ordinateur. Pour ce faire, indiquez l’URL dans le fichier azuredeploy.json pour le modèle dans votre commande à l’aide de l’option **--template-uri**. Pour obtenir l'URL, ouvrez azuredeploy.json dans GitHub en mode _raw (brut)_ et copiez l'URL qui apparaît dans la barre d'adresse du navigateur. Vous pouvez ensuite utiliser directement cette URL pour créer un déploiement en utilisant une commande similaire à l'exemple suivant.

	azure group deployment create testRG testRGDeploy --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-linux/azuredeploy.json
Vous êtes invité à entrer les paramètres de modèle nécessaires.

> [AZURE.NOTE] Il est très important d'ouvrir le modèle JSON en mode _raw_. L'URL qui apparaît dans la barre d'adresses du navigateur est différente de celle qui s'affiche en mode normal. Pour ouvrir le fichier en mode _raw_ quand vous affichez le fichier dans GitHub, cliquez sur **Raw** dans le coin supérieur droit.

## Utiliser les ressources

Les modèles vous permettent de déclarer des modifications de la configuration au niveau du groupe, mais vous devez parfois vous concentrer sur une ressource spécifique. Pour ce faire, utilisez les commandes `azure resource`.

> [AZURE.NOTE] Quand vous utilisez d'autres commandes `azure resource` que la commande `list`, vous devez spécifier la version de l'API de la ressource avec laquelle vous travaillez au moyen du paramètre `-o`. Si vous ne connaissez pas la version de l'API à utiliser, consultez le fichier de modèle et recherchez le champ **apiVersion** de la ressource.

1. Pour répertorier toutes les ressources d'un groupe, utilisez la commande suivante.

		azure resource list testRG

2. Pour afficher une ressource individuelle dans le groupe, par exemple la machine virtuelle *MyUbuntuVM*, utilisez une commande comme dans l’exemple suivant.

		azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

	Notez le paramètre **Microsoft.Compute/virtualMachines**. Il indique le type de la ressource sur laquelle vous demandez des informations. Si vous regardez le fichier de modèle téléchargé plus tôt, vous pouvez noter que cette même valeur est utilisée pour définir le type de la ressource de machine virtuelle décrite dans le modèle.

	Cette commande renvoie des informations concernant la machine virtuelle.

3. Lorsque vous affichez des détails sur une ressource, il est souvent utile d'utiliser le paramètre `--json`. Cela rend la sortie plus lisible, car certaines valeurs sont des structures imbriquées ou des ensembles de valeurs collectées. L'exemple suivant montre les résultats renvoyés par la commande **show** dans un document JSON.

		azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

	>[AZURE.NOTE] Vous pouvez enregistrer les données JSON dans un fichier au moyen du caractère &gt; pour diriger la sortie vers un fichier. Par exemple :
	>
	> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

4. Pour supprimer une ressource, utilisez une commande comme dans l'exemple suivant.

		azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

## Afficher les journaux de groupe

Pour afficher les informations de journalisation sur les opérations effectuées sur un groupe, utilisez la commande `azure group log show`. Par défaut, la dernière opération effectuée sur le groupe sera répertoriée. Pour afficher toutes les opérations, utilisez le paramètre facultatif `--all`. Pour le dernier déploiement, utilisez `--last-deployment`. Pour un déploiement spécifique, utilisez `--deployment`, puis spécifiez le nom du déploiement. L’exemple suivant renvoie un journal contenant toutes les opérations effectuées dans le groupe *testRG*.

	azure group log show testRG --all
    
## Exporter un groupe de ressources en tant que modèle

Vous pouvez afficher le modèle Resource Manager pour un groupe de ressources existant. L’exportation du modèle offre deux avantages :

1. Vous pouvez facilement automatiser des déploiements futurs de la solution car l’ensemble de l’infrastructure est définie dans le modèle.

2. Vous pouvez vous familiariser avec la syntaxe de modèle en regardant dans la JSON qui représente votre solution.

À l’aide de l’interface de ligne de commande Azure, vous pouvez exporter un modèle qui représente l’état actuel de votre groupe de ressources ou télécharger le modèle qui a été utilisé pour un déploiement spécifique.

* L’**exportation du modèle pour un groupe de ressources** est utile lorsque vous avez apporté des modifications à un groupe de ressources et que vous devez récupérer la représentation JSON de son état actuel. Toutefois, le modèle généré contient uniquement un nombre minimal de paramètres et aucune variable. La plupart des valeurs dans le modèle sont codées en dur. Avant de déployer le modèle généré, vous pouvez convertir plusieurs valeurs en paramètres afin de personnaliser le déploiement pour différents environnements.

    Pour exporter le modèle pour un groupe de ressources dans un répertoire local, exécutez la commande `azure group export` comme indiqué dans l’exemple suivant. (Indiquez un répertoire local adapté à l’environnement de votre système d’exploitation.)

        azure group export testRG ~/azure/templates/

* Le **téléchargement du modèle pour un déploiement spécifique** est utile lorsque vous avez besoin d’afficher le modèle réel qui a été utilisé pour déployer des ressources. Le modèle comprend tous les paramètres et toutes variables définis pour le déploiement d’origine. Toutefois, si un membre de votre organisation a apporté des modifications au groupe de ressources qui ne sont pas définies dans le modèle, ce dernier ne représente pas l’état actuel du groupe de ressources.

    Pour télécharger le modèle utilisé pour un déploiement spécifique dans un répertoire local, exécutez la commande `azure group deployment template download`.

        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/
 
>[AZURE.NOTE] L’exportation de modèle est en version préliminaire. Elle n’est pas encore prise en charge par tous les types de ressources. Lorsque vous tentez d’exporter un modèle, une erreur indiquant que certaines ressources n’ont pas été exportées peut s’afficher. Le cas échéant, vous pouvez définir manuellement ces ressources dans votre modèle après l’avoir téléchargé.

## Étapes suivantes

* Pour plus d'informations sur l'utilisation d'Azure Resource Manager avec Azure PowerShell, voir [Utilisation d'Azure PowerShell avec Azure Resource Manager](powershell-azure-resource-manager.md).
* Pour plus d’informations sur l’utilisation d’Azure Resource Manager à partir du Portail Azure, voir [Utilisation du Portail Azure pour déployer et gérer vos ressources Azure](./azure-portal/resource-group-portal.md).

<!---HONumber=AcomDC_0525_2016-->