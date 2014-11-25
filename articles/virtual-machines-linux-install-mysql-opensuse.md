<properties linkid="manage-linux-common-task-mysql-virtual-machine" urlDisplayName="Install MySQL" pageTitle="Install MySQL on a Linux virtual machine in Azure" metaKeywords="Azure vm OpenSUSE, Linux vm" description="Learn how to create an Azure virtual machine with OpenSUSE Linux, and then use SSH or PuTTY to install MySQL." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running OpenSUSE Linux in Azure" authors="" solutions="" manager="" editor="" />

Installation de MySQL sur une machine virtuelle exécutant OpenSUSE Linux dans Azure
===================================================================================

[MySQL](http://www.mysql.com) est une base de données SQL open source connue. À l'aide du [portail de gestion Azure](http://manage.windowsazure.com), vous pouvez créer une machine virtuelle exécutant OpenSUSE Linux à partir de la bibliothèque d'images. Vous pouvez alors installer et configurer une base de données MySQL sur la machine virtuelle.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   créer, à l'aide du portail de gestion, une machine virtuelle OpenSUSE Linux à partir de la bibliothèque ;

-   vous connecter à la machine virtuelle à l'aide de SSH or PuTTY ;

-   installer MySQL sur la machine virtuelle.

Création d'une machine virtuelle exécutant OpenSUSE Linux
---------------------------------------------------------

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

Installation et exécution de MySQL sur la machine virtuelle
-----------------------------------------------------------

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

Résumé
------

Dans ce didacticiel, vous avez appris à créer une machine virtuelle OpenSUSE Linux et à vous y connecter à distance à l'aide de SSH ou PuTTY. Vous avez également appris à installer et à configurer MySQL sur la machine virtuelle Linux. Pour plus d'informations sur MySQL, consultez la [documentation MySQL](http://dev.mysql.com/doc/).

[AzurePreviewPortal]: http://manage.windowsazure.com
