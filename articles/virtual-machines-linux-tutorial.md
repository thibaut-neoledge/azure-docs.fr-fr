<properties pageTitle="Création d'une machine virtuelle exécutant Linux dans Azure" description="Apprenez à créer une machine virtuelle Azure exécutant Linux en utilisant une image provenant d'Azure." services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor="tysonn"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="1/19/2015" ms.author="kathydav"/>

#Création d'une machine virtuelle exécutant Linux 

La création d'une machine virtuelle qui exécute Linux est facilitée lorsque vous utilisez la galerie d'images du portail de gestion Azure. Ce guide explique comment procéder ; il suppose que vous n'avez aucune expérience de l'utilisation d'Azure.

> [AZURE.NOTE] Même si vous n'avez besoin d'aucune expérience avec les machines virtuelles Azure pour suivre ce didacticiel, il vous faut un compte Azure. Vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Création d'un compte Azure](http://www.windowsazure.com/fr-fr/develop/php/tutorials/create-a-windows-azure-account/). 

Ce didacticiel contient les sections suivantes :

- [À propos des machines virtuelles dans Azure] []
- [Création de la machine virtuelle] []
- [Connexion à la machine virtuelle une fois celle-ci créée] []
- [Association d'un disque de données avec la nouvelle machine virtuelle] []

**Important** : ce didacticiel permet de créer une machine virtuelle qui n'est pas connectée à un réseau virtuel. Si vous voulez que votre machine virtuelle utilise un réseau virtuel, lorsque vous la créez, vous devez indiquer le réseau virtuel. Pour plus d'informations sur les réseaux virtuels, consultez [Vue d'ensemble du réseau virtuel Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

## <a id="virtualmachine"> </a>À propos des machines virtuelles dans Azure ##

Une machine virtuelle dans Azure est un serveur que vous pouvez contrôler et gérer dans le cloud. Une fois la machine virtuelle créée dans Azure, vous pouvez la supprimer et la recréer à votre convenance, et y accéder tout comme vous le feriez avec un serveur situé dans votre bureau. Les fichiers de disque dur virtuel permettent de créer une machine virtuelle. Les types suivants sont disponibles :

- **Image** - Fichier de disque dur virtuel utilisé comme modèle pour créer une nouvelle machine virtuelle. Une image est considérée comme un modèle, car elle n'inclut aucun paramètre spécifique, comme ceux liés au nom d'ordinateur et au compte d'utilisateur, contrairement à une machine virtuelle en cours d'exécution. Si vous créez une machine virtuelle à l'aide d'une image, un disque de système d'exploitation est créé automatiquement pour la nouvelle machine virtuelle.
- **Disque** - Un disque est un fichier de disque dur virtuel que vous pouvez démarrer ou monter comme version en cours d'exécution d'un système d'exploitation. Une fois l'image déployée, elle devient un disque. Un disque est toujours créé lorsque vous vous servez d'une image pour créer une machine virtuelle. Un fichier de disque dur virtuel lié à un matériel virtualisé et qui s'exécute dans le cadre d'un service est un disque.

Les options suivantes sont disponibles dans le cadre de l'utilisation d'images pour créer une machine virtuelle :

- Créez une machine virtuelle en utilisant une image fournie dans la galerie d'images du portail de gestion Azure.
- Créez et téléchargez un fichier .vhd contenant une image dans Azure, puis créez une machine virtuelle à l'aide de l'image. Pour plus d'informations sur la création et le téléchargement d'une image personnalisée, consultez la page [Création et téléchargement d'un disque dur virtuel contenant le système d'exploitation Linux](/fr-fr/manage/linux/common-tasks/upload-a-vhd/).

Chaque machine virtuelle réside dans un service cloud, soit seule, soit avec d'autres machines virtuelles. Vous pouvez placer les machines virtuelles dans le même service cloud pour leur permettre de communiquer les unes avec les autres, pour équilibrer la charge du trafic réseau et pour garantir leur haute disponibilité. Pour plus d'informations sur les services cloud et les machines virtuelles, consultez la section " Modèles d'exécution " de la page [Présentation d'Azure](http://go.microsoft.com/fwlink/p/?LinkId=311926).

## <a id="custommachine"> </a>Création de la machine virtuelle ##

Ce didacticiel utilise la méthode **À partir de la galerie** pour créer une machine virtuelle, car elle offre plus d'options que la méthode **Création rapide**. Vous pouvez choisir les ressources connectées, le nom DNS et la connectivité réseau, le cas échéant.

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../includes/virtual-machines-create-LinuxVM.md)]

[AZURE.INCLUDE [virtual-machines-Linux-tutorial-log-on-attach-disk](../includes/virtual-machines-Linux-tutorial-log-on-attach-disk.md)]

> [AZURE.NOTE] Vous pouvez également vous connecter à votre machine virtuelle Linux à l'aide d'une clé SSH pour l'identification. Pour plus d'informations sur la configuration et la connexion avec une clé SSH, consultez la rubrique [Utilisation de SSH avec Linux sur Azure](./virtual-machines-linux-use-ssh-key/).

##Étapes suivantes 

Pour en savoir plus sur Linux sous Azure, consultez les articles suivants :

- [Présentation de Linux sous Azure](http://www.windowsazure.com/fr-fr/documentation/articles/introduction-linux/)

- [Utilisation des outils en ligne de commande Azure pour Mac et Linux](http://www.windowsazure.com/fr-fr/documentation/articles/xplat-cli/)

- [À propos des paramètres de configuration de machine virtuelle Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

- [Extension Docker VM pour Linux sur Azure](../virtual-machines-docker-vm-extension/)


[Étapes suivantes]: #next
[À propos des machines virtuelles dans Azure]: #virtualmachine
[Création de la machine virtuelle]: #custommachine
[Connexion à la machine virtuelle une fois celle-ci créée]: #logon
[Association d'un disque de données avec la nouvelle machine virtuelle]: #attachdisk




<!--HONumber=42-->
