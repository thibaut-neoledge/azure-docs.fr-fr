Cet article présente un ensemble de pratiques éprouvées pour l’exécution d’une machine virtuelle Linux sur Azure, surtout en matière d’évolutivité, de résilience, de gestion et de sécurité. Azure prend en charge l’exécution de plusieurs distributions Linux populaires, y compris CentOS, Debian, Red Hat Enterprise, Ubuntu et FreeBSD. Pour plus d’informations, consultez [Azure et Linux][azure-linux].

> [AZURE.NOTE] Azure propose deux modèles de déploiement : [Resource Manager][resource-manager-overview] et classique. Cet article utilise Resource Manager, solution recommandée par Microsoft pour les nouveaux déploiements.

Nous déconseillons l’utilisation d’une seule machine virtuelle pour les charges de travail de production, car il n’existe aucun contrat de niveau de service (SLA) concernant la durée de bon fonctionnement des machines virtuelles sur Azure. Pour bénéficier du SLA, vous devez déployer plusieurs machines virtuelles dans un [groupe à haute disponibilité][availability-set]. Pour plus d’informations, consultez [Running multiple VMs on Azure][multi-vm] \(Exécution de plusieurs machines virtuelles sur Azure).

## Diagramme de l’architecture

L’approvisionnement d’une machine virtuelle dans Azure implique de déplacer davantage d’éléments que la machine virtuelle. Il faut inclure les éléments de calcul, de mise en réseau et de stockage.

![[0]][0]

- **Groupe de ressources.** Un [_groupe de ressources_][resource-manager-overview] est un conteneur qui héberge les ressources associées. Créez un groupe de ressources pour héberger les ressources de cette machine virtuelle.

- **Machine virtuelle**. Vous pouvez approvisionner une machine virtuelle issue d’une liste d’images publiées ou d’un fichier de disque dur virtuel (VHD) que vous téléchargez sur Azure Blob Storage.

- **Disque du système d’exploitation.** Le disque du système d’exploitation est un disque dur virtuel situé dans [Azure Storage][azure-storage]. Son contenu est conservé même si l’ordinateur hôte tombe en panne. Le disque du système d’exploitation est `/dev/sda1`.

- **Disque temporaire.** La machine virtuelle est créée avec un disque temporaire. Ce disque est stocké sur un lecteur physique de l’ordinateur hôte. Il n’est _pas_ enregistré dans Azure Storage et peut disparaître lors des redémarrages ou d’autres événements de cycle de vie de la machine virtuelle. N’utilisez ce disque que pour des données temporaires, telles que des fichiers de pagination ou d’échange. Le disque temporaire est `/dev/sdb1`. Il est monté sur `/mnt/resource` ou `/mnt`.

- **Disques de données.** Un [disque de données][data-disk] est un disque dur virtuel persistant utilisé pour les données d’application. Les disques de données sont stockés dans Azure Storage, comme le disque du système d’exploitation.

- **Réseau virtuel (VNet) et sous-réseau.** Chaque machine virtuelle dans Azure est déployée dans un réseau virtuel (VNet) divisé en sous-réseaux.

- **Adresse IP publique.** Une adresse IP publique est nécessaire pour communiquer avec la machine virtuelle, par exemple avec le protocole SSH.

- **Interface réseau (NIC)**. La carte d’interface réseau permet à la machine virtuelle de communiquer avec le réseau virtuel.

- **Groupe de sécurité réseau**. Le [groupe de sécurité réseau][nsg] \(NSG) sert à autoriser ou refuser le trafic réseau destiné au sous-réseau. Vous pouvez associer un NSG à une carte réseau individuelle ou à un sous-réseau. Si vous l’associez à un sous-réseau, les règles du NSG s’appliquent à toutes les machines virtuelles de ce sous-réseau.
 
- **Diagnostics.** La journalisation des diagnostics est essentielle à la gestion et au dépannage de la machine virtuelle.

## Recommandations

### Recommandations pour les machines virtuelles

- Nous vous recommandons d’utiliser les gammes DS et GS, sauf si vous disposez d’une charge de travail spécialisée tels qu’un système de calcul hautes performances. Pour en savoir plus, voir [Tailles de machines virtuelles][virtual-machine-sizes]. Lorsque vous déplacez une charge de travail vers Azure, commencez par choisir la taille de machine virtuelle qui correspond le mieux à vos serveurs locaux. Mesurez ensuite les performances de votre charge de travail réelle en termes de processeur, de mémoire et d’opérations d’entrée/sortie par seconde du disque, puis ajustez la taille si nécessaire. En outre, si vous avez besoin de plusieurs cartes réseau, tenez compte de la limite de la carte réseau pour chaque taille.

- Lorsque vous approvisionnez la machine virtuelle et d’autres ressources, vous devez spécifier un emplacement. En général, choisissez un emplacement le plus proche possible de vos utilisateurs internes ou de vos clients. Sachez toutefois que certaines tailles de machine virtuelle ne sont pas disponibles dans tous les emplacements. Pour en savoir plus, voir [Services par région][services-by-region]. Pour répertorier les tailles de machines virtuelles disponibles dans un emplacement donné, exécutez la commande d’interface de ligne de commande (CLI) Azure suivante :

    ```
    azure vm sizes --location <location>
    ```

- Pour en savoir plus sur le choix d’une image de machine virtuelle publiée, voir [Parcourir et sélectionner des images de machine virtuelle Azure][select-vm-image].

### Recommandations pour le disque et le stockage

- Pour optimiser les performances d’E/S du disque, nous vous recommandons [Premium Storage][premium-storage], qui stocke les données sur des disques SSD. Le coût est basé sur la taille du disque approvisionné. Le nombre d’E/S par seconde et le débit (c’est-à-dire le taux de transfert des données) dépendent également de la taille du disque. Lorsque vous approvisionnez un disque, vous devez donc tenir compte des trois facteurs : capacité, E/S par seconde et débit.

- Un compte de stockage peut prendre en charge de 1 à 20 machines virtuelles.

- Ajoutez un ou plusieurs disques de données. Lorsque vous créez un disque dur virtuel, il n’est pas formaté. Connectez-vous à la machine virtuelle pour formater le disque. Les disques de données s’affichent sous la forme `/dev/sdc`, `/dev/sdd`, et ainsi de suite. Vous pouvez exécuter `lsblk` pour répertorier les périphériques de bloc, y compris les disques. Pour utiliser un disque de données, créez une partition et un système de fichiers puis montez le disque. Par exemple :

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

- Cette adresse IP publique peut être dynamique ou statique. Par défaut, elle est dynamique.

    - Utilisez une [adresse IP statique][static-ip] si vous avez besoin d’une adresse IP non modifiable, par exemple pour créer un enregistrement A dans le DNS ou insérer l’adresse IP dans la liste approuvée.

    - Vous pouvez également créer un nom de domaine complet (FQDN) pour l’adresse IP. Vous pouvez inscrire un [enregistrement CNAME][cname-record] dans le DNS qui pointe vers FQDN. Pour en savoir plus, voir [Créer un nom de domaine complet dans le portail Azure][fqdn].

- Tous les groupes de sécurité réseau contiennent un ensemble de [règles par défaut][nsg-default-rules], y compris une règle qui bloque tout le trafic Internet entrant. Les règles par défaut ne peuvent pas être supprimées, mais d’autres règles peuvent les remplacer. Pour autoriser le trafic Internet, créez des règles qui autorisent le trafic entrant vers des ports spécifiques &mdash; par exemple, le port 80 pour HTTP.

- Pour activer le protocole SSH, ajoutez au groupe de sécurité réseau une règle qui autorise le trafic entrant sur le port TCP 22.

## Considérations relatives à l’extensibilité

- Vous pouvez ajuster une machine virtuelle en [modifiant sa taille][vm-resize].

- Pour évoluer horizontalement, placez deux ou plusieurs machines virtuelles dans un groupe à haute disponibilité derrière un équilibreur de charge. Pour en savoir plus, voir [Running multiple VMs on Azure][multi-vm] \(Exécution de plusieurs machines virtuelles sur Azure).

## Considérations relatives à la disponibilité

- Comme indiqué ci-dessus, il n’existe aucun contrat SLA pour une machine virtuelle unique. Pour obtenir le contrat SLA, vous devez déployer plusieurs machines virtuelles dans un groupe à haute disponibilité.

- Votre machine virtuelle peut être affectée par la [maintenance planifiée][planned-maintenance] ou la [maintenance non planifiée][manage-vm-availability]. Vous pouvez utiliser les [journaux de redémarrage de machine virtuelle][reboot-logs] pour déterminer si un redémarrage de la machine virtuelle a été provoqué par une maintenance planifiée.

- Les disques durs virtuels sont gérés par [Azure Storage][azure-storage], qui est répliqué à des fins de durabilité et de disponibilité.

- Pour vous protéger contre la perte accidentelle de données pendant les opérations normales (par exemple, en cas d’erreur d’un utilisateur), vous devez également implémenter des sauvegardes ponctuelles, à l’aide [d’instantanés d’objet blob][blob-snapshot] ou d’un autre outil.

## Considérations relatives à la facilité de gestion

- **Groupes de ressources.** Placez les ressources fortement couplées qui partagent le même cycle de vie dans un même [groupe de ressources][resource-manager-overview]. Les groupes de ressources vous permettent de déployer et de surveiller les ressources en tant que groupe et de répercuter les coûts de facturation par groupe de ressources. Vous pouvez également supprimer des ressources dans un ensemble, ce qui est très utile pour les déploiements de test. Nommez les ressources de façon explicite. Cela permet de localiser plus facilement une ressource spécifique et de mieux comprendre son rôle. Voir [Conventions d’affectation de noms recommandées pour les ressources Azure][naming conventions].

- **SSH**. Avant de créer une machine virtuelle Linux, générez une paire de clés publique-privée RSA 2048 bits. Utilisez le fichier de clé publique lorsque vous créez la machine virtuelle. Pour en savoir plus, voir [Utilisation de SSH avec Linux et Mac sur Azure][ssh-linux].

- **VM diagnostics.** Permet la surveillance et le diagnostic, avec notamment des indicateurs d’intégrité de base, des journaux d’infrastructure de diagnostic et des [diagnostics de démarrage][boot-diagnostics]. Les diagnostics de démarrage peuvent vous aider à identifier le problème de démarrage si votre machine virtuelle refuse de démarrer. Pour en savoir plus, voir [Activation de la surveillance et des diagnostics][enable-monitoring].

    La commande CLI suivante active les diagnostics :

    ```text
    azure vm enable-diag <resource-group> <vm-name>
    ```

- **Arrêt d’une machine virtuelle.** Azure fait une distinction entre les états « Arrêté » et « Désalloué ». Vous êtes facturé quand l’état de la machine virtuelle est arrêté, vous n’êtes pas facturé lorsque la machine virtuelle est désallouée.

    Utilisez la commande CLI suivante pour désallouer une machine virtuelle :

    ```text
    azure vm deallocate <resource-group> <vm-name>
    ```

    Le bouton **Arrêter** du portail Azure désalloue aussi la machine virtuelle. Toutefois, si vous arrêtez la machine virtuelle via le système d’exploitation pendant que vous êtes connecté, la machine virtuelle est arrêtée mais _non_ désallouée. Vous serez donc toujours facturé.

- **Suppression d’une machine virtuelle.** La suppression d’une machine virtuelle n’entraîne pas celle des disques durs virtuels. Vous pouvez donc supprimer la machine virtuelle, sans risque de perdre des données. Toutefois, vous serez toujours facturé pour le stockage. Pour supprimer le disque dur virtuel, supprimez le fichier de [Blob Storage][blob-storage].

  Pour éviter toute suppression accidentelle, utilisez un [verrou de ressource][resource-lock] pour verrouiller tout le groupe de ressources ou des ressources individuelles, par exemple la machine virtuelle.

## Sécurité

- Automatisez les mises à jour du système d’exploitation à l’aide de l’extension de machine virtuelle [OSPatching]. Installez cette extension lorsque vous configurez la machine virtuelle. Vous pouvez spécifier la fréquence d’installation des correctifs et si un redémarrage est nécessaire après la mise à jour corrective.

- Utilisez le [contrôle d’accès en fonction du rôle][rbac] \(RBAC) pour contrôler l’accès aux ressources Azure que vous déployez. Le contrôle RBAC vous permet d’affecter des rôles d’autorisation aux membres de votre équipe DevOps. Par exemple, le rôle Lecteur permet d’afficher des ressources Azure mais pas de les créer, gérer ou supprimer. Certains rôles sont spécifiques à des types de ressources Azure particuliers. Par exemple, le rôle Contributeur de machine virtuelle peut redémarrer ou désallouer une machine virtuelle, réinitialiser le mot de passe administrateur, créer une machine virtuelle, et ainsi de suite. D’autres [rôles RBAC intégrés][rbac-roles] peuvent être utiles dans cette architecture de référence, notamment [Utilisateur DevTest Lab][rbac-devtest] et [Collaborateur de réseau][rbac-network]. Un utilisateur peut être affecté à plusieurs rôles, et vous pouvez créer des rôles personnalisés pour d’autres autorisations plus affinées.

    > [AZURE.NOTE] Le contrôle RBAC ne limite pas les actions qu’un utilisateur connecté peut effectuer sur une machine virtuelle. Ces autorisations dépendent du type de compte installé sur le système d’exploitation invité.

- Utilisez les [journaux d’audit][audit-logs] pour voir les actions d’approvisionnement et d’autres événements concernant la machine virtuelle.

- Utilisez [Azure Disk Encryption][disk-encryption] si vous devez chiffrer les disques du système d’exploitation et de données.

## Composants de la solution

Un exemple de script de solution, [Deploy-ReferenceArchitecture.ps1][solution-script], est disponible. Vous pouvez l’utiliser pour implémenter l’architecture qui suit les recommandations décrites dans cet article. Ce script utilise des modèles [Azure Resource Manager][arm-templates]. Les modèles sont disponibles en tant qu’ensemble de blocs de construction fondamentaux, dont chacun exécute une action spécifique, telle que la création d’un réseau virtuel ou la configuration d’un groupe de sécurité réseau. L’objectif du script consiste à orchestrer le déploiement du modèle.

Les modèles sont paramétrés et les paramètres sont stockés dans des fichiers JSON distincts. Vous pouvez modifier les paramètres dans ces fichiers pour configurer le déploiement selon vos besoins. Il est inutile de modifier les modèles eux-mêmes. Notez que vous ne devez pas modifier les schémas des objets dans les fichiers de paramètres.

Lorsque vous modifiez les modèles, créez des objets qui respectent les conventions d’affectation de noms décrites dans la section [Conventions d’affectation de noms recommandées pour les ressources Azure][naming conventions].

Le script fait référence aux fichiers de paramètre suivants pour créer la machine virtuelle et l’infrastructure environnante :

- **[virtualNetwork.parameters.json][vnet-parameters]**. Ce fichier définit les paramètres du réseau virtuel, comme le nom, l’espace d’adressage, les sous-réseaux et les adresses de tous les serveurs DNS requis. Notez que les adresses des sous-réseaux doivent être intégrées à l’espace d’adressage du réseau virtuel.

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/linux/virtualNetwork.parameters.json#L4-L21 -->
	```json
  "parameters": {
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg",
        "addressPrefixes": [
          "172.17.0.0/16"
        ],
        "subnets": [
          {
            "name": "ra-single-vm-sn",
            "addressPrefix": "172.17.0.0/24"
          }
        ],
        "dnsServers": [ ]
      }
    }
  }
	```

- **[networkSecurityGroup.parameters.json][nsg-parameters]**. Ce fichier contient les définitions des groupes de sécurité réseau et de leurs règles. Le paramètre `name` dans le bloc `virtualNetworkSettings` indique le réseau virtuel auquel le groupe de sécurité réseau est lié. Le paramètre `subnets` dans le bloc `networkSecurityGroupSettings` identifie tous les sous-réseaux qui appliquent les règles du groupe de sécurité réseau dans le réseau virtuel. Il doit s’agir d’éléments définis dans le fichier **virtualNetwork.parameters.json**.

	La règle de sécurité indiquée dans l’exemple permet à un utilisateur de se connecter à la machine virtuelle à l’aide d’une connexion SSH. Vous pouvez ouvrir des ports supplémentaires (ou refuser l’accès via des ports spécifiques) en ajoutant des éléments supplémentaires au tableau `securityRules`.
	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/linux/networkSecurityGroups.parameters.json#L4-L36 -->
	```json
  "parameters": {
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg"
      }
    },
    "networkSecurityGroupsSettings": {
      "value": [
        {
          "name": "ra-single-vm-nsg",
          "subnets": [
            "ra-single-vm-sn"
          ],
          "networkInterfaces": [
          ],
          "securityRules": [
            {
              "name": "default-allow-ssh",
              "direction": "Inbound",
              "priority": 1000,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "22",
              "access": "Allow",
              "protocol": "Tcp"
            }
          ]
        }
      ]
    }
  }
	```

- **[virtualMachineParameters.json][vm-parameters]**. Ce fichier définit les paramètres de la machine virtuelle, notamment le nom et la taille de la machine virtuelle, les informations d’identification de sécurité pour l’utilisateur administrateur, les disques à créer et les comptes de stockage contenant ces disques.

	N’oubliez pas de définir le paramètre `osType` sur `linux`. Vous devez également spécifier une image dans la section `imageReference`. Les valeurs indiquées ci-dessous créent une machine virtuelle avec la dernière version de RedHat Linux 7.2. Vous pouvez utiliser la commande CLI Azure suivante pour obtenir une liste de toutes les images RedHat disponibles dans une région (l’exemple utilise la région westus) :

	```powershell
	azure vm image list westus redhat rhel
	```

	Le paramètre `subnetName` dans la section `nics` spécifie le sous-réseau pour la machine virtuelle. De même, le paramètre `name` dans `virtualNetworkSettings` identifie le réseau virtuel à utiliser. Il doit s’agir du nom d’un sous-réseau et réseau virtuel défini dans le fichier **virtualNetwork.parameters.json**.

	Vous pouvez créer plusieurs machines virtuelles partageant un compte de stockage ou ayant leurs propres comptes de stockage, en modifiant les paramètres de la section `buildingBlockSettings`. Si vous créez plusieurs machines virtuelles, vous devez également spécifier le nom d’un jeu de disponibilité à utiliser ou à créer dans la section `availabilitySet`.

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/linux/virtualMachine.parameters.json#L4-L63 -->
	```json
  "parameters": {
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-single-vm",
        "computerNamePrefix": "cn",
        "size": "Standard_DS1_v2",
        "osType": "linux",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "true",
            "subnetName": "ra-single-vm-sn",
            "privateIPAllocationMethod": "dynamic",
            "publicIPAllocationMethod": "dynamic",
            "enableIPForwarding": false,
            "dnsServers": [
            ],
            "isPrimary": "true"
          }
        ],
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.5-LTS",
          "version": "latest"
        },
        "dataDisks": {
          "count": 2,
          "properties": {
            "diskSizeGB": 128,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [ ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": ""
        }
      }
    },
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg"
      }
    },
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 1,
        "vmCount": 1,
        "vmStartIndex": 0
      }
    }
  }
	```

## Déploiement de la solution

La solution suppose que les conditions préalables suivantes sont respectées :

- Vous avez un abonnement Azure existant dans lequel vous pouvez créer des groupes de ressources.

- Vous avez téléchargé et installé la version la plus récente d’Azure Powershell. Pour obtenir des instructions, voir [cet article][azure-powershell-download].

Pour exécuter le script qui déploie la solution :

1. Créez un dossier qui contient des sous-dossiers nommés `Scripts` et `Templates`.

2. Dans le dossier Modèles, créez un autre sous-dossier nommé Linux.

3. Téléchargez le fichier [Deploy-ReferenceArchitecture.ps1][solution-script] dans le dossier Scripts.

4. Téléchargez les fichiers suivants dans le dossier Modèles/Linux :

	- [virtualNetwork.parameters.json][vnet-parameters]

	- [networkSecurityGroup.parameters.json][nsg-parameters]

	- [virtualMachineParameters.json][vm-parameters]

5. Modifiez le fichier Deploy-ReferenceArchitecture.ps1 dans le dossier Scripts et modifiez la ligne suivante pour spécifier le groupe de ressources qui doit être créé ou utilisé pour contenir la machine virtuelle et les ressources créées par le script :

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/Deploy-ReferenceArchitecture.ps1#L37 -->
	```powershell
	$resourceGroupName = "ra-single-vm-rg"
	```
6. Modifiez chacun des fichiers JSON dans le dossier Modèles/Linux pour définir les paramètres du réseau virtuel, du groupe de sécurité réseau et de la machine virtuelle, comme décrit dans la section Composants de la solution précédente.

	>[AZURE.NOTE] N’oubliez pas de définir le paramètre `resourceGroup` dans la section `virtualNetworkSettings` du fichier virtualMachineParameters.json de manière à ce qu’il soit identique à celui spécifié dans le fichier de script Deploy-ReferenceArchitecture.ps1.

7. Ouvrez une fenêtre Azure PowerShell, accédez au dossier Scripts et exécutez la commande suivante :

	```powershell
	.\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Linux
	```

	Remplacez `<subscription id>` par l’identifiant de votre abonnement Azure.

	Pour `<location>`, indiquez une région Azure, comme `eastus` ou `westus`.

8. Une fois le script terminé, utilisez le portail Azure pour vérifier que le réseau, le groupe de sécurité réseau et la machine virtuelle ont été créés avec succès.

## Étapes suivantes

Pour appliquer le [SLA pour Virtual Machines][vm-sla], vous devez déployer au moins deux instances dans un groupe à haute disponibilité. Pour plus d’informations, voir [Running multiple VMs on Azure][multi-vm] \(Exécution de plusieurs machines virtuelles sur Azure).

<!-- links -->

[audit-logs]: https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-linux]: ../articles/virtual-machines/virtual-machines-linux-azure-overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
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
[reboot-logs]: https://azure.microsoft.com/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/fr-FR/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[select-vm-image]: ../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/regions/#services
[ssh-linux]: ../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-linux-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/
[arm-templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
[solution-script]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/parameters/linux/virtualNetwork.parameters.json
[nsg-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/linux/networkSecurityGroups.parameters.json
[vm-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/parameters/linux/virtualMachine.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Architecture de machine virtuelle Linux unique dans Azure"

<!---HONumber=AcomDC_0831_2016-->