<properties title="About Chef and Azure Virtual Machines" pageTitle="About Chef and Azure Virtual Machines" description="Describes installing and configuring Chef on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# À propos des machines virtuelles Chef et Azure

Chef offre un système d'automatisation pour l'élaboration, le déploiement et la gestion de votre infrastructure. Les ressources sont gérées à l'aide de recettes, qui sont des définitions réutilisables qui fournissent des instructions pour les tâches telles que la configuration d'un serveur web.

Chef est un système client-serveur. Pour découvrir les options d'utilisation d'un serveur Chef, consultez la page [Choose your installation][Choose your installation] (Choisir votre installation). Vous aurez besoin des informations relatives au serveur Chef pour configurer le client. Pour installer le client Chef sur une machine virtuelle Azure, vous avez les options suivantes :

-   Utilisez le portail de gestion pour installer le client Chef lorsque vous créez une machine virtuelle exécutant Windows Server 2012 ou Windows Server 2012 R2. Pour plus d'instructions, consultez le [portail Microsoft Azure][portail Microsoft Azure].
-   Utilisez Windows PowerShell pour installer le client Chef sur une machine virtuelle existante. Un [script][script] d'exemple est disponible sur Github.
-   Utilisez un plug-in Chef, [knife-azure][knife-azure], pour créer une instance de machine virtuelle et installer le client Chef.

## Ressources supplémentaires

[Chef et Microsoft Azure][Chef et Microsoft Azure]

[Connexion à une machine virtuelle exécutant Windows Server][Connexion à une machine virtuelle exécutant Windows Server]

[Connexion à une machine virtuelle exécutant Linux][Connexion à une machine virtuelle exécutant Linux]

[Gestion des extensions][Gestion des extensions]

<!--Link references-->

  [Choose your installation]: http://www.getchef.com/chef/choose-your-version/
  [portail Microsoft Azure]: http://docs.opscode.com/azure_portal.html
  [script]: https://gist.github.com/kaustubh-d/cea1aa75baebd3615609
  [knife-azure]: http://docs.getchef.com/plugin_knife_azure.html
  [Chef et Microsoft Azure]: http://www.getchef.com/solutions/azure/
  [Connexion à une machine virtuelle exécutant Windows Server]: ../virtual-machines-log-on-windows-server/
  [Connexion à une machine virtuelle exécutant Linux]: ../virtual-machines-linux-how-to-log-on
  [Gestion des extensions]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
