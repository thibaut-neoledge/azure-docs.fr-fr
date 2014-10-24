<properties title="How to install and configure Symantec Endpoint Protection on an Azure VM" pageTitle="How to install and configure Symantec Endpoint Protection on an Azure VM" description="Describes installing and configuring Symantec Endpoint Protection on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Installation et configuration de Symantec Endpoint Protection sur une machine virtuelle Azure

Cet article décrit comment installer et configurer le client Symantec Endpoint Protection sur une machine virtuelle nouvelle ou existante exécutant Windows Server. Il s'agit du client complet, qui inclut des services tels qu'une protection antivirus et anti-programmes espions, un pare-feu et une prévention contre les intrusions.

Le client est installé sous forme d'extension de sécurité en utilisant l'agent de machine virtuelle. Sur une nouvelle machine virtuelle, vous devez installer l'agent en même temps que le client du point de terminaison. Sur une machine virtuelle existante sans l'agent, vous devez d'abord télécharger et installer l'agent. Cet article aborde ces deux situations.

Si vous avez un abonnement auprès de Symantec <for an on-premises solution, you can use it to protect your azure virtual machines> Si vous n'êtes pas encore client, vous pouvez vous inscrire pour une version d'évaluation. Pour plus d'informations sur cette solution, consultez la rubrique [Symantec Endpoint Protection sur la plateforme Azure de Microsoft][Symantec Endpoint Protection sur la plateforme Azure de Microsoft]. Cette page fournit également des liens vers des informations de licence, ainsi que d'autres instructions d'installation du client si vous êtes déjà un client de Symantec.

## Installation de Symantec Endpoint Protection sur une nouvelle machine virtuelle

Le [portail de gestion Azure][portail de gestion Azure] vous permet d'installer l'agent de machine virtuelle et l'extension de sécurité Symantec lors de l'utilisation de l'option **À partir de la galerie** pour créer la machine virtuelle. Il s'agit d'une méthode simple pour ajouter une protection Symantec en cas de création d'une seule machine virtuelle.

L'option **À partir de la galerie** ouvre un Assistant qui vous aide à configurer la machine virtuelle. Utilisez la dernière page de l'Assistant pour installer l'agent de machine virtuelle et l'extension de sécurité Symantec.

Pour des instructions générales, consultez le didacticiel [Création d'une machine virtuelle exécutant Windows Server][Création d'une machine virtuelle exécutant Windows Server]. Lorsque vous atteignez la dernière page de l'Assistant, procédez comme suit :

1.  Sous l'agent de machine virtuelle, cochez **Installer l'agent de machine virtuelle**.

2.  Sous Extensions de sécurité, cochez **Symantec Endpoint Protection**.

    ![Install the VM Agent and the Endpoint Protection Client][Install the VM Agent and the Endpoint Protection Client]

3.  Cliquez sur la coche en bas de la page pour créer la machine virtuelle.

## Installation de Symantec Endpoint Protection sur une machine virtuelle existante

Pour installer l'agent Deep Security sur une machine virtuelle existante, il vous faut les éléments suivants :

-   Le module Azure PowerShell, version 0.8.2 ou ultérieure. Pour des instructions et un lien vers la dernière version, consultez la rubrique [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].

-   L'agent de machine virtuelle. Pour des instructions et un lien vers le téléchargement, consultez le billet de blog [Agent de machine virtuelle et extensions - 2e partie][Agent de machine virtuelle et extensions - 2e partie].

Ouvrez une session Azure PowerShell et exécutez les commandes ci-après. Veillez à remplacer les espaces réservés, tels que MyServiceName, par vos propres valeurs.

1.  Obtenez le nom du service cloud, ainsi que le nom de la machine virtuelle et la machine virtuelle, et stockez-les dans des variables de façon à pouvoir être utilisés par les commandes suivantes :

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] Si vous ignorez le nom du service cloud et de la machine virtuelle, exécutez Get-AzureVM pour afficher ces informations pour toutes les machines virtuelles de l'abonnement en cours.

2.  Ajoutez l'agent Symantec Endpoint Protection à la machine virtuelle. Pour installer la dernière version, exécutez :

    `Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -VM $vm.VM`

    > [WACOM.NOTE] Si vous voulez installer une version spécifique, exécutez la commande suivante pour obtenir la liste des versions disponibles : `Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`.
    > Incluez ensuite le paramètre Version lors de l'exécution de Set-AzureVMExtension.

3.  Mettez à jour la machine virtuelle, qui installe l'agent Symantec Endpoint Protection :

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## Ressources supplémentaires

[Connexion à une machine virtuelle exécutant Windows Server][Connexion à une machine virtuelle exécutant Windows Server]

[Gestion des extensions][Gestion des extensions]

<!--Link references-->

  [Symantec Endpoint Protection sur la plateforme Azure de Microsoft]: http://go.microsoft.com/fwlink/p/?LinkId=403942
  [portail de gestion Azure]: http://manage.windowsazure.com
  [Création d'une machine virtuelle exécutant Windows Server]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Install the VM Agent and the Endpoint Protection Client]: ./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png
  [Installation et configuration d'Azure PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [Agent de machine virtuelle et extensions - 2e partie]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Connexion à une machine virtuelle exécutant Windows Server]: ../virtual-machines-log-on-windows-server/
  [Gestion des extensions]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
