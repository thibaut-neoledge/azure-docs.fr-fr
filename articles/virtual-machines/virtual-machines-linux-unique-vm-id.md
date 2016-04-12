<properties
   pageTitle="Accès à l’ID de machine virtuelle"
   description="Décrit l’accès et l’utilisation de l’ID unique de machine virtuelle Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="drewm"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# Accès et utilisation de l’ID unique de machine virtuelle Azure

L’ID unique de machine virtuelle Azure est un identificateur 128 bits codé et stocké dans le SMBIOS de la machine virtuelle IaaS Azure et qui peut être lu simultanément à l’aide des commandes du BIOS de la plate-forme.

L’ID unique de machine virtuelle Azure est une propriété en lecture seule. L’ID unique de machine virtuelle Azure ne sera pas modifié lors de l’arrêt pour redémarrage (planifié ou non planifié), lors du démarrage ou de l’arrêt pour désallouer, lors d’une réparation de service ou de la restauration en place. Toutefois, si la machine virtuelle est un instantané copié pour créer une nouvelle instance, un nouvel ID de machine virtuelle Azure est configuré.

> [AZURE.NOTE] Si vous avez des machines virtuelles plus anciennes créées et en cours d’exécution depuis que cette nouvelle fonctionnalité a été déployée (18 septembre 2014), veuillez redémarrer votre machine virtuelle pour obtenir automatiquement un ID unique Azure.


Pour accéder à l’ID unique de machine virtuelle Azure à partir de la machine virtuelle :


## Créer une machine virtuelle
 

Pour plus d’informations, consultez la rubrique [Création d’une machine virtuelle](virtual-machines-linux-creation-choices.md).


## Connexion à la machine virtuelle
 

Pour plus d’informations, consultez [SSH à partir de Linux](virtual-machines-linux-ssh-from-linux.md)


## Interrogation de l’ID unique de machine virtuelle

Commande (l’exemple utilise **Ubuntu**) :

    sudo dmidecode | grep UUID
    
Résultats attendus de l’exemple :

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
    
En raison du classement de bit Big Endian, l’ID unique de machine virtuelle réel dans ce cas sera :

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
    
    
L’ID unique de machine virtuelle Azure peut être utilisé dans différents scénarios selon si la machine virtuelle s’exécute sur Azure ou en local, et il peut vous aider avec vos exigences en matière de licence, de création de rapports ou de suivi général sur vos déploiements IaaS Azure. Plusieurs éditeurs de logiciels indépendants créant des applications et les certifiant sur Azure peuvent avoir besoin d’identifier une machine virtuelle Azure tout au long de son cycle de vie et pour savoir si la machine virtuelle s’exécute sur Azure, en local ou sur d’autres fournisseurs de cloud. Cet identificateur de plate-forme peut par exemple aider à détecter si le logiciel est concédé sous licence correctement ou il peut vous aider à mettre en corrélation des données de machine virtuelle avec la source, afin de vous permettre de définir les mesures correctes pour la bonne plate-forme, ainsi que suivre et mettre en corrélation ces mesures avec d’autres utilisations.

<!---HONumber=AcomDC_0323_2016-->