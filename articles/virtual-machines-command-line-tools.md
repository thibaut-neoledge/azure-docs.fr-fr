<properties
	pageTitle="Commandes de l’interface de ligne de commande Azure en mode Service Management | Microsoft Azure"
	description="Commandes de l'interface de ligne de commande (CLI) Azure en mode Service Management pour gérer les déploiements dans le modèle de déploiement classique"
	services="virtual-machines-linux,virtual-machines-windows,mobile-services, cloud-services"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="danlep"/>

# Commandes de l’interface de ligne de commande Azure en mode Azure Service Management (ASM)

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines/azure-cli-arm-commands.md).

Cet article fournit la syntaxe et les options des commandes de l’interface de ligne de commande Azure régulièrement utilisées pour créer et gérer les ressources Azure dans le modèle de déploiement classique. Pour accéder à ces commandes, exécutez l’interface de ligne de commande en mode Azure Service Management (ASM). Il ne s’agit pas d’une référence complète, et votre version de l’interface de ligne de commande peut présenter des commandes ou paramètres légèrement différents.

Pour commencer, installez l’[interface de ligne de commande Azure](xplat-cli-install.md) et [connectez-vous à votre abonnement Azure](xplat-cli-connect.md).

Pour la syntaxe et les options de commande courantes dans la ligne de commande en mode Service Management, tapez `azure help` ou, pour afficher de l’aide concernant une commande particulière, `azure help [command]`. Vous trouverez également des exemples d’interface CLI dans la documentation pour la création et la gestion de services Azure spécifiques.

Les paramètres facultatifs sont indiqués entre crochets (par exemple, `[parameter]`). Tous les autres paramètres sont obligatoires.

Outre les paramètres facultatifs propres aux commandes qui vous sont présentés dans ce document, vous pouvez utiliser trois paramètres facultatifs pour afficher une sortie détaillée, telle que les options de demande et les codes d'état. Si le paramètre `-v` fournit une sortie détaillée, le paramètre `-vv` propose des informations encore plus complètes. L’option `--json` génère le résultat au format json brut.

## Configuration du mode Service Management

Le mode Service Management est actuellement activé par défaut lorsque vous installez l'interface CLI. Si vous en avez besoin, utilisez la commande suivante pour activer les commandes Service Management d’Azure CLI.

	azure config mode asm

>[AZURE.NOTE] Le mode Gestionnaire de ressources et le mode Azure Service Management s'excluent mutuellement. En d'autres termes, les ressources créées dans un mode ne peuvent pas être gérées dans l'autre mode.

## Gérer vos informations de compte et les paramètres de publication
L’interface de ligne de commande peut se servir des informations sur votre abonnement Azure pour se connecter à votre compte. (Pour d’autres options, consultez [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure](xplat-cli-connect.md).) Ces informations peuvent être obtenues depuis le portail Azure Classic dans un fichier de paramètres de publication, comme décrit dans ce document. Vous pouvez importer le fichier de paramètres de publication en tant que paramètre de configuration local persistant dont l’interface de ligne de commande se servira pour les opérations ultérieures. Vous importez vos paramètres de publication une fois pour toutes.

**account download [options]**

Cette commande permet de démarrer un navigateur pour télécharger votre fichier .publishsettings depuis le portail Azure Classic.

	~$ azure account download
	info:   Executing command account download
	info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
	help:   Save the downloaded file, then execute the command
	help:   account import <file>
	info:   account download command OK

**account import [options] &lt;fichier>**


Cette commande permet d'importer un fichier publishsettings ou un certificat pour pouvoir être utilisé ultérieurement par l'outil.

	~$ azure account import publishsettings.publishsettings
	info:   Importing publish settings file publishsettings.publishsettings
	info:   Found subscription: 3-Month Free Trial
	info:   Found subscription: Pay-As-You-Go
	info:   Setting default subscription to: 3-Month Free Trial
	warn:   The 'publishsettings.publishsettings' file contains sensitive information.
	warn:   Remember to delete it now that it has been imported.
	info:   Account publish settings imported successfully

> [AZURE.NOTE] Le fichier publishsettings peut contenir les détails (c'est-à-dire, le nom et l'ID d'abonnement) concernant plusieurs abonnements. Lorsque vous importez le fichier publishsettings, le premier abonnement est utilisé comme description par défaut. Pour utiliser un abonnement différent, exécuter la commande suivante. <code>~$ azure config set subscription &lt;id-autre-abonnement&gt;</code>

**account clear [options]**

Cette commande permet de supprimer les paramètres de publications qui ont été importés. Utilisez-la si vous avez fini d'utiliser l'outil sur cet ordinateur et si vous voulez vous assurer que l'outil ne peut pas être utilisé avec votre compte à un moment ultérieur.

	~$ azure account clear
	Clearing account info.
	info:   OK

**account list [options]**

Répertorie les abonnements importés.

	~$ azure account list
	info:    Executing command account list
	data:    Name                                    Id
	       Current
	data:    --------------------------------------  -------------------------------
	-----  -------
	data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
	data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
	data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**account set [options] &lt;abonnement&gt;**

Définit l'abonnement actif.

###Commandes pour gérer vos groupes d'affinités

**account affinity-group list [options]**

Cette commande permet de répertorier vos groupes d'affinités Azure.

Des groupes d'affinités peuvent être définis lorsqu'un groupe de machines virtuelles englobe plusieurs machines physiques. Un groupe d'affinités indique que les machines physiques doivent être aussi proches que possible les unes des autres pour réduire le temps de réponse du réseau.

	~$ azure account affinity-group list
	+ Fetching affinity groups
	data:   Name                                  Label   Location
	data:   ------------------------------------  ------  --------
	data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
	info:   account affinity-group list command OK

**account affinity-group create [options] &lt;nom&gt;**

Cette commande permet de créer un groupe d'affinités.

	~$ azure account affinity-group create opentec -l "West US"
	info:    Executing command account affinity-group create
	+ Creating affinity group
	info:    account affinity-group create command OK

**account affinity-group show [options] &lt;nom&gt;**

Cette commande permet d'afficher les détails du groupe d'affinités.

	~$ azure account affinity-group show opentec
	info:    Executing command account affinity-group show
	+ Getting affinity groups
	data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
	data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
	data:    Name "opentec"
	data:    Label "b3BlbnRlYw=="
	data:    Description $ i:nil "true"
	data:    Location "West US"
	data:    HostedServices ""
	data:    StorageServices ""
	data:    Capabilities Capability 0 "PersistentVMRole"
	data:    Capabilities Capability 1 "HighMemory"
	info:    account affinity-group show command OK

**account affinity-group delete [options] &lt;nom&gt;**

Cette commande permet de supprimer le groupe d'affinités spécifié.

	~$ azure account affinity-group delete opentec
	info:    Executing command account affinity-group delete
	Delete affinity group opentec? [y/n] y
	+ Deleting affinity group
	info:    account affinity-group delete command OK

###Commandes pour gérer votre environnement de compte

**account env list [options]**

Répertorie les environnements de compte.

	C:\windows\system32>azure account env list
	info:    Executing command account env list
	data:    Name
	data:    ---------------
	data:    AzureCloud
	data:    AzureChinaCloud
	info:    account env list command OK

**account env show [options] [environnement]**

Affiche les détails d'environnement de compte.

	~$ azure account env show
	info:    Executing command account env show
	Environment name: AzureCloud
	data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
	data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
	info:    account env show command OK

**account env add [options] [environnement]**

Cette commande permet d'ajouter un environnement au compte.

**account env set [options] [environnement]**

Cette commande permet de définir l'environnement de compte.

**account env delete [options] [environnement]**

Cette commande permet de supprimer l'environnement spécifié du compte.

## Commandes pour gérer vos machines virtuelles classiques
Le schéma suivant montre comment les machines virtuelles Azure classiques sont hébergées dans l'environnement de déploiement de production d'un service cloud Azure.

![Schéma technique Azure](./media/virtual-machines-command-line-tools/architecturediagram.jpg)

**create-new** crée le lecteur dans le stockage d'objets blob (en l'occurrence, e:\\ dans le schéma) ; **attach** attache un disque déjà créé mais non attaché à une machine virtuelle.

**vm create [options] &lt;nom-dns> &lt;image> &lt;nomUtilisateur> [motdepasse]**

Cette commande permet de créer une machine virtuelle Azure. Par défaut, chaque machine virtuelle est créée dans son propre service cloud ; toutefois, vous pouvez faire en sorte qu'une machine virtuelle soit ajoutée à un service cloud existant à l'aide de l'option -c décrite dans ce document.

Comme le portail Azure Classic, la commande vm create ne crée les machines virtuelles que dans l’environnement de déploiement de production. Il n'existe aucune option permettant de créer une machine virtuelle dans l'environnement de déploiement intermédiaire d'un service cloud. Si votre abonnement ne dispose pas d'un compte Azure Storage existant, la commande en crée un.

Vous pouvez spécifier un emplacement via le paramètre --location ou un groupe d'affinités via le paramètre --affinity-group. Si aucun n'est fourni, vous êtes invité à en fournir un dans la liste des emplacements valides.

Le mot de passe fourni doit être constitué de 8 à 123 caractères et doit respecter les exigences de complexité des mots de passe du système d'exploitation que vous utilisez pour la machine virtuelle.

Si vous prévoyez la nécessité de recourir à SSH pour gérer une machine virtuelle Linux déployée (comme c'est généralement le cas), vous devez activer SSH via l'option -e au moment de créer la machine virtuelle. Il n'est pas possible d'activer SSH une fois que la machine virtuelle a été créée.

Sur les machines virtuelles Windows, il est possible d'activer RDP ultérieurement en ajoutant le port 3389 comme point de terminaison.

Les paramètres facultatifs pris en charge pour cette commande sont les suivants :

**-c, --connect** crée la machine virtuelle dans un déploiement déjà créé dans un service d'hébergement. Si la commande -vmname n’est pas utilisée avec cette option, le nom de la nouvelle machine virtuelle sera généré automatiquement.<br /> **-n, --vm-name** Spécifie le nom de la machine virtuelle. Ce paramètre prend le nom du service d'hébergement par défaut. Si -vmname n’est pas spécifié, le nom de la nouvelle machine virtuelle est généré sous la forme &lt;nom-service>&lt;id>, où &lt;id> correspond au nombre de machines virtuelles existant dans le service plus 1. Par exemple, si vous utilisez cette commande pour ajouter une machine virtuelle à un service d’hébergement MyService qui comporte une machine virtuelle existante, la nouvelle machine virtuelle est nommée MyService2.<br /> **-u, --blob-url** Spécifie l’URL cible du stockage d’objets blob à partir duquel le disque système de la machine virtuelle est créé. <br /> **-z, --vm-size** Spécifie la taille de la machine virtuelle. Les valeurs valides sont : ExtraSmall, Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9, A10, A11, Basic\_A0, Basic\_A1, Basic\_A2, Basic\_A3, Basic\_A4, Standard\_D1, Standard\_D2, Standard\_D3, Standard\_D4, Standard\_D11, Standard\_D12, Standard\_D13, Standard\_D14, Standard\_DS1, Standard\_DS2, Standard\_DS3, Standard\_DS4, Standard\_DS11, Standard\_DS12, Standard\_DS13, Standard\_DS14, Standard\_G1, Standard\_G2, Standard\_G3, Standard\_G4, Standard\_G55. La valeur par défaut est Small. <br /> **-r** Ajoute une connectivité RDP à une machine virtuelle Windows. <br /> **-e, --ssh** Ajoute une connectivité SSH à une machine virtuelle Windows. <br /> **-t, --ssh-cert** Spécifie le certificat SSH. <br /> **-s** L’abonnement <br /> **-o, --community** L’image communiquée est une image de communauté. <br /> **-w** Le nom du réseau virtuel <br/> **-l, --location** Spécifie le lieu (par exemple « Nord du centre des États-Unis »). <br /> **-a, --affinity-group** Spécifie le groupe d’affinité.<br /> **-w, --virtual-network-name** Spécifie le réseau virtuel auquel ajouter la nouvelle machine virtuelle. Les réseaux virtuels peuvent être configurés et gérés à partir du portail Azure Classic.<br /> **-b, --subnet-names** Spécifie les noms de sous-réseau à attribuer à la machine virtuelle.

Dans cet exemple, MSFT\_\_Win2K8R2SP1-120514-1520-141205-01-en-us-30GB est une image fournie par la plateforme. Pour plus d'informations sur les images de système d'exploitation, consultez la commande vm image list.

	~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "West US" -r
	info:   Executing command vm create
	Enter VM 'my-vm-name' password: ************
	info:   vm create command OK

**vm create-from &lt;nom-dns> &lt;fichier-rôle>**

Cette commande permet de créer une machine virtuelle Azure à partir d'un fichier de rôle JSON.

	~$ azure vm create-from my-vm example.json
	info:   OK

**vm list [options]**

Cette commande permet de répertorier les machines virtuelles Azure. L'option -json spécifie que les résultats sont retournés au format JSON brut.

	~$ azure vm list
	info:   Executing command vm list
	data:   DNS Name                          VM Name      Status
	data:   --------------------------------  -----------  ---------
	data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
	info:   vm list command OK

**vm location list [options]**

Cette commande permet de répertorier tous les emplacements de compte Azure disponibles.

	~$ azure vm location list
	info:   Executing command vm location list
	data:   Name                   Display Name
	data:   ---------------------  ------------
	data:   Azure Preview  West US
	info:   account location list command OK

**vm show [options] &lt;nom>**

Cette commande permet d'afficher les détails concernant une machine virtuelle Azure. L'option -json spécifie que les résultats sont retournés au format JSON brut.

	~$ azure vm show my-vm
	info:   Executing command vm show
	data:   {
	data:       InstanceSize: 'Small',
	data:       InstanceStatus: 'ReadyRole',
	data:       DataDisks: [],
	data:       IPAddress: '10.26.192.206',
	data:       DNSName: 'my-vm.cloudapp.net',
	data:       InstanceStateDetails: {},
	data:       VMName: 'my-vm',
	data:       Network: {
	data:           Endpoints: [
	data:               {
	data:                   Protocol: 'tcp',
	data:                   Vip: '65.52.250.250',
	data:                   Port: '63238' ,
	data:                   LocalPort: '3389',
	data:                   Name: 'RemoteDesktop'
	data:               }
	data:           ]
	data:       },
	data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
	data:   }
	info:   vm show command OK

**vm delete [options] &lt;nom>**

Cette commande permet de supprimer une machine virtuelle Azure. Par défaut, elle ne supprime pas l'objet blob Azure à partir duquel le disque de système d'exploitation et le disque de données sont créés. Pour supprimer l'objet blob, ainsi que la machine virtuelle sur laquelle il est basé, spécifiez l'option -b.

	~$ azure vm delete my-vm
	info:   Executing command vm delete
	info:   vm delete command OK

**vm start [options] &lt;nom>**

Cette commande permet de démarrer une machine virtuelle Azure.

	~$ azure vm start my-vm
	info:   Executing command vm start
	info:   vm start command OK

**vm restart [options] &lt;nom>**

Cette commande permet de redémarrer une machine virtuelle Azure.

	~$ azure vm restart my-vm
	info:   Executing command vm restart
	info:   vm restart command OK

**vm shutdown [options] &lt;nom>**

Cette commande permet d'arrêter une machine virtuelle Azure. Vous pouvez utiliser l'option -p pour indiquer que la ressource de calcul ne doit pas être libérée lors de l'arrêt.

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**vm capture &lt;nom-mv> &lt;nom-image-cible>**

Cette commande permet de capturer une image de machine virtuelle Azure.

Une image de machine virtuelle ne peut être capturée que si l’état de la machine virtuelle est **Arrêté**. Arrêtez la machine virtuelle avant de continuer.

	~$ azure.cmd vm capture my-vm mycaptureimagename --delete
	info:   Executing command vm capture
	+ Fetching VMs
	+ Capturing VM
	info:   vm capture command OK

**vm export [options] &lt;nom-mv> &lt;chemin-fichier>**

Cette commande permet d'exporter une image de machine virtuelle Azure vers un fichier.

	~$ azure vm export "myvm" "C:"
	info:    Executing command vm export
	+ Getting virtual machines
	+ Exporting the VM
	info:   vm export command OK

##  Commandes pour gérer vos points de terminaison de machine virtuelle Azure
Le schéma suivant illustre l'architecture d'un déploiement type de plusieurs instances d'une machine virtuelle classique. Dans cet exemple, notez que le port 3389 est ouvert sur chaque machine virtuelle (pour l'accès RDP) et que chacune d'elles a une adresse IP interne (en l'occurrence, 168.55.11.1) qui est utilisée par l'équilibrage de charge pour acheminer le trafic vers la machine virtuelle. Cette adresse IP interne peut également être utilisée pour permettre la communication entre les machines virtuelles.

![azurenetworkdiagram](./media/virtual-machines-command-line-tools/networkdiagram.jpg)

Les demandes externes adressées aux machines virtuelles transitent par l'équilibrage de charge. De ce fait, les demandes ne peuvent pas être spécifiées pour une machine virtuelle en particulier dans les déploiements constitués de plusieurs machines virtuelles. Dans ces déploiements, le mappage de port doit être configuré entre les machines virtuelles (vm-port) et l'équilibrage de charge (lb-port).

**vm endpoint create &lt;nom-mv> &lt;port-équilibrage> [port-mv]**

Cette commande permet de créer un point de terminaison de machine virtuelle. Vous pouvez également utiliser -u ou --enable-direct-server-return pour indiquer si le retour au serveur direct doit être activé ou non sur ce point de terminaison (option désactivée par défaut).

	~$ azure vm endpoint create my-vm 8888 8888
	azure vm endpoint create my-vm 8888 8888
	info:   Executing command vm endpoint create
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint create command OK

**vm endpoint create-multiple [options] &lt;nom-mv> &lt;port-équilibrage>[:&lt;port-mv>[:&lt;protocole>[:&lt;enable-direct-server-return>[:&lt;nom-définition-équilibrage>[:&lt;protocole-sondage>[:&lt;port-sondage>[:&lt;chemin-sondage>[:&lt;nom-équilibrage-interne>]]]]]]]] {1-*}**

Cette commande permet de créer plusieurs points de terminaison de machine virtuelle.

**vm endpoint delete [options] &lt;nom-mv> &lt;nom-point-terminaison>**

Cette commande permet de supprimer un point de terminaison de machine virtuelle.

	~$ azure vm endpoint delete my-vm http
	azure vm endpoint delete my-vm http
	info:   Executing command vm endpoint delete
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint delete command OK

**vm endpoint list &lt;nom-mv>**

Cette commande permet de répertorier tous les points de terminaison de machine virtuelle. L'option -json spécifie que les résultats sont retournés au format JSON brut.

	~$ azure vm endpoint list my-linux-vm
	data:   Name  External Port  Local Port
	data:   ----  -------------  ----------
	data:   ssh   22             22

**vm endpoint update [options] &lt;nom-mv> &lt;nom-point-terminaison>**

Cette commande permet de mettre à jour un point de terminaison de machine virtuelle avec de nouvelles valeurs en utilisant ces options.

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp)

**vm endpoint show [options] &lt;nom-mv>**

Cette commande permet d'afficher des détails concernant les points de terminaison d'une machine virtuelle.

	~$ azure vm endpoint show "mycouchvm"
	info:    Executing command vm endpoint show
	+ Getting virtual machines
	data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
	data:    Network Endpoints 0 LocalPort "5984"
	data:    Network Endpoints 0 Name "CouchDB_EP"
	data:    Network Endpoints 0 Port "5984"
	data:    Network Endpoints 0 Protocol "tcp"
	data:    Network Endpoints 0 Vip "168.61.9.97"
	data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
	data:    Network Endpoints 1 LocalPort "2020"
	data:    Network Endpoints 1 Name "CouchEP_2"
	data:    Network Endpoints 1 Port "2020"
	data:    Network Endpoints 1 Protocol "tcp"
	data:    Network Endpoints 1 Vip "168.61.9.97"
	data:    Network Endpoints 2 LocalPort "3389"
	data:    Network Endpoints 2 Name "RemoteDesktop"
	data:    Network Endpoints 2 Port "3389"
	data:    Network Endpoints 2 Protocol "tcp"
	data:    Network Endpoints 2 Vip "168.61.9.97"
	info:    vm endpoint show command OK

## Commandes pour gérer vos images de machine virtuelle Azure

Les images de machine virtuelle sont des captures de machines virtuelles déjà configurées qui peuvent être répliquées selon les nécessités.

**vm image list [options]**

Cette commande permet d'obtenir une liste d'images de machine virtuelle. Il existe trois types d'images : les images créées par Microsoft, ayant pour préfixe « MSFT », les images créées par des tiers, qui ont généralement le nom du fournisseur en préfixe, et les images que vous créez. Pour créer des images, vous pouvez capturer une machine virtuelle existante ou créer une image à partir d'un fichier .vhd personnalisé téléchargé sur le stockage d'objets blob. Pour plus d'informations sur l'utilisation d'un fichier .vhd personnalisé, consultez la commande vm image create. L'option -json spécifie que les résultats sont retournés au format JSON brut.

	~$ azure vm image list
	data:   Name                                                                   Category   OS
	data:   ---------------------------------------------------------------------  ---------  -------
	data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-en-us-30GB.vhd   Canonical  Linux
	data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.en-us.30GB.2012-03-22                      Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1.en-us.30GB.2012-3-22                  Microsoft  Windows
	data:   OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd                 OpenLogic  Linux
	data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-en-us-30GB.vhd       SUSE       Linux
	data:   SUSE__OpenSUSE64121-03192012-en-us-15GB.vhd                            SUSE       Linux
	data:   WIN2K8-R2-WINRM                                                        User       Windows
	info:   vm image list command OK

**vm image show [options] &lt;nom>**

Cette commande permet d'afficher les détails d'une image de machine virtuelle.

	~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
	+ Fetching VM image
	info:   Executing command vm image show
	data:   {
	data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
	data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
	data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
	data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
	data:       Category: 'Microsoft',
	data:       OS: 'Windows',
	data:       Eula: 'http://www.microsoft.com',
	data:       LogicalSizeInGB: '30'
	data:   }
	info:   vm image show command OK

**vm image delete [options] &lt;nom>**

Cette commande permet de supprimer une image de machine virtuelle.

	~$ azure vm image delete my-vm-image
	info:   Executing command vm image delete
	info:   VM image deleted: my-vm-image
	info:   vm image delete command OK

**vm image create &lt;nom> [chemin-source]**

Cette commande permet de créer une image de machine virtuelle. Vos fichiers .vhd personnalisés sont téléchargés vers le stockage d'objets blob et l'image de machine virtuelle est ensuite créée à partir de cet emplacement. C'est à partir de cette image de machine virtuelle que vous créez une machine virtuelle. Les paramètres Location (emplacement) et OS (système d'exploitation) sont obligatoires.

>[AZURE.NOTE]Cette commande prend en charge le téléchargement de fichiers .vhd fixes uniquement. Pour télécharger un fichier .vhd dynamique, utilisez [Azure VHD Utilities for Go](https://github.com/Microsoft/azure-vhd-utils-for-go).

Certains systèmes imposent des limites de descripteurs de fichiers par processus. Si cette limite est dépassée, l'outil affiche une erreur de limite de descripteurs de fichiers. Vous pouvez de nouveau exécuter la commande à l’aide du paramètre -p &lt;number> afin de réduire le nombre maximal de téléchargements parallèles. Par défaut, ce nombre est de 96.

	~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
	info:   Executing command vm image create
	+ Retrieving storage accounts
	info:   VHD size : 13 MB
	info:   Uploading 13312.5 KB
	Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
	info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
	info:   vm image create command OK

## Commandes pour gérer vos disques de données de machine virtuelle Azure

Les disques de données sont des fichiers .vhd contenus dans le stockage d'objets blob qui peuvent être utilisés par une machine virtuelle. Pour plus d'informations sur le déploiement de disques de données dans le stockage d'objets blob, consultez le schéma technique Azure présenté plus haut.

Les commandes permettant d'attacher des disques de données (azure vm disk attach et azure vm disk attach-new) attribuent un numéro d'unité logique (LUN) au disque de données attaché, comme l'exige le protocole SCSI. Le premier disque de données attaché à une machine virtuelle est le LUN 0, le suivant est le LUN 1 et ainsi de suite.

Lorsque vous détachez un disque de données à l’aide de la commande azure vm disk detach, utilisez le paramètre &lt;lun&gt; pour indiquer quel disque détacher.

> [MICROSOFT AZURE>REMARQUE] Notez que vous devez toujours détacher les disques de données dans l’ordre inverse des numéros d’unité logique affectés, c’est-à-dire en commençant par le numéro le plus élevé. En effet, la couche SCSI Linux ne permet pas de détacher un numéro d'unité logique inférieur à un autre toujours attaché. Par exemple, vous ne devez pas détacher LUN 0 si LUN 1 est toujours attaché.

**vm disk show [options] &lt;nom>**

Cette commande permet d'afficher les détails concernant un disque Azure.

	~$ azure vm disk show anucentos-anucentos-0-20120524070008
	info:   Executing command vm disk show
	data:   AttachedTo DeploymentName "mycentos"
	data:   AttachedTo HostedServiceName "myanucentos"
	data:   AttachedTo RoleName "myanucentos"
	data:   OS "Linux"
	data:   Location "Azure Preview"
	data:   LogicalDiskSizeInGB "30"
	data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
	data:   Name "mycentos-mycentos-0-20120524070008"
	data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd"
	info:   vm disk show command OK

**vm disk list [options] [nom-mv]**

Cette commande répertorie les disques Azure, ou les disques attachés à une machine virtuelle spécifiée. Si elle est exécutée avec un paramètre de nom de machine virtuelle, elle retourne tous les disques attachés à la machine virtuelle. LUN 1 est créé avec la machine virtuelle et les autres disques éventuellement répertoriés sont attachés séparément.

	~$ azure vm disk list mycentos
	info:   Executing command vm disk list
	data:   Lun  Size(GB)  Blob-Name
	data:   ---  --------  --------------------------------
	data:   1    30        mycentos-cb39b8223b01f95c.vhd
	data:   2    10        mycentos-e3f0d717950bb78d.vhd
	info:   vm disk list command OK

L'exécution de cette commande sans paramètre de nom de machine virtuelle renvoie tous les disques.

	~$ azure vm disk list
	data:   Name                                        OS
	data:   ------------------------------------------  -------
	data:   mycentos-mycentos-0-20120524070008          Linux
	data:   mycentos-mycentos-2-20120525055052
	data:   mywindows-winvm-20120522223119              Windows
	info:   vm disk list command OK

**vm disk delete [options] &lt;nom>**

Cette commande permet de supprimer un disque Azure d'un référentiel personnel. Le disque doit être détaché de la machine virtuelle avant d'être supprimé.

	~$ azure vm disk delete mycentos-mycentos-2-20120525055052
	info:   Executing command vm disk delete
	info:   Disk deleted: mycentos-mycentos-2-20120525055052
	info:   vm disk delete command OK

**vm disk create &lt;nom> [chemin-source]**

Cette commande télécharge et enregistre un disque Microsoft Azure. Les paramètres --blob-url, --location ou --affinity-group doivent être spécifiés. Si vous utilisez cette commande avec [source-path], le fichier .vhd spécifié est téléchargé et une nouvelle image est créée. Vous pouvez ensuite attacher cette image à une machine virtuelle à l'aide de la commande vm disk attach.

Certains systèmes imposent des limites de descripteurs de fichiers par processus. Si cette limite est dépassée, l'outil affiche une erreur de limite de descripteurs de fichiers. Vous pouvez de nouveau exécuter la commande à l’aide du paramètre -p &lt;number> afin de réduire le nombre maximal de téléchargements parallèles. Par défaut, ce nombre est de 96.

	~$ azure vm disk create my-data-disk ~/test.vhd --location "West US"
	info:   Executing command vm disk create
	info:   VHD size : 10 MB
	info:   Uploading 10240.5 KB
	Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s
	info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
	info:   vm disk create command OK

**vm disk upload [options] &lt;chemin-source> &lt;url-blob> &lt;clé-compte-stockage>**

Cette commande permet de télécharger un disque de machine virtuelle.

	~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
	info:   Executing command vm disk upload
	info:   Uploading 12351.5 KB
	info:   vm disk upload command OK

**vm disk attach &lt;nom-mv> &lt;nom-image-disque>**

Cette commande permet d'attacher un disque existant dans le stockage d'objets blob à une machine virtuelle existante déployée dans un service cloud.

	~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
	info:   Executing command vm disk attach
	info:   vm disk attach command OK

**vm disk attach-new &lt;nom-mv> &lt;taille-en-go> [url-blob]**

Cette commande permet d'attacher un disque de données à une machine virtuelle Azure. Dans cet exemple, la valeur 20 correspond à la taille en gigaoctets du nouveau disque à attacher. Vous pouvez éventuellement utiliser une URL d'objet blob comme dernier argument pour spécifier explicitement l'objet blob cible à créer. Si vous ne spécifiez pas d'URL d'objet blob, un objet blob est généré automatiquement.

	~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
	info:   Executing command vm disk attach-new
	info:   vm disk attach-new command OK  

**vm disk detach &lt;nom-mv> &lt;numéro-unité-logique>**

Cette commande permet de détacher un disque de données attaché à une machine virtuelle Microsoft Azure. &lt;numéro-unité-logique> identifie le disque à détacher. Pour obtenir une liste des disques associés à un disque avant de le détacher, utilisez la commande vm disk-list &lt;nom-mv>.

	~$ azure vm disk detach my-vm 2
	info:   Executing command vm disk detach
	info:   vm disk detach command OK

## Commandes pour gérer vos services cloud Azure

Les services cloud Azure sont des applications et des services hébergés sur des rôles web et de travail. Les commandes suivantes peuvent être utilisées pour gérer les services cloud Azure.

**service create [options] &lt;nom\_service>**

Cette commande permet de créer un service cloud.

	~$ azure service create newservicemsopentech
	info:    Executing command service create
	+ Getting locations
	help:    Location:
	  1) East Asia
	  2) Southeast Asia
	  3) North Europe
	  4) West Europe
	  5) East US
	  6) West US
	  : 6
	+ Creating cloud service
	data:    Cloud service name newservicemsopentech
	info:    service create command OK

**service show [options] &lt;nom\_service>**

Cette commande permet d'afficher les détails concernant un service cloud Azure.

	~$ azure service show newservicemsopentech
	info:    Executing command service show
	+ Getting cloud service
	data:    Name newservicemsopentech
	data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
	data:    Properties location West US
	data:    Properties label newservicemsopentech
	data:    Properties status Created
	data:    Properties dateCreated
	data:    Properties dateLastModified
	info:    service show command OK

**service list [options]**

Cette commande permet de répertorier les services cloud Azure.

	~$ azure service list
	info:   Executing command service list
	data:   Name         Status
	data:   -----------  -------
	data:   service1     Created
	data:   service2     Created
	info:   service list command OK

**service delete [options] &lt;nom>**

Cette commande permet de supprimer un service cloud Azure.

	~$ azure service delete myservice
	info:   Executing command service delete myservice
	info:   cloud-service delete command OK

Pour forcer la suppression, utilisez le paramètre `-q`.


## Commandes pour gérer vos certificats Azure

Les certificats Azure sont des certificats SSL connectés à votre compte Azure. Pour plus d'informations sur les certificats Azure, consultez la page [Gérer les certificats](http://msdn.microsoft.com/library/azure/gg981929.aspx).

**service cert list [options]**

Cette commande permet de répertorier les certificats Azure.

	~$ azure service cert list
	info:   Executing command service cert list
	+ Fetching cloud services
	+ Fetching certificates
	data:   Service   Thumbprint                                Algorithm
	data:   --------  ----------------------------------------  ---------
	data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1
	info:   service cert list command OK

**service cert create &lt;préfixe-dns> &lt;fichier> [mot-de-passe]**

Cette commande permet de télécharger un certificat. Laissez l'invite de mot de passe vide pour les certificats qui ne sont pas protégés par mot de passe.

	~$ azure service cert create nghinazz ~/publishSet.pfx
	info:   Executing command service cert create
	Cert password:
	+ Creating certificate
	info:   service cert create command OK

**service cert delete [options] &lt;empreinte>**

Cette commande permet de supprimer un certificat.

	~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
	info:   Executing command service cert delete
	+ Deleting certificate
	info:   nghinazz : cert deleted
	info:   service cert delete command OK

## Commandes pour gérer vos applications Web

Une application Web Microsoft Azure est une configuration Web accessible par URI. Les applications Web sont hébergées sur des machines virtuelles, mais la création et le déploiement de la machine virtuelle ne sont pas de votre ressort. Azure s'en charge à votre place.

**site list [options]**

Cette commande répertorie vos applications Web.

	~$ azure site list
	info:   Executing command site list
	data:   Name            State    Host names
	data:   --------------  -------  --------------------------------------------------
	data:   mongosite       Running  mongosite.antdf0.antares.windows.net
	data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net
	data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
	info:   site list command OK

**site set [options] [nom]**

Cette commande définit les options de configuration de votre application web [nom].

	~$ azure site set
	info:    Executing command site set
	Web site name: mydemosite
	+ Getting sites
	+ Updating site config information
	info:    site set command OK

**site deploymentscript [options]**

Cette commande permet de générer un script de déploiement personnalisé.

	~$ azure site deploymentscript --node
	info:    Executing command site deploymentscript
	info:    Generating deployment script for node.js Web Site
	info:    Generated deployment script files
	info:    site deploymentscript command OK

**site create [options] [nom]**

Cette commande permet de créer une nouvelle application Web et un répertoire local.

	~$ azure site create mysite
	info:   Executing command site create
	info:   Using location northeuropewebspace
	info:   Creating a new web site
	info:   Created web site at  mysite.antdf0.antares.windows.net
	info:   Initializing repository
	info:   Repository initialized
	info:   site create command OK

> [AZURE.NOTE] Le nom du site doit être unique. Vous ne pouvez pas créer un site avec le même nom DNS qu'un site existant.

**site browse [options] [nom]**

Cette commande ouvre votre application Web dans un navigateur.

	~$ azure site browse mysite
	info:   Executing command site browse
	info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
	info:   site browse command OK

**site show [options] [nom]**

Cette commande affiche les détails associés à une application Web.

	~$ azure site show mysite
	info:   Executing command site show
	info:   Showing details for site
	data:   Site AdminEnabled true
	data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
	data:   Site Name mysite
	data:   Site Owner 00060000814EDDEE
	data:   Site RepositorySiteName mysite
	data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
	data:   Site State Running
	data:   Site UsageState Normal
	data:   Site WebSpace northeuropewebspace
	data:   Config AppSettings
	data:   Config ConnectionStrings
	data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
	data:   Config DetailedErrorLoggingEnabled false
	data:   Config HttpLoggingEnabled false
	data:   Config Metadata
	data:   Config NetFrameworkVersion v4.0
	data:   Config NumberOfWorkers 1
	data:   Config PhpVersion 5.3
	data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
	data:   Config RequestTracingEnabled false
	data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
	info:   site show command OK

**site delete [options] [nom]**

Cette commande permet de supprimer une application Web.

	~$ azure site delete mysite
	info:   Executing command site delete
	info:   Deleting site mysite
	info:   Site mysite has been deleted
	info:   site delete command OK

 **site swap [options] [nom]**

Cette commande échange deux connecteurs d’applications Web.

Cette commande prend en charge l'option supplémentaire suivante :

****-q ou **--quiet** : ne demande pas de confirmation. Utilisez cette option dans les scripts automatisés.


**site start [options] [nom]**

Cette commande permet de démarrer une application Web.

	~$ azure site start mysite
	info:   Executing command site start
	info:   Starting site mysite
	info:   Site mysite has been started
	info:   site start command OK

**site stop [options] [nom]**

Cette commande permet d’arrêter une application Web.

	~$ azure site stop mysite
	info:   Executing command site stop
	info:   Stopping site mysite
	info:   Site mysite has been stopped
	info:   site stop command OK

****site restart [options] [name]

Cette commande arrête puis démarre une application Web spécifique.

Cette commande prend en charge l'option supplémentaire suivante :

**--slot** &lt;connecteur> : nom du connecteur à redémarrer.


**site location list [options]**

Cette commande répertorie les emplacements de vos applications Web.

	~$ azure site location list
	info:    Executing command site location list
	+ Getting locations
	data:    Name
	data:    ----------------
	data:    West Europe
	data:    West US
	data:    North Central US
	data:    North Europe
	data:    East Asia
	data:    East US
	info:    site location list command OK

###Commandes pour gérer les paramètres de vos applications Web

**site appsetting list [options] [nom]**

Cette commande répertorie les paramètres ajoutés à l’application Web.

	~$ azure site appsetting list
	info:    Executing command site appsetting list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Name  Value
	data:    ----  -----
	data:    test  value
	info:    site appsetting list command OK

**site appsetting add [options] &lt;paire-clé-valeur> [nom]**

Cette commande ajoute un paramètre à votre application Web, sous la forme d’une paire clé-valeur.

	~$ azure site appsetting add test=value
	info:    Executing command site appsetting add
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	+ Updating site config information
	info:    site appsetting add command OK

**site appsetting delete [options] &lt;clé> [nom]**

Cette commande supprime le paramètre spécifique de l’application Web.

	~$ azure site appsetting delete test
	info:    Executing command site appsetting delete
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	Delete application setting test? [y/n] y
	+ Updating site config information
	info:    site appsetting delete command OK

**site appsetting show [options] &lt;clé> [nom]**

Cette commande permet d'afficher des détails concernant le paramètre d'application spécifié.

	~$ azure site appsetting show test
	info:    Executing command site appsetting show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Value:  value
	info:    site appsetting show command OK

###Commandes pour gérer vos certificats d’applications Web

**site cert list [options] [nom]**

Cette commande affiche une liste des certificats d’applications Web.

	~$ azure site cert list
	info:    Executing command site cert list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	data:    Subject                       Expiration Date	                  Thumbprint
	data:    ----------------------------  -----------------------------------------
	----------------  ----------------------------------------
	data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
	data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
	info:    site cert list command OK

**site cert add [options] &lt;chemin-certificat> [nom]**

**site cert delete [options] &lt;thumbprint> [nom]**

**site cert show [options] &lt;thumbprint> [nom]**

Cette commande permet d'afficher des détails concernant les certificats.

	~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    Executing command site cert show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	data:    Certificate hostNames 0=msopentech.azurewebsites.net
	data:    Certificate expirationDate
	data:    Certificate friendlyName msopentech.azurewebsites.net
	data:    Certificate issueDate
	data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
	data:    Certificate subjectName msopentech.azurewebsites.net
	data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
	info:    site cert show command OK

###Commandes pour gérer les chaînes de connexion de vos applications Web

**site connectionstring list [options] [nom]**

**site connectionstring add [options] &lt;nom\_connexion> &lt;valeur> &lt;type> [nom]**

**site connectionstring delete [options] &lt;nomconnexion> [name]**

**site connectionstring show [options] &lt;nom\_connexion> [nom]**

###Commandes pour gérer les documents par défaut de vos applications Web

**site defaultdocument list [options] [nom]**

**site defaultdocument add [options] &lt;document> [nom]**

**site defaultdocument delete [options] &lt;document> [nom]**

###Commandes pour gérer vos déploiements d’applications Web

**site deployment list [options] [nom]**

**site deployment show [options] &lt;IDvalidation> [nom]**

**site deployment redeploy [options] &lt;IDvalidation> [nom]**

**site deployment github [options] [nom]**

**site deployment user set [options] [nom\_utilisateur] [pass]**

###Commandes pour gérer vos domaines d’applications Web

**site domain list [options] [nom]**

**site domain add [options] &lt;domaine> [nom]**

**site domain delete [options] &lt;domaine> [nom]**

###Commandes pour gérer vos mappages de gestionnaire d’applications Web

**site handler list [options] [nom]**

**site handler add [options] &lt;extension> &lt;processeur> [nom]**

**site handler delete [options] &lt;extension> [nom]**

###Commandes pour gérer vos tâches Web

**site job list [options] [nom]**

Cette commande répertorie l’ensemble des tâches Web associées à une application Web.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-type** &lt;type-tâche> : facultative. Type de tâche Web. Les valeurs valides sont « triggered » et « continuous ». Renvoie par défaut tous les types de tâches Web.
+ **--slot** &lt;connecteur> : nom du connecteur à redémarrer.

**site job show [options] &lt;nom-tâche> &lt;type\_tâche> [nom]**

Cette commande permet d'afficher les détails d'une tâche Web spécifique.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-name** &lt;nom-tâche> : requise. Nom de la tâche Web.
+ **--job-type** &lt;type-tâche> : requise. Type de tâche Web. Les valeurs valides sont « triggered » et « continuous ».
+ **--slot** &lt;connecteur> : nom du connecteur à redémarrer.

**site job delete [options] &lt;nom\_tâche> &lt;type\_tâche> [nom]**

Cette commande permet de supprimer la tâche Web spécifiée.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-name** &lt;nom-tâche> : requise. Nom de la tâche Web.
+ **--job-type** &lt;type-tâche> : requise. Type de tâche Web. Les valeurs valides sont « triggered » et « continuous ».
+ **-q** ou **--quiet** : ne demande pas de confirmation. Utilisez cette option dans les scripts automatisés.
+ **--slot** &lt;connecteur> : nom du connecteur à redémarrer.

**site job upload [options] &lt;nom\_tâche> &lt;type\_tâche> <jobFile> [nom]**

Cette commande permet de supprimer la tâche Web spécifiée.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-name** &lt;nom-tâche> : requise. Nom de la tâche Web.
+ **--job-type** &lt;type-tâche> : requise. Type de tâche Web. Les valeurs valides sont « triggered » et « continuous ».
+ **--job-file** &lt;fichier-tâche> : requise. Fichier de tâche.
+ **--slot** &lt;connecteur> : nom du connecteur à redémarrer.

**site job start [options] &lt;nom\_tâche> &lt;type\_tâche> [nom]**

Cette commande permet de démarrer la tâche Web spécifiée.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-name** &lt;nom-tâche> : requise. Nom de la tâche Web.
+ **--job-type** &lt;type-tâche> : requise. Type de tâche Web. Les valeurs valides sont « triggered » et « continuous ».
+ **--slot** &lt;connecteur> : nom du connecteur à redémarrer.

**site job stop [options] &lt;nomTâche> &lt;type\_tâche> [nom]**

Cette commande permet d'arrêter la tâche web spécifiée. Seules les tâches continues peuvent être arrêtées.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-name** &lt;nom-tâche> : requise. Nom de la tâche Web.
+ **--slot** &lt;connecteur> : nom du connecteur à redémarrer.

###Commandes pour gérer l’historique de vos tâches Web

**site job history list [options] [nom\_tâche] [nom]**

Cette commande permet d'afficher l'historique des exécutions de la tâche Web spécifiée.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-name** &lt;nom-tâche> : requise. Nom de la tâche Web.
+ **--slot** &lt;connecteur> : nom du connecteur à redémarrer.

**site job history show [options] [nom\_tâche] [ID\_exécution] [nom]**

Cette commande permet d'obtenir les détails relatifs à l'exécution de la tâche pour la tâche Web spécifiée.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-name** &lt;nom-tâche> : requise. Nom de la tâche Web.
+ **--run-id** &lt;id-exécution> : facultative. Identifiant de l'historique des exécutions. S'il n'est pas spécifié, la dernière exécution s'affiche.
+ **--slot** &lt;connecteur> : nom du connecteur à redémarrer.

###Commandes pour gérer les diagnostics de vos applications Web

**site log download [options] [nom]**

Télécharge un fichier compressé contenant les diagnostics de vos applications Web.

	~$ azure site log download
	info:    Executing command site log download
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	+ Downloading diagnostic log to diagnostics.zip
	info:    site log download command OK

**site log tail [options] [nom]**

Cette commande permet de connecter votre terminal au service de diffusion de journal en continu.

	~$ azure site log tail
	info:    Executing command site log tail
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**site log set [options] [nom]**

Cette commande permet de configurer les options de diagnostic de votre application Web.

	~$ azure site log set -a
	info:    Executing command site log set
	+ Getting output options
	help:    Output:
	  1) file
	  2) storage
	  : 1
	Web site name: mydemosite
	+ Getting locations
	+ Getting sites
	+ Getting site information
	+ Getting diagnostic settings
	+ Updating diagnostic settings
	info:    site log set command OK

###Commandes pour gérer vos référentiels d’applications Web

**site repository branch [options] &lt;branche> [nom]**

**site repository delete [options] [nom]**

**site repository sync [options] [nom]**

###Commandes pour gérer la mise à l’échelle de vos applications

**site scale mode [options] &lt;mode> [nom]**

**site scale instances [options] &lt;instances> [nom]**


## Commandes pour gérer Azure Mobile Services

Azure Mobile Services réunit un ensemble de services Azure qui dotent vos applications de fonctionnalités principales. Les commandes Mobile Services se répartissent en différentes catégories, à savoir :

+ [Commandes pour gérer des instances de services mobiles](#Mobile_Services)
+ [Commandes pour gérer la configuration des services mobiles](#Mobile_Configuration)
+ [Commandes pour gérer les tables des services mobiles](#Mobile_Tables)
+ [Commandes pour gérer les scripts des services mobiles](#Mobile_Scripts)
+ [Commandes pour gérer les tâches planifiées](#Mobile_Jobs)
+ [Commandes pour mettre à l’échelle un service mobile](#Mobile_Scale)

Les options suivantes s'appliquent à la plupart des commandes Mobile Services :

+ **-h** ou **--help** : affiche des informations sur l’utilisation de la sortie.
+ **-s `<id>`** ou **--subscription `<id>`** : utilise un abonnement spécifique, sous la forme `<id>`.
+ **-v** ou **--verbose** : écrit une sortie détaillée.
+ **--json** : écrit une sortie JSON.

### <a name="Mobile_Services"></a>Commandes pour gérer les instances de services mobiles

**mobile locations [options]**

Cette commande permet de répertorier les emplacements géographiques pris en charge par Mobile Services.

	~$ azure mobile locations
	info:    Executing command mobile locations
	info:    East US (default)
	info:    West US
	info:    North Europe

**mobile create [options] [nom\_service] [sqlAdminUsername] [sqlAdminPassword]**

Cette commande permet de créer un service mobile avec une base de données et un serveur SQL.

	~$ azure mobile create todolist your_login_name Secure$Password
	info:    Executing command mobile create
	+ Creating mobile service
	info:    Overall application state: Healthy
	info:    Mobile service (todolist) state: ProvisionConfigured
	info:    SQL database (todolist_db) state: Provisioned
	info:    SQL server (e96ean1c6v) state: ProvisionConfigured
	info:    mobile create command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-r `<sqlServer>`** ou **--sqlServer `<sqlServer>`** : utilise un serveur de base de données SQL existant, spécifié sous la forme `<sqlServer>`.
+ **-d `<sqlDb>`** ou **--sqlDb `<sqlDb>`** : utilise une base de données SQL existante, spécifiée sous la forme `<sqlDb>`.
+ **-l `<location>`** ou **--location `<location>`** : crée le service à un emplacement particulier, spécifié sous la forme `<location>`. Exécutez azure mobile locations pour obtenir les emplacements disponibles.
+ **--sqlLocation `<location>`** : crée le serveur SQL dans un emplacement spécifique `<location>`; par défaut, il s’agit de l’emplacement du service mobile.

**mobile delete [options] [nom\_service]**

Cette commande permet de supprimer un service mobile en même temps que la base de données et le serveur SQL qui lui sont associés.

	~$ azure mobile delete todolist -a -q
	info:    Executing command mobile delete
	data:    Mobile service todolist
	data:    SQL database todolistAwrhcL60azo1C401
	data:    SQL server fh1kvbc7la
	+ Deleting mobile service
	info:    Deleted mobile service
	+ Deleting SQL server
	info:    Deleted SQL server
	+ Deleting mobile application
	info:    Deleted mobile application
	info:    mobile delete command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-d** ou **--deleteData** : supprime toutes les données de ce service mobile de la base de données.
+ **-a** ou **--deleteAll** : supprime la base de données et le serveur SQL.
+ **-q** ou **--quiet** : ne demande pas de confirmation. Utilisez cette option dans les scripts automatisés.

**mobile list [options]**

Cette commande permet de répertorier vos services mobiles.

	~$ azure mobile list
	info:    Executing command mobile list
	data:    Name          State  URL
	data:    ------------  -----  --------------------------------------
	data:    todolist      Ready  https://todolist.azure-mobile.net/
	data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
	info:    mobile list command OK

**mobile show [options] [nom\_service]**

Cette commande permet d'afficher les détails concernant un service mobile.

	~$ azure mobile show todolist
	info:    Executing command mobile show
	+ Getting information
	info:    Mobile application
	data:    status Healthy
	data:    Mobile service name todolist
	data:    Mobile service status ProvisionConfigured
	data:    SQL database name todolistAwrhcL60azo1C401
	data:    SQL database status Linked
	data:    SQL server name fh1kvbc7la
	data:    SQL server status Linked
	info:    Mobile service
	data:    name todolist
	data:    state Ready
	data:    applicationUrl https://todolist.azure-mobile.net/
	data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
	data:    webspace WESTUSWEBSPACE
	data:    region West US
	data:    tables TodoItem
	info:    mobile show command OK

**mobile restart [options] [nom\_service]**

Cette commande permet de redémarrer une instance de service mobile.

	~$ azure mobile restart todolist
	info:    Executing command mobile restart
	+ Restarting mobile service
	info:    Service was restarted.
	info:    mobile restart command OK

**mobile log [options] [nom\_service]**

Cette commande renvoie les journaux du service mobile, en filtrant tous les types de journaux sauf `error`.

	~$ azure mobile log todolist -t error
	info:    Executing command mobile log
	data:
	data:    timeCreated 2013-01-07T16:04:43.351Z
	data:    type error
	data:    source /scheduler/TestingLogs.js
	data:    message This is an error.
	data:
	info:    mobile log command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-r `<query>`** ou **--query `<query>`** : exécute la requête de journal spécifiée.
+ **-t `<type>`** ou **--type `<type>`** : filtre les journaux renvoyés par l’entrée `<type>`, qui peut être `information`, `warning`, ou `error`.
+ **-k `<skip>`** ou **--skip `<skip>`** : ignore le nombre de lignes spécifié par `<skip>`.
+ **-p `<top>`** ou **--top `<top>`** : renvoie un nombre spécifique de lignes, spécifié par `<top>`.

> [AZURE.NOTE] Le paramètre **--query** a la priorité sur **--type**, **--skip** et **--top**.

**mobile recover [options] [nom\_service\_non\_sain] [nom\_service\_sain]**

Cette commande permet de récupérer un service mobile défectueux en le déplaçant vers un service mobile sain dans une autre région.

Cette commande prend en charge l'option supplémentaire suivante :

**-q** ou **--quiet** : supprime l’invite de confirmation de la récupération.

**mobile key regenerate [options] [nom\_service] [type]**

Cette commande permet de régénérer la clé d'application du service mobile.

	~$ azure mobile key regenerate todolist application
	info:    Executing command mobile key regenerate
	info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
	info:    mobile key regenerate command OK

Les types de clés sont `master` et `application`.

> [AZURE.NOTE] Lorsque vous régénérez une clé, les clients qui utilisent l'ancienne clé risquent de ne pas pouvoir accéder à votre service mobile. Lorsque vous régénérez la clé d'application, vous devez mettre à jour votre application avec la nouvelle valeur de clé.

**mobile key set [options] [nom\_service] [type] [valeur]**

Cette commande permet de définir la clé du service mobile sur une valeur spécifique.


### <a name="Mobile_Configuration"></a>Commandes pour gérer la configuration des services mobiles

**mobile config list [options] [nom\_service]**

Cette commande permet de répertorier les options de configuration d'un service mobile.

	~$ azure mobile config list todolist
	info:    Executing command mobile config list
	+ Getting mobile service configuration
	data:    dynamicSchemaEnabled true
	data:    microsoftAccountClientSecret Not configured
	data:    microsoftAccountClientId Not configured
	data:    microsoftAccountPackageSID Not configured
	data:    facebookClientId Not configured
	data:    facebookClientSecret Not configured
	data:    twitterClientId Not configured
	data:    twitterClientSecret Not configured
	data:    googleClientId Not configured
	data:    googleClientSecret Not configured
	data:    apnsMode none
	data:    apnsPassword Not configured
	data:    apnsCertifcate Not configured
	info:    mobile config list command OK

**mobile config get [options] [nom\_service] [clé]**

Cette commande permet d'obtenir une option de configuration spécifique pour un service mobile, dans ce cas, le schéma dynamique.

	~$ azure mobile config get todolist dynamicSchemaEnabled
	info:    Executing command mobile config get
	data:    dynamicSchemaEnabled true
	info:    mobile config get command OK

**mobile config set [options] [nom\_service] [clé] [valeur]**

Cette commande permet de définir une option de configuration spécifique pour un service mobile, dans ce cas, le schéma dynamique.

	~$ azure mobile config set todolist dynamicSchemaEnabled false
	info:    Executing command mobile config set
	info:    mobile config set command OK


### <a name="Mobile_Tables"></a>Commandes pour gérer les tables des services mobiles

**mobile table list [options] [nom\_service]**

Cette commande permet de répertorier toutes les tables de votre service mobile.

	~$azure mobile table list todolist
	info:    Executing command mobile table list
	data:    Name      Indexes  Rows
	data:    --------  -------  ----
	data:    Channel   1        0
	data:    TodoItem  1        0
	info:    mobile table list command OK

**mobile table show [options] [nom\_service] [nom\_table]**

Cette commande permet d'afficher les détails concernant une table spécifique.

	~$azure mobile table show todolist
	info:    Executing command mobile table show
	+ Getting table information
	info:    Table statistics:
	data:    Number of records 5
	info:    Table operations:
	data:    Operation  Script       Permissions
	data:    ---------  -----------  -----------
	data:    insert     1900 bytes   user
	data:    read       Not defined  user
	data:    update     Not defined  user
	data:    delete     Not defined  user
	info:    Table columns:
	data:    Name  Type           Indexed
	data:    ----  -------------  -------
	data:    id    bigint(MSSQL)  Yes
	data:    text      string
	data:    complete  boolean
	info:    mobile table show command OK

**mobile table create [options] [nom\_service] [nom\_table]**

Cette commande permet de créer une table.

	~$azure mobile table create todolist Channels
	info:    Executing command mobile table create
	+ Creating table
	info:    mobile table create command OK

Cette commande prend en charge l'option supplémentaire suivante :

+ **-p `&lt;permissions>`** ou **--permissions `&lt;permissions>`** : liste de paires `<operation>`=`<permission>` séparées par des virgules, où `<operation>` est `insert`, `read`, `update` ou `delete` et `&lt;permissions>` est `public`, `application` (valeur par défaut), `user` ou `admin`.

**mobile data read [options] [nom\_service] [nom\_table] [requête]**

Cette commande permet de lire les données d'une table.

	~$azure mobile data read todolist TodoItem
	info:    Executing command mobile data read
	data:    id  text     complete
	data:    --  -------  --------
	data:    1   item #1  false
	data:    2   item #2  true
	data:    3   item #3  false
	data:    4   item #4  true
	info:    mobile data read command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-k `<skip>`** ou **--skip `<skip>`** : ignore le nombre de lignes spécifié par `<skip>`.
+ **-t `<top>`** ou **--top `<top>`** : renvoie un nombre spécifique de lignes, spécifié par `<top>`.
+ **-l** ou **--list** : renvoie les données sous forme de liste.

**mobile table update [options] [nom\_service] [nom\_table]**

Cette commande permet de modifier les autorisations de suppression d'une table, qui deviennent exclusives aux administrateurs

	~$azure mobile table update todolist Channels -p delete=admin
	info:    Executing command mobile table update
	+ Updating permissions
	info:    Updated permissions
	info:    mobile table update command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-p `&lt;permissions>`** ou **--permissions `&lt;permissions>`** : liste de paires `<operation>`=`<permission>` séparées par des virgules, où `<operation>` est `insert`, `read`, `update` ou `delete` et `&lt;permissions>` est `public`, `application` (valeur par défaut), `user` ou `admin`.
+ **--deleteColumn `<columns>`** : liste de colonnes séparées par des virgules, sous la forme `<columns>`.
+ **-q** ou **--quiet** : supprime les colonnes sans demander de confirmation.
+ **--addIndex `<columns>`** : liste de colonnes séparées par des virgules, à inclure dans l’index.
+ **--deleteIndex `<columns>`** : liste de colonnes séparées par des virgules, à exclure de l’index.

**mobile table delete [options] [nom\_service] [nom\_table]**

Cette commande permet de supprimer une table.

	~$azure mobile table delete todolist Channels
	info:    Executing command mobile table delete
	Do you really want to delete the table (yes/no): yes
	+ Deleting table
	info:    mobile table delete command OK

Spécifiez le paramètre -q pour supprimer la table sans confirmation. Cela empêche le blocage des scripts d'automatisation.

**mobile data truncate [options] [nom\_service] [nom\_table]**

Cette commande permet de supprimer toutes les lignes de données de la table.

	~$azure mobile data truncate todolist TodoItem
	info:    Executing command mobile data truncate
	info:    There are 7 data rows in the table.
	Do you really want to delete all data from the table? (y/n): y
	info:    Deleted 7 rows.
	info:    mobile data truncate command OK


### <a name="Mobile_Scripts"></a>Commandes pour gérer les scripts

Les commandes de cette section permettent de gérer les scripts de serveur qui appartiennent à un service mobile. Pour plus d'informations, consultez la page [Utilisation des scripts serveur dans Mobile Services](mobile-services/mobile-services-how-to-use-server-scripts.md).

**mobile script list [options] [nom\_service]**

Cette commande permet de répertorier les scripts inscrits, y compris les scripts de table et de planificateur.

	~$azure mobile script list todolist
	info:    Executing command mobile script list
	+ Getting script information
	info:    Table scripts
	data:    Name                   Size
	data:    ---------------------  ----
	data:    table/TodoItem.delete  256
	data:    table/Devices.insert   1660
	error:   Unable to get shared scripts
	info:    Scheduler scripts
	data:    Name                 Status     Interval   Last run   Next run
	data:    -------------------  ---------  ---------  ---------  ---------
	data:    scheduler/undefined  undefined  undefined  undefined  undefined
	data:    scheduler/undefined  undefined  undefined  undefined  undefined
	info:    mobile script list command OK

**mobile script download [options] [nom\_service] [nom\_script]**

Cette commande permet de télécharger le script d’insertion de la table Todoltem vers un fichier nommé `todoitem.insert.js` dans le sous-dossier `table`.

	~$azure mobile script download todolist table/todoitem.insert.js
	info:    Executing command mobile script download
	info:    Saved script to ./table/todoitem.insert.js
	info:    mobile script download command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-p `<path>`** ou **--path `<path>`** : emplacement où le script doit être enregistré dans le fichier, dans la mesure où le répertoire de travail actuel est celui par défaut.
+ **-f `<file>`** ou **--file `<file>`** : nom du fichier dans lequel le script doit être enregistré.
+ **-o** ou **--override** : permet de remplacer un fichier existant.
+ **-c** ou **--console** : écrit le script dans la console et non dans un fichier.

**mobile script upload [options] [nom\_service] [nom\_script]**

Cette commande permet de télécharger un nouveau script nommé `todoitem.insert.js` à partir du sous-dossier `table`.

	~$azure mobile script upload todolist table/todoitem.insert.js
	info:    Executing command mobile script upload
	info:    mobile script upload command OK

Le nom du fichier doit se composer des noms d'une table et d'une opération et doit être situé dans le sous-dossier table par rapport à l'emplacement où est exécutée la commande. Vous pouvez également utiliser le paramètre **-f `<file>`** ou **--file `<file>`** pour spécifier un nom et un chemin d’accès de fichier différents, contenant le script à inscrire.


**mobile script delete [options] [nom\_service] [nom\_script]**

Cette commande permet de supprimer le script d'insertion existant de la table TodoItem.

	~$azure mobile script delete todolist table/todoitem.insert.js
	info:    Executing command mobile script delete
	info:    mobile script delete command OK

### <a name="Mobile_Jobs"></a>Commandes pour gérer les tâches planifiées

Les commandes de cette section permettent de gérer les travaux planifiés qui appartiennent à un service mobile. Pour plus d'informations, consultez la page [Planifier les travaux](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx).

**mobile job list [options] [nom\_service]**

Cette commande permet de répertorier les travaux planifiés.

	~$azure mobile job list todolist
	info:    Executing command mobile job list
	info:    Scheduled jobs
	data:    Job name    Script name           Status    Interval     Last run              Next run
	data:    ----------  --------------------  --------  -----------  --------------------  --------------------
	data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
	info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
	info:    mobile job list command OK

**mobile job create [options] [nom\_service] [nom\_tâche]**

Cette commande permet de créer une tâche nommée `getUpdates`, dont l’exécution est prévue toutes les heures.

	~$azure mobile job create -i 1 -u hour todolist getUpdates
	info:    Executing command mobile job create
	info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
	info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
	info:    mobile job create command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-i `<number>`** ou **--interval `<number>`** : intervalle de travail, défini sous la forme d’entier ; la valeur par défaut est `15`.
+ **-u `<unit>`** ou **--intervalUnit `<unit>`** : unité du paramètre _interval_, qui peut être l’une des valeurs suivantes :
	+ **minute** (valeur par défaut)
	+ **heure**
	+ **jour**
	+ **mois**
	+ **none** (travaux à la demande)
+ **-t `<time>`** **--startTime `<time>`** : heure de début de la première exécution du script, au format ISO ; la valeur par défaut est `now`.

> [AZURE.NOTE] Les nouveaux travaux créés sont désactivés, car il reste encore à télécharger un script. Utilisez la commande **mobile script upload** pour télécharger un script et la commande **mobile job update** pour activer le travail.

**mobile job update [options] [nom\_service] [nom\_tâche]**

La commande suivante active la tâche `getUpdates` désactivée.

	~$azure mobile job update -a enabled todolist getUpdates
	info:    Executing command mobile job update
	info:    mobile job update command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-i `<number>`** ou **--interval `<number>`** : intervalle de travail, défini sous la forme d’entier ; la valeur par défaut est `15`.
+ **-u `<unit>`** ou **--intervalUnit `<unit>`** : unité du paramètre _interval_, qui peut être l’une des valeurs suivantes :
	+ **minute** (valeur par défaut)
	+ **heure**
	+ **jour**
	+ **mois**
	+ **none** (travaux à la demande)
+ **-t `<time>`** **--startTime `<time>`** : heure de début de la première exécution du script, au format ISO ; la valeur par défaut est `now`.
+ **-a `<status>`** ou **--status `<status>`** : statut de la tâche, qui peut être `enabled` ou `disabled`.

**mobile job delete [options] [nom\_service] [nom\_tâche]**

Cette commande permet de supprimer le travail planifié getUpdates du serveur TodoList.

	~$azure mobile job delete todolist getUpdates
	info:    Executing command mobile job delete
	info:    mobile job delete command OK

> [AZURE.NOTE] La suppression d'un travail entraîne celle du script téléchargé.

### <a name="Mobile_Scale"></a>Commandes pour mettre à l’échelle un service mobile

Les commandes de cette section permettent de mettre à l'échelle un service mobile. Pour plus d'informations, consultez la page [Augmenter l'extensibilité d'un service mobile](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx).

**mobile scale show [options] [nom\_service]**

Cette commande permet d'afficher des informations de mise à l'échelle, notamment le mode de calcul et le nombre d'instances actuels.

	~$azure mobile scale show todolist
	info:    Executing command mobile scale show
	data:    webspace WESTUSWEBSPACE
	data:    computeMode Free
	data:    numberOfInstances 1
	info:    mobile scale show command OK

**mobile scale change [options] [nom\_service]**

Cette commande permet de modifier l'échelle du service mobile, la faisant passer du mode gratuit (free) au mode premium.

	~$azure mobile scale change -c Reserved -i 1 todolist
	info:    Executing command mobile scale change
	+ Rescaling the mobile service
	info:    mobile scale change command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-c `<mode>`** ou **--computeMode `<mode>`** : le mode de calcul doit être `Free` ou `Reserved`.
+ **-i `<count>`** ou **--numberOfInstances `<count>`** : nombre d’instances utilisées lors de l’exécution en mode réservé.

> [AZURE.NOTE] Lorsque vous attribuez au mode de calcul la valeur `Reserved`, tous vos services mobiles d’une même région s’exécutent en mode premium.


###Commandes pour activer les fonctionnalités préliminaires pour votre service mobile

**mobile preview list [options] [nom\_service]**

Cette commande permet d'afficher les fonctionnalités préliminaires disponibles dans le service spécifié et si elles sont activées.

	~$ azure mobile preview list mysite
	info:    Executing command mobile preview list
	+ Getting preview features
	data:    Preview feature  Enabled
	data:    ---------------  -------
	data:    SourceControl    No
	data:    Users            No
	info:    You can enable preview features using the 'azure mobile preview enable' command.
	info:    mobile preview list command OK

**mobile preview enable [options] [nom\_service] [nom\_fonctionnalité]**

Cette commande permet d'activer la fonctionnalité préliminaire spécifiée pour un service mobile. Notez que les fonctionnalités préliminaires qui ont été activées pour un service mobile ne peuvent plus être désactivées.

###Commandes pour gérer vos API de service mobile

**mobile api list [options] [nom]**

Cette commande permet d'afficher la liste des API personnalisées que vous avez créées pour votre service mobile.

	~$ azure mobile api list mysite
	info:    Executing command mobile api list
	+ Retrieving list of APIs
	info:    APIs
	data:    Name                  Get          Put          Post         Patch        Delete
	data:    --------------------  -----------  -----------  -----------  -----------  -----------
	data:    myCustomRetrieveAPI   application  application  application  application  application
	info:    You can manipulate API scripts using the 'azure mobile script' command.
	info:    mobile api list command OK

**mobile api create [options] [nom\_service] [nom\_API]**

Crée une API personnalisée de service mobile.

	~$ azure mobile api create mysite myCustomRetrieveAPI
	info:    Executing command mobile api create
	+ Creating custom API: 'myCustomRetrieveAPI'
	info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
	info:    mobile api create command OK

Cette commande prend en charge l'option supplémentaire suivante :

**-p** ou **--permissions** &lt;autorisations> : liste délimitée par des virgules de paires &lt;méthode>=&lt;autorisation>.

**mobile api update [options] [nom\_service] [nom\_API]**

Cette commande permet de mettre à jour l'API personnalisée de service mobile spécifiée.

Cette commande prend en charge l'option supplémentaire suivante :

Cette commande prend en charge les options supplémentaires suivantes :

+ **-p** ou **--permissions** &lt;autorisations> : liste délimitée par des virgules de paires &lt;méthode>=&lt;autorisation>.
+ **-f** ou **--force** : écrase les modifications personnalisées apportées au fichier de métadonnées des autorisations.

**mobile api delete [options] [nom\_service] [nom\_API]**

	~$ azure mobile api delete mysite myCustomRetrieveAPI
	info:    Executing command mobile api delete
	+ Deleting API: 'myCustomRetrieveAPI'
	info:    mobile api delete command OK

Cette commande permet de supprimer l'API personnalisée de service mobile spécifiée.

###Commandes pour gérer vos paramètres d'application mobile

**mobile appsetting list [options] [nom\_service]**

Cette commande permet d'afficher les paramètres d'application mobile pour le service spécifié.

	~$ azure mobile appsetting list mysite
	info:    Executing command mobile appsetting list
	+ Retrieving app settings
	data:    Name               Value
	data:    -----------------  -----
	data:    enablebetacontent  true
	info:    mobile appsetting list command OK

**mobile appsetting add [options] [nom\_service] [nom] [valeur]**

Cette commande permet d'ajouter un paramètre d'application personnalisé pour votre service mobile.

	~$ azure mobile appsetting add mysite enablebetacontent true
	info:    Executing command mobile appsetting add
	+ Retrieving app settings
	+ Adding app setting
	info:    mobile appsetting add command OK

**mobile appsetting delete [options] [nom\_service] [nom]**

Cette commande permet de supprimer le paramètre d'application spécifié pour votre service mobile.

	~$ azure mobile appsetting delete mysite enablebetacontent
	info:    Executing command mobile appsetting delete
	+ Retrieving app settings
	+ Removing app setting 'enablebetacontent'
	info:    mobile appsetting delete command OK

**mobile appsetting show [options] [nom\_service] [nom]**

Cette commande permet de supprimer le paramètre d'application spécifié pour votre service mobile.

	~$ azure mobile appsetting show mysite enablebetacontent
	info:    Executing command mobile appsetting show
	+ Retrieving app settings
	info:    enablebetacontent: true
	info:    mobile appsetting show command OK

## Gestion des paramètres locaux d'outil

Les paramètres locaux correspondent à votre ID d'abonnement et à votre nom de compte de stockage par défaut.

**config list [options]**

Cette commande permet d'afficher les paramètres de configuration.

	~$ azure config list
	info:   Displaying config settings
	data:   Setting                Value
	data:   ---------------------  ------------------------------------
	data:   subscription           32-digit-subscription-key
	data:   defaultStorageAccount  name

**config set [options] &lt;nom&gt;,&lt;valeur&gt;**

Cette commande permet de modifier un paramètre de configuration.

	~$ azure config set defaultStorageAccount myname
	info:   Setting 'defaultStorageAccount' to value 'myname'
	info:   Changes saved.

## Commandes pour gérer Service Bus

Utilisez ces commandes pour gérer votre compte Service Bus.

**sb namespace check [options] &lt;nom>**

Vérifiez que l'espace de noms Service Bus est légal et disponible.

**sb namespace create &lt;name> &lt;emplacement>**

Crée un espace de noms Service Bus.

	~$ azure sb namespace create mysbnamespacea-test "West US"
	info:    Executing command sb namespace create
	+ Creating namespace mysbnamespacea-test in region West US
	data:    Name: mysbnamespacea-test
	data:    Region: West US
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Activating
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Enabled: true
	data:    _: [object Object]
	info:    sb namespace create command OK


**sb namespace delete &lt;nom>**

Supprime un espace de noms.

	~$ azure sb namespace delete mysbnamespacea-test
	info:    Executing command sb namespace delete
	Delete namespace mysbnamespacea-test? [y/n] y
	+ Deleting namespace mysbnamespacea-test
	info:    sb namespace delete command OK

**sb namespace list**

Répertorie tous les espaces de noms créés pour votre compte.

	~$ azure sb namespace list
	info:    Executing command sb namespace list
	+ Getting namespaces
	data:    Name                 Region   Status
	data:    -------------------  -------  ------
	data:    mysbnamespacea-test  West US  Active
	info:    sb namespace list command OK


**sb namespace location list**

Affiche la liste de tous les emplacements d'espace de noms disponibles.

	~$ azure sb namespace location list
	info:    Executing command sb namespace location list
	+ Getting locations
	data:    Name              Code
	data:    ----------------  ----------------
	data:    East Asia         East Asia
	data:    West Europe       West Europe
	data:    North Europe      North Europe
	data:    East US           East US
	data:    Southeast Asia    Southeast Asia
	data:    North Central US  North Central US
	data:    West US           West US
	data:    South Central US  South Central US
	info:    sb namespace location list command OK

**sb namespace show &lt;nom>**

Affiche les détails concernant un espace de noms spécifique.

	~$ azure sb namespace show mysbnamespacea-test
	info:    Executing command sb namespace show
	+ Getting namespace
	data:    Name: mysbnamespacea-test
	data:    Region: West US
	data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
	data:    Status: Active
	data:    CreatedAt: 2013-11-14T16:23:29.32Z
	data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
	data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

	data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
	net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
	WMoRpYy0Ynk=
	data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
	data:    Enabled: true
	data:    UpdatedAt: 2013-11-14T16:25:37.85Z
	info:    sb namespace show command OK

**sb namespace verify &lt;nom>**

Vérifie si l'espace de noms est disponible.

## Commandes pour gérer vos objets de stockage

###Commandes pour gérer vos comptes de stockage

**storage account list [options]**

Cette commande permet d'afficher les comptes de stockage de votre abonnement.

	~$ azure storage account list
	info:    Executing command storage account list
	+ Getting storage accounts
	data:    Name             Label  Location
	data:    ---------------  -----  --------
	data:    mybasestorage           West US
	info:    storage account list command OK

**storage account show [options] <name>**

Cette commande permet d'afficher des informations concernant le compte de stockage spécifié, dont l'URI et les propriétés du compte.

**storage account create [options] <name>**

Cette commande permet de créer un compte de stockage basé sur les options fournies.

	~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
	info:    Executing command storage account create
	+ Creating storage account
	info:    storage account create command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-e** ou **--label** &lt;étiquette> : étiquette du compte de stockage.
+ **-d** ou **--description** &lt;description> : description du compte de stockage.
+ **-l** ou **--location** &lt;nom> : région géographique où créer le compte de stockage.
+ **-a** ou **--affinity-group** &lt;nom> : groupe d'affinités auquel associer le compte d stockage.
+ **--type** : indique le type de compte à créer, soit stockage Standard avec option redondance (LRS/ZRS/GRS/RAGRS) soit Premium Storage (PLRS).

**storage account set [options] <name>**

Cette commande permet de mettre à jour le compte de stockage spécifié.

	~$ azure storage account set mybasestorage --type GRS
	info:    Executing command storage account set
	+ Updating storage account
	info:    storage account set command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-e** ou **--label** &lt;étiquette> : étiquette du compte de stockage.
+ **-d** ou **--description** &lt;description> : description du compte de stockage.
+ **-l** ou **--location** &lt;nom> : région géographique où créer le compte de stockage.
+ **--type** : indique le nouveau type de compte, soit stockage Standard avec option redondance (LRS/ZRS/GRS/RAGRS) soit Premium Storage (PLRS).

**storage account delete [options] <name>**

Cette commande permet de supprimer le compte de stockage spécifié.

Cette commande prend en charge l'option supplémentaire suivante :

**-q** ou **--quiet** : ne demande pas de confirmation. Utilisez cette option dans les scripts automatisés.

###Commandes pour gérer vos clés de compte de stockage

**storage account keys list [options] <name>**

Cela commande permet de répertorier les clés primaires et secondaires du compte de stockage spécifié.

**storage account keys renew [options] <name>**

###Commandes pour gérer votre conteneur de stockage

**storage container list [options] [préfixe]**

Cette commande permet d'afficher la liste des conteneurs de stockage pour un compte de stockage spécifié. Le compte de stockage est spécifié par la chaîne de connexion ou le nom du compte de stockage et la clé du compte.

Cette commande prend en charge les options supplémentaires suivantes :

+ **-p** ou **-prefix** &lt;préfixe> : préfixe du nom du conteneur de stockage.
+ **-a** ou **--account-name** &lt;nomCompte> : nom du compte de stockage.
+ **-k** ou **--account-key** &lt;cléCompte> : clé du compte de stockage.
+ **-c** ou **--connection-string** &lt;chaîneConnexion> : chaîne de connexion du stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

**storage container show [options] [conteneur]** **storage container create [options] [conteneur]**

Cette commande permet de créer un conteneur de stockage pour le compte de stockage spécifié. Le compte de stockage est spécifié par la chaîne de connexion ou le nom du compte de stockage et la clé du compte.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** &lt;conteneur> : nom du conteneur de stockage à créer.
+ **-p** ou **-prefix** &lt;préfixe> : préfixe du nom du conteneur de stockage.
+ **-a** ou **--account-name** &lt;nomCompte> : nom du compte de stockage.
+ **-k** ou **--account-key** &lt;cléCompte> : clé du compte de stockage.
+ **-c** ou **--connection-string** &lt;chaîneConnexion> : chaîne de connexion du stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

**storage container delete [options] [conteneur]**

Cette commande permet de supprimer le conteneur de stockage spécifié. Le compte de stockage est spécifié par la chaîne de connexion ou le nom du compte de stockage et la clé du compte.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** &lt;conteneur> : nom du conteneur de stockage à créer.
+ **-p** ou **-prefix** &lt;préfixe> : préfixe du nom du conteneur de stockage.
+ **-a** ou **--account-name** &lt;nomCompte> : nom du compte de stockage.
+ **-k** ou **--account-key** &lt;cléCompte> : clé du compte de stockage.
+ **-c** ou **--connection-string** &lt;chaîneConnexion> : chaîne de connexion du stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

**storage container set [options] [conteneur]**

Cette commande permet de définir la liste de contrôle d'accès pour le conteneur de stockage. Le compte de stockage est spécifié par la chaîne de connexion ou le nom du compte de stockage et la clé du compte.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** &lt;conteneur> : nom du conteneur de stockage à créer.
+ **-p** ou **-prefix** &lt;préfixe> : préfixe du nom du conteneur de stockage.
+ **-a** ou **--account-name** &lt;nomCompte> : nom du compte de stockage.
+ **-k** ou **--account-key** &lt;cléCompte> : clé du compte de stockage.
+ **-c** ou **--connection-string** &lt;chaîneConnexion> : chaîne de connexion du stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

###Commandes pour gérer votre objet blob de stockage

**storage blob list [options] [container] [préfixe]**

Cette commande renvoie une liste des objets blobs de stockage dans le conteneur de stockage spécifié.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** &lt;conteneur> : nom du conteneur de stockage à créer.
+ **-p** ou **-prefix** &lt;préfixe> : préfixe du nom du conteneur de stockage.
+ **-a** ou **--account-name** &lt;nomCompte> : nom du compte de stockage.
+ **-k** ou **--account-key** &lt;cléCompte> : clé du compte de stockage.
+ **-c** ou **--connection-string** &lt;chaîneConnexion> : chaîne de connexion du stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

**storage blob show [options] [container] [blob]**

Cette commande permet d'afficher les détails concernant l'objet blob de stockage spécifié.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** &lt;conteneur> : nom du conteneur de stockage à créer.
+ **-p** ou **-prefix** &lt;préfixe> : préfixe du nom du conteneur de stockage.
+ **-a** ou **--account-name** &lt;nomCompte> : nom du compte de stockage.
+ **-k** ou **--account-key** &lt;cléCompte> : clé du compte de stockage.
+ **-c** ou **--connection-string** &lt;chaîneConnexion> : chaîne de connexion du stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

**storage blob delete [options] [container] [blob]**

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** &lt;conteneur> : nom du conteneur de stockage à créer.
+ **-b** ou **--blob** &lt;nomObjetblob> : nom de l’objet blob de stockage à supprimer.
+ **-q** ou **--quiet** : supprime l’objet blob de stockage spécifié sans confirmation.
+ **-a** ou **--account-name** &lt;nomCompte> : nom du compte de stockage.
+ **-k** ou **--account-key** &lt;cléCompte> : clé du compte de stockage.
+ **-c** ou **--connection-string** &lt;chaîneConnexion> : chaîne de connexion du stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

**storage blob upload [options] [file] [container] [blob]**

Cette commande permet de mettre à jour le fichier spécifié sur l'objet blob de stockage spécifié.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** &lt;conteneur> : nom du conteneur de stockage à créer.
+ **-b** ou **--blob** &lt;nomObjetblob> : nom de l’objet blob de stockage à télécharger.
+ **-t** ou **--blobtype** &lt;typeObjetblob> : type d’objet blob de stockage : Page ou bloc.
+ **-p** ou **--properties** &lt;propriétés> : propriétés de l’objet blob de stockage pour le fichier téléchargé. Les propriétés sont des paires clé-valeur séparées par un point-virgule (;). Les propriétés disponibles sont contentType, contentEncoding, contentLanguage et cacheControl.
+ **-m** ou **--metadata** &lt;métadonnées> : métadonnées de l’objet blob de stockage pour le fichier téléchargé. Les métadonnées sont des paires clé-valeur séparées par un point-virgule (;).
+ **--concurrenttaskcount** &lt;nombretâchessimultanées> : nombre maximal de demandes de téléchargement simultanées.
+ **-q** ou **--quiet** : écrase l’objet blob de stockage spécifié sans confirmation.
+ **-a** ou **--account-name** &lt;nomCompte> : nom du compte de stockage.
+ **-k** ou **--account-key** &lt;cléCompte> : clé du compte de stockage.
+ **-c** ou **--connection-string** &lt;chaîneConnexion> : chaîne de connexion du stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

**storage blob download [options] [container] [blob] [destination]**

Cette commande permet de télécharger l'objet blob de stockage spécifié.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** &lt;conteneur> : nom du conteneur de stockage à créer.
+ **-b** ou **--blob** &lt;nomObjetblob> : nom de l’objet blob de stockage.
+ **-d** ou **--destination** [destination] : fichier de destination du téléchargement ou chemin d’accès du répertoire.
+ **-m** ou **--checkmd5** : contrôle md5sum du fichier téléchargé.
+ **--concurrenttaskcount** &lt;nombretâchessimultanées> : nombre maximal de demandes de téléchargements simultanées.
+ **-q** ou **--quiet** : écrase le fichier de destination sans confirmation.
+ **-a** ou **--account-name** &lt;nomCompte> : nom du compte de stockage.
+ **-k** ou **--account-key** &lt;cléCompte> : clé du compte de stockage.
+ **-c** ou **--connection-string** &lt;chaîneConnexion> : chaîne de connexion du stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

## Commandes pour gérer les bases de données SQL

Utilisez ces commandes pour gérer vos bases de données SQL Azure.

###Commandes pour gérer les serveurs SQL

Utilisez ces commandes pour gérer vos serveurs SQL.

**sql server create &lt;connexionAdministrateur> &lt;motdepasseAdministrateur> &lt;emplacement>**

Permet de créer un serveur de base de données.

	~$ azure sql server create test T3stte$t "West US"
	info:    Executing command sql server create
	+ Creating SQL Server
	data:    Server Name i1qwc540ts
	info:    sql server create command OK

**sql server show &lt;nom>**

Affiche des détails sur le serveur.

	~$ azure sql server show xclfgcndfg
	info:    Executing command sql server show
	+ Getting SQL server
	data:    SQL Server Name xclfgcndfg
	data:    SQL Server AdministratorLogin msopentechforums
	data:    SQL Server Location West US
	data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
	info:    sql server show command OK

**sql server list**

Obtient la liste des serveurs.

	~$ azure sql server list
	info:    Executing command sql server list
	+ Getting SQL server
	data:    Name        Location
	data:    ----------  --------
	data:    xclfgcndfg  West US
	info:    sql server list command OK

**sql server delete &lt;nom>**

Supprime un serveur.

	~$ azure sql server delete i1qwc540ts
	info:    Executing command sql server delete
	Delete server i1qwc540ts? [y/n] y
	+ Removing SQL Server
	info:    sql server delete command OK

###Commandes pour gérer les bases de données SQL

Utilisez ces commandes pour gérer vos bases de données SQL.

**sql db create [options] &lt;nom\_serveur> &lt;nom\_base\_de\_données> &lt;mot\_de\_passe\_Administrateur>**

Crée une instance de base de données.

	~$ azure sql db create fr8aelne00 newdb test
	info:    Executing command sql db create
	Administrator password: ********
	+ Creating SQL Server Database
	info:    sql db create command OK

**sql db show [options] &lt;nomServeur> &lt;nom\_base\_de\_données> &lt;mot\_de\_passe\_Administrateur>**

Affiche des détails sur la base de données.

	C:\windows\system32>azure sql db show fr8aelne00 newdb test
	info:    Executing command sql db show
	Administrator password: ********
	+ Getting SQL server databases
	data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
	ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
	m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
	icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
	ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
	ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
	/Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
	ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
	Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
	services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
	tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
	om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
	pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
	d=2013-11-18T19:48:27Z, name=
	data:    Database Id 4
	data:    Database Name newdb
	data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
	data:    Database ServiceObjectiveAssignmentState 1
	data:    Database ServiceObjectiveAssignmentStateDescription Complete
	data:    Database ServiceObjectiveAssignmentErrorCode
	data:    Database ServiceObjectiveAssignmentErrorDescription
	data:    Database ServiceObjectiveAssignmentSuccessDate
	data:    Database Edition Web
	data:    Database MaxSizeGB 1
	data:    Database MaxSizeBytes 1073741824
	data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
	data:    Database CreationDate
	data:    Database RecoveryPeriodStartDate
	data:    Database IsSystemObject
	data:    Database Status 1
	data:    Database IsFederationRoot
	data:    Database SizeMB -1
	data:    Database IsRecursiveTriggersOn
	data:    Database IsReadOnly
	data:    Database IsFederationMember
	data:    Database IsQueryStoreOn
	data:    Database IsQueryStoreReadOnly
	data:    Database QueryStoreMaxSizeMB
	data:    Database QueryStoreFlushPeriodSeconds
	data:    Database QueryStoreIntervalLengthMinutes
	data:    Database QueryStoreClearAll
	data:    Database QueryStoreStaleQueryThresholdDays
	info:    sql db show command OK

**sql db list [options] &lt;nomServeur> &lt;motdepasseAdministrateur>**

Répertorie les bases de données.

	~$ azure sql db list fr8aelne00 test
	info:    Executing command sql db list
	Administrator password: ********
	+ Getting SQL server databases
	data:    Name    Edition  Collation                     MaxSizeInGB
	data:    ------  -------  ----------------------------  -----------
	data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
	info:    sql db list command OK

**sql db delete [options] &lt;nomServeur> &lt;nom\_base\_de\_données> &mot\_de\_passe\_Administrateur>**

Supprime une base de données.

	~$ azure sql db delete fr8aelne00 newdb test
	info:    Executing command sql db delete
	Administrator password: ********
	Delete database newdb? [y/n] y
	+ Getting SQL server databases
	+ Removing database
	info:    sql db delete command OK

###Commandes pour gérer les règles de pare-feu SQL Server

Utilisez ces commandes pour gérer les règles de pare-feu SQL Server.

**sql firewallrule create [options] &lt;nom\_serveur> &lt;nom\_règle> &lt;Adresse\_IP\_Début> &lt;Adresse\_IP\_Fin>**

Crée une règle de pare-feu pour un serveur SQL.

	~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
	info:    Executing command sql firewallrule create
	+ Creating Firewall Rule
	info:    sql firewallrule create command OK

**sql firewallrule show [options] &lt;nom\_serveur> &lt;nom\_règle>**

Affiche les détails sur les règles de pare-feu.

	~$ azure sql firewallrule show fr8aelne00 allowed
	info:    Executing command sql firewallrule show
	+ Getting firewall rule
	data:    Firewall rule Name allowed
	data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
	data:    Firewall rule State Normal
	data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
	5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
	data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
	055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
	data:    Firewall rule StartIPAddress 131.107.0.0
	data:    Firewall rule EndIPAddress 131.107.255.255
	info:    sql firewallrule show command OK

**sql firewallrule list [options] &lt;nom\_serveur>**

Répertorie les règles de pare-feu.

	~$ azure sql firewallrule list fr8aelne00
	info:    Executing command sql firewallrule list
	\data:    Name     Start IP address  End IP address
	data:    -------  ----------------  ---------------
	data:    allowed  131.107.0.0       131.107.255.255
	+
	info:    sql firewallrule list command OK

**sql firewallrule delete [options] &lt;nom\_serveur> &lt;nom\_règle>**

Cette commande permet de supprimer une règle de pare-feu.

	~$ azure sql firewallrule delete fr8aelne00 allowed
	info:    Executing command sql firewallrule delete
	Delete rule allowed? [y/n] y
	+ Removing firewall rule
	info:    sql firewallrule delete command OK

## Commandes pour gérer vos réseaux virtuels

Utilisez ces commandes pour gérer vos réseaux virtuels.

**network vnet create [options] &lt;emplacement>**

Crée un réseau virtuel.

	~$ azure network vnet create vnet1 --location "West US" -v
	info:    Executing command network vnet create
	info:    Using default address space start IP: 10.0.0.0
	info:    Using default address space cidr: 8
	info:    Using default subnet start IP: 10.0.0.0
	info:    Using default subnet cidr: 11
	verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
	verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
	verbose: Fetching Network Configuration
	verbose: Fetching or creating affinity group
	verbose: Fetching Affinity Groups
	verbose: Fetching Locations
	verbose: Creating new affinity group AG1
	info:    Using affinity group AG1
	verbose: Updating Network Configuration
	info:    network vnet create command OK

**network vnet show &lt;nom>**

Affiche les détails concernant un réseau virtuel.

	~$ azure network vnet show vnet1
	info:    Executing command network vnet show
	+ Fetching Virtual Networks
	data:    Name "vnet1"
	data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
	data:    AffinityGroup "AG1"
	data:    State "Created"
	data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
	data:    Subnets 0 Name "subnet-1"
	data:    Subnets 0 AddressPrefix "10.0.0.0/11"
	info:    network vnet show command OK

**network vnet list**

Répertorie tous les réseaux virtuels existants.

	~$ azure network vnet list
	info:    Executing command network vnet list
	+ Fetching Virtual Networks
	data:    Name        Status   AffinityGroup
	data:    ----------  -------  -------------
	data:    vnet1      Created  AG1
	data:    vnet2      Created  AG1
	data:    vnet3      Created  AG1
	data:    vnet4      Created  AG1
	info:    network vnet list command OK


**network vnet delete &lt;nom>**

Supprime le réseau virtuel spécifié.

	~$ azure network vnet delete opentechvn1
	info:    Executing command network vnet delete
	+ Fetching Network Configuration
	Delete the virtual network opentechvn1 ?  (y/n) y
	+ Deleting the virtual network opentechvn1
	info:    network vnet delete command OK

**network export [chemin-fichier]**

Si vous disposez d'une configuration réseau avancée, vous pouvez l'exporter en local. Notez que la configuration réseau exportée comprend les paramètres de serveur DNS, les paramètres de réseau virtuel, les paramètres de site de réseau local et d'autres paramètres.

**network import [chemin-fichier]**

Importe une configuration de réseau local.

**network dnsserver register [options] &lt;IPdns>**

Inscrit le serveur DNS que vous prévoyez d'utiliser pour la résolution de noms dans votre configuration réseau.

	~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
	info:    Executing command network dnsserver register
	+ Fetching Network Configuration
	+ Updating Network Configuration
	info:    network dnsserver register command OK

**network dnsserver list**

Répertorie tous les serveurs DNS inscrits dans votre configuration réseau.

	~$ azure network dnsserver list
	info:    Executing command network dnsserver list
	+ Fetching Network Configuration
	data:    DNS Server ID         DNS Server IP
	data:    --------------------  --------------
	data:    DNS-bb39b4ac34d66a86  44.55.22.11
	data:    FrontEndDnsServer     98.138.253.109
	info:    network dnsserver list command OK

**network dnsserver unregister [options] &lt;IPdns>**

Supprime une entrée de serveur DNS de la configuration réseau.

	~$ azure network dnsserver unregister 77.88.99.11
	info:    Executing command network dnsserver unregister
	+ Fetching Network Configuration
	Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
	+ Deleting the DNS server entry dns-4 ( 77.88.99.11 )
	info:    network dnsserver unregister command OK

<!---HONumber=AcomDC_0518_2016-->