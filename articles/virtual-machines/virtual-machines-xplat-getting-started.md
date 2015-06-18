<properties
   pageTitle="Comment créer une machine virtuelle Azure avec l'interface de ligne de commande multiplateforme xplat-cli"
   description="Cette rubrique décrit comment installer l'interface xplat-cli sur n'importe quelle plateforme, comment l'utiliser pour se connecter à un compte Azure et comment créer une machine virtuelle à partir de cette interface."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="rasquill"/>

# Création d'une machine virtuelle à l'aide de l'interface de ligne de commande multiplateforme Azure (xplat-cli)
L'interface xplat-cli est un excellent moyen de gérer votre infrastructure Windows Azure à partir de n'importe quelle plateforme.

Il ne suffit pas d'installer l'interface xplat-cli et de détenir un abonnement Azure pour créer une machine virtuelle sur-le-champ. Examinons la procédure en détail. Si vous n'avez pas de compte Azure, [obtenez-en un gratuitement](http://azure.microsoft.com/pricing/free-trial/).

## Installation de l'interface xplat-cli

Suivez ces instructions pour installer l'[interface xplat-cli](http://azure.microsoft.com/documentation/articles/xplat-cli/#install).

## Connexion à Azure avec l'interface xplat-cli

Vous pouvez connecter votre installation xplat-cli avec un compte Azure personnel, professionnel ou scolaire. Pour comprendre les différences et effectuer votre choix, consultez [Connexion à votre abonnement Azure](http://azure.microsoft.com/documentation/articles/xplat-cli/#configure).

## Création d'une machine virtuelle et connexion à celle-ci dans Azure

La création d'une machine virtuelle commence par le choix (ou le téléchargement) d'une image et l'utilisation de la commande `azure vm create`.

1. Pour choisir une image depuis la ligne de commande, vous pouvez répertorier les images de machine virtuelle disponibles à l'aide de la commande `azure vm image list`. Comme il y a beaucoup d'images, vous pouvez afficher les résultats page par page en utilisant `more` ou les filtrer à l'aide de `grep` (Linux) ou `findstr` (Windows). Par exemple, si vous recherchez des images Ubuntu sur Linux, utilisez une commande similaire à celle-ci :

        azure vm image list | grep Ubuntu

    Cela génère toujours une longue liste d'images, que vous pouvez filtrer par version :

        azure vm image list | grep Ubuntu-14_10

    À partir de là, vous pouvez choisir une image et utiliser la commande `show` pour afficher ses propriétés de façon plus détaillée :

        azure vm image show b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-fr-fr-30GB

2. Création de votre machine virtuelle.

    Une fois que vous avez choisi une image de machine virtuelle, utilisez la commande `vm create` pour créer l'image. Cette commande comporte de nombreuses options que vous pouvez répertorier avec la commande help :

        vm create --help

    Outre l'image issue de l'étape 1, les arguments clés nécessaires pour créer une machine virtuelle sont l'emplacement, le nom DNS et le nom d'utilisateur.

    Pour l'authentification, vous pouvez choisir de spécifier un mot de passe (sur la ligne de commande ou de manière interactive) ou de vous authentifier à l'aide d'un certificat. Si vous choisissez un mot de passe, celui-ci doit comporter au moins 8 caractères, contenir des majuscules et des minuscules, ainsi qu'un caractère spécial (par exemple, un caractère parmi !@#$%^&+=). Nous vous conseillons de mettre le mot de passe entre guillemets et de placer les caractères spéciaux dans une séquence d'échappement si vous le passez depuis la ligne de commande.

    Pour choisir un emplacement, vous pouvez utiliser la commande `vm location list` afin de sélectionner une région proche de chez vous.

  Le nom DNS que vous choisissez doit être unique (il sera mappé à `dnsname.cloudapp.net`), et sera le même que le nom de la machine si vous ne spécifiez pas de nom de machine sur la ligne de commande séparément.  

    L'exemple Linux ci-dessous crée une machine virtuelle dans l'ouest des États-Unis, ouvre le port SSH 22 par défaut (argument -e) et crée un utilisateur appelé `myadminuser` :

        azure vm create -e -l "West US"  my-new-cli-vm b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-fr-fr-30GB "myadminuser" "myAdm1n@passwd"

## Étapes suivantes

Expérimentons votre machine virtuelle. 

Dans l'exemple ci-dessus, comme c'est le port SSH par défaut qui a été a ouvert, il est facile de se connecter à la machine virtuelle une fois qu'elle est opérationnelle. Depuis une ligne de commande Linux :

    ssh myadminuser@my-new-cli-vm.cloudapp.net

Pour obtenir d'autres exemples d'utilisation de l'interface xplat-cli pour gérer l'infrastructure Azure, n'hésitez pas à visiter la page [Outil en ligne de commande Azure pour Mac et Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png

<!--HONumber=47-->
 