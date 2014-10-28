<properties linkid="manage-linux-common-task-mongodb-virtual-machine" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Linux virtual machine in Azure" metaKeywords="Azure vm CentOS, Azure vm Linux, Linux vm, Linux MongoDB" description="Learn how to create an Azure virtual machine with CentOS Linux, and then use SSH or PuTTY to install MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Installation de MongoDB sur une machine virtuelle exécutant CentOS Linux dans Azure

[MongoDB][] est une base de données NoSQL libre qui offre des performances élevées. À l'aide du portail de gestion Azure, vous pouvez créer une machine virtuelle exécutant CentOS Linux à partir de la bibliothèque d'images. Vous pouvez alors installer et configurer une base de données MongoDB sur la machine virtuelle.

Vous apprendrez à effectuer les opérations suivantes :

-   sélectionner et installer, à l'aide du portail de gestion, une machine virtuelle CentOS Linux à partir de la bibliothèque ;

-   vous connecter à la machine virtuelle à l'aide de SSH or PuTTY ;
-   installer MongoDB sur la machine virtuelle.

## Création d'une machine virtuelle exécutant CentOS Linux

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal][]]

## Association d'un disque de données

[WACOM.INCLUDE [attach-data-disk-centos-vm-in-portal][]]

## Installation et exécution de MongoDB sur la machine virtuelle

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm][]]

## Résumé

Dans ce didacticiel, vous avez appris à créer une machine virtuelle Linux et à vous y connecter à distance à l'aide de SSH ou PuTTY. Vous avez également appris à installer et à configurer MongoDB sur la machine virtuelle Linux. Pour plus d'informations sur MongoDB, consultez la [documentation MongoDB][].

  [MongoDB]: http://www.mongodb.org/
  [create-and-configure-centos-vm-in-portal]: ../includes/create-and-configure-centos-vm-in-portal.md
  [attach-data-disk-centos-vm-in-portal]: ../includes/attach-data-disk-centos-vm-in-portal.md
  [install-and-run-mongo-on-centos-vm]: ../includes/install-and-run-mongo-on-centos-vm.md
  [documentation MongoDB]: http://www.mongodb.org/display/DOCS/Home
