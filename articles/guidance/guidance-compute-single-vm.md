<properties
   pageTitle="Exécution d’une machine virtuelle (Windows) | Plan | Microsoft Azure"
   description="Comment exécuter une seule machine virtuelle sur Azure, en privilégiant l’évolutivité, la résilience, la gestion et la sécurité."
   services=""
   documentationCenter="na"
   authors="mikewasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="mikewasson"/>

# Exécution d’une seule machine virtuelle Windows sur Azure

Cet article présente un ensemble de pratiques éprouvées pour l’exécution d’une seule machine virtuelle Windows sur Azure, surtout en matière d’évolutivité, de résilience, de gestion et de sécurité.

> [AZURE.WARNING] Il n’existe aucun contrat SLA concernant la durée de bon fonctionnement des machines virtuelles sur Azure. Utilisez cette configuration pour le développement et de test, mais pas comme un déploiement de production.

Azure propose deux modèles de déploiement : [Resource Manager][resource-manager-overview] et classique. Cet article utilise Resource Manager, solution recommandée par Microsoft pour les nouveaux déploiements. Il existe plusieurs façons d’utiliser Resource Manager, notamment le [portail Azure][azure-portal], [Azure PowerShell][azure-powershell], les commandes [Azure CLI][azure-cli] ou les [modèles de Resource Manager][arm-templates]. Cet article inclut un exemple d’utilisation de l’interface Azure CLI.

![IaaS : machine virtuelle unique](media/guidance-compute-single-vm.png)

L’approvisionnement d’une machine virtuelle unique dans Azure implique de déplacer davantage d’éléments que le cœur de la machine virtuelle. Il faut inclure les éléments de calcul, de mise en réseau et de stockage.

- **Groupe de ressources**. Créez un [groupe de ressources][resource-manager-overview] pour héberger les ressources de cette machine virtuelle. Un _groupe de ressources_ est un conteneur qui héberge les ressources associées.

- **MV**. Vous pouvez approvisionner une machine virtuelle issue d’une liste d’images publiées ou d’un fichier de disque dur virtuel (VHD) que vous téléchargez sur Azure Blob Storage.

- **Disque du système d’exploitation.** Le disque du système d’exploitation est un disque dur virtuel situé dans [Azure Storage][azure-storage]. Son contenu est conservé même si l’ordinateur hôte tombe en panne.

- **Disque temporaire.** La machine virtuelle est créée avec un disque temporaire (le lecteur `D:` sous Windows). Ce disque est stocké sur un lecteur physique de l’ordinateur hôte. Il _n’est pas_ enregistré dans Azure Storage et peut disparaître lors des redémarrages ou d’autres événements de cycle de vie de la machine virtuelle. N’utilisez ce disque que pour des données temporaires, telles que des fichiers de pagination ou d’échange.

- **Disques de données.** Un [disque de données][data-disk] est un disque dur virtuel persistant utilisé pour les données d’application. Les disques de données sont stockés dans Azure Storage, comme le disque du système d’exploitation.

- **Réseau virtuel (VNet) et sous-réseau.** Chaque machine virtuelle dans Azure est déployée dans un réseau virtuel (VNet) divisé en sous-réseaux.

- **Adresse IP publique.** Une adresse IP publique est nécessaire pour communiquer avec la machine virtuelle, par exemple avec le protocole RDP.

- **Groupe de sécurité réseau (NSG)**. Le [NSG][nsg] sert à autoriser ou refuser le trafic réseau destiné à la machine virtuelle. Par défaut, les règles du NSG interdisent tout le trafic Internet entrant.

- **Carte d’interface réseau (NIC)**. La carte d’interface réseau permet à la machine virtuelle de communiquer avec le réseau virtuel.

- **Diagnostics.** La journalisation des diagnostics est essentielle à la gestion et au dépannage de la machine virtuelle.

## Recommandations pour les machines virtuelles

- Lorsque vous déplacez une charge de travail vers Azure, choisissez la [taille de machine virtuelle][virtual-machine-sizes] qui correspond le mieux à vos serveurs locaux. Nous recommandons les séries DS et GS, qui peuvent utiliser Premium Storage pour les charges de travail consommatrices d’E/S.

    - Si votre charge de travail ne nécessite pas un accès au disque très performant et à faible latence, privilégiez les tailles Standard de machine virtuelle, telles que la série A ou D.

- Lorsque vous approvisionnez la machine virtuelle et d’autres ressources, vous devez spécifier un emplacement. En général, choisissez un emplacement le plus proche possible de vos utilisateurs internes ou de vos clients. Sachez toutefois que certaines références (SKU) de machine virtuelle ne sont pas disponibles dans tous les emplacements. Pour plus d’informations, consultez la page [Services par région][services-by-region].

- Pour plus d’informations sur le choix d’une image de machine virtuelle publiée, consultez [Rechercher par navigation et sélectionner des images de machines virtuelles Azure avec Windows PowerShell et l’interface de ligne de commande Azure][select-vm-image].

## Recommandations pour le disque et le stockage

Nous vous recommandons [Premium Storage][premium-storage] pour des performances d’E/S de disque optimales. Toutefois, notez que Premium Storage requiert des machines virtuelles DS ou GS.

- Pour Premium Storage, le coût est basé sur la taille du disque approvisionné. Le nombre d’E/S par seconde et le débit (c’est-à-dire le taux de transfert des données) dépendent également de la taille du disque. Lorsque vous approvisionnez un disque, vous devez donc tenir compte des trois facteurs : capacité, E/S par seconde et débit.

- Pour le stockage Standard, le coût est basé sur la quantité de données écrites sur le disque. Il est donc conseillé de configurer la taille maximale (1 023 Go). Toutefois, veillez à utiliser le formatage rapide pour les disques. Un formatage complet écrit des zéros sur le disque, utilisant la capacité réelle de stockage. Consultez la page [Tarification d’Azure Storage][storage-price].

- Si vous sélectionnez l’option Standard, nous vous recommandons le stockage géo-redondant (GRS), car il est rémanent même en cas de panne régionale totale ou d’incident empêchant la récupération depuis la région primaire.

- Ajoutez un ou plusieurs disques de données. Lorsque vous créez un disque dur virtuel, il n’est pas formaté. Connectez-vous à la machine virtuelle pour formater le disque.

- Si vous avez un grand nombre de disques de données, n’oubliez pas les limites d’E/S du compte de stockage. Pour plus d’informations, consultez la page [Limites du nombre de disques de machine virtuelle][vm-disk-limits].

- Pour des performances optimales, créez un compte de stockage distinct destiné à héberger les journaux de diagnostic. Un compte de stockage localement redondant (LRS) standard suffit pour les journaux de diagnostic.

## Recommandations pour le réseau

- Pour une seule machine virtuelle, créez un réseau virtuel avec un sous-réseau. Créez également un groupe de sécurité réseau et une adresse IP publique.

- Cette adresse IP publique peut être dynamique ou statique. Par défaut, elle est dynamique.

    - Utilisez une [adresse IP statique][static-ip] si vous avez besoin d’une adresse IP non modifiable, par exemple pour créer un enregistrement A dans le DNS ou insérer l’adresse IP dans la liste blanche.

    - Par défaut, l’adresse IP ne dispose pas d’un nom de domaine complet (FQDN). Pour plus d’informations, consultez la page [Créer un nom de domaine complet dans le portail Azure][fqdn].

- Affectez une carte d’interface réseau et associez-la à l’adresse IP, au sous-réseau et au groupe de sécurité réseau.

- Les règles du groupe de sécurité réseau par défaut n’autorisent pas le protocole RDP. Pour activer le protocole RDP, ajoutez au groupe de sécurité réseau une règle qui autorise le trafic entrant sur le port TCP 3389.

## Extensibilité

Vous pouvez ajuster une machine virtuelle en modifiant sa taille. La commande Azure CLI suivante redimensionne une machine virtuelle :

```text
azure vm set -g <<resource-group>> --vm-size <<new-vm-size>
    --boot-diagnostics-storage-uri <<storage-account-uri>> <<vm-name>>
```

Le redimensionnement de la machine virtuelle déclenche un redémarrage du système et remappe les disques du système d’exploitation et des données après le redémarrage. Tout le contenu du disque temporaire est perdu. L’option `--boot-diagnostics-storage-uri` active la consignation des erreurs de démarrage dans [Diagnostics de démarrage][boot-diagnostics].

Il se peut que vous ne puissiez pas modifier une famille de références (SKU) en une autre (par exemple, une série A en une série G). Pour obtenir la liste des tailles disponibles pour une machine virtuelle, utilisez la commande CLI suivante :

```text
azure vm sizes -g <<resource-group>> --vm-name <<vm-name>>
```

Pour configurer une taille non répertoriée, vous devez supprimer l’instance de machine virtuelle et en créer une autre. La suppression d’une machine virtuelle ne supprime pas les disques durs virtuels.

## Availability

- Il n’existe aucun contrat SLA pour la disponibilité des machines virtuelles. Pour obtenir un contrat SLA de disponibilité, vous devez déployer plusieurs machines virtuelles dans un groupe à haute disponibilité.

- Votre machine virtuelle peut être affectée par la [maintenance planifiée][planned-maintenance] ou la [maintenance non planifiée][manage-vm-availability]. Vous pouvez utiliser les [journaux de redémarrage de machine virtuelle][reboot-logs] pour déterminer si un redémarrage de la machine virtuelle a été provoqué par une maintenance planifiée.

- Ne placez pas une seule machine virtuelle dans un groupe à haute disponibilité. L’intégration d’une seule machine virtuelle dans un groupe à haute disponibilité indique à Azure de la traiter comme une partie d’un ensemble d’instances, et vous ne recevrez aucun avertissement ou notification préalable sur les redémarrages dus à une maintenance planifiée.

- Les disques durs virtuels sont gérés par [Azure Storage][azure-storage], qui est répliqué à des fins de durabilité et de disponibilité.

- Pour vous protéger contre la perte accidentelle de données pendant les opérations normales (par exemple, en cas d’une erreur d’un utilisateur), vous devez également effectuer des sauvegardes ponctuelles, à l’aide d’[instantanés d’objet blob][blob-snapshot] ou d’un autre outil.

## Facilité de gestion

- Exécutez la commande CLI suivante pour activer les diagnostics de la machine virtuelle :
    
    ```text
    azure vm enable-diag <<resource-group>> <<vm-name>>
    ```
    
    Cette commande active les indicateurs d’intégrité de base, les journaux d’infrastructure de diagnostic et les diagnostics de démarrage. Pour plus d’informations, consultez la page [Activation de la surveillance et des diagnostics][enable-monitoring].

- Utilisez l’extension [Azure Log Collector][log-collector] pour collecter les journaux et les télécharger sur Azure Storage.

- Azure fait une distinction entre les états « Arrêté » et « Désalloué ». Vous êtes facturé quand l’état de la machine virtuelle est arrêté, mais pas lorsque la machine virtuelle est désallouée. (Consultez le [Forum aux questions sur les machines virtuelles Azure créées avec le modèle de déploiement classique][vm-faq].)

    Utilisez la commande CLI suivante pour désallouer une machine virtuelle :
    
    ```text
    azure vm deallocate <<resource-group>> <<vm-name>>
    ```
    
    Remarque : le bouton **Arrêter** du portail Azure désalloue aussi la machine virtuelle. Toutefois, si vous arrêtez la machine virtuelle dans Windows (via le protocole RDP), elle est arrêtée, mais _pas_ désallouée. Vous serez donc toujours facturé.

- La suppression d’une machine virtuelle n’entraîne pas celle des disques durs virtuels. Vous pouvez donc supprimer la machine virtuelle, sans risque de perdre des données. Toutefois, vous serez toujours facturé pour le stockage. Pour supprimer le disque dur virtuel, supprimez le fichier de [Blob Storage][blog-storage].

- Pour redimensionner le disque du système d’exploitation, téléchargez le fichier VHD et utilisez un outil du type [Resize-VHD][Resize-VHD] pour redimensionner le disque dur virtuel. Téléchargez le disque dur virtuel redimensionné sur Blob Storage, puis supprimez l’instance de machine virtuelle et approvisionnez une nouvelle instance utilisant le disque dur virtuel redimensionné.


## Sécurité

- Le [Centre de sécurité Azure][security-center] vous offre un aperçu global de l’état de sécurité de toutes vos ressources Azure. Il surveille les problèmes potentiels de sécurité tels que les mises à jour système, les logiciels malveillants ainsi que les ACL de point de terminaison, et fournit une image complète de la sécurité de votre déploiement. **Remarque :** au moment de la rédaction de ce document, le Centre de sécurité est toujours en version préliminaire.

- Utilisez le [contrôle d’accès basé sur les rôles][rbac] (RBAC) pour identifier les membres de votre équipe de développement autorisés à gérer les ressources Azure (MV, réseau, etc.) que vous déployez.

- Envisagez l’installation d’[extensions de sécurité][security-extensions].

- Utilisez [Azure Disk Encryption][disk-encryption] pour chiffrer les disques du système d’exploitation et de données. **Remarque :** au moment de la rédaction de ce document, Azure Disk Encryption est toujours en version préliminaire.

## Résolution de problèmes

- Pour réinitialiser le mot de passe de l’administrateur local, exécutez la commande Azure CLI `vm reset-access`.
    
    ```text
    azure vm reset-access -u <<user>> -p <<new-password>> <<resource-group>> <<vm-name>>
    ```
    
- Si votre machine virtuelle refuse de démarrer, utilisez [Diagnostics de démarrage][boot-diagnostics] pour diagnostiquer le problème.

- Examinez les [journaux d’audit][audit-logs] pour voir les actions d’approvisionnement et d’autres événements concernant la machine virtuelle.

## Exécution de commandes Azure CLI (exemple)

Le script de commandes Windows suivant exécute des commandes [Azure CLI][azure-cli] pour déployer une seule instance de machine virtuelle ainsi que les ressources de stockage et le réseau associés, comme indiqué dans le précédent schéma.

```bat
ECHO OFF
SETLOCAL

IF "%~1"=="" (
    ECHO Usage: %0 subscription-id
    EXIT /B
    )

:: Set up variables to build out the naming conventions for deploying
:: the cluster

SET LOCATION=eastus2
SET APP_NAME=app1
SET ENVIRONMENT=dev
SET USERNAME=testuser
SET PASSWORD=AweS0me@PW

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1

:: Set up the names of things using recommended conventions
SET RESOURCE_GROUP=%APP_NAME%-%ENVIRONMENT%-rg
SET VM_NAME=%APP_NAME%-vm0

SET IP_NAME=%APP_NAME%-pip
SET NIC_NAME=%VM_NAME%-0nic
SET NSG_NAME=%APP_NAME%-nsg
SET SUBNET_NAME=%APP_NAME%-subnet
SET VNET_NAME=%APP_NAME%-vnet
SET VHD_STORAGE=%VM_NAME:-=%st0
SET DIAGNOSTICS_STORAGE=%VM_NAME:-=%diag

:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see...
SET VM_SIZE=Standard_DS1

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --resource-group %RESOURCE_GROUP% ^
  --subscription %SUBSCRIPTION%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% %POSTFIX%

:: Create the NIC
CALL azure network nic create --network-security-group-name %NSG_NAME% ^
  --public-ip-name %IP_NAME% --subnet-name %SUBNET_NAME% --subnet-vnet-name ^
  %VNET_NAME%  --name %NIC_NAME% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS %POSTFIX% %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS %POSTFIX% %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --nic-name %NIC_NAME% --vnet-name %VNET_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new -g %RESOURCE_GROUP% --vm-name %VM_NAME% ^
  --size-in-gb 128 --vhd-name data1.vhd --storage-account-name %VHD_STORAGE%

:: Allow RDP
CALL azure network nsg rule create -g %RESOURCE_GROUP% --nsg-name %NSG_NAME% ^
  --direction Inbound --protocol Tcp --destination-port-range 3389 ^
  --source-port-range * --priority 100 --access Allow RDPAllow
```

<!-- links -->

[arm-templates]: ../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md
[audit-logs]: https://azure.microsoft.com/fr-FR/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../virtual-machines/virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-storage]: ../storage/storage-introduction.md
[blob-snapshot]: ../storage/storage-blob-snapshots.md
[blog-storage]: ../storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/fr-FR/blog/boot-diagnostics-for-virtual-machines-v2/
[data-disk]: ../virtual-machines/virtual-machines-disks-vhds.md
[disk-encryption]: ../azure-security-disk-encryption.md
[enable-monitoring]: ../azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../virtual-machines/virtual-machines-create-fqdn-on-portal.md
[log-collector]: https://azure.microsoft.com/fr-FR/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../virtual-machines/virtual-machines-manage-availability.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[password-reset]: ../virtual-machines/virtual-machines-windows-reset-password.md
[planned-maintenance]: ../virtual-machines/virtual-machines-planned-maintenance.md
[premium-storage]: ../storage/storage-premium-storage-preview-portal.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[reboot-logs]: https://azure.microsoft.com/fr-FR/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/fr-FR/library/hh848535.aspx
[resource-manager-overview]: ../resource-group-overview.md
[security-center]: https://azure.microsoft.com/fr-FR/services/security-center/
[security-extensions]: ../virtual-machines/virtual-machines-extensions-features.md#security-and-protection
[select-vm-image]: ../virtual-machines/resource-groups-vm-searching.md
[services-by-region]: https://azure.microsoft.com/fr-FR/regions/#services
[static-ip]: ../virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../virtual-machines/virtual-machines-size-specs.md
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-faq]: ../virtual-machines/virtual-machines-questions.md

<!---HONumber=AcomDC_0302_2016-->