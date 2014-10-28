<properties linkid="virtual-machines-linux-mysql-use-opensuse" urlDisplayName="Install MongoDB" pageTitle="nstall MongoDB on a virtual machine running CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Learn how to install Mongo DB on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt"></tags>

# Installation de MySQL sur une machine virtuelle exécutant OpenSUSE Linux dans Azure

[MySQL][] est une base de données SQL open source connue. À l'aide du [portail de gestion Azure][], vous pouvez créer une machine virtuelle exécutant OpenSUSE Linux à partir de la bibliothèque d'images. Vous pouvez alors installer et configurer une base de données MySQL sur la machine virtuelle.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   créer, à l'aide du portail de gestion, une machine virtuelle OpenSUSE Linux à partir de la bibliothèque ;
-   vous connecter à la machine virtuelle à l'aide de SSH or PuTTY ;
-   installer MySQL sur la machine virtuelle.

## Création d'un abonnement Azure

Si vous n'avez pas encore d'abonnement Azure, vous pouvez vous enregistrer [gratuitement][]. Lorsque vous êtes inscrit, procédez aux étapes suivantes du didacticiel.

[WACOM.INCLUDE [antares-iaas-signup-iaas][]]

## Création d'une machine virtuelle exécutant OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal][]]

## Installation et exécution de MySQL sur la machine virtuelle

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm][]]

## Résumé

Dans ce didacticiel, vous avez appris à créer une machine virtuelle exécutant OpenSUSE Linux et à vous y connecter à distance à l'aide de SSH ou PuTTY. Vous avez également appris à installer et à configurer MySQL sur la machine virtuelle Linux. Pour plus d'informations sur MySQL, consultez la [documentation MySQL][].

  [MySQL]: http://www.mysql.com
  [portail de gestion Azure]: http://manage.windowsazure.com
  [gratuitement]: http://azure.microsoft.com
  [antares-iaas-signup-iaas]: ../includes/antares-iaas-signup-iaas.md
  [create-and-configure-opensuse-vm-in-portal]: ../includes/create-and-configure-opensuse-vm-in-portal.md
  [install-and-run-mysql-on-opensuse-vm]: ../includes/install-and-run-mysql-on-opensuse-vm.md
  [documentation MySQL]: http://dev.mysql.com/doc/
