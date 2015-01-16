<properties urlDisplayName="Install MySQL" pageTitle="Installation de MySQL sur une machine virtuelle exécutant OpenSUSE Linux dans Azure" metaKeywords="Azure, MySQL" description="Découvrez comment installer MySQL sur une machine virtuelle dans Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="kathydav" />

# Installation de MySQL sur une machine virtuelle exécutant OpenSUSE Linux dans Azure

[MySQL][MySQL] est une base de données SQL open source connue.  Vous pouvez créer une machine virtuelle exécutant OpenSUSE Linux à l'aide du [portail de gestion Azure][AzurePortal], you can create a virtual machine running OpenSUSE Linux.  Vous pouvez alors installer et configurer une base de données MySQL sur la machine virtuelle.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

- créer, à l'aide du portail de gestion, une machine virtuelle OpenSUSE Linux à partir d'une image disponible dans Azure ;
- vous connecter à la machine virtuelle à l'aide de SSH or PuTTY ;
- installer MySQL sur la machine virtuelle.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## Création d'une machine virtuelle exécutant OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

##Installation et exécution de MySQL sur la machine virtuelle

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

##Résumé
Dans ce didacticiel, vous avez appris à créer une machine virtuelle exécutant OpenSUSE Linux et à vous y connecter à distance à l'aide de SSH ou PuTTY.  Vous avez également appris à installer et à configurer MySQL sur la machine virtuelle Linux.  Pour plus d'informations sur MySQL, consultez la [documentation MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com

<!--HONumber=35.1-->
