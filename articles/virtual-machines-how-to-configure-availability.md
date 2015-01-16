<properties title="How to Configure An Availability Set for Virtual Machines" pageTitle="Configuration d'un groupe à haute disponibilité pour des machines virtuelles" description="Indique les opérations à effectuer pour configurer un groupe à haute disponibilité pour une machine virtuelle dans Azure" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="11/17/2014" ms.author="kathydav" />

#Configuration d'un groupe à haute disponibilité pour des machines virtuelles#

Un groupe à haute disponibilité maintient la disponibilité de vos machines virtuelles pendant une interruption (par exemple, en cas de maintenance). Le placement de deux machines virtuelles ou plus dans un groupe à haute disponibilité crée les conditions de redondance indispensables au maintien de la disponibilité des applications ou des services exécutés par votre machine virtuelle. Pour plus d'informations sur le fonctionnement, consultez [Gestion de la disponibilité des machines virtuelles] []. 

Il est recommandé d'utiliser des groupes à haute disponibilité et des points de terminaison à équilibrage de la charge pour garantir que votre application soit toujours disponible et qu'elle s'exécute correctement. Pour plus d'informations sur les points de terminaison à équilibrage de la charge, consultez la page [Équilibrage de charge pour les services d'infrastructure Azure] [].

Vous pouvez placer des machines virtuelles dans un groupe à haute disponibilité en utilisant une des deux options suivantes :

- [Option 1 : création simultanée d'une machine virtuelle et d'un groupe à haute disponibilité] []. Ajoutez ensuite de nouvelles machines virtuelles au groupe lorsque vous les créez.
- [Option 2 : ajout d'une machine virtuelle existante à un groupe à haute disponibilité] [].


>[WACOM.NOTE] Les machines virtuelles que vous voulez placer dans le même groupe à haute disponibilité doivent appartenir au même service cloud.   

## <a id="createset"> </a>Option 1 : création simultanée d'une machine virtuelle et d'un groupe à haute disponibilité##

Pour cela, vous pouvez utiliser le portail de gestion Azure ou des cmdlets Azure PowerShell. 

Pour utiliser le portail de gestion Azure :

1. Si ce n'est pas déjà fait, connectez-vous au [portail de gestion](http://manage.windowsazure.com)Azure.

2. Dans la barre de commandes, cliquez sur **Nouveau**.

3. Cliquez sur **Machine virtuelle**, puis sur  **À partir de la galerie**.

4. Utilisez les deux premiers écrans pour sélectionner une image, un nom d'utilisateur et un mot de passe, et ainsi de suite. Pour en savoir plus, consultez la page [Création d'une machine virtuelle exécutant Windows][].
 
5. Le troisième écran vous permet de configurer les ressources pour la mise en réseau, le stockage et la disponibilité. Effectuez les actions suivantes :
	 
	1. Sélectionnez l'option adaptée au service cloud. Laissez-la sur **Créer un nouveau service de cloud computing** (sauf si vous ajoutez cette nouvelle machine virtuelle à un service cloud de machine virtuelle existant). Ensuite, sous **Nom du cloud Service DNS**, tapez un nom. Ce nom fait alors partie de l'URI utilisé pour contacter la machine virtuelle. Le service cloud agit comme un groupe de communication et d'isolement. Toutes les machines virtuelles du même service cloud peuvent communiquer entre elles, être configurées pour équilibrer la charge et placées dans le même groupe à haute disponibilité. 

	2. Sous **Région/Groupe d'affinités/Réseau virtuel**, sélectionnez un réseau virtuel si vous envisagez d'en utiliser un. **Important** : si vous voulez qu'une machine virtuelle utilise un réseau virtuel, lorsque vous la créez, vous devez la lier au réseau virtuel. Vous ne pouvez pas joindre la machine virtuelle à un réseau virtuel après avoir créé celle-ci. Pour plus d'informations, consultez la page [Vue d'ensemble d'Azure Virtual Network][]. 
	
	3. Créez le groupe à haute disponibilité. Sous **Jeu de disponibilité**, laissez l'option **Créer un groupe à haute disponibilité** activée. Entrez ensuite le nom du groupe. 
	4. Créez les points de terminaison par défaut et ajoutez-en d'autres si nécessaire. Vous pourrez également ajouter d'autres points de terminaison par la suite. 

	![Create an availabililty set for a new VM](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png) 

6. Dans le quatrième écran, choisissez les extensions que vous voulez installer. Les extensions procurent des fonctionnalités qui facilitent la gestion de la machine virtuelle, telles que l'exécution de logiciels anti-programmes malveillants ou la réinitialisation de mots de passe. Pour en savoir plus, consultez la page [Agent de machine virtuelle Azure et extensions de machine virtuelle](http://go.microsoft.com/fwlink/p/?LinkId=XXX).

7.	Cliquez sur la flèche pour créer la machine virtuelle et le groupe à haute disponibilité.

	Dans le tableau de bord de la nouvelle machine virtuelle, vous pouvez cliquer sur **Configurer** pour voir que la machine virtuelle appartient au nouveau groupe à haute disponibilité.

Pour utiliser les cmdlets Azure :

1.	Ouvrez une session Azure PowerShell et exécutez des commandes similaires à celles des exemples ci-dessous. Remarque : ces exemples supposent que vous êtes en train de créer la machine virtuelle, le service cloud et le groupe à haute disponibilité.

2.	Obtenez le nom de l'image que vous voulez utiliser pour créer la machine virtuelle et enregistrez-le dans une variable. L'exemple de commande suivant utilise le numéro d'index pour obtenir la propriété ImageName de l'objet image. Il part du principe que vous connaissez le numéro d'index actuel de l'image souhaitée et que vous remplacerez &lt;index_number&gt; par ce numéro. <br>

	`C:\PS> $image = (Get-AzureVMImage)[<index_number>].ImageName`

	>[WACOM.NOTE] Exécutez `Get-AzureVMImage` sans paramètre pour obtenir la liste de toutes les images disponibles pour votre abonnement. Cette commande risque de retourner une grande liste. Pour la raccourcir, utilisez des propriétés telles que le nom de famille d'image. Pour obtenir des conseils et des exemples qui illustrent comment rechercher une image spécifique, consultez [Gérer des images à l'aide de Windows PowerShell](http://msdn.microsoft.com/fr-fr/library/azure/dn790330.aspx).

3.	Spécifiez la configuration de la nouvelle machine virtuelle et utilisez le pipeline pour transmettre un objet de configuration à la cmdlet qui crée la machine virtuelle. Veillez à remplacer les espaces réservés, tels que &lt;VmName&gt; et &lt;VmSize&gt;, par vos propres valeurs.

	`C:\PS> New-AzureVMConfig -Name "<VmName>" -InstanceSize <VmSize> -AvailabilitySetName "<SetName>" -ImageName $image | Add-AzureProvisioningConfig -Windows -AdminUsername "<UserName>" -Password "<MyPassword>" | Add-AzureDataDisk -CreateNew -DiskSizeInGB 50 -DiskLabel 'datadisk1' -LUN 0 | New-AzureVM -ServiceName "<MySvc1>" `

## <a id="addmachine"> </a>Option 2 : ajout d'une machine virtuelle existante à un groupe à haute disponibilité##

Dans le portail de gestion, vous pouvez ajouter des machines virtuelles existantes à un groupe à haute disponibilité existant ou en créer un pour ces machines. (N'oubliez pas que les machines virtuelles doivent appartenir au même service cloud). Les opérations à effectuer sont pratiquement identiques. Dans Azure PowerShell, vous pouvez ajouter la machine virtuelle à un groupe à haute disponibilité existant. 

1. Si ce n'est pas déjà fait, connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com)Azure.

2. Dans la barre de navigation, cliquez sur **Machines virtuelles**.

3. Dans la liste des machines virtuelles, sélectionnez une machine virtuelle que vous voulez ajouter au groupe. Cliquez sur la ligne de la machine virtuelle pour ouvrir son tableau de bord.

4. Sous les onglets sous le nom de la machine virtuelle, cliquez sur  **Configurer**. 

5. Dans la zone Paramètres, recherchez **Jeu de disponibilité**. Effectuez l'une des actions suivantes :

	A. Sélectionnez **Créer un groupe à haute disponibilité** et tapez un nom pour le groupe.

	B. Sélectionnez **Sélectionner un groupe à haute disponibilité** et sélectionnez un groupe dans la liste.

	![Create an availabililty set for an existing VM](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png) 

6. Cliquez sur **Enregistrer**.

Pour utiliser les cmdlets Azure :

Ouvrez une session Azure PowerShell et exécutez la commande suivante. Veillez à remplacer les espaces réservés, tels que &&lt;VmCloudServiceName&gt; et &lt;VmName&gt;, par vos propres valeurs.

	C:\PS> Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<MyAvSet>" | Update-AzureVM

>[WACOM.NOTE] Vous pouvez redémarrer la machine virtuelle pour terminer l'ajout au groupe à haute disponibilité.


##Ressources supplémentaires
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
[À propos des paramètres de configuration de machine virtuelle Azure]: http://msdn.microsoft.com/fr-fr/library/azure/dn763935.aspx


<!--HONumber=35.1-->
