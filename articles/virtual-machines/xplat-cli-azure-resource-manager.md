<properties
	pageTitle="Interface de ligne de commande Azure pour Mac, Linux et Windows | Microsoft Azure"
	description="Utilisation de l’interface de ligne de commande Microsoft Azure pour Mac, Linux et Windows avec Azure Resource Manager"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services"" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="06/09/2015" ms.author="danlep"/>

# Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager

> [AZURE.SELECTOR]
- [Azure PowerShell](../powershell-azure-resource-manager.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique


Cette rubrique décrit comment créer, gérer et supprimer des ressources et des machines virtuelles Azure en utilisant l’interface de ligne de commande Azure pour Mac, Linux et Windows en mode Azure Resource Manager.

>[AZURE.NOTE]Pour créer et gérer des ressources Azure sur la ligne de commande, vous devez disposer d’un compte Azure ([essai gratuit ici](http://azure.microsoft.com/pricing/free-trial/)). Vous devrez également [installer l’interface de ligne de commande Azure ](../xplat-cli-install.md), et [vous connecter pour utiliser des ressources Azure associées à votre compte](../xplat-cli-connect.md). Si ces opérations ont déjà été effectuées, vous pouvez dès à présent créer et gérer ces ressources.

## Ressources Azure

Utilisez Azure Resource Manager pour gérer un groupe de _ressources_ (qui sont des entités gérées par l’utilisateur, comme une machine virtuelle, un serveur de bases de données, une base de données ou un site web) comme une seule unité logique ou _groupe de ressources_. Vous pouvez créer, gérer et supprimer ces ressources de façon impérative sur la ligne de commande, exactement comme vous pouvez le faire dans le mode asm.

En utilisant le mode Azure Resource Manager, vous pouvez également gérer vos ressources Azure de façon _déclarative_ en décrivant la structure et les relations d’un groupe de ressources déployable dans des *modèles* JSON. Le modèle décrit les paramètres qui peuvent être renseignés en ligne lors de l’exécution d’une commande ou stockés dans un fichier JSON azuredeploy-parameters.json distinct. Vous pouvez donc facilement créer de nouvelles ressources en utilisant le même modèle et en changeant simplement les paramètres. Par exemple, un modèle qui crée un site web a des paramètres pour le nom du site, la région du site web et d’autres paramètres courants.

Lorsqu'un modèle est utilisé pour modifier ou créer un groupe, un _déploiement_ est créé, avant d'être appliqué au groupe. Pour plus d’informations sur Azure Resource Manager, voir la [Vue d’ensemble d’Azure Resource Manager](../resource-group-overview.md).

## Authentification

Pour utiliser Azure Resource Manager avec l’interface de ligne de commande Azure, vous devez vous authentifier auprès de Microsoft Azure en utilisant un compte professionnel ou scolaire. L'authentification avec un certificat installé avec un fichier .publishsettings ne fonctionne pas.

Pour plus d'informations sur l'authentification à l'aide d'un compte professionnel ou scolaire, consultez la rubrique [Se connecter à un abonnement Azure à partir de l'interface de ligne de commande Azure](../xplat-cli-connect.md).

> [AZURE.NOTE]Étant donné que vous utilisez un compte professionnel ou scolaire, qui est géré par Azure Active Directory, vous pouvez également utiliser Azure Role-Based Access Control (RBAC) pour gérer l’accès et l’utilisation des ressources Azure. Pour plus d’informations, consultez la rubrique [Gestion et audit d’accès aux ressources](../resource-group-rbac.md)

## Définition du mode Azure Resource Manager

Étant donné que le mode Azure Resource Manager n’est pas activé par défaut, vous devez utiliser la commande suivante pour activer les commandes Resource Manager d'interface de ligne de commande Azure.

	azure config mode arm

>[AZURE.NOTE]Le mode Azure Resource Manager et le mode Azure Service Management s'excluent mutuellement. En d'autres termes, les ressources créées dans un mode ne peuvent pas être gérées dans l'autre mode.

## Recherche des emplacements

La plupart des commandes Azure Resource Manager ont besoin d’un emplacement valide pour créer ou trouver une ressource. Vous pouvez trouver tous les emplacements disponibles à l’aide de la commande suivante.

	azure location list

Celle-ci répertorie les emplacements spécifiques aux régions « Ouest des États-Unis », « Est des États-Unis », etc.

## Création d’un groupe de ressources

Un groupe de ressources est un regroupement logique de réseaux, de stockage et d’autres ressources. Presque toutes les commandes en mode Azure Resource Manager ont besoin d’un groupe de ressources. Vous pouvez créer un groupe de ressources nommé par exemple _testrg_ à l’aide de la commande suivante.

	azure group create -n "testrg" -l "West US"

Vous pouvez ensuite commencer à ajouter des ressources à ce groupe et utiliser ce dernier pour configurer une machine virtuelle.

## Création de machines virtuelles

Il existe deux façons de créer des machines virtuelles en mode Azure Resource Manager :

1. en utilisant des commandes Azure CLI individuelles ;
2. en utilisant des modèles de groupes de ressources.

Assurez-vous de créer au moins un groupe de ressources avant d’opter pour l’une de ces méthodes.

### Utilisation des commandes individuelles de l’interface de ligne de commande Azure

Ceci est une approche de base vous permettant de configurer et de créer une machine virtuelle en fonction de vos besoins. En mode Azure Resource Manager, vous devez configurer des ressources obligatoires, comme la mise en réseau, avant de pouvoir utiliser la commande **vm create**.

>[AZURE.NOTE]Si vous créez des ressources pour la première fois sur la ligne de commande pour votre abonnement, vous pouvez être invité à vous inscrire pour certains fournisseurs de ressources. Si c’est le cas, il est très facile d’inscrire le fournisseur et de réessayer la commande qui a échoué, comme le montre l’exemple suivant.
>
> `azure provider register Microsoft.Storage`
>
> Vous pouvez trouver la liste des fournisseurs inscrits pour votre abonnement en exécutant la commande suivante.
>
> `azure provider list`


#### Création d’une ressource IP publique.

Vous devrez créer une adresse IP publique pour pouvoir exécuter SSH dans votre nouvelle machine virtuelle pour tout travail significatif. La création d'une adresse IP publique est une opération très simple. La commande a besoin, dans l’ordre, d’un groupe de ressources, d’un nom pour votre ressource IP publique et d’un emplacement.

	azure network public-ip create "testrg" "testip" "westus"

#### Création d'une ressource Carte d'interface réseau

La carte d'interface réseau (NIC) a tout d’abord besoin d’un sous-réseau et d’un réseau virtuel. Créez un réseau virtuel dans un emplacement et un groupe de ressources spécifiques à l’aide de la commande **network vnet create**.

	azure network vnet create "testrg" "testvnet" "westus"

Vous pouvez ensuite créer un sous-réseau dans ce réseau virtuel à l’aide de la commande **network vnet subnet create**.

	azure network vnet subnet create "testrg" "testvnet" "testsubnet"

Vous devriez normalement pouvoir créer une carte d’interface réseau en utilisant ces ressources avec la commande **network nic create**.

	azure network nic create "testrg" "testnic" "westus" -k "testsubnet" -m "testvnet" -p "testip"

>[AZURE.NOTE]Bien que ceci soit facultatif, il est très important de transmettre le nom de l’adresse IP publique en tant que paramètre de la commande **network nic create**, car ceci permet de relier la carte d’interface réseau à cette adresse IP, qui sera ultérieurement utilisée pour exécuter SSH sur la machine virtuelle créée à l'aide de cette carte d’interface réseau.

Pour plus d’informations sur les commandes **network**, voir l’aide de la ligne de commande ou [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Management](azure-cli-arm-commands.md).

#### Recherche de l’image du système d’exploitation

Pour l’instant, vous ne pouvez rechercher qu’un système d'exploitation basé sur l’éditeur de l'image. En d’autres termes, vous devez exécuter la commande suivante pour obtenir la liste des éditeurs d’images de système d’exploitation à l’emplacement de votre choix.

	azure vm image list-publishers "westus"

Choisissez ensuite un éditeur répertorié dans la liste, puis obtenez la liste des images de cet éditeur en exécutant la commande suivante.

	azure vm image list "westus" "CoreOS"

Enfin, choisissez dans la liste une image de système d’exploitation ressemblant à ce que montre l’exemple suivant.

	info:    Executing command **vm image list**
	warn:    The parameters --offer and --sku if specified will be ignored
	+ Getting virtual machine image offers (Publisher: "Canonical" Location: "westus")
	data:    Publisher  Offer        Sku          Version          Location  Urn

	data:    ---------  -----------  -----------  ---------------  --------- ----------------------------------------
	data:    CoreOS     CoreOS       Alpha        475.1.0          westus    CoreOS:CoreOS:Alpha:475.1.0
	data:    CoreOS     CoreOS       Alpha        490.0.0          westus    CoreOS:CoreOS:Alpha:490.0.0

Notez le nom URN de l’image que vous voulez charger sur votre machine virtuelle. Vous l’utiliserez plus loin dans l’article.

#### Création d’une machine virtuelle

Vous êtes maintenant prêt à créer une machine virtuelle en exécutant la commande **vm create** et en passant les informations requises. À ce stade, la transmission de l'adresse IP publique est facultative, puisque la carte d’interface réseau dispose déjà de ces informations. Votre commande peut ressembler à l’exemple suivant, où _testvm_ est le nom de la machine virtuelle créée dans le groupe de ressources _testrg_.

	azure-cli@0.8.0:/# azure vm create "testrg" "testvm" "westus" "Linux" -Q "CoreOS:CoreOS:Alpha:660.0.0" -u "azureuser" -p "Pass1234!" -N "testnic"
	info:    Executing command vm create
	+ Looking up the VM "testvm"
	info:    Using the VM Size "Standard_A1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Retrieving storage accounts
	info:    Could not find any storage accounts in the region "westus", trying to create new one
	+ Creating storage account "cli02f696bbfda6d83414300" in "westus"
	+ Looking up the storage account cli02f696bbfda6d83414300
	+ Looking up the NIC "testnic"
	+ Creating VM "testvm"
	info:    vm create command OK

Vous devez normalement pouvoir démarrer cette machine virtuelle en exécutant la commande suivante.

	azure vm start "testrg" "testvm"

Connectez-vous ensuite avec SSH à l’aide de la commande **ssh username@ipaddress**. Pour trouver rapidement l’adresse IP de votre ressource IP publique, utilisez la commande suivante.

	azure network public-ip show "testrg" "testip"

La gestion de cette machine virtuelle est facile avec les commandes **vm**. Pour plus d’informations, voir [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Management](azure-cli-arm-commands.md).

### Raccourci vm quick-create

Le nouveau raccourci **vm quick-create** coupe la plupart des étapes de la méthode impérative de création de la machine virtuelle. Ceci est très pratique lorsque vous souhaitez créer de simples machines virtuelles ou si vous ne vous souciez pas des configurations de mise en réseau. Il s'agit ici d'une commande interactive, dont vous ne devez connaître que l'URN de l’image du système d'exploitation pour pouvoir l'exécuter.

	azure-cli@0.8.0:/# azure vm quick-create
	info:  Executing command vm quick-create
	Resource group name: CLIRG
	Virtual machine name: myqvm
	Location name: westus
	Operating system Type [Windows, Linux]: Linux
	ImageURN (format: "publisherName:offer:skus:version"): CoreOS:CoreOS:Alpha:660.0.0
	User name: azureuser
	Password: ********
	Confirm password: ********

L’interface de ligne de commande Azure créera une machine virtuelle avec une taille de machine virtuelle par défaut. Elle créera également un compte de stockage, une carte d’interface réseau, un réseau virtuel, un sous-réseau et une adresse IP publique. Vous pouvez exécuter SSH dans la machine virtuelle, à l'aide de l'adresse IP publique, une fois que celle-ci sera démarrée.

### Utilisation de modèles de groupes de ressources

#### Recherche et configuration d’un modèle de groupe de ressources

1. Quand vous utilisez des modèles, vous pouvez créer vos propres modèles, utiliser les modèles de la galerie ou utiliser les modèles disponibles sur [GitHub](https://github.com/azurermtemplates/azurermtemplates). Pour commencer, nous allons utiliser un modèle nommé CoreOS.CoreOSStable.0.2.40-preview provenant de la galerie. Pour répertorier les modèles disponibles dans la galerie, utilisez la commande suivante. Étant donné qu’il existe des milliers de modèles disponibles, vous pouvez faire défiler les résultats ou utiliser **grep** ou **findstr** (sur Windows) ou votre commande de recherche de chaîne préférée pour trouver des modèles intéressants. Vous pouvez également utiliser l'option **--json** et télécharger la liste complète au format JSON pour faciliter la recherche.

		azure group template list

	La réponse contient le nom de l’éditeur et celui du modèle, et elle est similaire à l’exemple suivant (même s’il y en a en fait beaucoup plus).

		data:    Publisher               Name
		data:    ----------------------------------------------------------------------------
		data:    CoreOS                  CoreOS.CoreOSStable.0.2.40-preview
		data:    CoreOS                  CoreOS.CoreOSAlpha.0.2.39-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer12.2.0.36-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer11SP3PremiumImage0.2.54-preview

2. Pour afficher les détails du modèle, utilisez la commande suivante :

		azure group template show CoreOS.CoreOSStable.0.2.40-preview

	Des informations sur le modèle sont alors renvoyées. Le modèle que nous utilisons créera une machine virtuelle Linux.

3. Lorsque vous avez sélectionné un modèle, vous pouvez le télécharger avec la commande suivante.

		azure group template download CoreOS.CoreOSStable.0.2.40-preview

	Les modèles téléchargés peuvent être personnalisés pour vos besoins. Vous pouvez, par exemple, ajouter une ressource.

	>[AZURE.NOTE]Si vous modifiez le modèle, utilisez la commande `azure group template validate` pour le valider avant de l'utiliser pour créer ou modifier un groupe de ressources.

4. Pour configurer le modèle de groupe de ressources pour votre usage, ouvrez le fichier de modèle dans un éditeur de texte. Remarquez la collection JSON **parameters** vers le début. Elle contient la liste des paramètres attendus par ce modèle pour créer les ressources décrites par le modèle. Certains paramètres peuvent posséder des valeurs par défaut, tandis que d'autres spécifient le type de la valeur ou une plage de valeurs autorisées.

	Lorsque vous utilisez un modèle, vous pouvez fournir des paramètres de ligne de commande ou spécifier un fichier contenant les valeurs des paramètres. Vous pouvez également écrire vos champs **valeur** directement dans la section **paramètres** du modèle, bien que le modèle serait étroitement lié à un déploiement spécifique et qu’il ne serait pas facilement réutilisable. Dans les deux cas, les paramètres doivent être au format JSON, et vous devez fournir vos propres valeurs pour ces clés qui n'ont pas de valeurs par défaut.

	Par exemple, pour créer un fichier qui contient les paramètres du modèle CoreOS.CoreOSStable.0.2.40-preview, utilisez les données suivantes pour créer un fichier nommé params.json. Remplacez les valeurs utilisées dans cet exemple par vos propres valeurs. L’**emplacement** doit spécifier une région Azure proche de chez vous, comme **Europe du Nord** ou **Sud du centre des États-Unis**. (Cet exemple utilise **Ouest des États-Unis**.)

		{
		  "newStorageAccountName": {
			"value": "testStorage"
		  },
		  "newDomainName": {
			"value": "testDomain"
		  },
		  "newVirtualNetworkName": {
			"value": "testVNet"
		  },
		  "vnetAddressSpace": {
			"value": "10.0.0.0/11"
		  },
		  "hostName": {
			"value": "testHost"
		  },
		  "userName": {
			"value": "azureUser"
		  },
		  "password": {
			"value": "Pass1234!"
		  },
		  "location": {
			"value": "West US"
		  },
		  "hardwareSize": {
			"value": "Medium"
		  }
	    }

5. Après avoir enregistré le fichier params.json, utilisez la commande suivante pour créer un groupe de ressources basé sur le modèle. Le paramètre `-e` spécifie le fichier params.json créé à l’étape précédente. Remplacez le **testRG** par le nom de groupe que vous souhaitez utiliser, et **testDeploy** par le nom de votre déploiement. L’emplacement doit être identique à celui spécifié dans votre fichier de paramètres du modèle params.json.

		azure group create "testRG" "West US" -f CoreOS.CoreOSStable.0.2.40-preview.json -d "testDeploy" -e params.json

	Cette commande renvoie la valeur OK une fois le déploiement chargé, mais avant qu’il soit appliqué aux ressources du groupe. Pour vérifier le statut du déploiement, utilisez la commande suivante.

		azure group deployment show "testRG" "testDeploy"

	**ProvisioningState** indique le statut du déploiement.

	Si votre déploiement a été effectué avec succès, vous verrez une sortie similaire à celle de l’exemple suivant.

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-04-27T07:49:18.5237635Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ----------------
		data:    newStorageAccountName  String        testStorage
		data:    newDomainName          String        testDomain
		data:    newVirtualNetworkName  String        testVNet
		data:    vnetAddressSpace       String        10.0.0.0/11
		data:    hostName               String        testHost
		data:    userName               String        azureUser
		data:    password               SecureString  undefined
		data:    location               String        West US
		data:    hardwareSize           String        Medium
		info:    group deployment show command OK

	>[AZURE.NOTE]Si vous réalisez que votre configuration n'est pas correcte et que vous devez arrêter un déploiement long, utilisez la commande suivante.
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> Si vous n'indiquez pas de nom de déploiement, un nom est créé automatiquement d'après celui du fichier de modèle. Il est renvoyé dans la sortie de la commande `azure group create`.

6. Pour afficher le groupe, utilisez la commande suivante.

		azure group show "testRG"

	Cette commande renvoie des informations sur les ressources du groupe. Si vous avez plusieurs groupes, vous pouvez utiliser la commande `azure group list` pour extraire la liste des noms de groupes et `azure group show` pour afficher les détails d'un groupe donné.

7. Vous pouvez également utiliser les derniers modèles directement depuis GitHub au lieu de les télécharger depuis la bibliothèque de modèles. Ouvrez [GitHub.com](http://www.github.com) et recherchez AzureRmTemplates. Sélectionnez le référentiel AzureRmTemplates et recherchez tous les modèles qui vous intéressent, par exemple, _101-simple-vm-from-image_. Si vous cliquez sur le modèle, vous verrez qu’il contient, entre autres fichiers, azuredeploy.json. Il représente ici le modèle que vous souhaitez utiliser dans votre commande à l’aide de l’option **--template-url**. Ouvrez-le dans le mode _raw_ et copiez l'URL qui apparaît dans la barre d'adresses du navigateur. Vous pouvez ensuite utiliser directement cette URL pour créer un déploiement, au lieu d’effectuer un téléchargement depuis une bibliothèque de modèles, en utilisant une commande similaire à l’exemple suivant.

		azure group deployment create "testDeploy" -g "testResourceGroup" --template-uri https://raw/githubusercontent.com/azurermtemplates/azurermtemplates/master/101-simple-vm-from-image/azuredeploy.json

	> [AZURE.NOTE]Il est très important d’ouvrir le modèle json en mode _raw_. L'URL qui apparaît dans la barre d'adresses du navigateur est différente de celle qui s'affiche en mode normal. Pour ouvrir le fichier en mode _raw_ quand vous voyez le fichier sur GitHub, dans le coin supérieur droit, cliquez sur **Raw**.

#### Utilisation des ressources

Les modèles vous permettent de déclarer des modifications de la configuration au niveau du groupe, mais vous devez parfois vous concentrer sur une ressource spécifique. Pour ce faire, utilisez les commandes `azure resource`.

> [AZURE.NOTE]Quand vous utilisez d'autres commandes `azure resource` que la commande `list`, vous devez spécifier la version de l'API de la ressource avec laquelle vous travaillez au moyen du paramètre `-o`. Si vous ne connaissez pas la version de l'API à utiliser, consultez le fichier de modèle et recherchez le champ **apiVersion** de la ressource.

1. Pour répertorier toutes les ressources d'un groupe, utilisez la commande suivante.

		azure resource list "testRG"

2. Pour afficher des ressources individuelles dans le groupe, utilisez la commande suivante :

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

	Notez le paramètre **Microsoft.ClassicCompute/virtualMachines**. Il indique le type de la ressource sur laquelle vous demandez des informations. Si vous regardez le fichier de modèle téléchargé plus tôt, vous pouvez noter que cette même valeur est utilisée pour définir le type de la ressource de machine virtuelle décrite dans le modèle.

	Cette commande renvoie des informations concernant la machine virtuelle.

3. Lorsque vous affichez des détails sur une ressource, nous vous recommandons d'utiliser le paramètre `--json`, qui facilite la lecture de la sortie, car certaines valeurs sont des structures imbriquées ou des collections. L’exemple suivant montre le retour des résultats de la commande **show** dans un document JSON.

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json

	>[AZURE.NOTE]Vous pouvez enregistrer les données JSON dans un fichier au moyen du caractère &gt; pour canaliser la sortie vers le fichier. Par exemple :
	>
	> `azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json > myfile.json`

4. Pour supprimer une ressource, utilisez la commande suivante.

		azure resource delete "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

## Journalisation

Pour afficher les informations de journalisation sur les opérations effectuées sur un groupe, utilisez la commande `azure group log show`. Par défaut, la dernière opération effectuée sur le groupe sera répertoriée. Pour afficher toutes les opérations, utilisez le paramètre facultatif `--all`. Pour le dernier déploiement, utilisez `--last-deployment`. Pour un déploiement spécifique, utilisez `--deployment`, puis spécifiez le nom du déploiement. L’exemple suivant retourne un journal de toutes les opérations effectuées sur le groupe « MyGroup ».

	azure group log show mygroup --all

## Étapes suivantes

* Pour plus d’informations sur l’utilisation de l’interface de ligne de commande Azure, voir [Installer et configurer l’interface de ligne de commande Azure][clisetup].
* Pour plus d’informations sur l’utilisation d’Azure Resource Manager avec Azure PowerShell, voir [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).
* Pour plus d’informations sur l’utilisation d’Azure Resource Manager à partir du portail Azure, voir [Utilisation des groupes de ressources pour gérer vos ressources Azure][psrm].

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[clisetup]: ../xplat-cli-install.md
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!---HONumber=Oct15_HO3-->