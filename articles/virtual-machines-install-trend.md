<properties title="How to install and configure Trend on an Azure VM" pageTitle="How to install and configure Trend Micro Deep Security as a Service on an Azure VM" description="Describes installing and configuring Trend Micro security on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Installation et configuration de Trend Micro Deep Security comme service sur une machine virtuelle Azure

Cet article décrit comment installer et configurer Trend Micro Deep Security comme service sur une machine virtuelle nouvelle ou existante exécutant Windows Server. Deep Security inclut une protection anti-programmes malveillants, un pare-feu, un système de prévention contre les intrusions et une surveillance de l'intégrité.

Le client est installé sous forme d'extension de sécurité en utilisant l'agent de machine virtuelle. Sur une nouvelle machine virtuelle, vous devez installer l'agent de machine virtuelle en même temps que l'agent Deep Security. Sur une machine virtuelle existante sans l'agent, vous devez d'abord télécharger et installer l'agent. Cet article aborde ces deux situations.

Si vous disposez d'un abonnement Trend pour une solution locale, vous pouvez l'utiliser pour protéger vos machines virtuelles Azure. Si vous n'êtes pas encore client, vous pouvez vous inscrire pour une version d'évaluation. Pour plus d'informations sur cette solution, consultez le billet de blog [Extension de l'agent de machine virtuelle Microsoft Azure pour Deep Security][Extension de l'agent de machine virtuelle Microsoft Azure pour Deep Security].

## Installation de l'agent Deep Security sur une nouvelle machine virtuelle

Le [portail de gestion Azure][portail de gestion Azure] vous permet d'installer l'agent de machine virtuelle et l'extension de sécurité Trend lors de l'utilisation de l'option **À partir de la galerie** pour créer la machine virtuelle. Il s'agit d'une méthode simple pour ajouter une protection Trend en cas de création d'une seule machine virtuelle.

L'option **À partir de la galerie** ouvre un Assistant qui vous aide à configurer la machine virtuelle. Utilisez la dernière page de l'Assistant pour installer l'agent de machine virtuelle et l'extension de sécurité Trend. Pour des instructions générales, consultez le didacticiel [Création d'une machine virtuelle exécutant Windows Server][Création d'une machine virtuelle exécutant Windows Server]. Lorsque vous atteignez la dernière page de l'Assistant, procédez comme suit :

1.  Sous l'agent de machine virtuelle, cochez **Installer l'agent de machine virtuelle**.

2.  Sous les extensions de sécurité, cochez **Agent Trend Micro Deep Security**.

3.  Cliquez sur la coche pour créer la machine virtuelle.

    ![Installez l'agent de machine virtuelle et l'agent Deep Security.][Installez l'agent de machine virtuelle et l'agent Deep Security.]

## Installation de l'agent Deep Security sur une machine virtuelle existante

Pour ce faire, vous avez besoin des éléments suivants :

-   Le module Azure PowerShell, version 0.8.2 ou ultérieure. Pour des instructions et un lien vers la dernière version, consultez la rubrique [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].

-   L'agent de machine virtuelle. Pour des instructions et un lien vers le téléchargement, consultez le billet de blog [Agent de machine virtuelle et extensions - 2e partie][Agent de machine virtuelle et extensions - 2e partie].

Ouvrez une session Azure PowerShell et exécutez les commandes ci-après. Veillez à remplacer les espaces réservés, tels que MyServiceName, par vos propres valeurs.

1.  Obtenez le nom du service cloud, ainsi que le nom de la machine virtuelle et la machine virtuelle, et stockez-les dans des variables de façon à pouvoir être utilisés par les commandes suivantes :

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] Si vous ignorez le nom du service cloud et de la machine virtuelle, exécutez Get-AzureVM pour afficher ces informations pour toutes les machines virtuelles de l'abonnement en cours.

2.  Ajoutez l'agent Deep Security à la machine virtuelle :

    `Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA -VM $vm.VM`

    > [WACOM.NOTE] Si vous voulez installer une version spécifique, exécutez la commande suivante pour obtenir la liste des versions disponibles : `Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA`. Incluez ensuite le paramètre Version lors de l'exécution de Set-AzureVMExtension.

3.  Mettez à jour la machine virtuelle, qui installe l'agent Deep Security :

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## Étapes suivantes

Une fois l'agent installé, il lui faut quelques minutes pour démarrer. Vous devez ensuite activer Deep Security sur la machine virtuelle de façon à ce qu'elle puisse être gérée par Deep Security Manager. Consultez les pages suivantes :

-   L'article de Trend sur cette solution, [Sécurité cloud instantanée pour Microsoft Azure][Sécurité cloud instantanée pour Microsoft Azure].
-   Un [exemple de script Windows PowerShell][exemple de script Windows PowerShell] pour configurer la machine virtuelle.
-   Les [instructions][instructions] de l'exemple.

## Ressources supplémentaires

[Connexion à une machine virtuelle exécutant Windows Server][Connexion à une machine virtuelle exécutant Windows Server]

[Gestion des extensions][Gestion des extensions]

<!--Link references-->

  [Extension de l'agent de machine virtuelle Microsoft Azure pour Deep Security]: http://go.microsoft.com/fwlink/p/?LinkId=403945
  [portail de gestion Azure]: http://manage.windowsazure.com
  [Création d'une machine virtuelle exécutant Windows Server]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Installez l'agent de machine virtuelle et l'agent Deep Security.]: ./media/virtual-machines-install-trend/InstallVMAgentandTrend.png
  [Installation et configuration d'Azure PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [Agent de machine virtuelle et extensions - 2e partie]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Sécurité cloud instantanée pour Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=404101
  [exemple de script Windows PowerShell]: http://go.microsoft.com/fwlink/?LinkId=404100
  [instructions]: http://go.microsoft.com/fwlink/?LinkId=404099
  [Connexion à une machine virtuelle exécutant Windows Server]: ../virtual-machines-log-on-windows-server/
  [Gestion des extensions]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
