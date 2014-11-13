<properties urlDisplayName="Install MongoDB" pageTitle="Installation de MongoDB sur une machine virtuelle ex&eacute;cutant CentOS Linux dans Azure" metaKeywords="Azure, MongoDB" description="Apprenez &agrave; installer Mongo&nbsp;DB sur une machine virtuelle dans&nbsp;Azure." metaCanonical="" services="" documentationCenter="" title="Installation de MongoDB sur une machine virtuelle ex&eacute;cutant CentOS Linux dans Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Installation de MySQL sur une machine virtuelle exécutant OpenSUSE Linux dans Azure

[MySQL][MySQL] est une base de données SQL open source connue. À l'aide du [portail de gestion Azure][portail de gestion Azure], vous pouvez créer une machine virtuelle exécutant OpenSUSE Linux à partir de la bibliothèque d'images. Vous pouvez alors installer et configurer une base de données MySQL sur la machine virtuelle.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   créer, à l'aide du portail de gestion, une machine virtuelle OpenSUSE Linux à partir de la bibliothèque ;
-   vous connecter à la machine virtuelle à l'aide de SSH or PuTTY ;
-   installer MySQL sur la machine virtuelle.

## Création d'un abonnement Azure

Si vous n'avez pas encore d'abonnement Azure, vous pouvez vous enregistrer [gratuitement][gratuitement]. Lorsque vous êtes inscrit, procédez aux étapes suivantes du didacticiel.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## Création d'une machine virtuelle exécutant OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Installation et exécution de MySQL sur la machine virtuelle

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Résumé

Dans ce didacticiel, vous avez appris à créer une machine virtuelle exécutant OpenSUSE Linux et à vous y connecter à distance à l'aide de SSH ou PuTTY. Vous avez également appris à installer et à configurer MySQL sur la machine virtuelle Linux. Pour plus d'informations sur MySQL, consultez la [documentation MySQL][documentation MySQL].

  [MySQL]: http://www.mysql.com
  [portail de gestion Azure]: http://manage.windowsazure.com
  [gratuitement]: http://azure.microsoft.com
  [documentation MySQL]: http://dev.mysql.com/doc/
