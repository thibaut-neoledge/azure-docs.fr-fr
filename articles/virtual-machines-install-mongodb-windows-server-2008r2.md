<properties linkid="manage-windows-common-task-mongodb-vm" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Windows Server virtual machine" metaKeywords="Azure vm, Azure MongoDB, Azure remote desktop" description="Learn how to create an Azure virtual machine with Windows Server 2008 R2, and then use Remote Desktop to install MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running Windows Server in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />




Installation de MongoDB sur une machine virtuelle exécutant Windows Server dans Azure
=====================================================================================

[MongoDB](http://www.mongodb.org/) est une base de données NoSQL open source qui offre des performances élevées. Vous pouvez créer une machine virtuelle exécutant Windows Server à partir de la bibliothèque d'images du [portail de gestion Azure](http://manage.windowsazure.com). Vous pouvez alors installer et configurer une base de données MongoDB sur la machine virtuelle.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   créer, à l'aide du portail de gestion, une machine virtuelle Windows Server à partir de la bibliothèque ;
-   vous connecter à la machine virtuelle à l'aide du Bureau à distance ;
-   installer MongoDB sur la machine virtuelle.

Création d'une machine virtuelle exécutant Windows Server 2008 R2
-----------------------------------------------------------------

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

Association d'un disque de données
----------------------------------

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

Installation et exécution de MongoDB sur la machine virtuelle
-------------------------------------------------------------

[WACOM.INCLUDE [install-and-run-mongo-on-win2k8-vm](../includes/install-and-run-mongo-on-win2k8-vm.md)]

Résumé
------

Ce didacticiel vous a montré comment créer une machine virtuelle Windows Server et vous y connecter à distance. Vous avez également appris à installer et à configurer MongoDB sur la machine virtuelle Windows. Pour plus d'informations sur MongoDB, consultez la [documentation MongoDB](http://www.mongodb.org/display/DOCS/Home).

