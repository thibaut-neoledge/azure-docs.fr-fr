Cet article présente un ensemble de pratiques éprouvées pour l’exécution d’une machine virtuelle Linux sur Azure, surtout en matière d’évolutivité, de résilience, de gestion et de sécurité. Azure prend en charge l’exécution de plusieurs distributions Linux populaires, y compris CentOS, Debian, Red Hat Enterprise, Ubuntu et FreeBSD. Pour plus d’informations, consultez [Azure et Linux][azure-linux].

> [AZURE.NOTE] Azure propose deux modèles de déploiement : [Resource Manager][resource-manager-overview] et classique. Cet article utilise Resource Manager, solution recommandée par Microsoft pour les nouveaux déploiements.

Nous déconseillons l’utilisation d’une seule machine virtuelle pour les charges de travail de production car il n’existe aucun contrat SLA concernant la durée de bon fonctionnement des machines virtuelles sur Azure. Pour obtenir le contrat SLA, vous devez déployer plusieurs machines virtuelles dans un groupe à haute disponibilité. Pour plus d’informations, consultez [Exécution de plusieurs machines virtuelles sur Azure][multi-vm].

## Diagramme de l’architecture

L’approvisionnement d’une machine virtuelle dans Azure implique de déplacer davantage d’éléments que la machine virtuelle. Il faut inclure les éléments de calcul, de mise en réseau et de stockage.

![[0]][0]

- **Groupe de ressources.** Un [groupe de ressources][resource-manager-overview] est un conteneur qui héberge des ressources associées. Créez un groupe de ressources pour héberger les ressources de cette machine virtuelle.

- **Machine virtuelle**. Vous pouvez approvisionner une machine virtuelle issue d’une liste d’images publiées ou d’un fichier de disque dur virtuel (VHD) que vous téléchargez sur Azure Blob Storage.

- **Disque du système d’exploitation.** Le disque du système d’exploitation est un disque dur virtuel situé dans [Azure Storage][azure-storage]. Son contenu est conservé même si l’ordinateur hôte tombe en panne. Le disque du système d’exploitation est `/dev/sda1`.

- **Disque temporaire.** La machine virtuelle est créée avec un disque temporaire. Ce disque est stocké sur un lecteur physique de l’ordinateur hôte. Il _n’est pas_ enregistré dans Azure Storage et peut disparaître lors des redémarrages ou d’autres événements du cycle de vie de la machine virtuelle. N’utilisez ce disque que pour des données temporaires, telles que des fichiers de pagination ou d’échange. Le disque temporaire est `/dev/sdb1`. Il est monté sur `/mnt/resource` ou `/mnt`.

- **Disques de données.** Un [disque de données][data-disk] est un disque dur virtuel persistant utilisé pour les données d’application. Les disques de données sont stockés dans Azure Storage, comme le disque du système d’exploitation.

- **Réseau virtuel (VNet) et sous-réseau.** Chaque machine virtuelle dans Azure est déployée dans un réseau virtuel (VNet) divisé en sous-réseaux.

- **Adresse IP publique.** Une adresse IP publique est nécessaire pour communiquer avec la machine virtuelle, par exemple avec le protocole ssh.

- **Interfaces réseau (NIC)**. La carte d’interface réseau permet à la machine virtuelle de communiquer avec le réseau virtuel.

- **Groupe de sécurité réseau (NSG)**. Le [NSG][nsg] sert à autoriser ou refuser le trafic réseau destiné au sous-réseau. Vous pouvez associer un NSG à une carte réseau individuelle ou à un sous-réseau. Si vous l’associez à un sous-réseau, les règles du NSG s’appliquent à toutes les machines virtuelles de ce sous-réseau.
 
- **Diagnostics.** La journalisation des diagnostics est essentielle à la gestion et au dépannage de la machine virtuelle.

## Recommandations

### Recommandations pour les machines virtuelles

- Nous vous recommandons d’utiliser les gammes DS et GS, sauf si vous disposez d’une charge de travail spécialisée tels qu’un système de calcul hautes performances. Pour en savoir plus, voir [Tailles de machines virtuelles][virtual-machine-sizes]. Lorsque vous déplacez une charge de travail vers Azure, commencez par choisir la taille de machine virtuelle qui correspond le mieux à vos serveurs locaux. Mesurez ensuite les performances de votre charge de travail réelle en termes de processeur, mémoire et E/S par seconde du disque, puis ajustez la taille si nécessaire. En outre, si vous avez besoin de plusieurs cartes réseau, tenez compte de la limite de la carte réseau pour chaque taille.

- Lorsque vous approvisionnez la machine virtuelle et d’autres ressources, vous devez spécifier un emplacement. En général, choisissez un emplacement le plus proche possible de vos utilisateurs internes ou de vos clients. Sachez toutefois que certaines tailles de machine virtuelle ne sont pas disponibles dans tous les emplacements. Pour en savoir plus, voir [Services par région][services-by-region]. Pour répertorier les tailles de machines virtuelles disponibles dans un emplacement donné, exécutez la commande CLI Azure suivante :

    ```
    azure vm sizes --location <location>
    ```

- Pour en savoir plus sur le choix d’une image de machine virtuelle publiée, voir [Rechercher par navigation et sélectionner des images de machines virtuelles Azure avec Windows PowerShell et l’interface de ligne de commande Azure][select-vm-image].

### Recommandations pour le disque et le stockage

- Pour bénéficier de meilleures performances d’E/S du disque, nous vous recommandons [Premium Storage][premium-storage], qui stocke les données sur des disques SSD. Le coût est basé sur la taille du disque approvisionné. Le nombre d’E/S par seconde et le débit (c’est-à-dire le taux de transfert des données) dépendent également de la taille du disque. Lorsque vous approvisionnez un disque, vous devez donc tenir compte des trois facteurs : capacité, E/S par seconde et débit.

- Ajoutez un ou plusieurs disques de données. Lorsque vous créez un disque dur virtuel, il n’est pas formaté. Connectez-vous à la machine virtuelle pour formater le disque. Les disques de données s’afficheront sous la forme `/dev/sdc`, `/dev/sdd`, et ainsi de suite. Vous pouvez exécuter `lsblk` pour répertorier les périphériques de bloc, y compris les disques. Pour utiliser un disque de données, créez une partition et un système de fichiers puis montez le disque. Par exemple :

    ```bat
    # Create a partition.
    sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     
    
    # Create a file system.
    sudo mkfs -t ext3 /dev/sdc1
    
    # Mount the drive.
    sudo mkdir /data1
    sudo mount /dev/sdc1 /data1
    ```

- Si vous avez un grand nombre de disques de données, n’oubliez pas les limites d’E/S du compte de stockage. Pour en savoir plus, voir [Limites du nombre de disques de machine virtuelle][vm-disk-limits].

- Lorsque vous ajoutez un disque de données, un numéro d’unité logique (LUN) lui est attribué. Vous pouvez également spécifier l’ID LUN &mdash; par exemple si vous remplacez un disque et souhaitez conserver le même ID LUN, ou si votre application nécessite un ID LUN spécifique. Toutefois, n’oubliez pas que les ID LUN doivent être uniques pour chaque disque.

- Vous pouvez modifier le planificateur d’E/S afin d’optimiser les performances des disques SSD (utilisés par Premium Storage). Il est généralement recommandé d’utiliser le planificateur NOOP pour les disques SSD, mais vous devriez utiliser un outil tel que [iostat] pour surveiller les performances d’E/S du disque pour votre charge de travail spécifique.

- Pour des performances optimales, créez un compte de stockage distinct destiné à héberger les journaux de diagnostic. Un compte de stockage localement redondant (LRS) standard suffit pour les journaux de diagnostic.

### Recommandations pour le réseau

- Cette adresse IP publique peut être dynamique ou statique. Par défaut, elle est dynamique.

    - Utilisez une [adresse IP statique][static-ip] si vous avez besoin d’une adresse IP fixe et non modifiable, par exemple pour créer un enregistrement A dans le DNS ou insérer l’adresse IP dans la liste blanche.

    - Vous pouvez également créer un nom de domaine complet (FQDN) pour l’adresse IP. Vous pouvez inscrire un [enregistrement CNAME][cname-record] dans le DNS qui pointe vers FQDN. Pour en savoir plus, voir [Créer un nom de domaine complet dans le portail Azure][fqdn].

- Tous les NSG contiennent un ensemble de [règles par défaut][nsg-default-rules], y compris une règle qui bloque tout le trafic Internet entrant. Les règles par défaut ne peuvent pas être supprimées, mais d’autres règles peuvent les remplacer. Pour autoriser le trafic Internet, créez des règles qui autorisent le trafic entrant vers des ports spécifiques &mdash; par exemple, le port 80 pour HTTP.

- Pour activer le protocole ssh, ajoutez au groupe de sécurité réseau une règle qui autorise le trafic entrant sur le port TCP 22.

## Considérations relatives à l’extensibilité

- Vous pouvez ajuster une machine virtuelle en [modifiant sa taille][vm-resize].

- Pour évoluer horizontalement, placez deux ou plusieurs machines virtuelles dans un groupe à haute disponibilité derrière un équilibreur de charge. Pour en savoir plus, voir [Exécution de plusieurs machines virtuelles sur Azure][multi-vm].

## Considérations relatives à la disponibilité

- Comme indiqué ci-dessus, il n’existe aucun contrat SLA pour une machine virtuelle unique. Pour obtenir le contrat SLA, vous devez déployer plusieurs machines virtuelles dans un groupe à haute disponibilité.

- Votre machine virtuelle peut être affectée par la [maintenance planifiée][planned-maintenance] ou la [maintenance non planifiée][manage-vm-availability]. Vous pouvez utiliser les [journaux de redémarrage de machine virtuelle][reboot-logs] pour déterminer si un redémarrage de la machine virtuelle a été provoqué par une maintenance planifiée.

- Les disques durs virtuels sont gérés par [Azure Storage][azure-storage], qui est répliqué à des fins de durabilité et de disponibilité.

- Pour vous protéger contre la perte accidentelle de données pendant les opérations normales (par exemple, en cas d’erreur d’un utilisateur), vous devez également implémenter des sauvegardes ponctuelles, à l’aide d’[instantanés d’objet blob][blob-snapshot] ou d’un autre outil.

## Considérations relatives à la facilité de gestion

- **Groupes de ressources.** Placez les ressources fortement couplées qui partagent le même cycle de vie dans un même [groupe de ressources][resource-manager-overview]. Les groupes de ressources vous permettent de déployer et de surveiller les ressources en tant que groupe et de répercuter les coûts de facturation par groupe de ressources. Vous pouvez également supprimer des ressources dans un ensemble, ce qui est très utile pour les déploiements de test. Nommez les ressources de façon explicite. Cela permet de localiser plus facilement une ressource spécifique et de mieux comprendre son rôle. Voir [Conventions d’affectation de noms recommandées pour les ressources Azure][naming conventions].

- **ssh**. Avant de créer une machine virtuelle Linux, générez une paire de clés publique-privée RSA 2048 bits. Utilisez le fichier de clé publique lorsque vous créez la machine virtuelle. Pour en savoir plus, voir [Utilisation de SSH avec Linux et Mac sur Azure][ssh-linux].

- **VM diagnostics.** Permet la surveillance et le diagnostic, avec notamment des indicateurs d’intégrité de base, des journaux d’infrastructure de diagnostic et des [diagnostics de démarrage][boot-diagnostics]. Les diagnostics de démarrage peuvent vous aider à identifier le problème de démarrage si votre machine virtuelle refuse de démarrer. Pour en savoir plus, voir [Activation de la surveillance et des diagnostics][enable-monitoring].

    La commande CLI suivante active les diagnostics :

    ```text
    azure vm enable-diag <resource-group> <vm-name>
    ```

- **Arrêt d’une machine virtuelle.** Azure fait une distinction entre les états « Arrêté » et « Désalloué ». Vous êtes facturé quand l’état de la machine virtuelle est arrêté, mais pas lorsque la machine virtuelle est désallouée.

    Utilisez la commande CLI suivante pour désallouer une machine virtuelle :

    ```text
    azure vm deallocate <resource-group> <vm-name>
    ```

    Le bouton **Arrêter** du portail Azure désalloue aussi la machine virtuelle. Toutefois, si vous arrêtez la machine virtuelle via le système d’exploitation pendant que vous êtes connecté, la machine virtuelle est arrêtée mais _non_ désallouée. Vous serez donc toujours facturé.

- **Suppression d’une machine virtuelle.** La suppression d’une machine virtuelle n’entraîne pas celle des disques durs virtuels. Vous pouvez donc supprimer la machine virtuelle, sans risque de perdre des données. Toutefois, vous serez toujours facturé pour le stockage. Pour supprimer le disque dur virtuel, supprimez le fichier de [Blob Storage][blob-storage].

  Pour éviter toute suppression accidentelle, utilisez un [verrou de ressource][resource-lock] pour verrouiller l’ensemble du groupe de ressources ou des ressources individuelles, par exemple la machine virtuelle.

## Sécurité

- Automatisez les mises à jour du système d’exploitation à l’aide de l’extension de machine virtuelle [OSPatching]. Installez cette extension lorsque vous configurez la machine virtuelle. Vous pouvez spécifier la fréquence d’installation des correctifs et si un redémarrage est nécessaire après la mise à jour corrective.

- Utilisez le [contrôle d’accès en fonction du rôle][rbac] (RBAC) pour contrôler l’accès aux ressources Azure que vous déployez. Le contrôle RBAC vous permet d’affecter des rôles d’autorisation aux membres de votre équipe DevOps. Par exemple, le rôle Lecteur permet d’afficher des ressources Azure mais pas de les créer, gérer ou supprimer. Certains rôles sont spécifiques à des types de ressources Azure particuliers. Par exemple, le rôle Contributeur de machine virtuelle peut redémarrer ou désallouer une machine virtuelle, réinitialiser le mot de passe administrateur, créer une machine virtuelle, etc. D’autres [rôles RBAC intégrés][rbac-roles] peuvent être utiles dans cette architecture de référence, notamment [Utilisateur DevTest Lab][rbac-devtest] et [Collaborateur de réseau][rbac-network]. Un utilisateur peut être affecté à plusieurs rôles, et vous pouvez créer des rôles personnalisés pour d’autres autorisations plus affinées.

    > [AZURE.NOTE] Le contrôle RBAC ne limite pas les actions qu’un utilisateur connecté peut effectuer sur une machine virtuelle. Ces autorisations dépendent du type de compte installé sur le système d’exploitation invité.

- Utilisez les [journaux d’audit][audit-logs] pour voir les actions d’approvisionnement et d’autres événements concernant la machine virtuelle.

- Utilisez [Azure Disk Encryption][disk-encryption] si vous devez chiffrer les disques du système d’exploitation et de données.

## Composants de la solution

<!-- TO BE UPDATED WHEN THE NEW TEMPLATES ARE AVAILABLE -->

Le script de commandes bash suivant exécute des commandes [Azure CLI][azure-cli] pour déployer une seule instance de machine virtuelle ainsi que les ressources de stockage et réseau associées, comme indiqué dans le précédent schéma.

Le script utilise les conventions d’affectation de noms décrites dans la section [Conventions d’affectation de noms recommandées pour les ressources Azure][naming conventions].

Pour exécuter le script :

1. Générez une clé d’authentification RSA 2048 bits.

        ssh-keygen -t rsa -b 2048

2. Transmettez au script votre ID d’abonnement Azure et le nom du fichier de clé publique en tant que paramètres.

        ./azurecli-single-vm-sample.sh <subscription ID> ~/.ssh/id_rsa.pub

3. Lorsque le script est terminé, connectez-vous à la machine virtuelle à l’aide de ssh. Utilisez la clé privée pour l’authentification.

        ssh testuser@<app>-vm1.<location>.cloudapp.azure.com -i ~/.ssh/id_rsa

    où `<app>` est la valeur de la variable de script `APP_NAME`, et `<location>` est la valeur de la variable `LOCATION`.

```bat
#!/bin/bash

############################################################################
#script for generating infrastructure for single VM running linux          #
# of user choice. It creates azure resource group, storage account for VM  #
# vnet, subnets for VM, and NSG rule                                       #
# tags for main variables used                                             #
# ScriptCommandParameters                                                  #
# ScriptVars                                                               #
############################################################################

############################################################################
# User defined functions for single VM script                              #
# errhandle : handles errors via trap if any exception happens             #
# in the cli execution or if the user interrupts with CTRL+C               #
# allowing for fast interruption                                           #
############################################################################

# error handling or interruption via ctrl-c.
# line number and error code of executed command is passed to errhandle function

trap 'errhandle $LINENO $?' SIGINT ERR

errhandle()
{
  echo "Error or Interruption at line ${1} exit code ${2} "
  exit ${2}
}

###############################################################################
############################## End of user defined functions ##################
###############################################################################

# 2 paramaters are expected
# public key file needs to be generated using ssh-keygen

if [ $# -ne 2  ]
then
	echo  "Usage:  ${0}  subscription-id public-ssh-key-file"
	exit
fi

if [ ! -f $2  ]
then
	echo "Public Key file ${2} does not exist. please generate it"
	echo "ssh-keygen -t rsa -b 2048"
	exit
fi

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
# ScriptCommandParameters
SUBSCRIPTION=$1
PUBLICKEYFILE=$2

# ScriptVars
LOCATION=eastus2
APP_NAME=app1
ENVIRONMENT=dev
USERNAME=testuser
VM_NAME="${APP_NAME}-vm1"
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
IP_NAME="${APP_NAME}-pip"
NIC_NAME="${VM_NAME}-1nic"
NSG_NAME="${APP_NAME}-nsg"
SUBNET_NAME="${APP_NAME}-subnet"
VNET_NAME="${APP_NAME}-vnet"
VHD_STORAGE="${VM_NAME//-}st1"
DIAGNOSTICS_STORAGE="${VM_NAME//-}diag"

# Use the following command to get the list of URNs for RHEL, UBUNTU, and OPENSUSE:
# RHEL
# azure vm image list $LOCATION  redhat RHEL 7.2
# UBUNTU
# azure vm image list $LOCATION canonical ubuntuserver 14.04.3-LTS
# SUSE
# azure vm image $LOCATION  suse opensuse 13.2

LINUX_BASE_IMAGE=redhat:rhel:7.2:7.2.20160302

# For a list of VM sizes see: 
#   https://azure.microsoft.com/documentation/articles/virtual-machines-size-specs/
# To see the VM sizes available in a region:
# 	azure vm sizes --location <location>
VM_SIZE=Standard_DS1

# Set up the postfix variables attached to most CLI commands

POSTFIX="--resource-group ${RESOURCE_GROUP} --subscription ${SUBSCRIPTION}"

azure config mode arm

#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#Create resources

#Create the enclosing resource group
azure group create --name $RESOURCE_GROUP --location $LOCATION --subscription $SUBSCRIPTION

# Create the VNet
azure network vnet create --address-prefixes 172.17.0.0/16 --name $VNET_NAME \
--location $LOCATION $POSTFIX

#Create the network security group
azure network nsg create --name $NSG_NAME --location $LOCATION $POSTFIX

#Create the subnet
azure network vnet subnet create --vnet-name $VNET_NAME --address-prefix  "172.17.0.0/24" \
--name $SUBNET_NAME --network-security-group-name $NSG_NAME $POSTFIX

#Create the public IP address (dynamic)
azure network public-ip create --name $IP_NAME --domain-name-label $VM_NAME \
--location $LOCATION $POSTFIX

#Create the NIC
azure network nic create --public-ip-name $IP_NAME --subnet-name $SUBNET_NAME \
--subnet-vnet-name $VNET_NAME --name $NIC_NAME --location $LOCATION $POSTFIX

#Create the storage account for the OS VHD
azure storage account create --type PLRS --location $LOCATION $POSTFIX $VHD_STORAGE

#Create the storage account for diagnostics logs
azure storage account create --type LRS --location $LOCATION $POSTFIX $DIAGNOSTICS_STORAGE

#Create the VM
azure vm create --name $VM_NAME --os-type Linux --image-urn  $LINUX_BASE_IMAGE \
--vm-size $VM_SIZE --vnet-subnet-name $SUBNET_NAME --vnet-name $VNET_NAME \
--nic-name $NIC_NAME --storage-account-name $VHD_STORAGE \
--os-disk-vhd "${VM_NAME}-osdisk.vhd" --admin-username $USERNAME \
--ssh-publickey-file $PUBLICKEYFILE --boot-diagnostics-storage-uri \
"https://${DIAGNOSTICS_STORAGE}.blob.core.windows.net/" --location $LOCATION $POSTFIX

#Attach a data disk
azure vm disk attach-new --vm-name $VM_NAME --size-in-gb 128 --vhd-name data1.vhd \
--storage-account-name $VHD_STORAGE $POSTFIX

#Allow SSH
azure network nsg rule create --nsg-name $NSG_NAME --direction Inbound --protocol Tcp \
--destination-port-range 22  --source-port-range "*"  --priority 100 --access Allow \
SSHAllow $POSTFIX

#Install patching extension
PATCH_CONFIG='{"rebootAfterPatch":"RebootIfNeed","startTime":"3:00","dayOfWeek":"Sunday","category":"ImportantAndRecommended"}'
azure vm extension set --name OSPatchingForLinux --publisher-name Microsoft.OSTCExtensions \
--public-config $PATCH_CONFIG --vm-name $VM_NAME --version 2.0 $POSTFIX
```

## Étapes suivantes

Afin d’appliquer le [contrat de niveau de service pour machines virtuelles][vm-sla], vous devez déployer au moins deux instances dans un groupe à haute disponibilité. Pour en savoir plus, voir [Exécution de plusieurs machines virtuelles sur Azure][multi-vm].

<!-- links -->

[arm-templates]: ../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md
[audit-logs]: https://azure.microsoft.com/fr-FR/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-linux]: ../articles/virtual-machines/virtual-machines-linux-azure-overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/fr-FR/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md
[disk-encryption]: ../articles/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-linux-portal-create-fqdn.md
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/fr-FR/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/fr-FR/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/fr-FR/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[select-vm-image]: ../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/fr-FR/regions/#services
[ssh-linux]: ../articles/virtual-machines/virtual-machines-linux-ssh-from-linux.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-linux-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/fr-FR/support/legal/sla/virtual-machines/v1_0/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Architecture générale d’une machine virtuelle Azure"

<!---HONumber=AcomDC_0720_2016-->