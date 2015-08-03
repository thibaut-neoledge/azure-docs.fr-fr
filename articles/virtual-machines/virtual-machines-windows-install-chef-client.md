<properties 
	pageTitle="À propos des machines virtuelles Chef et Azure" 
	description="Explique l'installation et la configuration de Chef sur une machine virtuelle dans Azure" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="kathydav"/>

#À propos des machines virtuelles Chef et Azure

Chef offre un système d'automatisation pour l'élaboration, le déploiement et la gestion de votre infrastructure. Les ressources sont gérées à l'aide de recettes, qui sont des définitions réutilisables qui fournissent des instructions pour les tâches telles que la configuration d'un serveur web.

Chef est un système client-serveur. Pour découvrir les options d'utilisation d'un serveur Chef, consultez la page [Choose your installation](http://www.getchef.com/chef/choose-your-version/) (Choisir votre installation). Vous aurez besoin des informations relatives au serveur Chef pour configurer le client.

Pour installer le client Chef sur une machine virtuelle Azure, vous disposez des options suivantes :

- Utilisez le portail de gestion Azure pour installer le client Chef lorsque vous créez une machine virtuelle exécutant Windows Server 2012 ou Windows Server 2012 R2. Pour plus d'instructions, consultez le [portail Microsoft Azure](https://docs.chef.io/azure_portal.html).
- Utilisez Azure PowerShell pour installer le client Chef sur une machine virtuelle existante. Un [script](https://gist.github.com/kaustubh-d/cea1aa75baebd3615609) d'exemple est disponible sur Github.
- Utilisez un plug-in Chef, [knife-azure](http://docs.getchef.com/plugin_knife_azure.html), pour créer une instance de machine virtuelle et installer le client Chef.   


##Ressources supplémentaires
[Chef et Microsoft Azure]

[Connexion à une machine virtuelle exécutant Windows Server]

[Connexion à une machine virtuelle exécutant Linux]

[Fonctionnalités et extensions de machine virtuelle Azure]

<!--Link references-->
[Chef et Microsoft Azure]: http://www.getchef.com/solutions/azure/
[Connexion à une machine virtuelle exécutant Windows Server]: virtual-machines-log-on-windows-server.md
[Connexion à une machine virtuelle exécutant Linux]: virtual-machines-linux-how-to-log-on.md
[Fonctionnalités et extensions de machine virtuelle Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409


 

<!---HONumber=July15_HO4-->