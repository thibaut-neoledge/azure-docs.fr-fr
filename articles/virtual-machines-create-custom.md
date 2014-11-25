<properties linkid="manage-linux-howto-custom-create-vm" urlDisplayName="Create a custom VM" pageTitle=" Create a custom virtual machine running Linux in Azure" metaKeywords="Azure custom vm, creating custom vm" description="Learn how to create a custom virtual machine in Azure." metaCanonical="http://www.windowsazure.com/fr-fr/manage/windows/how-to-guides/custom-create-a-vm/" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Création d’une machine virtuelle personnalisée

Une machine virtuelle *personnalisée* correspond à une machine virtuelle que vous avez créée à l'aide de l'option **From Gallery** parce qu'elle propose davantage de possibilités de configuration que l'option **Quick Create**. Elles comprennent :

-   Un plus grand choix d'images pour la création de la machine virtuelle (MV)
-   Connexion de la MV à un réseau virtuel
-   Installation de l'agent et des extensions de machine virtuelle, par exemple un logiciel anti-programme malveillant
-   Ajout de la MV à un service cloud existant
-   Ajout de la MV à un groupe à haute disponibilité

**Important** : si vous souhaitez que la machine virtuelle utilise un réseau virtuel pour pouvoir vous y connecter directement par nom d’hôte ou configurer des connexions entre différents locaux, veillez à spécifier le réseau virtuel lors de la création de la machine virtuelle. Vous pouvez configurer une machine virtuelle pour qu’elle rejoigne uniquement un réseau virtuel lorsque vous la créez. Pour plus d’informations sur les réseaux virtuels, consultez la page [Présentation du réseau virtuel Azure][Présentation du réseau virtuel Azure].

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

  [Présentation du réseau virtuel Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
