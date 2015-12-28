<properties 
	pageTitle="Utiliser SSH sous Linux et Mac | Microsoft Azure" 
	description="Générez et utilisez des clés SSH sous Linux et Mac pour les modèles de déploiement du gestionnaire de ressources et classiques sur Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/28/2015" 
	ms.author="rasquill"/>

#Utilisation de SSH avec Linux et Mac sur Azure

> [AZURE.SELECTOR]
- [Windows](../articles/virtual-machines/virtual-machines-windows-use-ssh-key.md)
- [Linux/Mac](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md)

Cette rubrique explique comment utiliser **ssh-keygen** et **openssl** sous Linux et Mac pour créer et utiliser les fichiers aux formats **ssh-rsa** et **.pem** pour sécuriser la communication avec des machines virtuelles Azure basées sur Linux. La création de machines virtuelles Azure basées sur Linux à l'aide du modèle de déploiement du gestionnaire de ressources est recommandée pour les nouveaux déploiements et s'appuie sur une chaîne ou un fichier de clé publique *ssh rsa* (en fonction du client de déploiement). La [version préliminaire du portail](https://portal.azure.com) n'accepte actuellement que les chaînes de format **ssh-rsa**, que ce soit pour les déploiements classiques ou du gestionnaire de ressources.

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]Pour créer ces types de fichiers à utiliser sur un ordinateur Windows afin de communiquer en toute sécurité avec des machines virtuelles Linux dans Azure, consultez [Utiliser des clés SSH sous Windows](virtual-machines-windows-use-ssh-key.md).

## Quels sont les fichiers requis ?

La configuration SSH de base pour Azure inclut une paire de clés publiques et privées **ssh-rsa** de 2 048 bits (par défaut, **ssh-keygen** stocke ces fichiers sous la forme **~/.ssh/id\_rsa** et **~/.ssh/id-rsa.pub**, sauf si vous modifiez les valeurs par défaut) ainsi qu'un fichier `.pem` généré à partir du fichier de clé privée **id\_rsa** pour une utilisation avec le modèle de déploiement classique du portail classique.

Voici les scénarios de déploiement et les types de fichiers que vous utilisez dans chacun d'eux

1. Les clés **ssh rsa** sont requises pour tout déploiement à l'aide du [portail en version préliminaire](https://portal.azure.com), quel que soit le modèle de déploiement.
2. Les fichiers .pem sont nécessaires pour créer des machines virtuelles à l'aide du [portail classique](https://manage.windowsazure.com). Les fichiers .pem sont également pris en charge dans les déploiements classiques qui utilisent l'[interface de ligne de commande CLI](xplat-cli-install.md). 

## Création de clés à utiliser avec SSH

Azure nécessite des fichiers de clé au format **ssh-rsa** 2 048 bits ou les fichiers .pem équivalents, en fonction de votre scénario. Si vous avez déjà ces fichiers, transmettez le fichier de clé publique lors de la création de votre machine virtuelle Azure.

Si vous avez besoin de créer les fichiers :

1. Assurez-vous que votre implémentation de **ssh-keygen** et **openssl** est à jour. Cela varie selon la plateforme. 

	- Pour Mac, consultez le [site web sur la sécurité des produits Apple](https://support.apple.com/HT201222) et choisissez les mises à jour appropriées si nécessaire.
	- Pour les distributions Linux basées sur Debian comme Ubuntu, Debian, Mint, et ainsi de suite :

			sudo apt-get update ssh-keygen
			sudo apt-get update openssl

	- Pour les distributions Linux basées sur RPM comme CentOS et Oracle Linux :

			sudo yum update ssh-keygen
			sudo yum update openssl

	- Pour SLES et OpenSUSE

			sudo zypper update ssh-keygen
			sudo zypper update openssl

2. Utilisez **ssh-keygen** pour créer des fichiers de clés publiques et privées RSA 2048 bits, et à moins d'avoir un emplacement spécifique ou des noms spécifiques pour les fichiers, acceptez l'emplacement par défaut et le nom `~/.ssh/id_rsa`. La commande de base est la suivante :

		ssh-keygen -t rsa -b 2048 

	En règle générale, votre implémentation **ssh-keygen** ajoute un commentaire, souvent le nom d'utilisateur et le nom d'hôte de l'ordinateur. Vous pouvez spécifier un commentaire spécifique à l'aide de l'option `-C`.

3. Créez un fichier .pem à partir de votre fichier `~/.ssh/id_rsa` pour pouvoir travailler avec le portail classique. Utilisez **openssl** comme suit :

		openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

	Si vous souhaitez créer un fichier .pem à partir d'un autre fichier de clé privée, modifiez l'argument `-key`.

> [AZURE.NOTE]Si vous prévoyez de gérer des services déployés avec le modèle de déploiement classique, vous pouvez également créer un fichier au format **.cer** à télécharger sur le portail, bien que cela n'implique pas **ssh** ni la connexion à des machines virtuelles Linux (l'objet de cet article). Pour créer ces fichiers sous Linux ou Mac, tapez : <br /> openssl.exe x509 -outform der -in myCert.pem -out myCert.cer

Pour convertir votre fichier .pem en un fichier de certificat DER X509 codé.

## Utiliser des clés SSH que vous avez déjà

Vous pouvez utiliser des clés ssh-rsa (`.pub`) pour tous les nouveaux travaux, surtout avec le modèle de déploiement du gestionnaire de ressources et la version préliminaire du portail. Vous devrez peut-être créer un fichier `.pem` à partir de vos clés pour utiliser le portail classique.

## Création d'une machine virtuelle avec votre fichier de clé publique

Une fois que vous avez créé les fichiers nécessaires, il existe de nombreuses façons de créer une machine virtuelle à laquelle vous pouvez vous connecter en toute sécurité à l'aide d'un échange de clés publiques/privées. Dans presque toutes les situations, notamment lors de déploiements du gestionnaire de ressources, transmettez le fichier .pub lorsque vous y êtes invité pour définir le chemin du fichier de clé ssh ou le contenu d'un fichier en tant que chaîne.

### Exemple : Création d'une machine virtuelle avec le fichier id\_rsa.pub

L'utilisation la plus courante concerne la création impérative d'une machine virtuelle ou le téléchargement d'un modèle pour créer une machine virtuelle. L'exemple de code suivant montre comment créer une machine virtuelle Linux sécurisée dans Azure en transmettant le nom de fichier public (dans ce cas, le fichier par défaut `~/.ssh/id_rsa`) à la commande `azure vm create`. (Les autres arguments ont été précédemment créés).

	azure vm create \
	--nic-name testnic \
	--public-ip-name testpip \
	--vnet-name testvnet \
	--vnet-subnet-name testsubnet \
	--storage-account-name computeteststore 
	--image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
	--username ops \
	-ssh-publickey-file ~/.ssh/id_rsa \
	testrg testvm westeurope linux

L'exemple suivant montre comment utiliser le format **ssh-rsa** avec un modèle de gestionnaire de ressources et l'interface de ligne de commande Azure pour créer une machine virtuelle Ubuntu sécurisée par un nom d'utilisateur et le contenu de `~/.ssh/id_rsa.pub` sous forme de chaîne. (Dans ce cas, la chaîne de clé publique est raccourcie pour être plus lisible).

	azure group deployment create \
	--resource-group test-sshtemplate \
	--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
	--name mysshdeployment
	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	testnewStorageAccountName: testsshvmtemplate3
	adminUserName: ops
	sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQA+/L+rHIjz+nXTzxApgnP+iKDZco9 user@macbookpro
	dnsNameForPublicIP: testsshvmtemplate
	location: West Europe
	vmName: sshvm
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "mysshdeployment"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mysshdeployment
	data:    ResourceGroupName  : test-sshtemplate
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-08T00:12:12.2529678Z
	data:    Mode               : Incremental
	data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	data:    ContentVersion     : 1.0.0.0
	data:    Name                   Type    Value

	data:    newStorageAccountName  String  testtestsshvmtemplate3
	data:    adminUserName          String  ops
	data:    sshKeyData             String  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDAkek3P6V3EhmD+xP+iKDZco9 user@macbookpro
	data:    dnsNameForPublicIP     String  testsshvmtemplate
	data:    location               String  West Europe
	data:    vmSize                 String  Standard_A2
	data:    vmName                 String  sshvm
	data:    ubuntuOSVersion        String  14.04.2-LTS
	info:    group deployment create command OK


### Exemple : Création d'une machine virtuelle avec un fichier .pem

Vous pouvez ensuite utiliser le fichier .pem du portail classique ou avec le mode de déploiement classique et `azure vm create`, comme dans l'exemple suivant :

	azure vm create \
	-l "West US" -n testpemasm \
	-P -t myCert.pem -e 22 \
	testpemasm \
	b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-fr-FR-30GB \
	ops
	info:    Executing command vm create
	warn:    --vm-size has not been specified. Defaulting to "Small".
	+ Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-fr-FR-30GB
	+ Looking up cloud service
	info:    cloud service testpemasm not found.
	+ Creating cloud service
	+ Retrieving storage accounts
	+ Configuring certificate
	+ Creating VM
	info:    vm create command OK


## Connexion à votre machine virtuelle

La commande **ssh** utilise un nom d'utilisateur pour ouvrir une session, l'adresse réseau de l'ordinateur, et le port auquel se connecter à l'adresse, ainsi que de nombreuses autres variations spéciales. (Pour plus d’informations sur **ssh**, consultez cet [article sur Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell))

Une utilisation typique avec le déploiement du gestionnaire de ressources peut ressembler à ce qui suit, si vous avez spécifié simplement un sous-domaine et un emplacement de déploiement :

	ssh user@subdomain.westus.cloudapp.azure.com -p 22

Ou, si vous vous connectez à un service cloud de déploiement classique, l'adresse que vous utiliseriez peut ressembler à ceci :

	ssh user@subdomain.cloudapp.net -p 22

Étant donné que le formulaire d'adresse peut changer, vous pouvez toujours utiliser l'adresse IP ou peut-être un nom de domaine personnalisé affecté, et vous devrez découvrir l'adresse de votre machine virtuelle Azure.

### Découverte de votre adresse SSH de machine virtuelle Azure avec les déploiements classiques

Vous pouvez découvrir l'adresse à utiliser avec une machine virtuelle et le modèle de déploiement classique en exécutant la commande `azure vm show` avec le nom de la machine virtuelle :

	azure vm show testpemasm
	info:    Executing command vm show
	+ Getting virtual machines
	data:    DNSName "testpemasm.cloudapp.net"
	data:    Location "West US"
	data:    VMName "testpemasm"
	data:    IPAddress "100.116.160.154"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Small"
	data:    Image "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-fr-FR-30GB"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "testpemasm-testpemasm-0-201510102050230517"
	data:    OSDisk mediaLink "https://portalvhds4blttsxgjj1rf.blob.core.windows.net/vhd-store/testpemasm-2747c9c432b043ff.vhd"
	data:    OSDisk sourceImageName "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-fr-FR-30GB"
	data:    OSDisk operatingSystem "Linux"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "40.83.178.221"
	data:    VirtualIPAddresses 0 name "testpemasmContractContract"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    Network Endpoints 0 localPort 22
	data:    Network Endpoints 0 name "ssh"
	data:    Network Endpoints 0 port 22
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "40.83.178.221"
	data:    Network Endpoints 0 enableDirectServerReturn false
	info:    vm show command OK

### Découverte de votre adresse SSH de machine virtuelle Azure avec les déploiements de gestionnaire de ressources

	azure vm show testrg testvm
	info:    Executing command vm show
	+ Looking up the VM "testvm"
	+ Looking up the NIC "testnic"
	+ Looking up the public ip "testpip"

Examinez la section de profil réseau :

	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-21-8E-AE
	data:          Provisioning State        :Succeeded
	data:          Name                      :testnic
	data:          Location                  :westeurope
	data:            Private IP alloc-method :Static
	data:            Private IP address      :192.168.1.101
	data:            Public IP address       :40.115.48.189
	data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
	data:
	data:    Diagnostics Instance View:
	info:    vm show command OK

Si vous n'avez pas utilisé le port par défaut SSH 22 lorsque vous avez créé la machine virtuelle, vous pouvez découvrir quels ports disposent de règles entrantes grâce à la `azure network nsg show` commande, comme dans l'exemple suivant :

	azure network nsg show testrg testnsg
	info:    Executing command network nsg show
	+ Looking up the network security group "testnsg"
	data:    Id                              : /subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testnsg
	data:    Name                            : testnsg
	data:    Type                            : Microsoft.Network/networkSecurityGroups
	data:    Location                        : westeurope
	data:    Provisioning state              : Succeeded
	data:    Security group rules:
	data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
	data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
	data:    testnsgrule                    *                  *            *               22                Tcp       Inbound    Allow   1000
	data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
	data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
	data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
	data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
	data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
	data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
	info:    network nsg show command OK

### Exemple : résultat d'une session SSH à l'aide de clés .pem et d'un déploiement classique

Si vous avez créé une machine virtuelle à l'aide d'un fichier .pem créé à partir de votre fichier `~/.ssh/id_rsa`, vous pouvez utiliser directement ssh dans cette machine virtuelle. Notez que dans ce cas, la négociation du certificat utilisera votre clé privée à `~/.ssh/id_rsa`. Elle devrait ressembler à l'exemple suivant :

	ssh ops@testpemasm.cloudapp.net -p 22
	The authenticity of host 'testpemasm.cloudapp.net (40.83.178.221)' can't be established.
	RSA key fingerprint is dc:bb:e4:cc:59:db:b9:49:dc:71:a3:c8:37:36:fd:62.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'testpemasm.cloudapp.net,40.83.178.221' (RSA) to the list of known hosts.
	Saving password to keychain failed
	Identity added: /Users/rasquill/.ssh/id_rsa (/Users/rasquill/.ssh/id_rsa)
	Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)

	* Documentation:  https://help.ubuntu.com/

	System information as of Sat Oct 10 20:53:08 UTC 2015

	System load: 0.52              Memory usage: 5%   Processes:       80
	Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

	Graph this data and manage this system at:
		https://landscape.canonical.com/

	Get cloud support with Ubuntu Advantage Cloud Guest:
		http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.

	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

## Si vous avez des problèmes de connexion

Vous pouvez lire les suggestions de la rubrique [Dépannage SSH connexions](virtual-machines-troubleshoot-ssh-connections.md) pour vérifier si elles peuvent vous aider à résoudre le problème.

## Étapes suivantes
 
Maintenant que vous êtes connecté à votre machine virtuelle, veillez à mettre à jour la distribution que vous avez choisie avant de continuer à l'utiliser.

<!---HONumber=AcomDC_1217_2015-->