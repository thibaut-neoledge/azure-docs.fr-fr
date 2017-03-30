

Les extensions de machine virtuelle peuvent vous aider à :

* modifier les fonctionnalités de sécurité et d'identité telles que la réinitialisation des valeurs de compte et l'utilisation de logiciels anti-programmes malveillants ;
* démarrer, arrêter ou configurer la surveillance et les diagnostics ;
* réinitialiser ou installer des fonctionnalités de connectivité, telles que RDP et SSH ;
* diagnostiquer, surveiller et gérer vos machines virtuelles.

Il existe de nombreuses autres fonctionnalités. De nouvelles fonctionnalités d’extension de machine virtuelle sont publiées régulièrement. Cet article décrit les agents de machine virtuelle Azure pour Windows et Linux, et la manière dont ils prennent en charge la fonctionnalité d'extension de machine virtuelle. Pour obtenir une liste des extensions de machine virtuelle par catégorie de fonctionnalité, consultez l’article [Fonctionnalités et extensions de machine virtuelle Azure](../articles/virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="azure-vm-agents-for-windows-and-linux"></a>Agents de machine virtuelle Azure pour Windows et Linux
L'agent de machines virtuelles Azure (VM Agent) est un processus léger et sécurisé qui installe, configure et supprime des extensions de machine virtuelle sur des instances de machines virtuelles Azure. L'agent de machine virtuelle joue le rôle du service de contrôle local sécurisé pour votre machine virtuelle Azure. Les extensions chargées par l'agent fournissent des fonctionnalités spécifiques pour augmenter votre productivité à l'aide de l'instance.

Il existe deux agents de machine virtuelle Azure, un pour les machines virtuelles Windows et l'autre pour les machines virtuelles Linux.

Si vous souhaitez qu’une instance de machine virtuelle utilise une ou plusieurs extensions de machine virtuelle, un agent de machine virtuelle doit avoir été installé. Une image de machine virtuelle créée à l’aide du portail Azure et une image du **Marketplace** installent automatiquement un agent de machine virtuelle dans le processus de création. Si une instance de machine virtuelle n’a pas d’agent de machine virtuelle, vous pouvez installer ce dernier après la création de l’instance de machine virtuelle. Vous pouvez également installer l’agent dans une image de machine virtuelle personnalisée que vous devez alors charger.

> [!IMPORTANT]
> Ces agents de machine virtuelle sont des services légers permettant la gestion sécurisée des instances de machine virtuelle. Il se peut que vous ne souhaitiez pas utiliser l'agent de machine virtuelle dans certains cas. Veillez alors à créer des machines virtuelles sur lesquelles l’agent de machine virtuelle n'est pas installé à l’aide de l’interface CLI Azure ou de PowerShell. Bien que l'agent de machine virtuelle puisse être supprimé physiquement, le comportement des extensions de machine virtuelle sur l'instance n'est pas défini. Par conséquent, la suppression d’un agent de machine virtuelle installé n’est pas prise en charge.
>

L'agent de machine virtuelle est activé dans les situations suivantes :

* Lorsque vous créez une instance d’une machine virtuelle à l’aide du portail Azure et que vous sélectionnez une image du **Marketplace**,
* Quand vous créez une instance d’une machine virtuelle à l’aide de l’applet de commande [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) ou [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx). Vous pouvez créer une machine virtuelle sans agent de machine virtuelle installé en ajoutant le paramètre **–DisableGuestAgent** à l’applet de commande [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx),

* Lorsque vous téléchargez et installez manuellement l’agent de machine virtuelle sur une instance de machine virtuelle existante et définissez la valeur **ProvisionGuestAgent** sur **true**. Vous pouvez utiliser cette technique pour les agents Windows et Linux, à l’aide d’une commande PowerShell ou d’un appel REST. (Si vous ne définissez pas la valeur **ProvisionGuestAgent** après l'installation manuelle de l'agent de machine virtuelle, l'ajout de l'agent de machine virtuelle n'est pas détecté correctement.) L’exemple de code suivant montre comment effectuer cette opération à l’aide de PowerShell où les arguments `$svc` et `$name` ont déjà été spécifiés :

      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

* Lorsque vous créez une image de machine virtuelle qui inclut un agent de machine virtuelle installé. Une fois que l’image avec l’agent de machine virtuelle existe, vous pouvez la télécharger sur Azure. Pour une machine virtuelle Windows, téléchargez le [fichier Windows VM Agent .msi](http://go.microsoft.com/fwlink/?LinkID=394789) et installez l’agent de machine virtuelle. Pour une machine virtuelle Linux, installez-le à partir du référentiel GitHub accessible à l’adresse <https://github.com/Azure/WALinuxAgent>. Pour plus d’informations sur la procédure d’installation de l’agent de machine virtuelle sur Linux, consultez le [Guide d’utilisateur de l’agent Linux Azure](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Dans PaaS, l’agent de machine virtuelle est appelé **WindowsAzureGuestAgent**. (Pour plus d’informations, consultez le billet de blog [Azure Role Architecture](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) (Architecture de rôle Azure).) L'agent de machine virtuelle pour les machines virtuelles de rôle peut maintenant ajouter des extensions aux machines virtuelles de service cloud de la même manière que pour les machines virtuelles persistantes. La différence majeure entre les extensions de machine virtuelle sur les machines virtuelles de rôle et les machines virtuelles persistantes se trouve lors de l’ajout des extensions de machine virtuelle. Avec les machines virtuelles de rôle, les extensions sont d’abord ajoutées au service cloud, puis aux déploiements au sein de ce service cloud.
>
> Utilisez l’applet de commande [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) pour répertorier toutes les extensions de machine virtuelle de rôle disponibles.
>
>

## <a name="find-add-update-and-remove-vm-extensions"></a>Rechercher, ajouter, mettre à jour et supprimer des extensions de machine virtuelle
Pour plus d’informations sur ces tâches, consultez l’article expliquant comment [ajouter, rechercher, mettre à jour et supprimer des extensions de machines virtuelles Azure](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
