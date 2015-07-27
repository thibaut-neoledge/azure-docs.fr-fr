<properties
	pageTitle="Création d’une machine virtuelle personnalisée dans Azure"
	description="Apprenez à créer une machine virtuelle personnalisée dans Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="03/13/2015"
	ms.author="kathydav"/>

#Création d’une machine virtuelle personnalisée

Une machine virtuelle *personnalisée* correspond à une machine virtuelle que vous avez créée à l’aide de l’option **À partir de la galerie** car elle propose davantage de possibilités de configuration que l’option **Création rapide**. Les choix sont les suivants :

- Connexion de la machine virtuelle à un réseau virtuel
- Installation de l'agent et des extensions de machine virtuelle, par exemple un logiciel anti-programme malveillant
- Ajout de la machine virtuelle à un service cloud existant
- Ajout de la machine virtuelle à un compte de stockage existant
- Ajout de la MV à un groupe à haute disponibilité

**Important** : si vous souhaitez que la machine virtuelle utilise un réseau virtuel pour pouvoir vous y connecter directement par nom d'hôte ou configurer des connexions entre différents locaux, veillez à spécifier le réseau virtuel lors de la création de la machine virtuelle. Vous pouvez configurer une machine virtuelle pour qu’elle rejoigne uniquement un réseau virtuel lorsque vous la créez. Pour plus d’informations sur les réseaux virtuels, consultez la page [Présentation du réseau virtuel Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]
 

<!---HONumber=July15_HO3-->