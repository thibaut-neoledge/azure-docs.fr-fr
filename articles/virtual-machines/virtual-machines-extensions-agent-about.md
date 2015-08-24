<properties 
 pageTitle="À propos de l'agent de machine virtuelle et des extensions | Microsoft Azure" 
 description="Propose une vue d'ensemble de l'agent et des extensions et indique comment installer l'agent." 
 services="virtual-machines" 
 documentationCenter="" 
 authors="squillace" 
 manager="timlt" 
 editor=""/>
<tags 
ms.service="virtual-machines" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.tgt_pltfrm="vm-multiple" 
 ms.workload="infrastructure-services"
 ms.date="03/10/2015" 
 ms.author="kathydav"/>
#À propos de l'agent de machine virtuelle et des extensions
L'Agent de machine virtuelle Azure (VM Agent) est utilisé pour installer, configurer, gérer et exécuter des extensions de machine virtuelle Azure (VM Extensions). Les extensions de machine virtuelle fournissent des fonctionnalités dynamiques fournies par Microsoft et des fournisseurs tiers. L'agent et les extensions sont ajoutées principalement dans le portail de gestion, mais vous pouvez également utiliser les applets de commande [Powershell](../install-configure-powershell.md) ou [xplat-cli](virtual-machines-command-line-tools.md) pour les ajouter et les configurer lorsque vous créez une machine virtuelle ou travaillez avec des machines virtuelles existantes. Les extensions de machine virtuelle prennent en charge entre autres [le débogage distant avec Visual Studio](https://msdn.microsoft.com/library/y7f5zaaa.aspx), [System Center 2012](http://social.technet.microsoft.com/wiki/contents/articles/18274.system-center-2012-r2-virtual-machine-role-authoring-guide-resource-extension-package.aspx), [les diagnostics Microsoft Azure](http://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) et [Docker](virtual-machines-docker-vm-extension.md) .

Les extensions de machine virtuelle peuvent vous aider à :

-   modifier les fonctionnalités de sécurité et d'identité telles que la réinitialisation des valeurs de compte et l'utilisation de logiciels anti-programmes malveillants ; 
-   démarrer, arrêter ou configurer la surveillance et les diagnostics ; 
-   réinitialiser ou installer des fonctionnalités de connectivité, telles que RDP et SSH ;
-   diagnostiquer, surveiller et gérer vos machines virtuelles.

Il existe de nombreuses autres fonctionnalités, et de nouvelles fonctionnalités sont régulièrement ajoutées aux extensions de machine virtuelle. Cet article décrit les agents de machine virtuelle Azure pour Windows et Linux, et la manière dont ils prennent en charge la fonctionnalité d'extension de machine virtuelle. Pour obtenir une liste des extensions de machine virtuelle par catégorie de fonctionnalité, consultez la rubrique [Fonctionnalités et extensions de machine virtuelle Azure](https://msdn.microsoft.com/library/dn606311.aspx).

##Agents de machine virtuelle Azure pour Windows et Linux

L'agent de machines virtuelles Azure (VM Agent) est un processus léger et sécurisé qui installe, configure et supprime des extensions de machine virtuelle sur des instances de machines virtuelles Azure à partir de la galerie d'images et sur des instances de machine virtuelle personnalisées si l'agent de machine virtuelle est installé. L'agent de machine virtuelle joue le rôle du service de contrôle local sécurisé pour votre machine virtuelle Azure. Les extensions chargées par l'agent fournissent des fonctionnalités spécifiques pour augmenter votre productivité à l'aide de l'instance.

Il existe deux agents de machine virtuelle Azure, un pour les machines virtuelles Windows et l'autre pour les machines virtuelles Linux. Par défaut, l'agent de machine virtuelle est installé automatiquement lorsque vous créez une machine virtuelle à partir de la galerie d'images, mais vous pouvez également l'installer une fois l'instance créée ou l'installer dans une image de machine virtuelle personnalisée que vous téléchargez ensuite vous-même.

>[AZURE.IMPORTANT]Ces agents de machine virtuelle sont des services légers permettant la gestion sécurisée des instances de machine virtuelle. Il se peut que vous ne souhaitiez pas utiliser l'agent de machine virtuelle dans certains cas. Veillez alors à créer des machines virtuelles sur lesquelles l'agent de machine virtuelle n'est pas installé. Bien que l'agent de machine virtuelle puisse être supprimé physiquement, le comportement de toute extension de machine virtuelle sur l'instance n'est pas défini. Par conséquent, la suppression de l'agent de machine virtuelle après son installation n'est pas actuellement prise en charge.

L'agent de machine virtuelle est activé dans les situations suivantes :

-   Lorsque vous créez une instance d'un ordinateur virtuel à l'aide de la méthode **Création rapide** dans le portail de gestion ou à l'aide de la méthode **Création personnalisée** dans le portail de gestion. Assurez-vous que la case à cocher **Installer l'agent de machine virtuelle** est sélectionnée (comme indiqué dans l'image ci-dessous). Pour plus d'informations, consultez la rubrique [Création d'une machine virtuelle personnalisée](virtual-machines-create-custom.md).

    ![Case à cocher Agent de machine virtuelle](./media/virtual-machines-extensions-agent-about/IC719409.png "Case à cocher Agent de machine virtuelle")

-   Lorsque vous créez une instance d'une machine virtuelle à l'aide de l'applet de commande [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) ou [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx). Vous pouvez créer une machine virtuelle sans l'Agent de machine virtuelle installé en ajoutant le paramètre **–DisableGuestAgent** à l'applet de commande [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

-   En téléchargeant et en installant manuellement l'Agent de machine virtuelle (version Linux ou Windows) sur une machine virtuelle existante d'instance et en définissant ensuite la valeur **ProvisionGuestAgent** sur **true** à l'aide de Powershell ou d'un appel REST. (Si vous ne définissez pas cette valeur après l'installation manuelle de l'Agent de machine virtuelle, l'ajout de l'Agent de machine virtuelle n'est pas détecté correctement). L'exemple de code suivant montre comment effectuer cette opération à l'aide de PowerShell où les arguments `$svc` et `$name` ont déjà été spécifiés.

        $vm = Get-AzureVM –serviceName $svc –Name $name
        $vm.VM.ProvisionGuestAgent = $TRUE
        Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

-   En créant une image de machine virtuelle disposant de l'agent de machine virtuelle installé avant son téléchargement dans Azure. Pour une machine virtuelle Windows, téléchargez le [fichier Windows VM Agent .msi](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) et installez l'agent de machine virtuelle. Pour une machine virtuelle Linux, installez-le depuis <https://github.com/Azure/WALinuxAgent>. Pour plus d'informations sur la manière d'installer l'agent de machine virtuelle sur Linux, consultez le [Guide de l'utilisateur de l'agent de machine virtuelle Linux Azure (Azure Linux VM Agent User Guide)](virtual-machines-linux-agent-user-guide.md).

>[AZURE.NOTE]Dans PaaS, l'agent de machine virtuelle est appelé **GuestAgent** et est toujours disponible sur les machines virtuelles Web et de Rôle de travail. (Pour plus d'informations, consultez la rubrique [Architecture de rôle Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx).) L'agent de machine virtuelle pour les machines virtuelles de rôle peut maintenant ajouter des extensions aux machines virtuelles de service cloud de la même manière que pour les machines virtuelles persistantes. La différence majeure entre les extensions de machine virtuelle sur des machines virtuelles de rôle et des machines virtuelles persistantes est que, avec des machines virtuelles de rôle, les extensions sont ajoutées au service de cloud en premier, puis ensuite aux déploiements au sein de ce service cloud.

>Utilisez l'applet de commande [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) pour répertorier toutes les extensions de machine virtuelle de rôle disponibles.

##Rechercher, ajouter, mettre à jour et supprimer des extensions de machine virtuelle  

Pour plus d'informations sur ces tâches, consultez [Ajouter, rechercher et supprimer des extensions de machines virtuelles Azure](https://msdn.microsoft.com/library/dn850373.aspx).

<!---HONumber=August15_HO7-->