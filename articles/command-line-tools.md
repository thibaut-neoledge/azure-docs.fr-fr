<properties 
	pageTitle="Outils en ligne de commande Azure pour Mac et Linux" 
	description="Découvrez comment utiliser l'outil en ligne de commande pour Mac et Linux dans Azure." 
	services="web-sites, virtual-machines, mobile-services, cloud-services" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="command-line-interface" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/13/2014" 
	ms.author="rasquill"/>

#Outil en ligne de commande Azure pour Mac et Linux

Les fonctionnalités de cet outil permettent de créer, de déployer et de gérer des machines virtuelles, des sites web et Azure Mobile Services à partir de postes de travail Mac et Linux. Elles sont comparables à celles fournies par les cmdlets Windows PowerShell installées avec les Kits de développement logiciel (SDK) Azure pour .NET, Node.JS et PHP.

Pour installer l'outil sur un Mac, téléchargez et exécutez le [programme d'installation du Kit de développement logiciel (SDK) Azure](http://go.microsoft.com/fwlink/?LinkId=252249).

Pour installer l'outil sur Linux, installez la dernière version de Node.JS et utilisez NPM pour l'installation :

    npm install azure-cli -g

Les paramètres facultatifs sont indiqués entre crochets (par exemple, [paramètres]). Tous les autres paramètres sont obligatoires.

Outre les paramètres facultatifs propres aux commandes qui vous sont présentés dans ce document, vous pouvez utiliser trois paramètres facultatifs pour afficher une sortie détaillée, telle que les options de demande et les codes d'état. Si le paramètre -v fournit une sortie détaillée, le paramètre -vv propose des informations encore plus complètes. L'option --json génère le résultat au format json brut.

**Sommaire :**

* [Gestion de vos informations de compte et des paramètres de publication](#Manage_your_account_information_and_publish_settings)
* [Commandes pour gérer vos machines virtuelles Azure](#Commands_to_manage_your_Azure_virtual_machines)
* [Commandes pour gérer vos points de terminaison de machine virtuelle Azure](#Commands_to_manage_your_Azure_virtual_machine_endpoints)
* [Commandes pour gérer vos images de machine virtuelle Azure](#Commands_to_manage_your_Azure_virtual_machine_images)
* [Commandes pour gérer vos disques de données de machine virtuelle Azure](#Commands_to_manage_your_Azure_virtual_machine_data_disks)
* [Commandes pour gérer vos services cloud Azure](#Commands_to_manage_your_Azure_cloud_services)
* [Commandes pour gérer vos certificats Azure](#Commands_to_manage_your_Azure_certificates)
* [Commandes pour gérer vos sites Web](#Commands_to_manage_your_web_sites)
* [Commandes pour gérer Azure Mobile Services](#Commands_to_manage_mobile_services)
* [Gestion des paramètres locaux d'outil](#Manage_tool_local_settings)
* [Commandes pour gérer Service Bus](#Commands_to_manage_service_bus)
* [Commandes pour gérer vos objets de stockage](#Commands_to_manage_your_Storage_objects)
* [Commandes pour gérer les bases de données SQL](#Commands_to_manage_sql)
* [Commandes pour gérer vos réseaux virtuels](#Commands_to_manage_vnet)

##<a name="Manage_your_account_information_and_publish_settings"></a>Gestion de vos informations de compte et des paramètres de publication
L'outil se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues depuis le portail Azure dans un fichier de paramètres de publication, comme décrit dans ce document. Vous pouvez importer le fichier de paramètres de publication en tant que paramètre de configuration local persistant dont l'outil se servira pour les opérations ultérieures. Vous importez vos paramètres de publication une fois pour toutes.

**account download [options]**

Cette commande permet de démarrer un navigateur pour télécharger votre fichier .publishsettings depuis le portail Azure.

	~$ azure account download
	info:   Executing command account download
	info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
	help:   Save the downloaded file, then execute the command
	help:   account import <file>
	info:   account download command OK

**account import [options] <file>**


Cette commande permet d'importer un fichier publishsettings ou un certificat pour pouvoir être utilisé ultérieurement par l'outil.

	~$ azure account import publishsettings.publishsettings
	info:   Importing publish settings file publishsettings.publishsettings
	info:   Found subscription: 3-Month Free Trial
	info:   Found subscription: Pay-As-You-Go
	info:   Setting default subscription to: 3-Month Free Trial
	warn:   The 'publishsettings.publishsettings' file contains sensitive information.
	warn:   Remember to delete it now that it has been imported.
	info:   Account publish settings imported successfully

<div class="dev-callout"><b>Remarque</b>
   <p>Le fichier publishsettings peut contenir les détails (c'est-à-dire, le nom et l'ID d'abonnement) concernant plusieurs abonnements. Lorsque vous importez le fichier publishsettings, le premier abonnement est utilisé comme description par défaut. Pour utiliser un autre abonnement, exécutez la commande suivante :</p>
<code>~$ azure config set subscription <other-subscription-id></code>
</div>

**account clear [options]**

Cette commande permet de supprimer les paramètres de publication stockés qui ont été importés. Utilisez-la si vous avez fini d'utiliser l'outil sur cet ordinateur et si vous voulez vous assurer que l'outil ne peut pas être utilisé avec votre compte à un moment ultérieur.

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

**account set [options] <subscription>**

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

**account affinity-group create [options] <name>**

Cette commande permet de créer un groupe d'affinités.

	~$ azure account affinity-group create opentec -l "West US"
	info:    Executing command account affinity-group create
	+ Creating affinity group
	info:    account affinity-group create command OK

**account affinity-group show [options] <name>**

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

**account affinity-group delete [options] <name>**

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

**account env show [options] [environment]**

Affiche les détails d'environnement de compte.

	~$ azure account env show
	info:    Executing command account env show
	Environment name: AzureCloud
	data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
	data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
	info:    account env show command OK

**account env add [options] [environment]**

Cette commande permet d'ajouter un environnement au compte.

**account env set [options] [environment]**

Cette commande permet de définir l'environnement de compte.

**account env delete [options] [environment]**

Cette commande permet de supprimer l'environnement spécifié du compte.

##<a name="Commands_to_manage_your_Azure_virtual_machines"></a>Commandes pour gérer vos machines virtuelles Azure
Le schéma suivant montre comment les machines virtuelles Azure sont hébergées dans l'environnement de déploiement de production d'un service cloud Azure.
 
![Azure Technical Diagram](./media/command-line-tools/architecturediagram.jpg)	

**create-new** crée le lecteur dans le stockage d'objets blob (en l'occurrence, e:\ dans le schéma) ; **attach** attache un disque déjà créé mais non attaché à une machine virtuelle.

**vm create [options] <dns-name> <image> <userName> [password]**

Cette commande permet de créer une machine virtuelle Azure. Par défaut, chaque machine virtuelle est créée dans son propre service cloud ; toutefois, vous pouvez faire en sorte qu'une machine virtuelle soit ajoutée à un service cloud existant à l'aide de l'option -c décrite dans ce document.

Comme le portail Azure, la commande vm create ne crée les machines virtuelles que dans l'environnement de déploiement de production. Il n'existe aucune option permettant de créer une machine virtuelle dans l'environnement de déploiement intermédiaire d'un service cloud. Si votre abonnement ne dispose pas d'un compte Azure Storage existant, la commande en crée un.

Vous pouvez spécifier un emplacement via le paramètre --location ou un groupe d'affinités via le paramètre --affinity-group. Si aucun n'est fourni, vous êtes invité à en fournir un dans la liste des emplacements valides.

Le mot de passe fourni doit être constitué de 8 à 123 caractères et doit respecter les exigences de complexité des mots de passe du système d'exploitation que vous utilisez pour la machine virtuelle.

Si vous prévoyez la nécessité de recourir à SSH pour gérer une machine virtuelle Linux déployée (comme c'est généralement le cas), vous devez activer SSH via l'option -e au moment de créer la machine virtuelle. Il n'est pas possible d'activer SSH une fois que la machine virtuelle a été créée.

Sur les machines virtuelles Windows, il est possible d'activer RDP ultérieurement en ajoutant le port 3389 comme point de terminaison.

Les paramètres facultatifs pris en charge pour cette commande sont les suivants :

**-c, --connect** crée la machine virtuelle dans un déploiement déjà créé dans un service d'hébergement. Si -vmname n'est pas utilisé avec cette option, le nom de la nouvelle machine virtuelle est généré automatiquement.<br />
**-n, --vm-name** spécifie le nom de la machine virtuelle. Ce paramètre prend le nom du service d'hébergement par défaut. Si -vmname n'est pas spécifié, le nom de la nouvelle machine virtuelle est généré sous la forme <service-name><id>, où <id> correspond au nombre de machines virtuelles existant dans le service plus 1. Par exemple, si vous utilisez cette commande pour ajouter une nouvelle machine virtuelle au service d'hébergement MyService, la nouvelle machine virtuelle est nommée MyService2.<br /> 
**-u, --blob-url** spécifie l'URL du stockage d'objets blob à partir duquel le disque système de la machine virtuelle est créé. <br />
**-z, --vm-size** spécifie la taille de la machine virtuelle. Les valeurs valides sont " extrasmall " (très petite), " small " (petite), " medium " (moyenne), " large " (grande), " extralarge " (très grande). La valeur par défaut est " small " (petite). <br />
**-r** ajoute la connectivité RDP à une machine virtuelle Windows. <br />
**-e, --ssh** ajoute la connectivité SSH à une machine virtuelle Windows. <br />
**-t, --ssh-cert** spécifie le certificat SSH. <br />
**-s** Abonnement <br />
**-o, --community** L'image spécifiée est une image de communauté <br />
**-w** Nom du réseau virtuel <br/>
**-l, --location** Spécifie le lieu (par exemple, " Nord du centre des États-Unis "). <br />
**-a, --affinity-group** spécifie le groupe d'affinités.<br />
**-w, --virtual-network-name** Spécifie le réseau virtuel sur lequel la nouvelle machine virtuelle doit être ajoutée. Les réseaux virtuels peuvent être configurés et gérés à partir du portail Azure.<br />
**-b, --subnet-names** Spécifie les noms de sous-réseaux à attribuer à la machine virtuelle.

Dans cet exemple, MSFT__Win2K8R2SP1-120514-1520-141205-01-fr-fr-30GB est une image fournie par la plateforme. Pour plus d'informations sur les images de système d'exploitation, consultez la commande vm image list.

	~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "Western US" -r
	info:   Executing command vm create
	Enter VM 'my-vm-name' password: ************                                     
	info:   vm create command OK

**vm create-from <<dns-name> <<role-file>**

Cette commande permet de créer une machine virtuelle Azure à partir d'un fichier de rôle JSON.

	~$ azure vm create-from my-vm example.json
	info:   OK

**vm list [options]**

Cette commande permet de répertorier les machines virtuelles Azure. L'option -json spécifie que les résultats sont renvoyés au format JSON brut. 

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

**vm show [options] <name>**

Cette commande permet d'afficher les détails concernant une machine virtuelle Azure. L'option -json spécifie que les résultats sont renvoyés au format JSON brut. 

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

**vm delete [options] <name>**

Cette commande permet de supprimer une machine virtuelle Azure. Par défaut, elle ne supprime pas l'objet blob Azure à partir duquel le disque de système d'exploitation et le disque de données sont créés. Pour supprimer l'objet blob, ainsi que la machine virtuelle sur laquelle il est basé, spécifiez l'option -b.

	~$ azure vm delete my-vm 
	info:   Executing command vm delete
	info:   vm delete command OK

**vm start [options] <name>**

Cette commande permet de démarrer une machine virtuelle Azure.

	~$ azure vm start my-vm
	info:   Executing command vm start
	info:   vm start command OK

**vm restart [options] <name>**

Cette commande permet de redémarrer une machine virtuelle Azure.

	~$ azure vm restart my-vm
	info:   Executing command vm restart
	info:   vm restart command OK

**vm shutdown [options] <name>**

Cette commande permet d'arrêter une machine virtuelle Azure. Vous pouvez utiliser l'option -p pour indiquer que la ressource de calcul ne doit pas être libérée lors de l'arrêt.

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**vm capture <vm-name> <target-image-name>**

Cette commande permet de capturer une image de machine virtuelle Azure.

La capture d'une image de machine virtuelle n'est possible que si la machine virtuelle est à l'arrêt (état **Stopped**).

	~$ azure.cmd vm capture my-vm mycaptureimagename --delete
	info:   Executing command vm capture
	+ Fetching VMs
	+ Capturing VM
	info:   vm capture command OK

**vm export [options] <vm-name> <file-path>**

Cette commande permet d'exporter une image de machine virtuelle Azure vers un fichier.

	~$ azure vm export "myvm" "C:\"
	info:    Executing command vm export
	+ Getting virtual machines
	+ Exporting the VM
	info:   vm export command OK

##<a name="Commands_to_manage_your_Azure_virtual_machine_endpoints"></a>Commandes pour gérer vos points de terminaison de machine virtuelle Azure
Le schéma suivant illustre l'architecture d'un déploiement type de plusieurs instances d'une machine virtuelle. Dans cet exemple, notez que le port 3389 est ouvert sur chaque machine virtuelle (pour l'accès RDP) et que chacune d'elles a une adresse IP interne (en l'occurrence, 168.55.11.1) qui est utilisée par l'équilibrage de charge pour acheminer le trafic vers la machine virtuelle. Cette adresse IP interne peut également être utilisée pour permettre la communication entre les machines virtuelles.

![azurenetworkdiagram](./media/command-line-tools/networkdiagram.jpg)
 
Les demandes externes adressées aux machines virtuelles transitent par l'équilibrage de charge. De ce fait, les demandes ne peuvent pas être spécifiées pour une machine virtuelle en particulier dans les déploiements constitués de plusieurs machines virtuelles. Dans ces déploiements, le mappage de port doit être configuré entre les machines virtuelles (vm-port) et l'équilibrage de charge (lb-port).

**vm endpoint create <vm-name> <lb-port> [vm-port]**

Cette commande permet de créer un point de terminaison de machine virtuelle. Vous pouvez également utiliser -u ou --enable-direct-server-return pour indiquer si le retour au serveur direct doit être activé ou non sur ce point de terminaison (option désactivée par défaut).

	~$ azure vm endpoint create my-vm 8888 8888
	azure vm endpoint create my-vm 8888 8888
	info:   Executing command vm endpoint create
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint create command OK

**vm endpoint create-multiple [options] <vm-name> <lb-port>[:<vm-port>[:<protocol>[:<lb-set-name>[:<prob-protocol>:<lb-prob-port>[:<prob-path>]]]]] ]{1-*}**

Cette commande permet de créer plusieurs points de terminaison de machine virtuelle. Vous pouvez également utiliser -u ou --enable-direct-server-return pour indiquer si le retour au serveur direct doit être activé ou non sur ce point de terminaison (option désactivée par défaut).

**vm endpoint delete <vm-name> <lb-port>**

Cette commande permet de supprimer un point de terminaison de machine virtuelle.

	~$ azure vm endpoint delete my-vm 8888
	azure vm endpoint delete my-vm 8888
	info:   Executing command vm endpoint delete
	+ Fetching VM
	+ Reading network configuration
	+ Updating network configuration
	info:   vm endpoint delete command OK

**vm endpoint list <vm-name>**

Cette commande permet de répertorier tous les points de terminaison de machine virtuelle. L'option -json spécifie que les résultats sont renvoyés au format JSON brut. 

	~$ azure vm endpoint list my-linux-vm
	data:   Name  External Port  Local Port                                        
	data:   ----  -------------  ----------
	data:   ssh   22             22

**vm endpoint update [options] <vm-name> <endpoint-name>**

Cette commande permet de mettre à jour un point de terminaison de machine virtuelle avec de nouvelles valeurs en utilisant ces options.

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp) 

**vm endpoint show [options] <vm-name>**

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

##<a name="Commands_to_manage_your_Azure_virtual_machine_images"></a>Commandes pour gérer vos images de machine virtuelle Azure

Les images de machine virtuelle sont des captures de machines virtuelles déjà configurées qui peuvent être répliquées selon les nécessités.

**vm image list [options]**

Cette commande permet d'obtenir une liste d'images de machine virtuelle. Il existe trois types d'image : les images créées par Microsoft, ayant pour préfixe " MSFT ", les images créées par des tiers, qui ont généralement le nom du fournisseur en préfixe, et les images que vous créez. Pour créer des images, vous pouvez capturer une machine virtuelle existante ou créer une image à partir d'un fichier .vhd personnalisé téléchargé sur le stockage d'objets blob. Pour plus d'informations sur l'utilisation d'un fichier .vhd personnalisé, consultez la commande vm image create.
L'option -json spécifie que les résultats sont renvoyés au format JSON brut. 

	~$ azure vm image list
	data:   Name                                                                   Category   OS
	data:   ---------------------------------------------------------------------  ---------  -------
	data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-fr-fr-30GB.vhd   Canonical  Linux
	data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.fr-fr.30GB.2012-03-22                      Microsoft  Windows
	data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
	data:   MSFT__Windows-Server-2008-R2-SP1.fr-fr.30GB.2012-3-22                  Microsoft  Windows
	data:   OpenLogic__OpenLogic-CentOS-62-20120509-fr-fr-30GB.vhd                 OpenLogic  Linux
	data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-fr-fr-30GB.vhd       SUSE       Linux
	data:   SUSE__OpenSUSE64121-03192012-fr-fr-15GB.vhd                            SUSE       Linux
	data:   WIN2K8-R2-WINRM                                                        User       Windows
	info:   vm image list command OK   

**vm image show [options] <name>**

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

**vm image delete [options] <name>**

Cette commande permet de supprimer une image de machine virtuelle.

	~$ azure vm image delete my-vm-image
	info:   Executing command vm image delete
	info:   VM image deleted: my-vm-image                                         
	info:   vm image delete command OK

**vm image create <name> [source-path]**

Cette commande permet de créer une image de machine virtuelle. Vos fichiers .vhd personnalisés sont téléchargés vers le stockage d'objets blob et l'image de machine virtuelle est ensuite créée à partir de cet emplacement. C'est à partir de cette image de machine virtuelle que vous créez une machine virtuelle. Les paramètres Location (emplacement) et OS (système d'exploitation) sont obligatoires.

Certains systèmes imposent des limites de descripteurs de fichiers par processus. Si cette limite est dépassée, l'outil affiche une erreur de limite de descripteurs de fichiers. Vous pouvez réexécuter la commande en utilisant le paramètre -p <number> pour réduire le nombre maximal de téléchargements parallèles. Par défaut, ce nombre est de 96.

	~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
	info:   Executing command vm image create
	+ Retrieving storage accounts
	info:   VHD size : 13 MB
	info:   Uploading 13312.5 KB
	Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
	info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
	info:   vm image create command OK

##<a name="Commands_to_manage_your_Azure_virtual_machine_data_disks"></a>Commandes pour gérer vos disques de données de machine virtuelle Azure

Les disques de données sont des fichiers .vhd contenus dans le stockage d'objets blob qui peuvent être utilisés par une machine virtuelle. Pour plus d'informations sur le déploiement de disques de données dans le stockage d'objets blob, consultez le schéma technique Azure présenté plus haut. 

Les commandes permettant d'attacher des disques de données (azure vm disk attach et azure vm disk attach-new) attribuent un numéro d'unité logique (LUN) au disque de données attaché, comme l'exige le protocole SCSI. Le premier disque de données attaché à une machine virtuelle est le LUN 0, le suivant est le LUN 1 et ainsi de suite.

Lorsque vous détachez un disque de données à l'aide de la commande azure vm disk detach, utilisez le paramètre <lun> pour indiquer quel disque détacher. 

<div class="dev-callout"><b>Remarque</b>
   <p>Notez que vous devez toujours détacher les disques de données dans l'ordre inverse des numéros d'unité logique attribués, c'est-à-dire en commençant par le numéro le plus élevé. En effet, la couche SCSI Linux ne permet pas de détacher un numéro d'unité logique inférieur à un autre toujours attaché. Par exemple, vous ne devez pas détacher LUN 0 si LUN 1 est toujours attaché.</p>
</div>

**vm disk show [options] <name>**

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
	data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-fr-fr-30GB.vhd"
	info:   vm disk show command OK

**vm disk list [options] [vm-name]**

Cette commande répertorie les disques Azure ou des disques attachés à une machine virtuelle spécifiée. Si elle est exécutée avec un paramètre de nom de machine virtuelle, elle renvoie tous les disques attachés à la machine virtuelle. LUN 1 est créé avec la machine virtuelle et les autres disques éventuellement répertoriés sont attachés séparément.

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

**vm disk delete [options] <name>**

Cette commande permet de supprimer un disque Azure d'un référentiel personnel. Le disque doit être détaché de la machine virtuelle avant d'être supprimé.

	~$ azure vm disk delete mycentos-mycentos-2-20120525055052
	info:   Executing command vm disk delete
	info:   Disk deleted: mycentos-mycentos-2-20120525055052                  
	info:   vm disk delete command OK

**vm disk create <name> [source-path]**

Cette commande permet de télécharger et d'inscrire un disque Azure. Les paramètres --blob-url, --location ou --affinity-group doivent être spécifiés. Si vous utilisez cette commande avec [source-path], le fichier .vhd spécifié est téléchargé et une nouvelle image est créée. Vous pouvez ensuite attacher cette image à une machine virtuelle à l'aide de la commande vm disk attach.

Certains systèmes imposent des limites de descripteurs de fichiers par processus. Si cette limite est dépassée, l'outil affiche une erreur de limite de descripteurs de fichiers. Vous pouvez réexécuter la commande en utilisant le paramètre -p <number> pour réduire le nombre maximal de téléchargements parallèles. Par défaut, ce nombre est de 96. 

	~$ azure vm disk create my-data-disk ~/test.vhd --location "Western US"
	info:   Executing command vm disk create
	info:   VHD size : 10 MB                                                       
	info:   Uploading 10240.5 KB
	Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s 
	info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
	info:   vm disk create command OK

**vm disk upload [options] <source-path> <blob-url> <storage-account-key>**

Cette commande permet de télécharger un disque de machine virtuelle.

	~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
	info:   Executing command vm disk upload                                                      
	info:   Uploading 12351.5 KB
	info:   vm disk upload command OK

**vm disk attach <vm-name> <disk-image-name>**

Cette commande permet d'attacher un disque existant dans le stockage d'objets blob à une machine virtuelle existante déployée dans un service cloud.

	~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
	info:   Executing command vm disk attach
	info:   vm disk attach command OK

**vm disk attach-new <vm-name> <size-in-gb> [blob-url]**

Cette commande permet d'attacher un disque de données à une machine virtuelle Azure. Dans cet exemple, la valeur 20 correspond à la taille en gigaoctets du nouveau disque à attacher. Vous pouvez éventuellement utiliser une URL d'objet blob comme dernier argument pour spécifier explicitement l'objet blob cible à créer. Si vous ne spécifiez pas d'URL d'objet blob, un objet blob est généré automatiquement.

	~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
	info:   Executing command vm disk attach-new
	info:   vm disk attach-new command OK  

**vm disk detach <vm-name> <lun>**

Cette commande permet de détacher un disque de données attaché à une machine virtuelle Azure. <lun> permet d'identifier le disque à détacher. Pour obtenir une liste de disques associés à un disque avant de le détacher, utilisez la commande vm disk-list <vm-name>.

	~$ azure vm disk detach my-vm 2
	info:   Executing command vm disk detach
	info:   vm disk detach command OK

##<a name="Commands_to_manage_your_Azure_cloud_services"></a>Commandes pour gérer vos services cloud Azure

Les services cloud Azure sont des applications et des services hébergés sur des rôles web et de travail. Les commandes suivantes peuvent être utilisées pour gérer les services cloud Azure.

**service create [options] <serviceName>**

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

**service show [options] <serviceName>**

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

**service delete [options] <name>**

Cette commande permet de supprimer un service cloud Azure.

	~$ azure service delete myservice
	info:   Executing command service delete myservice 
	info:   cloud-service delete command OK

Pour forcer la suppression, utilisez le paramètre `-q`.


##<a name="Commands_to_manage_your_Azure_certificates"></a>Commandes pour gérer vos certificats Azure

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

**service cert create <dns-prefix> <file> [password]**

Cette commande permet de télécharger un certificat. Laissez l'invite de mot de passe vide pour les certificats qui ne sont pas protégés par mot de passe.

	~$ azure service cert create nghinazz ~/publishSet.pfx
	info:   Executing command service cert create
	Cert password: 
	+ Creating certificate                                                         
	info:   service cert create command OK

**service cert delete [options] <thumbprint>**

Cette commande permet de supprimer un certificat.

	~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
	info:   Executing command service cert delete
	+ Deleting certificate                                                         
	info:   nghinazz : cert deleted
	info:   service cert delete command OK


##<a name="Commands_to_manage_your_web_sites"></a>Commands to manage your websites

Un site web Azure est une configuration web accessible par URI. Les sites Web sont hébergés dans des machines virtuelles, mais vous n'avez pas besoin de vous soucier des détails liés à la création et au déploiement de la machine virtuelle. Azure s'en charge à votre place.

**site list [options]**

Cette commande affiche la liste de vos sites Web.

	~$ azure site list
	info:   Executing command site list
	data:   Name            State    Host names                                        
	data:   --------------  -------  --------------------------------------------------
	data:   mongosite       Running  mongosite.antdf0.antares.windows.net     
	data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net     
	data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
	info:   site list command OK

**site set [options] [name]**

Cette commande permet de définir les options de configuration pour votre site web [name].

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

**site create [options] [name]**

Cette commande permet de créer un site Web et un répertoire local. 

	~$ azure site create mysite
	info:   Executing command site create
	info:   Using location northeuropewebspace
	info:   Creating a new web site
	info:   Created web site at  mysite.antdf0.antares.windows.net
	info:   Initializing repository
	info:   Repository initialized
	info:   site create command OK

<div class="dev-callout"><b>Remarque</b>
   <p>Le nom du site doit être unique. Vous ne pouvez pas créer un site avec le même nom DNS qu'un site existant.</p>
</div>

**site browse [options] [name]**

Cette commande permet d'ouvrir votre site Web dans un navigateur.

	~$ azure site browse mysite
	info:   Executing command site browse
	info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
	info:   site browse command OK

**site show [options] [name]**

Cette commande permet d'afficher les détails relatifs à un site Web.

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

**site delete [options] [name]**

Cette commande permet de supprimer un site Web.

	~$ azure site delete mysite
	info:   Executing command site delete
	info:   Deleting site mysite
	info:   Site mysite has been deleted
	info:   site delete command OK

 **site swap [options] [name]**
 
Cette commande permet d'intervertir les emplacements de deux sites Web.

Cette commande prend en charge l'option supplémentaire suivante :

**-q ou **--quiet** : ne demande pas de confirmation. Utilisez cette option dans les scripts automatisés.


**site start [options] [name]**

Cette commande permet de démarrer un site Web.

	~$ azure site start mysite
	info:   Executing command site start
	info:   Starting site mysite
	info:   Site mysite has been started
	info:   site start command OK

**site stop [options] [name]**

Cette commande permet d'arrêter un site Web.

	~$ azure site stop mysite
	info:   Executing command site stop
	info:   Stopping site mysite
	info:   Site mysite has been stopped
	info:   site stop command OK

**site restart [options] [name]

Cette commande permet d'arrêter, puis de démarrer un site Web spécifié.

Cette commande prend en charge l'option supplémentaire suivante :

**--slot** <slot> : nom de l'emplacement à redémarrer.


**site location list [options]**

Cette commande affiche la liste des emplacements de vos sites Web.

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

###Commandes pour gérer vos paramètres d'application de site Web

**site appsetting list [options] [name]**

Cette commande permet de répertorier les paramètres d'application ajoutés au site Web.

	~$ azure site appsetting list
	info:    Executing command site appsetting list
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Name  Value
	data:    ----  -----
	data:    test  value
	info:    site appsetting list command OK

**site appsetting add [options] <keyvaluepair> [name]**

Cette commande permet d'ajouter un paramètre d'application à votre site Web sous forme de paire clé-valeur.

	~$ azure site appsetting add test=value
	info:    Executing command site appsetting add
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	+ Updating site config information
	info:    site appsetting add command OK

**site appsetting delete [options] <key> [name]**

Cette commande permet de supprimer le paramètre d'application spécifié du site Web.

	~$ azure site appsetting delete test
	info:    Executing command site appsetting delete
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	Delete application setting test? [y/n] y
	+ Updating site config information
	info:    site appsetting delete command OK

**site appsetting show [options] <key> [name]**

Cette commande permet d'afficher des détails concernant le paramètre d'application spécifié.

	~$ azure site appsetting show test
	info:    Executing command site appsetting show
	Web site name: mydemosite
	+ Getting sites
	+ Getting site config information
	data:    Value:  value
	info:    site appsetting show command OK

###Commandes pour gérer vos certificats de site Web

**site cert list [options] [name]**

Cette commande permet de répertorier les certificats de site Web.

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

**site cert add [options] <certificate-path> [name]**

**site cert delete [options] <thumbprint> [name]**

**site cert show [options] <thumbprint> [name]**

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

###Commandes pour gérer vos chaînes de connexion de site Web

**site connectionstring list [options] [name]**

**site connectionstring add [options] <connectionname> <value> <type> [name]**

**site connectionstring delete [options] <connectionname> [name]**

**site connectionstring show [options] <connectionname> [name]**

###Commandes pour gérer vos documents par défaut de site Web

**site defaultdocument list [options] [name]**

**site defaultdocument add [options] <document> [name]**

**site defaultdocument delete [options] <document> [name]**

###Commandes pour gérer vos déploiements de site Web

**site deployment list [options] [name]**

**site deployment show [options] <commitId> [name]**

**site deployment redeploy [options] <commitId> [name]**

**site deployment github [options] [name]**

**site deployment user set [options] [username] [pass]**

###Commandes pour gérer vos domaines de site Web

**site domain list [options] [name]**

**site domain add [options] <dn> [name]**

**site domain delete [options] <dn> [name]**

###Commandes pour gérer vos mappages de gestionnaire de site Web

**site handler list [options] [name]**

**site handler add [options] <extension> <processor> [name]**

**site handler delete [options] <extension> [name]**

###Commandes pour gérer vos tâches Web de site Web

**site job list [options] [name]**

Cette commande permet de répertorier toutes les tâches Web d'un site Web.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-type** <job-type> : facultatif. Type de tâche Web. Les valeurs valides sont " triggered " et " continuous ". Renvoie par défaut
les tâches web de tous les types.
+ **--slot** <slot> : nom de l'emplacement à redémarrer.

**site job show [options] <jobName> <jobType> [name]**

Cette commande permet d'afficher les détails d'une tâche Web spécifique.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-name** <job-name> : obligatoire. Nom de la tâche web.
+ **--job-type** <job-type> : obligatoire. Type de tâche Web. Les valeurs valides sont " triggered " et " continuous ".
+ **--slot** <slot> : nom de l'emplacement à redémarrer.

**site job delete [options] <jobName> <jobType> [name]**

Cette commande permet de supprimer la tâche Web spécifiée.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-name** <job-name>    obligatoire. Nom de la tâche web.
+ **--job-type** <job-type>    obligatoire. Type de tâche Web. Les valeurs valides sont " triggered " et " continuous ".
+ **-q** ou **--quiet** : ne demande pas de confirmation. Utilisez cette option dans les scripts automatisés.
+ **--slot** <slot> : nom de l'emplacement à redémarrer.

**site job upload [options] <jobName> <jobType> <jobFile> [name]**

Cette commande permet de supprimer la tâche Web spécifiée.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-name** <job-name> : obligatoire. Nom de la tâche web.
+ **--job-type** <job-type> : obligatoire. Type de tâche Web. Les valeurs valides sont " triggered " et " continuous ".
+ **--job-file** <job-file> : obligatoire. fichier de tâche.
+ **--slot** <slot> : nom de l'emplacement à redémarrer.

**site job start [options] <jobName> <jobType> [name]**

Cette commande permet de démarrer la tâche Web spécifiée.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-name** <job-name> : obligatoire. Nom de la tâche web.
+ **--job-type** <job-type> : obligatoire. Type de tâche Web. Les valeurs valides sont " triggered " et " continuous ".
+ **--slot** <slot> : nom de l'emplacement à redémarrer.

**site job stop [options] <jobName> <jobType> [name]**

Cette commande permet d'arrêter la tâche web spécifiée. Seules les tâches continues peuvent être arrêtées.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-name** <job-name> : obligatoire. Nom de la tâche web.
+ **--slot** <slot> : nom de l'emplacement à redémarrer.

###Commandes pour gérer l'historique de vos tâches Web de site Web

**site job history list [options] [jobName] [name]**

Cette commande permet d'afficher l'historique des exécutions de la tâche Web spécifiée.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-name** <job-name> : obligatoire. Nom de la tâche web.
+ **--slot** <slot> : nom de l'emplacement à redémarrer.

**site job history show [options] [jobName] [runId] [name]**

Cette commande permet d'obtenir les détails relatifs à l'exécution de la tâche pour la tâche Web spécifiée.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--job-name** <job-name> : obligatoire. Nom de la tâche web.
+ **--run-id** <run-id> : facultatif. Identifiant de l'historique des exécutions. S'il n'est pas spécifié, la dernière exécution s'affiche.
+ **--slot** <slot> : nom de l'emplacement à redémarrer.

###Commandes pour gérer le diagnostic de votre site Web

**site log download [options] [name]**

Permet de télécharger un fichier .zip contenant le diagnostic de votre site Web.

	~$ azure site log download
	info:    Executing command site log download
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	+ Downloading diagnostic log to diagnostics.zip
	info:    site log download command OK

**site log tail [options] [name]**

Cette commande permet de connecter votre terminal au service de diffusion de journal en continu.

	~$ azure site log tail
	info:    Executing command site log tail
	Web site name: mydemosite
	+ Getting sites
	+ Getting site information
	2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**site log set [options] [name]**

Cette commande permet de configurer les options de diagnostic pour votre site Web.

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

###Commandes pour gérer les référentiels de votre site Web

**site repository branch [options] <branch> [name]**

**site repository delete [options] [name]**

**site repository sync [options] [name]**

###Commandes pour gérer la mise à l'échelle de votre site Web

**site scale mode [options] <mode> [name]**

**site scale instances [options] <instances> [name]**


##<a name="Commands_to_manage_mobile_services"></a>Commandes pour gérer Azure Mobile Services

Azure Mobile Services réunit un ensemble de services Azure qui dotent vos applications de fonctionnalités principales. Les commandes Mobile Services se répartissent en différentes catégories, à savoir :

+ [Commandes pour gérer des instances de service mobile](#Mobile_Services)
+ [Commandes pour gérer la configuration d'un service mobile](#Mobile_Configuration)
+ [Commandes pour gérer les tables d'un service mobile](#Mobile_Tables)
+ [Commandes pour gérer les scripts d'un service mobile](#Mobile_Scripts)
+ [Commandes pour gérer les travaux planifiés](#Mobile_Jobs)
+ [Commandes pour mettre à l'échelle un service mobile](#Mobile_Scale)

Les options suivantes s'appliquent à la plupart des commandes Mobile Services :

+ **-h** ou **--help** : affiche des informations sur l'utilisation de la sortie.
+ **-s `<id>`** ou **--subscription `<id>`** : utilise un abonnement spécifique, spécifié sous la forme `<id>`.
+ **-v** ou **--verbose** : écrit une sortie détaillée.
+ **--json** : écrit une sortie JSON.

###<a name="Mobile_Services"></a>Commandes pour gérer des instances de service mobile

**mobile locations [options]**

Cette commande permet de répertorier les emplacements géographiques pris en charge par Mobile Services.

	~$ azure mobile locations
	info:    Executing command mobile locations
	info:    East US (default)
	info:    West US		
	info:    North Europe

**mobile create [options] [servicename] [sqlAdminUsername] [sqlAdminPassword]**

Cette commande permet de créer un service mobile avec une base de données et un serveur SQL.

	~$ azure mobile create todolist your_login_name Secure$Password
	info:    Executing command mobile create
	+ Creating mobile service
	info:    Overall application state: Healthy
	info:    Mobile service (todolist) state: ProvisionConfigured
	info:    SQL database (todolist_db) state: Provisioned
	info:    SQL server (e96ean1c6v) state: ProvisionConfigured
	info:    mobile create command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-r `<sqlServer>`**  ou **--sqlServer `<sqlServer>`**:  utilise un serveur de base de données SQL existant, spécifié sous la forme `<sqlServer>`.
+ **-d `<sqlDb>`** ou **--sqlDb `<sqlDb>`** : utilise une base de données SQL existante, spécifiée sous la forme `<sqlDb>`.
+ **-l `<location>`** ou **--location `<location>`** : crée le service à un emplacement spécifique, spécifié sous la forme `<location>`. Exécutez la commande azure mobile locations pour obtenir les emplacements disponibles.	
+ **--sqlLocation `<location>`** : crée le serveur SQL dans un emplacement `<location>` spécifique ; par défaut, il s'agit de l'emplacement du service mobile.

**mobile delete [options] [servicename]**

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

Cette commande prend en charge les options supplémentaires suivantes :

+ **-d** ou **--deleteData** : supprime de la base de données toutes les données de ce service mobile.
+ **-a** ou **--deleteAll** : supprime la base de données et le serveur SQL.
+ **-q** ou **--quiet** : ne demande pas de confirmation. Utilisez cette option dans les scripts automatisés.

**mobile list [options]**

Cette commande permet de répertorier vos services mobiles.

	~$ azure mobile list
	info:    Executing command mobile list
	data:    Name          State  URL
	data:    ------------  -----  --------------------------------------
	data:    todolist      Ready  https://todolist.azure-mobile.net/
	data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
	info:    mobile list command OK

**mobile show [options] [servicename]**

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

**mobile restart [options] [servicename]**

Cette commande permet de redémarrer une instance de service mobile.

	~$ azure mobile restart todolist
	info:    Executing command mobile restart
	+ Restarting mobile service
	info:    Service was restarted.
	info:    mobile restart command OK

**mobile log [options] [servicename]**

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

Cette commande prend en charge les options supplémentaires suivantes :

+ **-r `<query>`** ou **--query `<query>`** : exécute la requête de journal spécifiée.
+ **-t `<type>`** ou **--type `<type>`** :  filtre les journaux renvoyés par l'entrée `<type>`, qui peut être `information`, `warning`, ou `error`.
+ **-k `<skip>`** ou **--skip `<skip>`** : ignore le nombre de lignes spécifié par `<skip>`.
+ **-p `<top>`** ou **--top `<top>`** : renvoie un nombre spécifique de lignes, spécifié par `<top>`.

<div class="dev-callout"><b>Remarque</b>
   <p>Le paramètre <b>--query</b> a la priorité sur <b>--type</b>, <b>--skip</b>, et <b>--top</b>.</p>
</div>

**mobile recover [options] [unhealthyservicename] [healthyservicename]**

Cette commande permet de récupérer un service mobile défectueux en le déplaçant vers un service mobile sain dans une autre région.

Cette commande prend en charge l'option supplémentaire suivante :

**-q** ou **--quiet** : supprime l'invite de confirmation de la récupération.

**mobile key regenerate [options] [servicename] [type]**

Cette commande permet de régénérer la clé d'application du service mobile.

	~$ azure mobile key regenerate todolist application
	info:    Executing command mobile key regenerate
	info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
	info:    mobile key regenerate command OK

Les types de clé sont `master` et `application`.

<div class="dev-callout"><b>Remarque</b>
   <p>Lorsque vous régénérez une clé, les clients qui utilisent l'ancienne clé risquent de ne pas pouvoir accéder à votre service mobile. Lorsque vous régénérez la clé d'application, vous devez mettre à jour votre application avec la nouvelle valeur de clé. </p>
</div> 

**mobile key set [options] [servicename] [type] [value]**

Cette commande permet de définir la clé du service mobile sur une valeur spécifique.


###<a name="Mobile_Configuration"></a>Commandes pour gérer la configuration d'un service mobile

**mobile config list [options] [servicename]**

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

**mobile config get [options] [servicename] [key]**

Cette commande permet d'obtenir une option de configuration spécifique pour un service mobile, dans ce cas, le schéma dynamique.

	~$ azure mobile config get todolist dynamicSchemaEnabled
	info:    Executing command mobile config get
	data:    dynamicSchemaEnabled true
	info:    mobile config get command OK

**mobile config set [options] [servicename] [key] [value]**

Cette commande permet de définir une option de configuration spécifique pour un service mobile, dans ce cas, le schéma dynamique.

	~$ azure mobile config set todolist dynamicSchemaEnabled false
	info:    Executing command mobile config set
	info:    mobile config set command OK


###<a name="Mobile_Tables"></a>Commandes pour gérer les tables d'un service mobile

**mobile table list [options] [servicename]**

Cette commande permet de répertorier toutes les tables de votre service mobile.

	~$azure mobile table list todolist
	info:    Executing command mobile table list
	data:    Name      Indexes  Rows
	data:    --------  -------  ----
	data:    Channel   1        0
	data:    TodoItem  1        0
	info:    mobile table list command OK

**mobile table show [options] [servicename] [tablename]**

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

**mobile table create [options] [servicename] [tablename]**

Cette commande permet de créer une table.

	~$azure mobile table create todolist Channels
	info:    Executing command mobile table create
	+ Creating table
	info:    mobile table create command OK

Cette commande prend en charge l'option supplémentaire suivante :

+ **-p `<permissions>`** ou **--permissions `<permissions>`** : liste de paires `<operation>`=`<permission>` séparées par des virgules, où `<operation>` est `insert`, `read`, `update` ou `delete` et `<permissions>` est `public`, `application` (default), `user` ou `admin`.

**mobile data read [options] [servicename] [tablename] [query]**

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

Cette commande prend en charge les options supplémentaires suivantes :

+ **-k `<skip>`** ou **--skip `<skip>`** : ignore le nombre de lignes spécifié par `<skip>`.
+ **-t `<top>`** ou **--top `<top>`** : renvoie un nombre spécifique de lignes, spécifié par `<top>`.
+ **-l** ou **--list** : renvoie des données sous forme de liste.

**mobile table update [options] [servicename] [tablename]**

Cette commande permet de modifier les autorisations de suppression d'une table, qui deviennent exclusives aux administrateurs

	~$azure mobile table update todolist Channels -p delete=admin
	info:    Executing command mobile table update
	+ Updating permissions
	info:    Updated permissions
	info:    mobile table update command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-p `<permissions>`** ou **--permissions `<permissions>`** : liste de paires `<operation>`=`<permission>` séparées par des virgules, où `<operation>` est `insert`, `read`, `update` ou `delete` et `<permissions>` est `public`, `application` (default), `user` ou `admin`.
+ **--deleteColumn `<columns>`** : liste séparée par des virgules des colonnes à supprimer, sous la forme `<columns>`.
+ **-q** ou **--quiet** : supprime les colonnes sans demander de confirmation.
+ **--addIndex `<columns>`** : liste séparée par des virgules des colonnes à inclure dans l'index.
+ **--deleteIndex `<columns>`** : liste séparée par des virgules des colonnes à exclure de l'index.

**mobile table delete [options] [servicename] [tablename]**

Cette commande permet de supprimer une table.

	~$azure mobile table delete todolist Channels
	info:    Executing command mobile table delete
	Do you really want to delete the table (yes/no): yes
	+ Deleting table
	info:    mobile table delete command OK

Spécifiez le paramètre -q pour supprimer la table sans confirmation. Cela empêche le blocage des scripts d'automatisation.

**mobile data truncate [options] [servicename] [tablename]**

Cette commande permet de supprimer toutes les lignes de données de la table.

	~$azure mobile data truncate todolist TodoItem
	info:    Executing command mobile data truncate
	info:    There are 7 data rows in the table.
	Do you really want to delete all data from the table? (y/n): y
	info:    Deleted 7 rows.
	info:    mobile data truncate command OK


###<a name="Mobile_Scripts"></a>Commandes pour gérer les scripts

Les commandes de cette section permettent de gérer les scripts de serveur qui appartiennent à un service mobile. Pour plus d'informations, consultez la page [Utilisation des scripts serveur dans Mobile Services](http://azure.microsoft.com/develop/mobile/how-to-guides/work-with-server-scripts/).

**mobile script list [options] [servicename]**

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

**mobile script download [options] [servicename] [scriptname]**

Cette commande permet de télécharger le script d'insertion de la table TodoItem vers un fichier nommé `todoitem.insert.js` dans le sous-dossier `table`.

	~$azure mobile script download todolist table/todoitem.insert.js
	info:    Executing command mobile script download
	info:    Saved script to ./table/todoitem.insert.js
	info:    mobile script download command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-p `<path>`** ou **--path `<path>`** : emplacement où le script doit être enregistré dans le fichier, dans la mesure où le répertoire de travail actuel est celui par défaut.
+ **-f `<file>`** ou **--file `<file>`** : nom du fichier dans lequel le script doit être enregistré.
+ **-o** ou **--override** : permet de remplacer un fichier existant.
+ **-c** ou **--console** : écrit le script dans la console et non dans un fichier.

**mobile script upload [options] [servicename] [scriptname]**

Cette commande permet de télécharger un nouveau script nommé `todoitem.insert.js` à partir du sous-dossier `table`.

	~$azure mobile script upload todolist table/todoitem.insert.js
	info:    Executing command mobile script upload
	info:    mobile script upload command OK

Le nom du fichier doit se composer des noms d'une table et d'une opération et doit être situé dans le sous-dossier table par rapport à l'emplacement où est exécutée la commande. Vous pouvez également utiliser le paramètre **-f `<file>`** ou **--file `<file>`** pour spécifier un nom et un chemin d'accès de fichier différents, contenant le script à inscrire.


**mobile script delete [options] [servicename] [scriptname]**

Cette commande permet de supprimer le script d'insertion existant de la table TodoItem.

	~$azure mobile script delete todolist table/todoitem.insert.js
	info:    Executing command mobile script delete
	info:    mobile script delete command OK

###<a name="Mobile_Jobs"></a>Commandes pour gérer les travaux planifiés

Les commandes de cette section permettent de gérer les travaux planifiés qui appartiennent à un service mobile. Pour plus d'informations, consultez la page [Planifier les travaux](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx).

**mobile job list [options] [servicename]**

Cette commande permet de répertorier les travaux planifiés.

	~$azure mobile job list todolist
	info:    Executing command mobile job list
	info:    Scheduled jobs
	data:    Job name    Script name           Status    Interval     Last run              Next run
	data:    ----------  --------------------  --------  -----------  --------------------  --------------------
	data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
	info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
	info:    mobile job list command OK

**mobile job create [options] [servicename] [jobname]**

Cette commande permet de créer une tâche nommée `getUpdates` dont l'exécution est prévue toutes les heures.

	~$azure mobile job create -i 1 -u hour todolist getUpdates 
	info:    Executing command mobile job create
	info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
	info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
	info:    mobile job create command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-i `<number>`** ou **--interval `<number>`** : intervalle de travail défini sous forme d'entier ; la valeur par défaut est `15`.
+ **-u `<unit>`** ou **--intervalUnit `<unit>`** : unité du paramètre _interval_, qui peut être l'une des valeurs suivantes : 
	+ **minute** (valeur par défaut)
	+ **hour**
	+ **day**
	+ **month** 
	+ **none** (travaux à la demande)
+ **-t `<time>`** **--startTime `<time>`** Heure de début de la première exécution du script, au format ISO ; la valeur par défaut est `now`.

<div class="dev-callout"><b>Remarque</b>
   <p>Les nouveaux travaux créés sont désactivés, car il reste encore à télécharger un script. Utilisez la commande <strong>mobile script upload</strong> pour télécharger un script et la commande <strong>mobile job update</strong> pour activer le travail.</p>
</div> 

**mobile job update [options] [servicename] [jobname]**

La commande suivante active le travail `getUpdates` désactivé.

	~$azure mobile job update -a enabled todolist getUpdates 
	info:    Executing command mobile job update
	info:    mobile job update command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-i `<number>`** ou **--interval `<number>`** : intervalle de travail défini sous forme d'entier ; la valeur par défaut est `15`.
+ **-u `<unit>`** ou **--intervalUnit `<unit>`** : unité du paramètre _interval_, qui peut être l'une des valeurs suivantes : 
	+ **minute** (valeur par défaut)
	+ **hour**
	+ **day**
	+ **month** 
	+ **none** (travaux à la demande)
+ **-t `<time>`** **--startTime `<time>`** Heure de début de la première exécution du script, au format ISO ; la valeur par défaut est `now`.
+ **-a `<status>`** ou **--status `<status>`** : statut du travail, qui peut être `enabled` (activé) ou `disabled` (désactivé).

**mobile job delete [options] [servicename] [jobname]**

Cette commande permet de supprimer le travail planifié getUpdates du serveur TodoList.

	~$azure mobile job delete todolist getUpdates
	info:    Executing command mobile job delete
	info:    mobile job delete command OK

<div class="dev-callout"><b>Remarque</b>
   <p>La suppression d'un travail entraîne celle du script téléchargé.</p>
</div> 

###<a name="Mobile_Scale"></a>Commandes pour mettre à l'échelle un service mobile

Les commandes de cette section permettent de mettre à l'échelle un service mobile. Pour plus d'informations, consultez la page [Augmenter l'extensibilité d'un service mobile](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx).

**mobile scale show [options] [servicename]**

Cette commande permet d'afficher des informations de mise à l'échelle, notamment le mode de calcul et le nombre d'instances actuels.

	~$azure mobile scale show todolist
	info:    Executing command mobile scale show
	data:    webspace WESTUSWEBSPACE
	data:    computeMode Free
	data:    numberOfInstances 1
	info:    mobile scale show command OK

**mobile scale change [options] [servicename]**

Cette commande permet de modifier l'échelle du service mobile, la faisant passer du mode gratuit (free) au mode premium.

	~$azure mobile scale change -c Reserved -i 1 todolist
	info:    Executing command mobile scale change
	+ Rescaling the mobile service
	info:    mobile scale change command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-c `<mode>`** ou **--computeMode `<mode>`** : le mode de calcul doit être `Free` (gratuit) ou `Reserved` (réservé).
+ **-i `<count>`** ou **--numberOfInstances `<count>`** : nombre d'instances utilisées lors de l'exécution en mode réservé.

<div class="dev-callout"><b>Remarque</b>
   <p>Lorsque vous attribuez au mode de calcul la valeur `Reserved` (réservé), tous vos services mobiles d'une même région s'exécutent en mode premium.</p>
</div>  


###Commandes pour activer les fonctionnalités préliminaires pour votre service mobile

**mobile preview list [options] [servicename]**

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

**mobile preview enable [options] [servicename] [featurename]**

Cette commande permet d'activer la fonctionnalité préliminaire spécifiée pour un service mobile. Notez que les fonctionnalités préliminaires qui ont été activées pour un service mobile ne peuvent plus être désactivées.

###Commandes pour gérer vos API de service mobile

**mobile api list [options] [servicename]**

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

**mobile api create [options] [servicename] [apiname]**

Crée une API personnalisée de service mobile.

	~$ azure mobile api create mysite myCustomRetrieveAPI
	info:    Executing command mobile api create
	+ Creating custom API: 'myCustomRetrieveAPI'
	info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
	info:    mobile api create command OK

Cette commande prend en charge l'option supplémentaire suivante :

**-p** ou **--permissions** <permissions> :  liste délimitée par des virgules de paires <method>=<permission>.

**mobile api update [options] [servicename] [apiname]**

Cette commande permet de mettre à jour l'API personnalisée de service mobile spécifiée.

Cette commande prend en charge l'option supplémentaire suivante :

Cette commande prend en charge les options supplémentaires suivantes :

+ **-p** ou **--permissions** <permissions> : liste délimitée par des virgules de paires <method>=<permission>  .
+ **-f** ou **--force** : écrase les modifications personnalisées apportées au fichier de métadonnées des autorisations.

**mobile api delete [options] [servicename] [apiname]**

	~$ azure mobile api delete mysite myCustomRetrieveAPI
	info:    Executing command mobile api delete
	+ Deleting API: 'myCustomRetrieveAPI'
	info:    mobile api delete command OK

Cette commande permet de supprimer l'API personnalisée de service mobile spécifiée.

###Commandes pour gérer vos paramètres d'application mobile

**mobile appsetting list [options] [servicename]**

Cette commande permet d'afficher les paramètres d'application mobile pour le service spécifié.

	~$ azure mobile appsetting list mysite
	info:    Executing command mobile appsetting list
	+ Retrieving app settings
	data:    Name               Value
	data:    -----------------  -----
	data:    enablebetacontent  true
	info:    mobile appsetting list command OK

**mobile appsetting add [options] [servicename] [name] [value]**

Cette commande permet d'ajouter un paramètre d'application personnalisé pour votre service mobile.

	~$ azure mobile appsetting add mysite enablebetacontent true
	info:    Executing command mobile appsetting add
	+ Retrieving app settings
	+ Adding app setting
	info:    mobile appsetting add command OK

**mobile appsetting delete [options] [servicename] [name]**

Cette commande permet de supprimer le paramètre d'application spécifié pour votre service mobile.

	~$ azure mobile appsetting delete mysite enablebetacontent
	info:    Executing command mobile appsetting delete
	+ Retrieving app settings
	+ Removing app setting 'enablebetacontent'
	info:    mobile appsetting delete command OK

**mobile appsetting show [options] [servicename] [name]**

Cette commande permet de supprimer le paramètre d'application spécifié pour votre service mobile.

	~$ azure mobile appsetting show mysite enablebetacontent
	info:    Executing command mobile appsetting show
	+ Retrieving app settings
	info:    enablebetacontent: true
	info:    mobile appsetting show command OK

##<a name="Manage_tool_local_settings"></a>Gestion des paramètres locaux d'outil

Les paramètres locaux correspondent à votre ID d'abonnement et à votre nom de compte de stockage par défaut.

**config list [options]**

Cette commande permet d'afficher les paramètres de configuration.

	~$ azure config list
	info:   Displaying config settings
	data:   Setting                Value                               
	data:   ---------------------  ------------------------------------
	data:   subscription           32-digit-subscription-key
	data:   defaultStorageAccount  name

**config set [options] <name>,<value>**

Cette commande permet de modifier un paramètre de configuration.

	~$ azure config set defaultStorageAccount myname
	info:   Setting 'defaultStorageAccount' to value 'myname'
	info:   Changes saved.

##<a name ="Commands_to_manage_service_bus"></a>Commandes pour gérer Service Bus

Utilisez ces commandes pour gérer votre compte Service Bus.

**sb namespace check [options] <name>**

Vérifiez que l'espace de noms Service Bus est légal et disponible.

**sb namespace create <name> <location>**

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


**sb namespace delete <name>**

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

**sb namespace show <name>**

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

**sb namespace verify <name>**

Vérifie si l'espace de noms est disponible.

##<a name="Commands_to_manage_your_Storage_objects"></a>Commandes pour gérer vos objets de stockage

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

Cette commande prend en charge les options supplémentaires suivantes :

+ **-e** ou **--label** <label> : étiquette du compte de stockage.
+ **-d** ou **--description** <description> :  description du compte de stockage.
+ **-l** ou **--location** <name> : région géographique où créer le compte de stockage.
+ **-a** ou **--affinity-group** <name> : groupe d'affinités auquel associer le compte de stockage.
+ **--geoReplication** :  indique si la géo-réplication est activée.
+ **--disable-geoReplication** : indique si la géo-réplication est désactivée.

**storage account set [options] <name>**

Cette commande permet de mettre à jour le compte de stockage spécifié.

	~$ azure storage account set mybasestorage --geoReplication
	info:    Executing command storage account set
	+ Updating storage account
	info:    storage account set command OK

Cette commande prend en charge les options supplémentaires suivantes :

+ **-e** ou **--label** <label> : étiquette du compte de stockage.
+ **-d** ou **--description** <description> :  description du compte de stockage.
+ **-l** ou **--location** <name> : région géographique où créer le compte de stockage.
+ **--geoReplication** :  indique si la géo-réplication est activée.
+ **--disable-geoReplication** : indique si la géo-réplication est désactivée.

**storage account delete [options] <name>**

Cette commande permet de supprimer le compte de stockage spécifié.

Cette commande prend en charge l'option supplémentaire suivante :

**-q** ou **--quiet** : ne demande pas de confirmation. Utilisez cette option dans les scripts automatisés.

###Commandes pour gérer vos clés de compte de stockage

**storage account keys list [options] <name>**

Cela commande permet de répertorier les clés primaires et secondaires du compte de stockage spécifié.

**storage account keys renew [options] <name>**

###Commandes pour gérer votre conteneur de stockage

**storage container list [options] [prefix]**

Cette commande permet d'afficher la liste des conteneurs de stockage pour un compte de stockage spécifié. Le compte de stockage est spécifié par la chaîne de connexion ou le nom du compte de stockage et la clé du compte.

Cette commande prend en charge les options supplémentaires suivantes :

+ **-p** ou **-prefix** <prefix> : préfixe du nom du conteneur de stockage.
+ **-a** ou **--account-name** <accountName> : nom du compte de stockage.
+ **-k** ou **--account-key** <accountKey> : clé du compte de stockage.
+ **-c** ou **--connection-string** <connectionString> : chaîne de connexion de stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

**storage container show [options] [container]**
**storage container create [options] [container]**

Cette commande permet de créer un conteneur de stockage pour le compte de stockage spécifié. Le compte de stockage est spécifié par la chaîne de connexion ou le nom du compte de stockage et la clé du compte.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** <container> : nom du conteneur de stockage à créer.
+ **-p** ou **-prefix** <prefix> : préfixe du nom du conteneur de stockage.
+ **-a** ou **--account-name** <accountName> : nom du compte de stockage
+ **-k** ou **--account-key** <accountKey> : clé du compte de stockage
+ **-c** ou **--connection-string** <connectionString> : chaîne de connexion de stockage
+ **--debug** : exécute la commande de stockage en mode de débogage.

**storage container delete [options] [container]**

Cette commande permet de supprimer le conteneur de stockage spécifié. Le compte de stockage est spécifié par la chaîne de connexion ou le nom du compte de stockage et la clé du compte.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** <container> : nom du conteneur de stockage à créer.
+ **-p** ou **-prefix** <prefix> : préfixe du nom du conteneur de stockage.
+ **-a** ou **--account-name** <accountName> : nom du compte de stockage.
+ **-k** ou **--account-key** <accountKey> : clé du compte de stockage.
+ **-c** ou **--connection-string** <connectionString> : chaîne de connexion de stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

**storage container set [options] [container]**

Cette commande permet de définir la liste de contrôle d'accès pour le conteneur de stockage. Le compte de stockage est spécifié par la chaîne de connexion ou le nom du compte de stockage et la clé du compte.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** <container> : nom du conteneur de stockage à créer.
+ **-p** ou **-prefix** <prefix> : préfixe du nom du conteneur de stockage.
+ **-a** ou **--account-name** <accountName> : nom du compte de stockage.
+ **-k** ou **--account-key** <accountKey> : clé du compte de stockage.
+ **-c** ou **--connection-string** <connectionString> : chaîne de connexion de stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

###Commandes pour gérer votre objet blob de stockage

**storage blob list [options] [container] [prefix]**

Cette commande renvoie une liste des objets blobs de stockage dans le conteneur de stockage spécifié.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** <container> : nom du conteneur de stockage à créer.
+ **-p** ou **-prefix** <prefix> : préfixe du nom du conteneur de stockage.
+ **-a** ou **--account-name** <accountName> : nom du compte de stockage.
+ **-k** ou **--account-key** <accountKey> : clé du compte de stockage.
+ **-c** ou **--connection-string** <connectionString> : chaîne de connexion de stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

**storage blob show [options] [container] [blob]**

Cette commande permet d'afficher les détails concernant l'objet blob de stockage spécifié.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** <container> : nom du conteneur de stockage à créer.
+ **-p** ou **-prefix** <prefix> : préfixe du nom du conteneur de stockage.
+ **-a** ou **--account-name** <accountName> : nom du compte de stockage.
+ **-k** ou **--account-key** <accountKey> : clé du compte de stockage.
+ **-c** ou **--connection-string** <connectionString> : chaîne de connexion de stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

**storage blob delete [options] [container] [blob]**

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** <container> : nom du conteneur de stockage à créer.
+ **-b** ou **--blob** <blobName> : nom de l'objet blob de stockage à supprimer.
+ **-q** ou **--quiet** : supprime l'objet blob de stockage spécifié sans confirmation.
+ **-a** ou **--account-name** <accountName> : nom du compte de stockage.
+ **-k** ou **--account-key** <accountKey> : clé du compte de stockage.
+ **-c** ou **--connection-string** <connectionString> : chaîne de connexion de stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

**storage blob upload [options] [file] [container] [blob]**

Cette commande permet de mettre à jour le fichier spécifié sur l'objet blob de stockage spécifié.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** <container> : nom du conteneur de stockage à créer.
+ **-b** ou **--blob** <blobName> : nom de l'objet blob de stockage à télécharger.
+ **-t** ou **--blobtype** <blobtype> : type d'objet blob de stockage : Page (de pages) ou Block (de blocs).
+ **-p** ou **--properties** <properties> : propriétés de l'objet blob de stockage pour le fichier téléchargé. Les propriétés sont des paires clé-valeur séparées par un point-virgule (;). Les propriétés disponibles sont contentType, contentEncoding, contentLanguage et cacheControl.
+ **-m** ou **--metadata** <metadata> : métadonnées de l'objet blob de stockage pour le fichier téléchargé. Les métadonnées sont des paires clé-valeur séparées par un point-virgule (;).
+ **--concurrenttaskcount** <concurrenttaskcount> : nombre maximal de demandes de téléchargement simultanées.
+ **-q** ou **--quiet** : écrase l'objet blob de stockage spécifié sans confirmation.
+ **-a** ou **--account-name** <accountName> : nom du compte de stockage.
+ **-k** ou **--account-key** <accountKey> : clé du compte de stockage.
+ **-c** ou **--connection-string** <connectionString> : chaîne de connexion de stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

**storage blob download [options] [container] [blob] [destination]**

Cette commande permet de télécharger l'objet blob de stockage spécifié.

Cette commande prend en charge les options supplémentaires suivantes :

+ **--container** <container> : nom du conteneur de stockage à créer.
+ **-b** ou **--blob** <blobName> : nom de l'objet blob de stockage.
+ **-d** ou **--destination** [destination]: Fichier de destination du téléchargement ou chemin d'accès au répertoire.
+ **-m** ou **--checkmd5** : contrôle md5sum du fichier téléchargé.
+ **--concurrenttaskcount** <concurrenttaskcount>  Nombre maximal de demandes de téléchargement simultanées
+ **-q** ou **--quiet** : écrase le fichier de destination sans confirmation.
+ **-a** ou **--account-name** <accountName> : nom du compte de stockage.
+ **-k** ou **--account-key** <accountKey> : clé du compte de stockage.
+ **-c** ou **--connection-string** <connectionString> : chaîne de connexion de stockage.
+ **--debug** : exécute la commande de stockage en mode de débogage.

##<a name ="Commands_to_manage_sql"></a>Commandes pour gérer les bases de données SQL

Utilisez ces commandes pour gérer vos bases de données SQL Azure.

###Commandes pour gérer les serveurs SQL

Utilisez ces commandes pour gérer vos serveurs SQL.

**sql server create <administratorLogin> <administratorPassword> <location>**

Permet de créer un serveur de base de données.

	~$ azure sql server create test T3stte$t "West US"
	info:    Executing command sql server create
	+ Creating SQL Server
	data:    Server Name i1qwc540ts
	info:    sql server create command OK

**sql server show <name>**

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

**sql server delete <name>**

Supprime un serveur. 

	~$ azure sql server delete i1qwc540ts
	info:    Executing command sql server delete
	Delete server i1qwc540ts? [y/n] y
	+ Removing SQL Server
	info:    sql server delete command OK

###Commandes pour gérer les bases de données SQL

Utilisez ces commandes pour gérer vos bases de données SQL.

**sql db create [options] <serverName> <databaseName> <administratorPassword>**

Crée une instance de base de données.

	~$ azure sql db create fr8aelne00 newdb test
	info:    Executing command sql db create
	Administrator password: ********
	+ Creating SQL Server Database
	info:    sql db create command OK

**sql db show [options] <serverName> <databaseName> <administratorPassword>**

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

**sql db list [options] <serverName> <administratorPassword>**

Répertorie les bases de données.

	~$ azure sql db list fr8aelne00 test
	info:    Executing command sql db list
	Administrator password: ********
	+ Getting SQL server databases
	data:    Name    Edition  Collation                     MaxSizeInGB
	data:    ------  -------  ----------------------------  -----------
	data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
	info:    sql db list command OK

**sql db delete [options] <serverName> <databaseName> <administratorPassword>**

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

**sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>**

Crée une règle de pare-feu pour un serveur SQL.

	~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
	info:    Executing command sql firewallrule create
	+ Creating Firewall Rule
	info:    sql firewallrule create command OK

**sql firewallrule show [options] <serverName> <ruleName>**

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

**sql firewallrule list [options] <serverName>**

Répertorie les règles de pare-feu.

	~$ azure sql firewallrule list fr8aelne00
	info:    Executing command sql firewallrule list
	\data:    Name     Start IP address  End IP address
	data:    -------  ----------------  ---------------
	data:    allowed  131.107.0.0       131.107.255.255
	+
	info:    sql firewallrule list command OK

**sql firewallrule delete [options] <serverName> <ruleName>**

Cette commande permet de supprimer une règle de pare-feu.

	~$ azure sql firewallrule delete fr8aelne00 allowed
	info:    Executing command sql firewallrule delete
	Delete rule allowed? [y/n] y
	+ Removing firewall rule
	info:    sql firewallrule delete command OK

##<a name ="Commands_to_manage_vnet"></a>Commandes pour gérer vos réseaux virtuels

Utilisez ces commandes pour gérer vos réseaux virtuels.

**network vnet create [options] <location>**

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

**network vnet show <name>**

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

**vnet list**

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


**network vnet delete <name>**

Supprime le réseau virtuel spécifié.

	~$ azure network vnet delete opentechvn1
	info:    Executing command network vnet delete
	+ Fetching Network Configuration
	Delete the virtual network opentechvn1 ?  (y/n) y
	+ Deleting the virtual network opentechvn1
	info:    network vnet delete command OK

**network export [file-path]**

Si vous disposez d'une configuration réseau avancée, vous pouvez l'exporter en local. Notez que la configuration réseau exportée comprend les paramètres de serveur DNS, les paramètres de réseau virtuel, les paramètres de site de réseau local et d'autres paramètres.

**network import [file-path]**

Importe une configuration de réseau local.

**network dnsserver register [options] <dnsIP>**

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

**network dnsserver unregister [options] <dnsIP>**

Supprime une entrée de serveur DNS de la configuration réseau.

	~$ azure network dnsserver unregister 77.88.99.11
	info:    Executing command network dnsserver unregister
	+ Fetching Network Configuration
	Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
	+ Deleting the DNS server entry dns-4 ( 77.88.99.11 )
	info:    network dnsserver unregister command OK

<!--HONumber=46--> 
