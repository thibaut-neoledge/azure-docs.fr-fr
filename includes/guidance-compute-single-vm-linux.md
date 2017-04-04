Cet article présente un ensemble de pratiques éprouvées pour l’exécution d’une machine virtuelle Linux sur Azure, surtout en matière d’évolutivité, de résilience, de gestion et de sécurité. Azure prend en charge l’exécution de plusieurs distributions Linux populaires, y compris CentOS, Debian, Red Hat Enterprise, Ubuntu et FreeBSD. Pour en savoir plus, voir [Azure et Linux][azure-linux].

> [!NOTE]
> Azure propose deux modèles de déploiement : le modèle [Resource Manager][resource-manager-overview] et le modèle classique. Cet article utilise Resource Manager, solution recommandée par Microsoft pour les nouveaux déploiements.
> 
> 

Il est déconseillé d’utiliser une seule et même machine virtuelle pour les charges de travail stratégiques, car ce faisant, un seul point de défaillance est créé. Pour bénéficier d’une disponibilité plus élevée, vous devez déployer plusieurs machines virtuelles dans un [groupe à haute disponibilité][availability-set]. Pour plus d’informations, voir [Running multiple VMs on Azure (Exécution de plusieurs machines virtuelles sur Azure)][multi-vm]. 

## <a name="architecture-diagram"></a>Diagramme de l’architecture
L’approvisionnement d’une machine virtuelle dans Azure implique de déplacer davantage d’éléments que la machine virtuelle. Il faut tenir compte des éléments de calcul, de mise en réseau et de stockage.

> Un document Visio incluant ce diagramme d’architecture est téléchargeable dans le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme figure sur la page « Compute - single VM » (Calcul - Machine virtuelle unique).
> 
> 

![[0]][0]

* **Groupe de ressources.** Un [*groupe de ressources*][resource-manager-overview] est un conteneur qui héberge les ressources associées. Créez un groupe de ressources pour héberger les ressources de cette machine virtuelle.
* **Machine virtuelle**. Vous pouvez approvisionner une machine virtuelle issue d’une liste d’images publiées ou d’un fichier de disque dur virtuel (VHD) que vous chargez dans Stockage Blob Azure.
* **Disque du système d’exploitation.** Le disque du système d’exploitation est un disque dur virtuel stocké dans [Stockage Azure][azure-storage]. Son contenu est conservé même si l’ordinateur hôte tombe en panne. Le disque du système d’exploitation est `/dev/sda1`.
* **Disque temporaire** La machine virtuelle est créée avec un disque temporaire. Ce disque est stocké sur un lecteur physique de l’ordinateur hôte. Il *n’est pas* enregistré dans Stockage Azure et peut être supprimé lors des redémarrages ou d’autres événements de cycle de vie de la machine virtuelle. N’utilisez ce disque que pour des données temporaires, telles que des fichiers de pagination ou d’échange. Le disque temporaire est `/dev/sdb1`. Il est monté sur `/mnt/resource` ou `/mnt`.
* **Disques de données.** Un [disque de données][data-disk] est un disque dur virtuel persistant utilisé pour les données d’application. Les disques de données sont stockés dans Stockage Azure, comme le disque du système d’exploitation.
* **Réseau virtuel (VNet) et sous-réseau.** Chaque machine virtuelle dans Azure est déployée dans un réseau virtuel divisé en sous-réseaux.
* **Adresse IP publique.** Une adresse IP publique est nécessaire pour communiquer avec la machine virtuelle, par exemple avec le protocole SSH.
* **Interfaces réseau (NIC)**. La carte d’interface réseau permet à la machine virtuelle de communiquer avec le réseau virtuel.
* **Groupe de sécurité réseau**. Le [groupe de sécurité réseau][nsg] (NSG) sert à autoriser ou refuser le trafic réseau destiné au sous-réseau. Vous pouvez associer un NSG à une carte réseau individuelle ou à un sous-réseau. Si vous l’associez à un sous-réseau, les règles du NSG s’appliquent à toutes les machines virtuelles de ce sous-réseau.
* **Diagnostics.** La journalisation des diagnostics est essentielle à la gestion et au dépannage de la machine virtuelle.

## <a name="recommendations"></a>Recommandations

Les recommandations suivantes s’appliquent à la plupart des scénarios. Suivez ces recommandations, sauf si vous avez un besoin spécifique qui vous oblige à les ignorer. 

### <a name="vm-recommendations"></a>Recommandations pour les machines virtuelles

Bien qu’Azure propose de nombreuses tailles de machines virtuelles, nous recommandons les séries DS et GS, car ces tailles de machines prennent en charge l’offre [Stockage Premium][premium-storage]. Choisissez l’une de ces tailles de machine virtuelle, sauf si vous disposez d’une charge de travail spécialisée tel qu’un système de calcul hautes performances. Pour en savoir plus, consultez les [tailles des machines virtuelles][virtual-machine-sizes].

Si vous déplacez une charge de travail vers Azure, commencez par choisir la taille de machine virtuelle qui correspond le mieux à vos serveurs locaux. Mesurez ensuite les performances de votre charge de travail réelle en termes de processeur, de mémoire et d’opérations d’entrée/sortie par seconde du disque, puis ajustez la taille si nécessaire. Si vous avez besoin de plusieurs cartes réseau (NIC) pour votre machine virtuelle, notez que le nombre maximal de cartes NIC est une fonction de la [taille de machine virtuelle][vm-size-tables].

Lorsque vous approvisionnez la machine virtuelle et d’autres ressources, vous devez spécifier une région. En général, choisissez une région la plus proche possible de vos utilisateurs internes ou de vos clients. Sachez toutefois que certaines tailles de machine virtuelle ne sont pas disponibles dans toutes les régions. Pour en savoir plus, consultez les [services par région][services-by-region]. Pour répertorier les tailles de machines virtuelles disponibles dans un emplacement donné, exécutez la commande CLI Azure suivante :

```
azure vm sizes --location <location>
```

Pour en savoir plus sur le choix d’une image de machine virtuelle publiée, voir [Sélectionner des images de VM Linux avec l’interface de ligne de commande Azure][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Recommandations pour le disque et le stockage

Pour optimiser les performances d’E/S du disque, nous vous recommandons le [Stockage Premium][premium-storage], qui stocke les données sur des disques SSD. Le coût est basé sur la taille du disque approvisionné. Le nombre d’E/S par seconde et le débit (c’est-à-dire le taux de transfert des données) dépendent également de la taille du disque. Lorsque vous approvisionnez un disque, vous devez donc tenir compte des trois facteurs : capacité, E/S par seconde et débit. 

Créez des comptes de stockage Azure distincts pour chaque machine virtuelle pour stocker les disques durs virtuels (VHD), afin d’éviter d’atteindre les limites d’E/S par seconde des comptes de stockage. 

Ajoutez un ou plusieurs disques de données. Lorsque vous créez un disque dur virtuel, il n’est pas formaté. Connectez-vous à la machine virtuelle pour formater ce disque. Dans l’interpréteur de commandes Linux, les disques de données sont affichés en tant que `/dev/sdc`, `/dev/sdd`, et ainsi de suite. Vous pouvez exécuter `lsblk` pour répertorier les périphériques de bloc, y compris les disques. Pour utiliser un disque de données, créez une partition et un système de fichiers, puis montez le disque. Par exemple :

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Si vous avez un grand nombre de disques de données, n’oubliez pas les limites d’E/S du compte de stockage. Pour en savoir plus, voir les [limites du nombre de disques de machine virtuelle][vm-disk-limits].

Lorsque vous ajoutez un disque de données, un numéro d’unité logique (LUN) lui est attribué. Vous pouvez également spécifier l’ID LUN, par exemple si vous remplacez un disque et souhaitez conserver le même ID LUN, ou si votre application nécessite un ID LUN spécifique. Toutefois, n’oubliez pas que les ID LUN doivent être uniques pour chaque disque.

Vous pouvez modifier le planificateur d’E/S afin d’optimiser les performances des disques SSD, les disques des machines virtuelles associées à des comptes de Stockage Premium étant de type SSD. Il est généralement recommandé d’utiliser le planificateur NOOP pour les disques SSD, mais vous devriez utiliser un outil tel que [iostat] pour surveiller les performances d’E/S du disque pour votre charge de travail spécifique.

Pour des performances optimales, créez un compte de stockage distinct destiné à héberger les journaux de diagnostic. Un compte de stockage localement redondant (LRS) standard suffit pour les journaux de diagnostic.

### <a name="network-recommendations"></a>Recommandations pour le réseau

Cette adresse IP publique peut être dynamique ou statique. Par défaut, elle est dynamique.

* Utilisez une [adresse IP statique][static-ip] si vous avez besoin d’une adresse IP non modifiable, par exemple pour créer un enregistrement A dans le DNS ou ajouter l’adresse IP dans une liste sécurisée.
* Vous pouvez également créer un nom de domaine complet (FQDN) pour l’adresse IP. Vous pouvez inscrire un [enregistrement CNAME][cname-record] dans le DNS qui pointe vers le nom de domaine complet (FQDN). Pour en savoir plus, voir la [création d’un nom de domaine complet dans le portail Azure][fqdn].

Tous les groupes de sécurité réseau contiennent un ensemble de [règles par défaut][nsg-default-rules], notamment une règle qui bloque tout le trafic Internet entrant. Les règles par défaut ne peuvent pas être supprimées, mais d’autres règles peuvent les remplacer. Pour autoriser le trafic Internet, créez des règles qui autorisent le trafic entrant vers des ports spécifiques, par exemple, le port 80 pour HTTP.  

Pour activer le protocole SSH, ajoutez au groupe de sécurité réseau une règle qui autorise le trafic entrant sur le port TCP 22.

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité

Pour effectuer une montée ou une descente en puissance, [modifiez la taille de la machine virtuelle][vm-resize]. 

Pour évoluer horizontalement, placez deux ou plusieurs machines virtuelles dans un groupe à haute disponibilité derrière un équilibreur de charge. Pour en savoir plus, voir [Exécution de plusieurs machines virtuelles sur Azure][multi-vm].

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Pour bénéficier d’une disponibilité plus élevée, vous devez déployer plusieurs machines virtuelles dans un groupe à haute disponibilité. Cela permet également un [contrat de niveau de service][vm-sla] (SLA) supérieur. 

Votre machine virtuelle peut être affectée par la [maintenance planifiée][planned-maintenance] ou la [maintenance non planifiée][manage-vm-availability]. Vous pouvez utiliser les [journaux de redémarrage de machine virtuelle][reboot-logs] pour déterminer si un redémarrage de la machine virtuelle a été provoqué par une maintenance planifiée.

Les disques durs virtuels sont stockés dans [Stockage Azure][azure-storage], lequel est répliqué à des fins de durabilité et de disponibilité.

Pour vous protéger contre la perte accidentelle de données pendant les opérations normales (par exemple, en cas d’erreur d’un utilisateur), vous devez également implémenter des sauvegardes ponctuelles, à l’aide de [captures instantanées d’objets blob][blob-snapshot] ou d’un autre outil.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

**Groupes de ressources.** Placez les ressources fortement couplées qui partagent le même cycle de vie dans un même [groupe de ressources][resource-manager-overview]. Les groupes de ressources vous permettent de déployer et de surveiller les ressources en tant que groupe et de répercuter les coûts de facturation par groupe de ressources. Vous pouvez également supprimer des ressources dans un ensemble, ce qui est très utile pour les déploiements de test. Nommez les ressources de façon explicite. Cela permet de localiser plus facilement une ressource spécifique et de mieux comprendre son rôle. Voir [Conventions d’affectation de noms recommandées pour les ressources Azure][naming conventions].

**SSH**. Avant de créer une machine virtuelle Linux, générez une paire de clés publique-privée RSA 2048 bits. Utilisez le fichier de clé publique lorsque vous créez la machine virtuelle. Pour en savoir plus, voir [Utilisation de SSH avec Linux et Mac sur Azure][ssh-linux].

**VM diagnostics.** Permet la surveillance et le diagnostic, avec notamment des indicateurs d’intégrité de base, des journaux d’infrastructure de diagnostic et des [diagnostics de démarrage][boot-diagnostics]. Les diagnostics de démarrage peuvent vous aider à identifier un problème de démarrage si votre machine virtuelle refuse de démarrer. Pour en savoir plus, voir [Activation de la surveillance et des diagnostics][enable-monitoring].  

La commande CLI suivante active les diagnostics :

```
azure vm enable-diag <resource-group> <vm-name>
```

**Arrêt d’une machine virtuelle.** Azure établit une distinction entre les états « arrêté » et « désalloué ». Vous payez lorsque l’état de la machine virtuelle est « arrêté », mais pas lorsque la machine virtuelle est désallouée.

Utilisez la commande CLI suivante pour désallouer une machine virtuelle :

```
azure vm deallocate <resource-group> <vm-name>
```

Le bouton **Arrêter** du portail Azure désalloue la machine virtuelle. Toutefois, si vous arrêtez la machine virtuelle via le système d’exploitation pendant que vous êtes connecté, la machine virtuelle est arrêtée mais *non* désallouée. Vous serez donc toujours facturé.

**Suppression d’une machine virtuelle.** La suppression d’une machine virtuelle n’entraîne pas celle des disques durs virtuels. Vous pouvez donc supprimer la machine virtuelle, sans risque de perdre des données. Toutefois, vous serez toujours facturé pour le stockage. Pour supprimer le disque dur virtuel, supprimez le fichier de [Stockage Blob][blob-storage].

Pour éviter toute suppression accidentelle, utilisez un [verrou de ressource][resource-lock] pour verrouiller tout le groupe de ressources ou des ressources individuelles, par exemple la machine virtuelle. 

## <a name="security-considerations"></a>Sécurité

Automatisez les mises à jour du système d’exploitation à l’aide de l’extension de machine virtuelle [OSPatching] . Installez cette extension lorsque vous configurez la machine virtuelle. Vous pouvez spécifier la fréquence d’installation des correctifs et si un redémarrage est nécessaire après la mise à jour corrective.

Utilisez le [contrôle d’accès en fonction du rôle][rbac] (RBAC) pour contrôler l’accès aux ressources Azure que vous déployez. Le contrôle RBAC vous permet d’affecter des rôles d’autorisation aux membres de votre équipe DevOps. Par exemple, le rôle Lecteur permet d’afficher des ressources Azure mais pas de les créer, gérer ou supprimer. Certains rôles sont spécifiques à des types de ressources Azure particuliers. Par exemple, le rôle Contributeur de machine virtuelle peut redémarrer ou désallouer une machine virtuelle, réinitialiser le mot de passe administrateur, créer une machine virtuelle, et ainsi de suite. D’autres [rôles RBAC intégrés][rbac-roles] peuvent être utiles dans cette architecture de référence, notamment [Utilisateur DevTest Lab][rbac-devtest] et [Collaborateur de réseau][rbac-network]. 

Un utilisateur peut être affecté à plusieurs rôles, et vous pouvez créer des rôles personnalisés pour d’autres autorisations plus affinées.

d> [!NOTE]
> Le contrôle RBAC ne limite pas les actions qu’un utilisateur connecté peut effectuer sur une machine virtuelle. Ces autorisations dépendent du type de compte installé sur le système d’exploitation invité.   
> 
> 

Utilisez les [journaux d’audit][audit-logs] pour voir les actions d’approvisionnement et d’autres événements concernant la machine virtuelle.

Utilisez [Azure Disk Encryption][disk-encryption] si vous devez chiffrer les disques du système d’exploitation et de données. 

## <a name="solution-deployment"></a>Déploiement de la solution
Un déploiement m A pour cette architecture de référence est disponible sur [GitHub][github-folder]. Il comprend un réseau virtuel, un groupe de sécurité réseau et une seule machine virtuelle. Pour déployer l’architecture, procédez comme suit : 

1. Cliquez avec le bouton droit sur le bouton ci-dessous, puis sélectionnez « Ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre ».
   [![Déploiement sur Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)
2. Une fois le lien ouvert dans le portail Azure, vous devez entrer des valeurs pour certains paramètres : 
   
   * Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres ; sélectionnez **Créer nouveau** et entrez `ra-single-vm-rg` dans la zone de texte.
   * Sélectionnez la région à partir de la zone déroulante **Emplacement**.
   * Ne modifiez pas les zones de texte **Template Root Uri** (Uri racine de modèle) ou **Parameter Root Uri** (Uri racine de paramètre).
.   * Sélectionnez **linux** dans la zone de liste déroulante **Type de système d’exploitation**.
   * Passez en revue les termes et conditions, puis cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**.
   * Cliquez sur le bouton **Acheter**.
3. Attendez la fin du déploiement.
4. Les fichiers de paramètres incluent un nom d’utilisateur administrateur et un mot de passe codés en dur ; il est vivement recommandé de les modifier immédiatement. Cliquez sur la machine virtuelle nommée `ra-single-vm0 ` dans le Portail Azure. Cliquez ensuite sur **Réinitialiser le mot de passe** dans la section **Support + dépannage**. Sélectionnez **Réinitialiser le mot de passe** dans la zone déroulante **Mode**, puis sélectionnez de nouveaux **nom d’utilisateur** et **mot de passe**. Cliquez sur le bouton **Mettre à jour** pour conserver les nouveaux nom d’utilisateur et mot de passe.

## <a name="next-steps"></a>Étapes suivantes
Pour augmenter la disponibilité, déployez au moins deux machines virtuelles derrière un équilibreur de charge. Pour plus d’informations, voir [Running multiple VMs on Azure (Exécution de plusieurs machines virtuelles sur Azure)][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]:../articles/virtual-machines/windows/create-availability-set.md
[azure-cli]: /cli/azure/get-started-with-az-cli2
[azure-linux]:../articles/virtual-machines/linux/overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/storage/storage-about-disks-and-vhds-linux.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]:../articles/virtual-machines/linux/portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]:../articles/virtual-machines/linux/manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]:../articles/virtual-machines/linux/planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-labs-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]:../articles/virtual-machines/linux/cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]:../articles/virtual-machines/linux/mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-account-limits]: ../articles/azure-subscription-service-limits.md#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]:../articles/virtual-machines/linux/sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]:../articles/virtual-machines/linux/change-vm-size.md
[vm-size-tables]:../articles/virtual-machines/windows/sizes.md#size-tables
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Architecture de machine virtuelle Linux unique dans Azure"

