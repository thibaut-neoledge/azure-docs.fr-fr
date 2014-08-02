<properties linkid="virtual-machines-linux-mysql-use-opensuse" urlDisplayName="Install MongoDB" pageTitle="nstall MongoDB on a virtual machine running CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Learn how to install Mongo DB on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="" solutions="" manager="" editor="" />

Installation de MySQL sur une machine virtuelle exécutant OpenSUSE Linux dans Azure
===================================================================================

[MySQL](http://www.mysql.com) est une base de données SQL open source connue. À l'aide du [portail de gestion Azure](http://manage.windowsazure.com), vous pouvez créer une machine virtuelle exécutant OpenSUSE Linux à partir de la bibliothèque d'images. Vous pouvez alors installer et configurer une base de données MySQL sur la machine virtuelle.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   créer, à l'aide du portail de gestion, une machine virtuelle OpenSUSE Linux à partir de la bibliothèque ;
-   vous connecter à la machine virtuelle à l'aide de SSH or PuTTY ;
-   installer MySQL sur la machine virtuelle.

Inscription à la version préliminaire de la fonctionnalité Virtual Machines
---------------------------------------------------------------------------

Vous devez vous inscrire à la version préliminaire de la fonctionnalité Azure Virtual Machines pour créer une machine virtuelle. Si vous n'avez pas de compte Azure, vous pouvez également créer un compte d'évaluation gratuite.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

Création d'une machine virtuelle exécutant OpenSUSE Linux
---------------------------------------------------------

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

Installation et exécution de MySQL sur la machine virtuelle
-----------------------------------------------------------

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

Résumé
------

Dans ce didacticiel, vous avez appris à créer une machine virtuelle exécutant OpenSUSE Linux et à vous y connecter à distance à l'aide de SSH ou PuTTY. Vous avez également appris à installer et à configurer MySQL sur la machine virtuelle Linux. Pour plus d'informations sur MySQL, consultez la [documentation MySQL](http://dev.mysql.com/doc/).

