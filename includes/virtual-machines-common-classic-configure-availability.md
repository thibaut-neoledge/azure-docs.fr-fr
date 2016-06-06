


Un groupe à haute disponibilité maintient la disponibilité de vos machines virtuelles pendant une interruption (par exemple, en cas de maintenance). Le placement de deux machines virtuelles ou plus dans un groupe à haute disponibilité crée les conditions de redondance indispensables au maintien de la disponibilité des applications ou des services exécutés par votre machine virtuelle. Pour plus d’informations sur cette procédure, voir la rubrique [Gestion de la disponibilité des machines virtuelles][].

Il est recommandé d’utiliser des groupes à haute disponibilité et des points de terminaison à équilibrage de charge pour garantir que votre application soit toujours disponible et qu’elle s’exécute correctement. Pour plus d'informations sur les points de terminaison à équilibrage de la charge, consultez la page [Équilibrage de charge pour les services d'infrastructure Azure][].

Vous pouvez ajouter des machines virtuelles classiques dans un groupe à haute disponibilité en utilisant l’une des deux options suivantes :

- [Option 1: Créer simultanément une machine virtuelle et un groupe à haute disponibilité][]. Ensuite, ajouter de nouvelles machines virtuelles à l’ensemble lorsque vous créez ces ordinateurs virtuels.
- [Option 2 : Ajouter une machine virtuelle existante à un groupe à haute disponibilité][].

>[AZURE.NOTE] Dans le modèle classique, les machines virtuelles que vous voulez placer dans le même groupe à haute disponibilité doivent appartenir au même service cloud.

## <a id="createset"> </a>Option 1 : Créer simultanément une machine virtuelle et un groupe à haute disponibilité.##

Pour cela, vous pouvez utiliser le portail Azure ou des commandes Azure PowerShell.

Pour utiliser le portail Azure :

1. Si ce n’est pas déjà fait, connectez-vous au portail Azure.

2. Dans le menu hub, cliquez sur **+ Nouveau**, puis sur **Machines virtuelles**.
    
    ![Texte de remplacement d’image](./media/virtual-machines-common-classic-configure-availability/ChooseVMImage.png)

3. Sélectionner l’image de machine virtuelle Marketplace que vous voulez utiliser. Vous pouvez choisir de créer une machine virtuelle Linux ou Windows.

4. Pour la machine virtuelle sélectionnée, vérifiez que le modèle de déploiement est défini sur **Classique**, puis cliquez sur **Créer**.
    
    ![Texte de remplacement d’image](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)

5. Entrez le nom de la machine virtuelle, le nom d’utilisateur et le mot de passe (pour les machines Windows) ou la clé publique SSH (pour les machines Linux).

6. Choisissez une taille de machine virtuelle, puis cliquez sur **Sélectionner** pour continuer.

7. Choisissez **Configuration facultative > Groupe à haute disponibilité**, et sélectionnez le groupe à haute disponibilité auquel vous voulez ajouter la machine virtuelle.
    
    ![Texte de remplacement d’image](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png)

8. Passez en revue vos paramètres de configuration. Une fois ces opérations effectuées, cliquez sur **Créer**.

9. Pendant qu’Azure crée votre machine virtuelle, vous pouvez suivre la progression de cette opération dans le menu Hub sous **Machines virtuelles**.

Pour utiliser des commandes Azure PowerShell afin de créer une machine virtuelle Azure et l’ajouter à un groupe à haute disponibilité nouveau ou existant, voir [Utiliser Azure PowerShell pour créer et préconfigurer des machines virtuelles Windows](../articles/virtual-machines/virtual-machines-windows-classic-create-powershell.md)

## <a id="addmachine"> </a>Option 2 : Ajouter une machine virtuelle existante à un groupe à haute disponibilité##

Dans le portail Azure, vous pouvez ajouter des machines virtuelles classiques existantes à un groupe à haute disponibilité existant, ou en créer un pour ces machines. (N’oubliez pas que les machines virtuelles dans un même groupe à haute disponibilité doivent appartenir au même service cloud). Les opérations à effectuer sont pratiquement identiques. Avec Azure PowerShell, vous pouvez ajouter la machine virtuelle à un groupe à haute disponibilité existant.

1. Si ce n’est pas déjà fait, connectez-vous au portail Azure.

2. Dans le menu hub, cliquez sur **Machines virtuelles (classiques)**.
    
    ![Texte de remplacement d’image](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)

3. Dans la liste des machines virtuelles, sélectionnez le nom de la machine virtuelle que vous voulez ajouter au groupe.

4. Choisissez **Groupe à haute disponibilité** dans les **Paramètres** de la machine virtuelle.
    
    ![Texte de remplacement d’image](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)

5. Sélectionner le groupe à haute disponibilité auquel vous voulez ajouter la machine virtuelle. La machine virtuelle doit appartenir au même service cloud que le groupe à haute disponibilité.
    
    ![Texte de remplacement d’image](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)

6. Cliquez sur **Save**.

Pour utiliser les commandes Azure PowerShell, ouvrez une session Azure PowerShell de niveau administrateur et exécutez la commande suivante. Pour les espaces réservés (tels que &lt;VmCloudServiceName&gt;), remplacez tout ce qui se trouve entre guillemets, y compris les caractères < and >, par les noms adéquats.

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE] Il se peut que la machine virtuelle doive être redémarrée pour terminer son ajout au groupe à haute disponibilité.

## Ressources supplémentaires

[Articles pour les machines virtuelles classiques][]

<!-- LINKS -->
[Option 1: Créer simultanément une machine virtuelle et un groupe à haute disponibilité]: #createset
[Option 2 : Ajouter une machine virtuelle existante à un groupe à haute disponibilité]: #addmachine

[Équilibrage de charge pour les services d'infrastructure Azure]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Gestion de la disponibilité des machines virtuelles]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md
[Articles pour les machines virtuelles classiques]: https://azure.microsoft.com/documentation/articles/?tag=azure-service-management&service=virtual-machines

<!---HONumber=AcomDC_0525_2016-->