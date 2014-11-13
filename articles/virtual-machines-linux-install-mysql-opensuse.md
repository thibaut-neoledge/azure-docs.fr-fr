<properties urlDisplayName="Install MySQL" pageTitle="Installation de&nbsp;MySQL sur une machine virtuelle&nbsp;Linux dans&nbsp;Azure" metaKeywords="Azure vm OpenSUSE, Linux vm" description="Apprenez &agrave; cr&eacute;er une machine virtuelle&nbsp;Azure avec&nbsp;OpenSUSE&nbsp;Linux et &agrave; utiliser SSH ou&nbsp;PuTTY pour installer&nbsp;MySQL." metaCanonical="" services="virtual-machines" documentationCenter="" title="Installation de MySQL sur une machine virtuelle ex&eacute;cutant OpenSUSE Linux dans Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Installation de MySQL sur une machine virtuelle exécutant OpenSUSE Linux dans Azure

[MySQL][MySQL] est une base de données SQL open source connue. À l'aide du [portail de gestion Azure][portail de gestion Azure], vous pouvez créer une machine virtuelle exécutant OpenSUSE Linux à partir de la bibliothèque d'images. Vous pouvez alors installer et configurer une base de données MySQL sur la machine virtuelle.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   créer, à l'aide du portail de gestion, une machine virtuelle OpenSUSE Linux à partir de la bibliothèque ;

-   vous connecter à la machine virtuelle à l'aide de SSH or PuTTY ;

-   installer MySQL sur la machine virtuelle.

## Création d'une machine virtuelle exécutant OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Installation et exécution de MySQL sur la machine virtuelle

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Résumé

Dans ce didacticiel, vous avez appris à créer une machine virtuelle OpenSUSE Linux et à vous y connecter à distance à l'aide de SSH ou PuTTY. Vous avez également appris à installer et à configurer MySQL sur la machine virtuelle Linux. Pour plus d'informations sur MySQL, consultez la [documentation MySQL][documentation MySQL].

  [MySQL]: http://www.mysql.com
  [portail de gestion Azure]: http://manage.windowsazure.com
  [documentation MySQL]: http://dev.mysql.com/doc/
