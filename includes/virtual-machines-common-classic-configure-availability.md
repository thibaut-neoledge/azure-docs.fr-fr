


Un groupe à haute disponibilité maintient la disponibilité de vos machines virtuelles pendant une interruption (par exemple, en cas de maintenance). Le placement de deux machines virtuelles ou plus dans un groupe à haute disponibilité crée les conditions de redondance indispensables au maintien de la disponibilité des applications ou des services exécutés par votre machine virtuelle. Pour plus d’informations sur cette procédure, voir la rubrique [Gestion de la disponibilité des machines virtuelles][].

Il est recommandé d’utiliser des groupes à haute disponibilité et des points de terminaison à équilibrage de charge pour garantir que votre application soit toujours disponible et qu’elle s’exécute correctement. Pour plus d'informations sur les points de terminaison à équilibrage de la charge, consultez la page [Équilibrage de charge pour les services d'infrastructure Azure][].

Dans le modèle de déploiement classique, vous pouvez placer des machines virtuelles dans un groupe à haute disponibilité en utilisant l’une des deux options suivantes :

- [Option 1: Créer simultanément une machine virtuelle et un groupe à haute disponibilité][]. Ensuite, ajouter de nouvelles machines virtuelles à l’ensemble lorsque vous créez ces ordinateurs virtuels.
- [Option 2 : Ajouter une machine virtuelle existante à un groupe à haute disponibilité][].

>[AZURE.NOTE] Dans le modèle classique, les machines virtuelles que vous voulez placer dans le même groupe à haute disponibilité doivent appartenir au même service cloud.

## <a id="createset"> </a>Option 1 : Créer simultanément une machine virtuelle et un groupe à haute disponibilité.##

Pour cela, vous pouvez utiliser le portail Azure Classic ou des commandes Azure PowerShell.

Pour utiliser le portail Azure Classic :

1. Si ce n’est pas déjà fait, connectez-vous au portail Azure Classic.

2. Dans la barre de commandes, cliquez sur **New**.

3. Cliquez sur **Virtual Machine**, puis sur **From Gallery**.

4. Utilisez les deux premiers écrans pour sélectionner une image, un nom d’utilisateur et un mot de passe, et ainsi de suite. Pour en savoir plus, consultez la rubrique [Création d'une machine virtuelle exécutant Windows][].

5. Le troisième écran vous permet de configurer des ressources pour la mise en réseau, le stockage et la disponibilité. Effectuez les actions suivantes :

	1. Utilisez le nom de service cloud approprié. Gardez la valeur **Créer un nouveau service cloud** activée (sauf si vous ajoutez cette nouvelle machine virtuelle à un service cloud de machine virtuelle existant). Ensuite, sous **Nom du cloud Service DNS**, tapez un nom. Ce nom fait alors partie de l’URI utilisé pour contacter la machine virtuelle. Le service cloud agit comme un groupe de communication et d’isolement. Toutes les machines virtuelles du même service cloud peuvent communiquer entre elles, être configurées pour équilibrer la charge et placées dans le même groupe à haute disponibilité.

	2. Sous **Région/Groupe d’affinités/Réseau virtuel**, sélectionnez un réseau virtuel si vous envisagez d’en utiliser un. **Important** : si vous souhaitez qu’une machine virtuelle utilise un réseau virtuel, vous devez l’associer au réseau virtuel lorsque vous la créez. Vous ne pouvez pas associer la machine virtuelle à un réseau virtuel après l’avoir créée. Pour en savoir plus, voir la page [Vue d’ensemble d’Azure Virtual Network][].

	3. Créez le groupe à haute disponibilité. Sous **Jeu de disponibilité**, laissez l’option **Créer un groupe à haute disponibilité** activée. Entrez ensuite le nom du groupe.

	4. Créez les points de terminaison par défaut et ajoutez-en d’autres si nécessaire. Vous pourrez également ajouter d’autres points de terminaison par la suite.

	![Création d’un groupe à haute disponibilité pour une nouvelle machine virtuelle](./media/virtual-machines-common-classic-configure-availability/VMavailabilityset.png)

6. Sur le quatrième écran, cliquez sur les extensions que vous voulez installer. Les extensions procurent des fonctionnalités qui facilitent la gestion de la machine virtuelle, telles que l’exécution de logiciels anti-programmes malveillants ou la réinitialisation de mots de passe. Pour en savoir plus, voir la page [Agent de machine virtuelle Azure et extensions de machine virtuelle](../articles/virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md).

7.	Cliquez sur la flèche pour créer la machine virtuelle et le groupe à haute disponibilité.

	Dans le tableau de bord de la nouvelle machine virtuelle, vous pouvez cliquer sur **Configurer** pour voir que la machine virtuelle appartient au nouveau groupe à haute disponibilité.

Pour utiliser des commandes Azure PowerShell afin de créer une machine virtuelle Azure et l’ajouter à un groupe à haute disponibilité nouveau ou existant, voir les rubriques suivantes :


- [Utiliser Azure PowerShell pour créer et préconfigurer des machines virtuelles Linux](../articles/virtual-machines/virtual-machines-linux-classic-createpowershell.md)
- [Utiliser Azure PowerShell pour créer et préconfigurer des machines virtuelles Windows](../articles/virtual-machines/virtual-machines-windows-classic-create-powershell.md)

## <a id="addmachine"> </a>Option 2 : Ajouter une machine virtuelle existante à un groupe à haute disponibilité##

Dans le portail Azure Classic, vous pouvez ajouter des machines virtuelles existantes à un groupe à haute disponibilité existant, ou en créer un pour ces machines. (N’oubliez pas que les machines virtuelles dans un même groupe à haute disponibilité doivent appartenir au même service cloud). Les opérations à effectuer sont pratiquement identiques. Avec Azure PowerShell, vous pouvez ajouter la machine virtuelle à un groupe à haute disponibilité existant.

1. Si ce n’est pas déjà fait, connectez-vous au portail Azure Classic.

2. Dans la barre de commandes, cliquez sur **Machines virtuelles**.

3. Dans la liste des machines virtuelles, sélectionnez le nom de la machine virtuelle que vous voulez ajouter au groupe.

4. Dans les onglets sous le nom de la machine virtuelle, cliquez sur **Configurer**.

5. Dans la zone Paramètres, recherchez **Jeu de disponibilité**. Effectuez l’une des actions suivantes :

	A. Sélectionnez **Créer un groupe à haute disponibilité**, puis saisissez un nom pour le groupe.

	B. Sélectionnez **Sélectionner un groupe à haute disponibilité**, puis choisissez un groupe dans la liste.

	![Création d’un groupe à haute disponibilité pour une machine virtuelle existante](./media/virtual-machines-common-classic-configure-availability/VMavailabilityExistingVM.png)

6. Cliquez sur **Save**.

Pour utiliser les commandes Azure PowerShell, ouvrez une session Azure PowerShell de niveau administrateur et exécutez la commande suivante. Pour les espaces réservés (tels que &lt;VmCloudServiceName&gt;), remplacez tout ce qui se trouve entre guillemets, y compris les caractères < and >, par les noms adéquats.

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE] Il se peut que la machine virtuelle doive être redémarrée pour terminer son ajout au groupe à haute disponibilité.

## Ressources supplémentaires

[Articles pour les machines virtuelles dans la gestion des services]

<!-- LINKS -->
[Option 1: Créer simultanément une machine virtuelle et un groupe à haute disponibilité]: #createset
[Option 2 : Ajouter une machine virtuelle existante à un groupe à haute disponibilité]: #addmachine

[Équilibrage de charge pour les services d'infrastructure Azure]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Gestion de la disponibilité des machines virtuelles]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md

[Création d'une machine virtuelle exécutant Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Vue d’ensemble d’Azure Virtual Network]: ../articles/virtual-network/virtual-networks-overview.md
[Articles pour les machines virtuelles dans la gestion des services]: https://azure.microsoft.com/documentation/articles/?tag=azure-service-management&service=virtual-machines

<!---HONumber=AcomDC_0330_2016-->