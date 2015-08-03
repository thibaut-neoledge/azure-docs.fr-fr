<properties
   pageTitle="Gestion de vos machines virtuelles Azure à l’aide de l’interface de ligne de commande Azure pour Mac, Linux et Windows | Microsoft Azure"
   description="Décrit comment créer, gérer et supprimer vos machines virtuelles Azure à l’aide de l’interface de ligne de commande Azure pour Mac, Linux et Windows."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="dlepow"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/09/2015"
   ms.author="danlep"/>

# Gestion de vos machines virtuelles à l’aide de l’interface de ligne de commande Azure pour Mac, Linux et Windows

Il est possible d’automatiser les nombreuses tâches quotidiennes liées à la gestion de vos machines virtuelles à l’aide de l’interface de ligne de commande Azure. Cet article donne des exemples de commandes pour réaliser des tâches simples et contient des liens vers des articles indiquant les commandes à utiliser pour des tâches plus complexes.

>[AZURE.NOTE]Si vous n’avez pas encore installé ni configuré l’interface de ligne de commande Azure, cliquez [ici](../xplat-cli-install.md) pour obtenir les instructions. Si vous souhaitez un démarrage rapide pour les mêmes tâches dans PowerShell, consultez [Gestion de vos machines virtuelles à l’aide d’Azure PowerShell](virtual-machines-manage-vms-powershell.md).

## Utilisation des exemples de commandes
Vous devrez remplacer une partie du texte des commandes par un texte approprié à votre environnement. Les symboles < and > indiquent le texte à remplacer. Lorsque vous remplacez le texte, supprimez les symboles, mais laissez les guillemets en place.

> [AZURE.NOTE]Si vous souhaitez stocker et manipuler la sortie des commandes de la console par programme, vous pouvez utiliser un outil d’analyse JSON tel que **[q](https://github.com/stedolan/jq)**, **[jsawk](https://github.com/micha/jsawk)** ou les bibliothèques de langages adaptées à cette tâche.

## Affichage des informations relatives à une machine virtuelle

Il s’agit d’une tâche de base que vous utiliserez souvent. Utilisez-la pour obtenir des informations sur une machine virtuelle, effectuer des tâches sur cette dernière ou pour obtenir un résultat à stocker dans une variable.

Pour obtenir des informations sur la machine virtuelle, exécutez cette commande en remplaçant tous les éléments entre guillemets notamment les caractères < and > :

     azure vm show -g <group name> -n <virtual machine name>

Pour stocker le résultat dans une variable $vm comme un document JSON, exécutez :

    vmInfo=$(azure vm show -g <group name> -n <virtual machine name> --json)

Vous pouvez également diriger le stdout vers un fichier.

## Connexion à une machine virtuelle Linux

En général, les machines Linux sont connectées via SSH. Pour plus d’informations, consultez la rubrique [Utilisation de SSH avec Linux dans Azure](virtual-machines-linux-use-ssh-key.md). Présentation d’Azure Resource Manager
## Arrêter une machine virtuelle

Exécutez cette commande :

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT]Utilisez ce paramètre pour conserver l’adresse IP virtuelle du service cloud s’il s’agit de la dernière machine virtuelle de ce service. <br><br> Si vous utilisez le paramètre StayProvisioned, vous êtes toujours facturé pour cette machine virtuelle.

## Démarrer une machine virtuelle

Exécutez cette commande : Azure Resource Manager Overview azure vm start <group name> <virtual machine name>

## Associer un disque de données

Vous devez également décider d’associer un nouveau disque ou un disque existant, qui contient des données. Dans le cas d’un nouveau disque, cette même commande entraîne la création du fichier .vhd et son association.

Pour associer un nouveau disque, exécutez cette commande :

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb>

Pour associer un disque existant, exécutez cette commande :

    azure vm disk attach <resource-group> <vm-name> [vhd-url]

## Créer une machine virtuelle Linux

Pour créer une nouvelle machine virtuelle Linux, vous aurez besoin de plusieurs valeurs, notamment un nom de groupe de ressources, un emplacement, un nom d’image, un nom de machine virtuelle et un compte de stockage pour stocker l’image .vhd de sauvegarde. Une fois que vous disposez des informations que vous souhaitez utiliser, l’interface de ligne de commande Azure peut créer une session interactive vous invitant à entrer ces valeurs en tapant :

    azure vm create

Bien sûr, si vous disposez déjà de ces valeurs, vous pouvez trouver des moyens de contournement pour les ignorer en tapant `azure help vm create`.

## Étapes suivantes

Pour consulter d’autres exemples d’utilisation de l’interface de ligne de commande Azure avec le mode **arm**, consultez [Utilisation de l’interface de ligne de commande Microsoft Azure pour Mac, Linux et Windows avec Azure Resource Management](../xplat-cli-resource-manager.md). Pour en savoir plus sur les ressources Azure et leurs concepts, consultez la page [Présentation d’Azure Resource Manager](../resource-group-overview.md).
 

<!---HONumber=July15_HO4-->