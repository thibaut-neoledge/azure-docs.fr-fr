Cet article présente un ensemble de pratiques éprouvées pour l’exécution d’une machine virtuelle Windows sur Azure, surtout en matière d’évolutivité, de résilience, de gestion et de sécurité.

> [AZURE.NOTE] Azure propose deux modèles de déploiement : [Resource Manager][resource-manager-overview] et classique. Cet article utilise Resource Manager, solution recommandée par Microsoft pour les nouveaux déploiements.

Nous déconseillons l’utilisation d’une seule machine virtuelle pour les charges de travail de production car il n’existe aucun contrat SLA concernant la durée de bon fonctionnement des machines virtuelles sur Azure. Pour obtenir le contrat SLA, vous devez déployer plusieurs machines virtuelles dans un groupe à haute disponibilité. Pour plus d’informations, consultez [Exécution de plusieurs machines virtuelles Windows sur Azure][multi-vm].

## Diagramme de l’architecture

L’approvisionnement d’une machine virtuelle dans Azure implique de déplacer davantage d’éléments que la machine virtuelle. Il faut inclure les éléments de calcul, de mise en réseau et de stockage.

![IaaS : machine virtuelle unique](./media/guidance-blueprints/compute-single-vm.png)

- **Groupe de ressources.** Un [_groupe de ressources_][resource-manager-overview] est un conteneur qui héberge les ressources associées. Créez un groupe de ressources pour héberger les ressources de cette machine virtuelle.

- **Machine virtuelle**. Vous pouvez approvisionner une machine virtuelle issue d’une liste d’images publiées ou d’un fichier de disque dur virtuel (VHD) que vous téléchargez sur Azure Blob Storage.

- **Disque du système d’exploitation.** Le disque du système d’exploitation est un disque dur virtuel situé dans [Azure Storage][azure-storage]. Son contenu est conservé même si l’ordinateur hôte tombe en panne.

- **Disque temporaire.** La machine virtuelle est créée avec un disque temporaire (le lecteur `D:` sous Windows). Ce disque est stocké sur un lecteur physique de l’ordinateur hôte. Il _n’est pas_ enregistré dans Azure Storage et peut disparaître lors des redémarrages ou d’autres événements de cycle de vie de la machine virtuelle. N’utilisez ce disque que pour des données temporaires, telles que des fichiers de pagination ou d’échange.

- **Disques de données.** Un [disque de données][data-disk] est un disque dur virtuel persistant utilisé pour les données d’application. Les disques de données sont stockés dans Azure Storage, comme le disque du système d’exploitation.

- **Réseau virtuel (VNet) et sous-réseau.** Chaque machine virtuelle dans Azure est déployée dans un réseau virtuel (VNet) divisé en sous-réseaux.

- **Adresse IP publique.** Une adresse IP publique est nécessaire pour communiquer avec la machine virtuelle, par exemple avec le protocole RDP.

- **Interfaces réseau (NIC)**. La carte d’interface réseau permet à la machine virtuelle de communiquer avec le réseau virtuel.

- **Groupe de sécurité réseau (NSG)**. Le [NSG][nsg] sert à autoriser ou refuser le trafic réseau destiné au sous-réseau. Vous pouvez associer un NSG à une carte réseau individuelle ou à un sous-réseau. Si vous l’associez à un sous-réseau, les règles du NSG s’appliquent à toutes les machines virtuelles de ce sous-réseau.
 
- **Diagnostics.** La journalisation des diagnostics est essentielle à la gestion et au dépannage de la machine virtuelle.

## Recommandations

### Recommandations pour les machines virtuelles

- Nous vous recommandons d’utiliser les gammes DS et GS, sauf si vous disposez d’une charge de travail spécialisée tels qu’un système de calcul hautes performances. Pour en savoir plus, consultez [Tailles de machines virtuelles][virtual-machine-sizes]. Lorsque vous déplacez une charge de travail vers Azure, commencez par choisir la taille de machine virtuelle qui correspond le mieux à vos serveurs locaux. Mesurez ensuite les performances de votre charge de travail réelle en termes de processeur, mémoire et E/S par seconde du disque, puis ajustez la taille si nécessaire. En outre, si vous avez besoin de plusieurs cartes réseau, tenez compte de la limite de la carte réseau pour chaque taille.  

- Lorsque vous approvisionnez la machine virtuelle et d’autres ressources, vous devez spécifier un emplacement. En général, choisissez un emplacement le plus proche possible de vos utilisateurs internes ou de vos clients. Sachez toutefois que certaines tailles de machine virtuelle ne sont pas disponibles dans tous les emplacements. Pour plus d’informations, consultez la page [Services par région][services-by-region]. Pour répertorier les tailles de machines virtuelles disponibles dans un emplacement donné, exécutez la commande CLI Azure suivante :

    ```
    azure vm sizes --location <location>
    ```

- Pour plus d’informations sur le choix d’une image de machine virtuelle publiée, consultez [Rechercher par navigation et sélectionner des images de machines virtuelles Azure avec Windows PowerShell et l’interface de ligne de commande Azure][select-vm-image].

### Recommandations pour le disque et le stockage

- Pour de meilleures performances d’E/S du disque, nous vous recommandons [Premium Storage][premium-storage], qui stocke les données sur des disques SSD. Le coût est basé sur la taille du disque approvisionné. Le nombre d’E/S par seconde et le débit (c’est-à-dire le taux de transfert des données) dépendent également de la taille du disque. Lorsque vous approvisionnez un disque, vous devez donc tenir compte des trois facteurs : capacité, E/S par seconde et débit. 

- Ajoutez un ou plusieurs disques de données. Lorsque vous créez un disque dur virtuel, il n’est pas formaté. Connectez-vous à la machine virtuelle pour formater le disque.

- Si vous avez un grand nombre de disques de données, n’oubliez pas les limites d’E/S du compte de stockage. Pour plus d’informations, consultez la page [Limites du nombre de disques de machine virtuelle][vm-disk-limits].

- Pour des performances optimales, créez un compte de stockage distinct destiné à héberger les journaux de diagnostic. Un compte de stockage localement redondant (LRS) standard suffit pour les journaux de diagnostic.

- Lorsque cela est possible, installez les applications sur un disque de données plutôt que sur le disque du système d’exploitation. Toutefois, certaines applications héritées peuvent installer des composants sur le lecteur C:. Dans ce cas, vous pouvez [redimensionner le disque du système d’exploitation][resize-os-disk] à l’aide de PowerShell.

### Recommandations pour le réseau

- Cette adresse IP publique peut être dynamique ou statique. Par défaut, elle est dynamique.

    - Utilisez une [adresse IP statique][static-ip] si vous avez besoin d’une adresse IP non modifiable, par exemple pour créer un enregistrement A dans le DNS ou insérer l’adresse IP dans la liste blanche.

    - Vous pouvez également créer un nom de domaine complet (FQDN) pour l’adresse IP. Vous pouvez inscrire un [enregistrement CNAME][cname-record] dans DNS qui pointe vers le nom de domaine complet. Pour plus d’informations, consultez la page [Créer un nom de domaine complet dans le portail Azure][fqdn].

- Tous les groupes de sécurité réseau contiennent un ensemble de [règles par défaut][nsg-default-rules], y compris une règle qui bloque tout le trafic Internet entrant. Les règles par défaut ne peuvent pas être supprimées, mais d’autres règles peuvent les remplacer. Pour autoriser le trafic Internet, créez des règles qui autorisent le trafic entrant vers des ports spécifiques &mdash; par exemple, le port 80 pour HTTP.

- Pour activer le protocole RDP, ajoutez une règle de groupe de sécurité réseau qui autorise le trafic entrant sur le port TCP 3389.

## Considérations relatives à l’extensibilité

- Vous pouvez ajuster une machine virtuelle en [modifiant sa taille][vm-resize]. 

- Pour évoluer horizontalement, placez deux ou plusieurs machines virtuelles dans un groupe à haute disponibilité derrière un équilibreur de charge. Pour plus d’informations, consultez [Exécution de plusieurs machines virtuelles Windows sur Azure][multi-vm].

## Considérations relatives à la disponibilité

- Comme indiqué ci-dessus, il n’existe aucun contrat SLA pour une machine virtuelle unique. Pour obtenir le contrat SLA, vous devez déployer plusieurs machines virtuelles dans un groupe à haute disponibilité.

- Votre machine virtuelle peut être affectée par la [maintenance planifiée][planned-maintenance] ou la [maintenance non planifiée][manage-vm-availability]. Vous pouvez utiliser les [journaux de redémarrage de machine virtuelle][reboot-logs] pour déterminer si un redémarrage de la machine virtuelle a été provoqué par une maintenance planifiée.

- Les disques durs virtuels sont gérés par [Azure Storage][azure-storage], qui est répliqué à des fins de durabilité et de disponibilité.

- Pour vous protéger contre la perte accidentelle de données pendant les opérations normales (par exemple, en cas d’une erreur d’un utilisateur), vous devez également effectuer des sauvegardes ponctuelles, à l’aide d’[instantanés d’objet blob][blob-snapshot] ou d’un autre outil.

## Considérations relatives à la facilité de gestion

- **Groupes de ressources.** Placez les ressources fortement couplées qui partagent le même cycle de vie dans un même [groupe de ressources][resource-manager-overview]. Les groupes de ressources vous permettent de déployer et de surveiller les ressources en tant que groupe et de répercuter les coûts de facturation par groupe de ressources. Vous pouvez également supprimer des ressources dans un ensemble, ce qui est très utile pour les déploiements de test. Nommez les ressources de façon explicite. Cela permet de localiser plus facilement une ressource spécifique et de mieux comprendre son rôle. Consultez [Conventions d’affectation de noms recommandées pour les ressources Azure][naming conventions].

- **VM diagnostics.** Permet la surveillance et le diagnostic, avec notamment des indicateurs d’intégrité de base, des journaux d’infrastructure de diagnostic et des [diagnostics de démarrage][boot-diagnostics]. Les diagnostics de démarrage peuvent vous aider à identifier le problème de démarrage si votre machine virtuelle refuse de démarrer. Pour plus d’informations, consultez la page [Activation de la surveillance et des diagnostics][enable-monitoring]. Utilisez l’extension [Azure Log Collector][log-collector] pour collecter les journaux de la plateforme Azure et les télécharger sur Azure Storage.

    La commande CLI suivante active les diagnostics :

    ```text
    azure vm enable-diag <resource-group> <vm-name>
     ```

- **Arrêt d’une machine virtuelle.** Azure fait une distinction entre les états « Arrêté » et « Désalloué ». Vous êtes facturé quand l’état de la machine virtuelle est arrêté, mais pas lorsque la machine virtuelle est désallouée.

    Utilisez la commande CLI suivante pour désallouer une machine virtuelle :

    ```text
    azure vm deallocate <resource-group> <vm-name>
    ```

    Le bouton **Arrêter** du portail Azure désalloue aussi la machine virtuelle. Toutefois, si vous arrêtez la machine virtuelle via le système d’exploitation pendant que vous êtes connecté, la machine virtuelle est arrêtée mais _pas_ désallouée. Vous serez donc toujours facturé.

- **Suppression d’une machine virtuelle.** La suppression d’une machine virtuelle n’entraîne pas celle des disques durs virtuels. Vous pouvez donc supprimer la machine virtuelle, sans risque de perdre des données. Toutefois, vous serez toujours facturé pour le stockage. Pour supprimer le disque dur virtuel, supprimez le fichier de [Blob Storage][blob-storage].

  Pour éviter toute suppression accidentelle, utilisez un [verrou de ressource][resource-lock] pour verrouiller tout le groupe de ressources ou des ressources individuelles, par exemple la machine virtuelle.



## Sécurité

- Le [Centre de sécurité Azure][security-center] vous offre un aperçu global de l’état de sécurité de toutes vos ressources Azure. Il surveille les problèmes potentiels de sécurité tels que les mises à jour système, les logiciels malveillants, et fournit une image complète de la sécurité de votre déploiement. 

    - Le Centre de sécurité est configuré pour chaque abonnement Azure. Activez la collecte de données de sécurité comme décrit dans [Utiliser le Centre de sécurité].
    - Une fois la collecte de données activée, le Centre de sécurité analyse automatiquement les machines virtuelles créées dans le cadre de cet abonnement.

- **Gestion des correctifs.** Si cette option est activée, le Centre de sécurité vérifie si des mises à jour critiques et de sécurité sont manquantes. Utilisez [les paramètres de stratégie de groupe][group-policy] sur la machine virtuelle pour activer les mises à jour automatiques du système.

- **Logiciel anti-programme malveillant.** Si cette option est activée, le Centre de sécurité vérifie si un logiciel anti-programme malveillant est installé. Vous pouvez également utiliser le Centre de sécurité pour installer des logiciels anti-programme malveillant dans le portail Azure.

- Utilisez le [contrôle d’accès en fonction du rôle][rbac] (RBAC) pour contrôler l’accès aux ressources Azure que vous déployez. Le contrôle RBAC vous permet d’affecter des rôles d’autorisation aux membres de votre équipe DevOps. Par exemple, le rôle Lecteur permet d’afficher des ressources Azure mais pas de les créer, gérer ou supprimer. Certains rôles sont spécifiques à des types de ressources Azure particuliers. Par exemple, le rôle Contributeur de machine virtuelle peut redémarrer ou désallouer une machine virtuelle, réinitialiser le mot de passe administrateur, créer une machine virtuelle, etc. D’autres [rôles RBAC intégrés][rbac-roles] peuvent être utiles dans cette architecture de référence, notamment [Utilisateur DevTest Lab][rbac-devtest] et [Collaborateur de réseau][rbac-network]. Un utilisateur peut être affecté à plusieurs rôles, et vous pouvez créer des rôles personnalisés pour d’autres autorisations plus affinées.

    > [AZURE.NOTE] Le contrôle RBAC ne limite pas les actions qu’un utilisateur connecté peut effectuer sur une machine virtuelle. Ces autorisations dépendent du type de compte installé sur le système d’exploitation invité.

- Pour réinitialiser le mot de passe de l’administrateur local, exécutez la commande Azure CLI `vm reset-access`.

    ```text
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
    ```

- Utilisez les [journaux d’audit][audit-logs] pour voir les actions d’approvisionnement et d’autres événements concernant la machine virtuelle.

- Utilisez [Azure Disk Encryption][disk-encryption] si vous devez chiffrer les disques du système d’exploitation et de données.

## Exemple de script de déploiement

Le script de commandes Windows suivant exécute des commandes [Azure CLI][azure-cli] pour déployer une seule instance de machine virtuelle ainsi que les ressources de stockage et le réseau associés, comme indiqué dans le précédent schéma.

Le script utilise les conventions d’affectation de noms décrites dans la section [Conventions d’affectation de noms recommandées pour les ressources Azure][naming conventions].

```bat
ECHO OFF
SETLOCAL

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Set up variables for deploying resources to Azure.
:: Change these variables for your own deployment.

:: The APP_NAME variable must not exceed 4 characters in size.
:: If it does the 15 character size limitation of the VM name may be exceeded.

SET APP_NAME=app1
SET LOCATION=eastus2
SET ENVIRONMENT=dev
SET USERNAME=testuser


:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see:
::   https://azure.microsoft.com/documentation/articles/virtual-machines-size-specs/
:: To see the VM sizes available in a region:
:: 	azure vm sizes --location <location>
SET VM_SIZE=Standard_DS1

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

IF "%2"=="" (
    ECHO Usage: %0 subscription-id admin-password
    EXIT /B
    )

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1
SET PASSWORD=%2

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

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% --location %LOCATION% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% --location %LOCATION% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --network-security-group-name %NSG_NAME% ^
  %POSTFIX%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% --location %LOCATION% %POSTFIX%

:: Create the NIC
CALL azure network nic create --public-ip-name %IP_NAME% --subnet-name ^
  %SUBNET_NAME% --subnet-vnet-name %VNET_NAME%  --name %NIC_NAME% --location ^
  %LOCATION% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS --location %LOCATION% %POSTFIX% ^
  %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS --location %LOCATION% %POSTFIX% ^
  %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --vnet-name %VNET_NAME% --nic-name %NIC_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" --location %LOCATION% ^
  %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new --vm-name %VM_NAME% --size-in-gb 128 --vhd-name ^
  data1.vhd --storage-account-name %VHD_STORAGE% %POSTFIX%

:: Allow RDP
CALL azure network nsg rule create --nsg-name %NSG_NAME% --direction Inbound ^
  --protocol Tcp --destination-port-range 3389 --source-port-range * ^
  --priority 100 --access Allow RDPAllow %POSTFIX%
```

## Étapes suivantes

Afin d’appliquer le [contrat SLA pour machines virtuelles][vm-sla], vous devez déployer au moins deux instances dans un groupe à haute disponibilité. Pour plus d’informations, consultez [Exécution de plusieurs machines virtuelles Windows sur Azure][multi-vm].

<!-- links -->

[arm-templates]: ../articles/virtual-machines/virtual-machines-windows-cli-deploy-templates.md
[audit-logs]: https://azure.microsoft.com/fr-FR/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/fr-FR/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[group-policy]: https://technet.microsoft.com/fr-FR/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/fr-FR/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/fr-FR/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/fr-FR/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/fr-FR/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[security-center]: https://azure.microsoft.com/fr-FR/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/fr-FR/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[Utiliser le Centre de sécurité]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/fr-FR/support/legal/sla/virtual-machines/v1_0/

<!---HONumber=AcomDC_0622_2016-->