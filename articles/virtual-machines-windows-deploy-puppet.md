<properties pageTitle="À propos des machines virtuelles Puppet et Azure" description="Explique l'installation et la configuration de Puppet sur une machine virtuelle dans Azure" services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/23/2015" ms.author="kathydav"/>

#À propos des machines virtuelles Puppet et Azure

<p>Puppet Enterprise est un logiciel d'automatisation pour l'élaboration, le déploiement et la gestion de votre infrastructure. Vous pouvez l'utiliser pour gérer le cycle de vie de votre infrastructure informatique, y compris la découverte, l'approvisionnement, la gestion de la configuration du système d'exploitation et des applications, l'orchestration et la création de rapports.   

Puppet est un système client-serveur. Puppet Master et Puppet Enterprise Agent peuvent être installés via Azure :

- Puppet Master est disponible sous forme d'image pré-configurée, installée sur un serveur Ubuntu. Vous pouvez également installer Puppet Enterprise sur un serveur existant, mais il est plus simple d'utiliser l'image pour démarrer. Vous aurez besoin des informations relatives au serveur pour configurer l'agent. 
- L'agent Puppet Enterprise est disponible sous forme d'extension de machine virtuelle que vous pouvez installer lorsque vous créez une machine virtuelle ou sur une machine virtuelle existante.

Pour obtenir des instructions, téléchargez le " Guide de prise en main " disponible sur la page [Microsoft Windows et Azure](http://puppetlabs.com/solutions/microsoft).  


##Ressources supplémentaires
[Nouvelles intégrations avec Microsoft Azure et Visual Studio]

[Connexion à une machine virtuelle exécutant Windows Server]

[Connexion à une machine virtuelle exécutant Linux]

[Gestion des extensions]

<!--Link references-->
[Nouvelles intégrations avec Microsoft Azure et Visual Studio]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
[Connexion à une machine virtuelle exécutant Windows Server]: ../virtual-machines-log-on-windows-server/
[Connexion à une machine virtuelle exécutant Linux]: ../virtual-machines-linux-how-to-log-on
[Fonctionnalités et extensions de machine virtuelle Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
[Gestion des extensions]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409




<!--HONumber=42-->
