<properties
	pageTitle="Capturer une machine virtuelle Linux à utiliser en tant que modèle | Microsoft Azure"
	description="Apprenez à capturer l’image d’une machine virtuelle Azure sous Linux créée avec le modèle de déploiement Azure Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/15/2016"
	ms.author="danlep"/>


# Comment capturer une machine virtuelle Linux pour utiliser un modèle de gestionnaire de ressources

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-linux-classic-capture-image.md).


Utilisez l’interface de ligne de commande Azure pour capturer une machine virtuelle Azure exécutant Linux afin de pouvoir l’utiliser comme modèle Azure Resource Manager pour créer d’autres machines virtuelles. Ce modèle spécifie le disque du système d’exploitation, ainsi que les disques de données attachés à la machine virtuelle. Il n’inclut pas des ressources de réseau virtuel, et vous devez créer une machine virtuelle de gestionnaire de ressource et donc, dans la plupart des cas, vous devrez les configurer séparément avant de pouvoir créer une autre machine virtuelle utilisant le modèle.

## Avant de commencer

Ces opérations supposent que vous avez déjà créé une machine virtuelle Azure dans le modèle de déploiement de gestionnaire de ressources Azure et configuré les systèmes d’exploitation, incluant l’attachement des disques de données et en réalisant d’autres personnalisations comme l’installation de nouvelles applications. Vous pouvez le faire de plusieurs façons, notamment via l'interface de ligne de commande Azure. Si vous ne l’avez pas encore fait, voir ces instructions pour l’utilisation de l’interface CLI Azure en mode Azure Resource Manager :

- [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](virtual-machines-linux-cli-deploy-templates.md)

Par exemple, vous pouvez créer un groupe de ressources nommé *MyResourceGroup* dans la région centrale des États-Unis. Utilisez ensuite la commande **azure vm quick-create** semblable à la suivante pour déployer une machine virtuelle Ubuntu 14.04 LTS dans le groupe de ressources.

 	azure vm quick-create -g MyResourceGroup -n <your-virtual-machine-name> "centralus" -y Linux -Q canonical:ubuntuserver:14.04.2-LTS:latest -u <your-user-name> -p <your-password>

Une fois que la machine virtuelle est configurée et en cours d’exécution, vous pouvez attacher et monter un disque de données. Consultez les instructions [ici](virtual-machines-linux-add-disk.md).


## Capture de la machine virtuelle

1. Une fois que vous êtes prêt à capturer la machine virtuelle, connectez-vous à l’aide d’un client SSH.

2. Dans la fenêtre SSH, tapez la commande suivante. Notez que le résultat de **waagent** peut varier légèrement en fonction de la version utilisée :

	`sudo waagent -deprovision+user`

	Cette commande essaie de nettoyer le système et de le préparer pour le réapprovisionnement. Cette opération effectue les tâches suivantes :

	- supprime toutes les clés de l'hôte SSH (si Provisioning.RegenerateSshHostKeyPair a la valeur « y » dans le fichier de configuration) ;
	- efface la configuration de Nameserver dans /etc/resolv.conf ;
	- supprime le mot de passe `root` de l’utilisateur de /etc/shadow (si Provisioning.DeleteRootPassword a la valeur « y » dans le fichier de configuration) ;
	- supprime les baux du client DHCP mis en cache ;
	- Réinitialise le nom d’hôte sur localhost.localdomain.
	- Supprime le dernier compte d’utilisateur approvisionné (obtenu de /var/lib/waagent) et les données associées.

	>[AZURE.NOTE] L’annulation de l’approvisionnement supprime les fichiers et les données dans le but de « généraliser » l’image. Exécutez uniquement cette commande sur une machine virtuelle que vous souhaitez capturer en tant qu’image. Cela ne garantit pas que l’image est exempte de toute information sensible ou qu’elle convient pour la redistribution à des tiers.

3. Tapez **y** pour continuer. Vous pouvez ajouter le paramètre **-force** pour éviter cette étape de confirmation.

4. Tapez **exit** pour fermer le client SSH.

	>[AZURE.NOTE] Les étapes suivantes présupposent que vous avez déjà [installé l’interface de ligne de commande Azure](../xplat-cli-install.md) sur votre ordinateur client.

5. À partir de votre ordinateur client, ouvrez l’interface de ligne de commande Azure et connectez-vous à votre abonnement Azure. Pour plus d’informations, consultez [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI)](../xplat-cli-connect.md).

6. Assurez-vous que vous êtes en mode Gestionnaire de ressources :

	`azure config mode arm`

7. Arrêtez l’ordinateur virtuel que vous avez déjà annulé à l’aide de la commande suivante :

	`azure vm deallocate -g <your-resource-group-name> -n <your-virtual-machine-name>`

8. Généralisez la machine virtuelle en exécutant la commande suivante :

	`azure vm generalize –g <your-resource-group-name> -n <your-virtual-machine-name>`

9. Capturez maintenant l’image et un modèle de fichier local avec la commande suivante :

	`azure vm capture <your-resource-group-name>  <your-virtual-machine-name> <your-vhd-name-prefix> -t <your-template-file-name.json>`

	Cette commande crée une image générale du système d’exploitation à l’aide du préfixe de nom de disque dur virtuel que vous spécifiez pour les disques de machine virtuelle. Les fichiers d’image de disque dur virtuel sont créés par défaut dans le même compte de stockage utilisé par la machine virtuelle d’origine. L’option **-t** crée un modèle de fichier JSON local que vous pouvez utiliser pour créer une nouvelle machine virtuelle à partir de l’image.

>[AZURE.TIP] Pour trouver l’emplacement d’une image, ouvrez le modèle de fichier JSON. Dans **storageProfile**, trouvez l’**uri** de l’**image** située dans le conteneur **système**. Par exemple, l’uri de l’image du disque du système d’exploitation est semblable à `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/<your-image-prefix>-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.

## Déployer une machine virtuelle à partir de l’image capturée
Maintenant, utilisez l’image avec un modèle pour créer une nouvelle machine virtuelle Linux. Ces étapes vous montrent comment utiliser l’interface CLI Azure et le modèle de fichier JSON créé avec la commande `azure vm capture` pour créer la machine virtuelle dans un nouveau réseau virtuel.

### Créer des ressources réseau

Pour utiliser le modèle, vous devez d’abord configurer un réseau virtuel et une carte réseau pour votre nouvelle machine virtuelle. Nous vous recommandons de créer un nouveau groupe de ressources pour ces ressources. Exécutez des commandes comme suit en remplaçant les noms de vos ressources et de l’emplacement Azure approprié (« centralus » dans ces commandes) :

	azure group create <your-new-resource-group-name> -l "centralus"

	azure network vnet create <your-new-resource-group-name> <your-vnet-name> -l "centralus"

	azure network vnet subnet create <your-new-resource-group-name> <your-vnet-name> <your-subnet-name>

	azure network public-ip create <your-new-resource-group-name> <your-ip-name> -l "centralus"

	azure network nic create <your-new-resource-group-name> <your-nic-name> -k <your-subnetname> -m <your-vnet-name> -p <your-ip-name> -l "centralus"

Pour déployer un ordinateur virtuel à partir de l’image à l’aide du JSON que vous avez enregistré pendant la capture, vous aurez besoin de l’ID de carte réseau. Obtenez-le en exécutant la commande suivante.

	azure network nic show <your-new-resource-group-name> <your-nic-name>

L’**Id** dans le résultat de sortie est une chaîne qui ressemble à ce qui suit.

	/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<your-new-resource-group-name>/providers/Microsoft.Network/networkInterfaces/<your-nic-name>



### Créer un déploiement
Exécutez maintenant la commande suivante pour créer votre machine virtuelle à partir de l’image de machine virtuelle capturée et le fichier de modèle JSON, que vous avez enregistré.

	azure group deployment create <your-new-resource-group-name> <your-new-deployment-name> -f <your-template-file-name.json>

Vous êtes invité à fournir un nouveau nom de machine virtuelle, le nom d’utilisateur administrateur et un mot de passe et l’Id de la carte réseau que vous avez créée précédemment.

	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	vmName: mynewvm
	adminUserName: myadminuser
	adminPassword: ********
	networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic

Si votre déploiement est exécuté avec succès, vous voyez une sortie ressemblant à ce qui suit.

	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "dlnewdeploy"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mynewdeploy
	data:    ResourceGroupName  : mynewrg
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-29T16:35:47.3419991Z
	data:    Mode               : Incremental
	data:    Name                Type          Value


	data:    ------------------  ------------  -------------------------------------

	data:    vmName              String        mynewvm


	data:    vmSize              String        Standard_D1


	data:    adminUserName       String        myadminuser


	data:    adminPassword       SecureString  undefined


	data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic
	info:    group deployment create command OK

### Vérifier le déploiement

Maintenant, exécutez le SSH sur la machine virtuelle que vous avez créée pour vérifier le déploiement et lancez le déploiement et le démarrage de l’utilisation de la nouvelle machine virtuelle. Pour vous connecter via SSH, recherchez l’adresse IP de la machine virtuelle que vous avez créée en exécutant la commande suivante :

	azure network public-ip show <your-new-resource-group-name> <your-ip-name>

L’adresse IP publique est répertoriée dans la sortie de commande. Par défaut, vous vous connectez à la VM Linux par SSH sur le port 22.

## Créez des ordinateurs virtuels supplémentaires avec le modèle

Utilisez le modèle et l’image capturée pour déployer des machines virtuelles supplémentaires à l’aide de la procédure décrite dans la section précédente.

* Assurez-vous que votre image de machine virtuelle se trouve dans le compte de stockage qui hébergera le disque dur virtuel de votre machine virtuelle
* Copiez le fichier de modèle de JSON et entrez une valeur unique comme **uri** du disque dur virtuel de chaque machine virtuelle
* Créez une nouvelle carte réseau dans le même réseau ou dans un autre réseau virtuel
* Création d’un déploiement dans le groupe de ressources dans lequel vous configurez le réseau virtuel, à l’aide du modèle de fichier JSON modifié

Si vous souhaitez que le réseau soit automatiquement configuré lorsque vous créez une machine virtuelle à partir de l’image, utilisez le [modèle 101-vm-from-user-image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) à partir de GitHub. Ce modèle crée un ordinateur virtuel à partir de votre image personnalisée et des ressources virtuelles réseau nécessaires, de l’adresse IP publique et des ressources de carte réseau. Pour une démonstration de l’utilisation du modèle dans le portail Azure, consultez [Comment créer une machine virtuelle à partir d’une image personnalisée à l’aide d’un modèle ARM](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/) (en anglais).

## Utilisation de la commande azure vm create

En général, pour créer une machine virtuelle à partir de l’image, vous devez utiliser un modèle de gestionnaire de ressource. Toutefois, vous pouvez créer la machine virtuelle de façon _impérative_ à l’aide de la commande **azure vm create** avec le paramètre **-Q** (**--image-urn**). Vous devez également passer le paramètre **-d** (**--os-disk-vhd**) pour spécifier l'emplacement du fichier .vhd du système d'exploitation pour la nouvelle machine virtuelle. Il doit se trouver dans le conteneur de disques durs virtuels du compte de stockage sur lequel se trouve le fichier d'image du disque dur virtuel. La commande copie le disque dur virtuel pour la nouvelle machine virtuelle automatiquement sur le conteneur de disques durs virtuels.

Procédez comme suit avant d’exécuter **azure vm create** avec l’image :

1.	Créez un groupe de ressources ou identifiez un groupe de ressources existant pour le déploiement.

2.	Créez une ressource d’adresse IP publique et une ressource de la carte réseau pour la nouvelle machine virtuelle. Pour savoir comment créer un réseau virtuel, une adresse IP publique et une carte réseau à l’aide de l’interface CLI, voir plus haut dans cet article. (**azure vm create** peut également créer une nouvelle carte réseau, mais vous devez transmettre des paramètres supplémentaires pour un réseau virtuel et un sous-réseau.)


Ensuite, exécutez une commande semblable à la suivante, en passant des URI vers le nouveau fichier de disque dur virtuel de système d'exploitation et l'image existante.

	azure vm create <your-resource-group-name> <your-new-vm-name> eastus Linux -d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/<your-new-VM-prefix>.vhd" -Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/<your-image-prefix>-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd" -z Standard_A1 -u <your-admin-name> -p <your-admin-password> -f <your-nic-name>

Pour obtenir d’autres options de commande supplémentaires, exécutez `azure help vm create`.

## Étapes suivantes

Pour gérer vos machines virtuelles à l’aide de l’interface de ligne de commande, consultez les tâches décrites dans [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](virtual-machines-linux-cli-deploy-templates.md).

<!---HONumber=AcomDC_0511_2016-->