<properties 
	pageTitle="Configuration d'un groupe à haute disponibilité pour des machines virtuelles" 
	description="Présente les étapes pour configurer un groupe à haute disponibilité pour un ordinateur virtuel, nouveau ou existant, dans Azure en utilisant les commandes du portail de gestion Azure et d'Azure PowerShell" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="kathydav"/>

# Configuration d'un groupe à haute disponibilité pour des machines virtuelles#


Un groupe à haute disponibilité maintient la disponibilité de vos machines virtuelles pendant une interruption (par exemple, en cas de maintenance). Le placement de deux machines virtuelles ou plus dans un groupe à haute disponibilité crée les conditions de redondance indispensables au maintien de la disponibilité des applications ou des services exécutés par votre machine virtuelle. Pour plus d'informations sur cette procédure, consultez la rubrique [Gestion de la disponibilité des machines virtuelles][]. 

Il est recommandé d'utiliser des groupes à haute disponibilité et des points de terminaison à équilibrage de charge pour garantir que votre application soit toujours disponible et qu'elle s'exécute correctement. Pour plus d'informations sur les points de terminaison à équilibrage de la charge, consultez la page [Équilibrage de charge pour les services d'infrastructure Azure].

Vous pouvez placer des machines virtuelles dans un groupe à haute disponibilité en utilisant une des deux options suivantes :

- [Option 1 : créer simultanément une machine virtuelle et un groupe à haute disponibilité][Option 1 : création simultanée d'une machine virtuelle et d'un groupe à haute disponibilité]. Ensuite, ajouter de nouvelles machines virtuelles à l'ensemble lorsque vous créez ces ordinateurs virtuels.
- [Option 2 : ajouter une machine virtuelle existante à un groupe à haute disponibilité][Option 2 : ajout d'une machine virtuelle existante à un groupe à haute disponibilité].


>[AZURE.NOTE] Les machines virtuelles que vous voulez placer dans le même groupe à haute disponibilité doivent appartenir au même service cloud.   

## <a id="createset"> </a>Option 1 : création simultanée d'une machine virtuelle et d'un groupe à haute disponibilité##

Pour cela, vous pouvez utiliser le portail de gestion Azure ou des commandes Azure PowerShell. 

Pour utiliser le portail de gestion Azure :

1. Si ce n'est pas déjà fait, connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com).

2. Dans la barre de commandes, cliquez sur **Nouveau**.

3. Cliquez sur **Machine virtuelle**, puis sur **À partir de la galerie**.

4. Utilisez les deux premiers écrans pour sélectionner une image, un nom d'utilisateur et un mot de passe, et ainsi de suite. Pour en savoir plus, consultez la page [Création d'une machine virtuelle exécutant Windows][].
 
5. Le troisième écran vous permet de configurer les ressources pour la mise en réseau, le stockage et la disponibilité. Effectuez les actions suivantes :
	 
	1. Sélectionnez l'option adaptée au service cloud. Laissez-la sur **Créer un nouveau service de cloud computing** (sauf si vous ajoutez cette nouvelle machine virtuelle à un service cloud de machine virtuelle existant). Ensuite, sous **Nom DNS du service cloud**, tapez un nom. Ce nom fait alors partie de l'URI utilisé pour contacter la machine virtuelle. Le service cloud agit comme un groupe de communication et d'isolement. Toutes les machines virtuelles du même service cloud peuvent communiquer entre elles, être configurées pour équilibrer la charge et placées dans le même groupe à haute disponibilité. 

	2. Sous **Région/Groupe d'affinités/Réseau virtuel**, sélectionnez un réseau virtuel si vous envisagez d'en utiliser un. **Important** : si vous souhaitez qu'une machine virtuelle utilise un réseau virtuel, vous devez la lier au réseau virtuel lorsque vous la créez. Vous ne pouvez pas joindre la machine virtuelle à un réseau virtuel après avoir créé celle-ci. Pour plus d'informations, consultez la page [Vue d'ensemble d'Azure Virtual Network][]. 
	
	3. Créez le groupe à haute disponibilité. Sous **Jeu de disponibilité**, laissez l'option **Créer un groupe à haute disponibilité** activée. Entrez ensuite le nom du groupe. 

	4. Créez les points de terminaison par défaut et ajoutez-en d'autres si nécessaire. Vous pourrez également ajouter d'autres points de terminaison par la suite. 

	![Création d'un groupe à haute disponibilité pour une nouvelle machine virtuelle](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png) 

6. Dans le quatrième écran, choisissez les extensions que vous voulez installer. Les extensions procurent des fonctionnalités qui facilitent la gestion de la machine virtuelle, telles que l'exécution de logiciels anti-programmes malveillants ou la réinitialisation de mots de passe. Pour plus d'informations, consultez la page [Agent de machine virtuelle Azure et extensions de machine virtuelle](http://go.microsoft.com/fwlink/p/?LinkId=XXX).

7.	Cliquez sur la flèche pour créer la machine virtuelle et le groupe à haute disponibilité.

	Dans le tableau de bord de la nouvelle machine virtuelle, vous pouvez cliquer sur **Configurer** pour voir que la machine virtuelle appartient au nouveau groupe à haute disponibilité.

Pour utiliser les commandes Azure PowerShell afin de créer une machine virtuelle Azure et l'ajouter à un groupe à haute disponibilité nouveau ou existant, consultez les rubriques suivantes :

- [Utilisation d'Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Windows](../virtual-machines-ps-create-preconfigure-windows-vms/)
- [Utilisation d'Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Linux](../virtual-machines-ps-create-preconfigure-linux-vms/)

	>[AZURE.NOTE] Exécutez `Get-AzureVMImage` sans paramètre pour obtenir la liste de toutes les images disponibles pour votre abonnement. Cette commande risque de retourner une grande liste. Pour la raccourcir, utilisez des propriétés telles que le nom de famille d'image. Pour obtenir des conseils et des exemples qui illustrent comment rechercher une image spécifique, consultez [Gérer des images à l'aide de Windows PowerShell](http://msdn.microsoft.com/library/azure/dn790330.aspx).

3.	Spécifiez la configuration de la nouvelle machine virtuelle et utilisez le pipeline pour transmettre un objet de configuration à la cmdlet qui crée la machine virtuelle. Veillez à remplacer les espaces réservés par vos propres valeurs, par exemple  &lt;VmName&gt; et &lt;VmSize&gt;.

	`C:\PS> New-AzureVMConfig -Name "<VmName>" -InstanceSize <VmSize> -AvailabilitySetName "<SetName>" -ImageName $image | Add-AzureProvisioningConfig -Windows -AdminUsername "<UserName>" -Password "<MyPassword>" | Add-AzureDataDisk -CreateNew -DiskSizeInGB 50 -DiskLabel 'datadisk1' -LUN 0 | New-AzureVM -ServiceName "<MySvc1>" `

## <a id="addmachine"> </a>Option 2 : ajout d'une machine virtuelle existante à un groupe à haute disponibilité##

Dans le portail de gestion, vous pouvez ajouter des machines virtuelles existantes à un groupe à haute disponibilité existant ou en créer un pour ces machines. (N'oubliez pas que les machines virtuelles dans un même groupe à haute disponibilité doivent appartenir au même service cloud). Les opérations à effectuer sont pratiquement identiques. Avec Azure PowerShell, vous pouvez ajouter la machine virtuelle à un groupe à haute disponibilité existant. 

1. Si ce n'est pas déjà fait, connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com)

2. Dans la barre de navigation, cliquez sur **Machines virtuelles**.

3. Dans la liste des machines virtuelles, cliquez sur le nom d'une machine virtuelle que vous voulez ajouter au groupe.

4. Dans les onglets sous le nom de la machine virtuelle, cliquez sur **Configurer**. 

5. Dans la zone Paramètres, recherchez **Jeu de disponibilité**. Effectuez l'une des actions suivantes :

	A. Sélectionnez **Créer un groupe à haute disponibilité** et tapez un nom pour le groupe.

	B. Sélectionnez **Sélectionner un groupe à haute disponibilité** et sélectionnez un groupe dans la liste.

	![Création d'un groupe à haute disponibilité pour une machine virtuelle existante](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png) 

6. Cliquez sur **Enregistrer**.

Pour utiliser les commandes Azure PowerShell, ouvrez une session Azure PowerShell de niveau administrateur et exécutez la commande suivante. Pour les espaces réservés (tels que &lt;VmCloudServiceName&gt;), remplacez tout ce qui se trouve entre guillemets, y compris les caractères < et >, par les noms adéquats.

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE] Vous pouvez redémarrer la machine virtuelle pour terminer l'ajout au groupe à haute disponibilité.


## Ressources supplémentaires
[À propos des paramètres de configuration de machine virtuelle Azure]

[Option 1 : création simultanée d'une machine virtuelle et d'un groupe à haute disponibilité]: #createset
[Option 2 : ajout d'une machine virtuelle existante à un groupe à haute disponibilité]: #addmachine

<!-- LINKS -->
[Équilibrage de charge pour les services d'infrastructure Azure]: ../virtual-machines-load-balance
[Gestion de la disponibilité des machines virtuelles]: ../virtual-machines-manage-availability
[Création d'une machine virtuelle exécutant Windows]: ../virtual-machines-windows-tutorial
[Vue d'ensemble d'Azure Virtual Network]: http://go.microsoft.com/fwlink/p/?linkid=294063
[À propos des groupes d'affinités pour Virtual Network]: http://msdn.microsoft.com/library/windowsazure/jj156085.aspx
[Connexion des machines virtuelles dans un service cloud]: ../virtual-machines-connect-cloud-service
[À propos des paramètres de configuration de machine virtuelle Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx
[Connexion des machines virtuelles dans un service cloud]: ../cloud-services-connect-virtual-machine
[À propos des paramètres de configuration de machine virtuelle Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx


<!--HONumber=47-->
