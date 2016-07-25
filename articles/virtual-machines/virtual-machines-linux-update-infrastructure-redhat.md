<properties
   pageTitle="Infrastructure RHUI (Red Hat Update Infrastructure) | Microsoft Azure"
   description="Découvrez l’infrastructure RHUI (Red Hat Update Infrastructure) pour les instances Red Hat Enterprise Linux à la demande dans Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/11/2016"
   ms.author="borisb"/>

# Infrastructure RHUI (Red Hat Update Infrastructure) pour machines virtuelles Red Hat Enterprise Linux à la demande dans Azure

Les machines virtuelles créées à partir des images Red Hat Enterprise Linux (RHEL) à la demande disponibles dans le service Place de marché Azure sont inscrites pour accéder à l’infrastructure RHUI (Red Hat Update Infrastructure) déployée dans Azure. Les instances RHEL à la demande auront accès à un référentiel yum régional et pourront recevoir des mises à jour incrémentielles.

La liste de référentiels yum, gérée par RHUI, est configurée dans votre instance RHEL lors de l’approvisionnement. Vous n'avez pas besoin d’effectuer de configuration supplémentaire : exécutez simplement `yum update` une fois que votre instance RHEL est en cours d’exécution pour obtenir les dernières mises à jour.

## Vue d’ensemble de RHUI
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) constitue une solution hautement évolutive pour gérer le contenu du référentiel yum pour les instances cloud de Red Hat Enterprise Linux hébergées par les fournisseurs cloud certifiés Red Hat. À partir du projet Pulp en amont, RHUI permet aux fournisseurs de cloud de mettre en miroir localement le contenu du référentiel hébergé par Red Hat, de créer des référentiels personnalisés avec leur propre contenu et de rendre ces référentiels accessibles à un grand groupe d'utilisateurs finaux au moyen d’un système à équilibrage de charge de distribution de contenu.

## Régions où RHUI est disponible
L’infrastructure RHUI est disponible dans toutes les régions où les images RHEL à la demande sont disponibles. Cela inclut actuellement toutes les régions publiques répertoriées dans la page [Tableau de bord Statut Azure](https://azure.microsoft.com/status/). L’accès à l’infrastructure RHUI pour les machines virtuelles mises en service à partir d’images RHEL à la demande est inclus dans leur prix. La disponibilité cloud régionale/nationale sera mise à jour à mesure que nous développerons la disponibilité à la demande RHEL.

> [AZURE.NOTE] L’accès à l’infrastructure RHUI hébergée sur Azure est limité aux machines virtuelles figurant dans les [plages IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## Obtenir des mises à jour à partir d’un autre référentiel de mise à jour

Si vous avez besoin d’obtenir des mises à jour à partir d’un référentiel de mise à jour différent (à la place de l’infrastructure RHUI hébergée sur Azure), vous devez annuler l’inscription de vos instances à l’infrastructure RHUI et les réinscrire auprès de l’infrastructure de mise à jour de votre choix (par exemple, Red Hat Satellite ou CDN Portail client Red Hat). Vous aurez besoin d’abonnements Red Hat appropriés pour ces services et d’une inscription pour l’[accès cloud Red Hat dans Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Pour annuler l’inscription à l’infrastructure RHUI et recréer une inscription à votre infrastructure de mise à jour, suivez les étapes ci-dessous.

1.	Modifiez /etc/yum.repos.d/rh-cloud.repo et remplacez tous les `enabled=1` par `enabled=0`. Par exemple :

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	Modifiez /etc/yum/pluginconf.d/rhnplugin.conf et remplacez `enabled=0` par `enabled=1`.
3.	Ensuite inscrivez-vous auprès de l’infrastructure de votre choix, telle que le Portail client Red Hat. Suivez le guide des solutions Red Hat pour savoir [comment inscrire et abonner un système au Portail client Red Hat](https://access.redhat.com/solutions/253273).

> [AZURE.NOTE] L’accès à l’infrastructure RHUI hébergée sur Azure est inclus dans le prix de l’image RHEL avec paiement à l’utilisation. Annuler l’inscription à l’infrastructure RHUI hébergée sur Azure d’une machine virtuelle RHEL avec paiement à l’utilisation ne convertit pas la machine virtuelle en une machine virtuelle de type BYOL (apportez votre propre licence) et, par conséquent, vous pouvez encourir des frais doubles si vous inscrivez la même machine virtuelle auprès d’une autre source de mises à jour.
> 
> Si vous devez systématiquement utiliser une infrastructure de mise à jour autre que l’infrastructure RHUI hébergée sur Azure, envisagez de créer et déployer vos propres images (de type BYOL), comme cela est décrit dans l’article [Créer et télécharger une machine virtuelle basée sur Red Hat pour Azure](virtual-machines-linux-redhat-create-upload-vhd.md).

## Étapes suivantes
Pour créer une machine virtuelle Red Hat Enterprise Linux à partir d’une image avec paiement à l’utilisation dans le service Place de marché Azure et tirer parti de l’infrastructure RHUI hébergée sur Azure, consultez la page [Place de marché Azure](https://azure.microsoft.com/marketplace/partners/redhat/). Vous serez en mesure d’utiliser `yum update` dans votre instance RHEL sans aucun paramétrage supplémentaire.

<!---HONumber=AcomDC_0713_2016-->