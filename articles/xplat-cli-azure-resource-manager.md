
<properties
	pageTitle="Interface de ligne de commande Azure avec Resource Manager | Microsoft Azure"
	description="Utilisez l'interface de ligne de commande Azure pour Mac, Linux et Windows pour déployer plusieurs ressources comme groupe de ressources."
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/19/2016"
	ms.author="danlep"/>

# Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager

> [AZURE.SELECTOR]
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)



Cette rubrique décrit comment créer et gérer des ressources Azure en utilisant l'interface de ligne de commande Azure pour Mac, Linux et Windows en mode Azure Resource Manager.

>[AZURE.NOTE] Pour créer et gérer des ressources Azure sur la ligne de commande, vous devez disposer d'un compte Azure ([version d'évaluation gratuite disponible ici](https://azure.microsoft.com/pricing/free-trial/)). Vous devrez également [installer l’interface de ligne de commande Azure ](xplat-cli-install.md), et [vous connecter pour utiliser des ressources Azure associées à votre compte](xplat-cli-connect.md). Si ces opérations ont déjà été effectuées, vous pouvez dès à présent créer et gérer ces ressources.

## Ressources Azure

Utilisez Azure Resource Manager pour créer et gérer un groupe de _ressources_ (qui sont des entités gérées par l'utilisateur, comme une machine virtuelle, un serveur de bases de données, une base de données ou un site web) comme une seule unité logique ou un seul _groupe de ressources_.

L'un des avantages d'Azure Resource Manager réside dans le fait que vous pouvez créer vos ressources Azure d'une manière _déclarative_ : vous décrivez la structure et les relations d'un groupe de ressources pouvant être déployé dans des *modèles* JSON. Le modèle identifie les paramètres qui peuvent être renseignés en ligne lors de l'exécution d'une commande ou stockés dans un fichier JSON azuredeploy-parameters.json distinct. Vous pouvez donc facilement créer de nouvelles ressources en utilisant le même modèle et en changeant simplement les paramètres. Par exemple, un modèle qui crée un site web a des paramètres pour le nom du site, la région du site web et d'autres paramètres courants.

Lorsqu'un modèle est utilisé pour modifier ou créer un groupe, un _déploiement_ est créé, avant d'être appliqué au groupe. Pour plus d'informations sur Azure Resource Manager, voir la [Présentation d'Azure Resource Manager](resource-group-overview.md).

Une fois le déploiement effectuez, vous devez gérer les ressources individuelles impérativement sur la ligne de commande, comme dans le modèle de déploiement classique (gestion des services). Par exemple, utilisez les commandes de l'interface de ligne de commande de l'Azure Resource Manager pour démarrer, arrêter ou supprimer des ressources telles que [les machines virtuelles Azure Resource Manager](virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md).

## Authentification

Pour utiliser Azure Resource Manager avec l'interface de ligne de commande Azure, vous devez vous authentifier auprès de Microsoft Azure en utilisant un compte professionnel ou scolaire (un compte d'organisation) ou un compte Microsoft (à partir de la version 0.9.10 de l'interface de ligne de commande). L'authentification avec un certificat installé à l'aide d'un fichier .publishsettings ne fonctionne pas dans ce mode.

Pour plus d'informations sur l'authentification auprès de Microsoft Azure, consultez la rubrique [Se connecter à un abonnement Azure à partir de l'interface de ligne de commande Azure](xplat-cli-connect.md).

>[AZURE.NOTE] Lorsque vous utilisez un compte professionnel ou scolaire, qui est géré par Azure Active Directory, vous pouvez également utiliser Azure Role-Based Access Control (RBAC) pour gérer l'accès et l'utilisation des ressources Azure. Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle Azure](./active-directory/role-based-access-control-configure.md).

## Définition du mode Azure Resource Manager

Étant donné que le mode Azure Resource Manager n'est pas activé par défaut, utilisez la commande suivante pour activer les commandes Resource Manager de l'interface de ligne de commande Azure.

	azure config mode arm

>[AZURE.NOTE] Le mode Azure Resource Manager et le mode Azure Service Management s'excluent mutuellement. En d'autres termes, les ressources créées dans un mode ne peuvent pas être gérées dans l'autre mode.

## Recherche d'emplacements

La plupart des commandes Azure Resource Manager ont besoin d’un emplacement valide pour créer ou trouver une ressource. Vous pouvez rechercher tous les emplacements disponibles pour les différentes ressources Azure à l'aide de la commande suivante.

	azure location list

Elle permet de répertorier les ressources Azure et les régions Azure dans lesquelles elles sont disponibles, telles que « Ouest des États-Unis », « Est des États-Unis » et ainsi de suite.

## Créer un groupe de ressources

Un groupe de ressources est un regroupement logique de réseaux, de stockage et d'autres ressources. Presque toutes les commandes en mode Azure Resource Manager ont besoin d’un groupe de ressources. Vous pouvez créer un groupe de ressources nommé par exemple _testRG_ à l'aide de la commande suivante.

	azure group create -n "testRG" -l "West US"

Vous allez déployer ce groupe de ressources « testRG » ultérieurement, lorsque de l'utilisation d'un modèle pour lancer une machine virtuelle Ubuntu. Une fois que vous avez créé un groupe de ressources, vous pouvez ajouter des ressources telles que des machines virtuelles et des réseaux ou du stockage.


## Utilisation de modèles de groupe de ressources

### Localisation et configuration d'un modèle de groupe de ressources

Quand vous utilisez des modèles, vous pouvez [créer vos propres modèles](resource-group-authoring-templates.md), utiliser l'un des modèles de la [galerie de modèles](https://azure.microsoft.com/documentation/templates/) également disponibles sur [GitHub](https://github.com/Azure/azure-quickstart-templates).

La création d’un nouveau modèle n’est pas abordée dans cet article. Pour commencer, utilisez le modèle _101-simple-vm-from-image_ disponible à partir dans la [galerie de modèles](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux/). Par défaut, ceci crée une seule machine virtuelle Ubuntu 14.04.2-LTS dans un nouveau réseau virtuel avec un seul sous-réseau dans la région Ouest des États-Unis. Vous devez uniquement spécifier les quelques paramètres suivants pour utiliser ce modèle :

* Nom d'utilisateur administrateur pour la machine virtuelle = `adminUsername`
* Mot de passe = `adminPassword`
* Nom de domaine pour la machine virtuelle = `dnsLabelPrefix`

>[AZURE.TIP] Ces étapes n'indiquent qu'une manière d'utiliser un modèle de machine virtuelle avec l'interface de ligne de commande Azure. Pour d'autres exemples, consultez la rubrique [Déploiement et gestion de machines virtuelles à l'aide des modèles Azure Resource Manager et de l'interface de ligne de commande Azure](virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md).

1. Suivez le lien « En savoir plus avec GitHub » pour télécharger les fichiers azuredeploy.json et azuredeploy.parameters.json depuis GitHub dans un dossier de travail sur votre ordinateur local. (Veillez à sélectionner le format _brut_ de chaque fichier dans GitHub.)

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
3.  À présent que les paramètres de déploiement ont été modifiés, vous allez déployer la machine virtuelle Ubuntu dans le groupe de ressources créé précédemment. Choisissez un nom pour le déploiement, puis utilisez la commande suivante pour le démarrer.

	```
	azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json testRG testRGdeploy
	```

	Cet exemple crée un déploiement nommé _testRGDeploy_ qui est déployé dans le groupe de ressources _testRG_. L'option `-e` spécifie le fichier azuredeploy.parameters.json que vous avez modifié à l'étape précédente. L’option `-f` spécifie le fichier de modèle azuredeploy.json.

	Cette commande renvoie la valeur OK une fois le déploiement chargé, mais avant qu'il ne soit appliqué aux ressources du groupe.

4. Pour vérifier le statut du déploiement, utilisez la commande suivante.

	```
	azure group deployment show "testRG" "testRGDeploy"
	```

	**ProvisioningState** indique le statut du déploiement.

	Si votre déploiement est exécuté avec succès, vous verrez une sortie similaire à celle de l'exemple suivant.

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-10-26T16:15:29.5562024Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ---------------------
		data:    newStorageAccountName  String        MyStorageAccount
		data:    adminUsername          String        MyUserName
		data:    adminPassword          SecureString  undefined
		data:    dnsNameForPublicIP     String        MyDomainName
		data:    ubuntuOSVersion        String        14.04.2-LTS
		info:    group deployment show command OK

	>[AZURE.NOTE] Si vous réalisez que votre configuration n'est pas correcte et que vous devez arrêter un déploiement long, utilisez la commande suivante.
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> Si vous n'indiquez pas de nom de déploiement, un nom est créé automatiquement sur la base de celui du fichier de modèle. Il est renvoyé dans la sortie de la commande `azure group create`.

	Maintenant vous pouvez exécuter SSH dans la machine virtuelle à l'aide du nom de domaine spécifié. Lorsque vous vous connectez à la machine virtuelle, vous devez utiliser un nom de domaine complet sous la forme `<domainName>.<region>.cloudapp.azure.com`, tel que `MyDomainName.westus.cloudapp.azure.com`.

5. Pour afficher le groupe, utilisez la commande suivante.

		azure group show "testRG"

	Cette commande renvoie des informations sur les ressources du groupe. Si vous avez plusieurs groupes, utilisez la commande `azure group list` pour extraire une liste des noms de groupes et `azure group show` pour afficher les détails d'un groupe donné.

Vous pouvez également utiliser un modèle directement à partir de [GitHub](https://github.com/Azure/azure-quickstart-templates) au lieu d'en télécharger un sur votre ordinateur. Pour ce faire, indiquez l'URL dans le fichier azuredeploy.json pour le modèle dans votre commande à l'aide de l'option **--template-url**. Pour obtenir l'URL, ouvrez azuredeploy.json dans GitHub en mode _raw (brut)_ et copiez l'URL qui apparaît dans la barre d'adresse du navigateur. Vous pouvez ensuite utiliser directement cette URL pour créer un déploiement en utilisant une commande similaire à l'exemple suivant.

	azure group deployment create "testDeploy" testResourceGroup --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-linux/azuredeploy.json
Vous êtes invité à entrer les paramètres de modèle nécessaires.

> [AZURE.NOTE] Il est très important d'ouvrir le modèle JSON en mode _raw_. L'URL qui apparaît dans la barre d'adresses du navigateur est différente de celle qui s'affiche en mode normal. Pour ouvrir le fichier en mode _raw_ quand vous affichez le fichier dans GitHub, cliquez sur **Raw** dans le coin supérieur droit.

## Utilisation des ressources

Les modèles vous permettent de déclarer des modifications de la configuration au niveau du groupe, mais vous devez parfois vous concentrer sur une ressource spécifique. Pour ce faire, utilisez les commandes `azure resource`.

> [AZURE.NOTE] Quand vous utilisez d'autres commandes `azure resource` que la commande `list`, vous devez spécifier la version de l'API de la ressource avec laquelle vous travaillez au moyen du paramètre `-o`. Si vous ne connaissez pas la version de l'API à utiliser, consultez le fichier de modèle et recherchez le champ **apiVersion** de la ressource.

1. Pour répertorier toutes les ressources d'un groupe, utilisez la commande suivante.

		azure resource list "testRG"

2. Pour afficher une ressource individuelle dans le groupe, utilisez une commande comme dans l'exemple suivant.

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

	Notez le paramètre **Microsoft.Compute/virtualMachines**. Il indique le type de la ressource sur laquelle vous demandez des informations. Si vous regardez le fichier de modèle téléchargé plus tôt, vous pouvez noter que cette même valeur est utilisée pour définir le type de la ressource de machine virtuelle décrite dans le modèle.

	Cette commande renvoie des informations concernant la machine virtuelle.

3. Lorsque vous affichez des détails sur une ressource, il est souvent utile d'utiliser le paramètre `--json`. Cela rend la sortie plus lisible, car certaines valeurs sont des structures imbriquées ou des ensembles de valeurs collectées. L'exemple suivant montre les résultats renvoyés par la commande **show** dans un document JSON.

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json

	>[AZURE.NOTE] Vous pouvez enregistrer les données JSON dans un fichier au moyen du caractère &gt; pour canaliser la sortie vers le fichier. Par exemple :
	>
	> `azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

4. Pour supprimer une ressource, utilisez une commande comme dans l'exemple suivant.

		azure resource delete "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

## Journalisation

Pour afficher les informations de journalisation sur les opérations effectuées sur un groupe, utilisez la commande `azure group log show`. Par défaut, la dernière opération effectuée sur le groupe sera répertoriée. Pour afficher toutes les opérations, utilisez le paramètre facultatif `--all`. Pour le dernier déploiement, utilisez `--last-deployment`. Pour un déploiement spécifique, utilisez `--deployment`, puis spécifiez le nom du déploiement. L'exemple suivant renvoie un journal contenant toutes les opérations effectuées dans le groupe *MyGroup*.

	azure group log show MyGroup --all

## Étapes suivantes

* Pour plus d'informations sur l'utilisation d'Azure Resource Manager avec Azure PowerShell, voir [Utilisation d'Azure PowerShell avec Azure Resource Manager](powershell-azure-resource-manager.md).
* Pour plus d’informations sur l’utilisation d’Azure Resource Manager à partir du portail Azure, voir [Utilisation de groupes de ressources pour gérer vos ressources Azure][psrm].

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!---HONumber=AcomDC_0128_2016-->