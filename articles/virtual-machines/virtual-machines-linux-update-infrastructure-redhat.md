<properties
   pageTitle="Une infrastructure de mise à jour pour les images Red Hat Enterprise Linux | Microsoft Azure"
   description="Présente le service de mise à jour yum pour une instance de Red Hat Enterprise Linux à la demande dans Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="01/13/2016"
   ms.author="kyliel"/>

# Une infrastructure de mise à jour pour les images Red Hat Enterprise Linux

En utilisant Red Hat Update Infrastructure (RHUI) dans Azure, vous pouvez gérer le contenu du référentiel yum pour les images Azure Red Hat Enterprise Linux (RHEL). Les instances RHEL à la demande que vous avez créées à partir d'Azure Marketplace auront ainsi accès au référentiel yum régional. Cela permet aux instances RHEL de recevoir des mises à jour incrémentielles.

La liste de référentiels yum, gérée par RHUI, est configurée dans votre instance RHEL lors de l’approvisionnement. Vous n'avez pas besoin effectuer de configuration supplémentaire : il suffit d'exécuter `yum update` une fois votre instance RHEL en fonctionnement.

## Vue d’ensemble de RHUI
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) constitue une solution hautement évolutive pour gérer le contenu du référentiel yum pour les instances cloud de Red Hat Enterprise Linux hébergées par les fournisseurs cloud certifiés Red Hat. À partir du projet Pulp en amont, RHUI permet aux fournisseurs de cloud de mettre en miroir localement le contenu du référentiel hébergé par Red Hat, de créer des référentiels personnalisés avec leur propre contenu et de rendre ces référentiels accessibles à un grand groupe d'utilisateurs finaux au moyen d’un système à équilibrage de charge de distribution de contenu.

## Régions où RHUI est déployé
RHUI est déployé dans toutes les régions publiques répertoriées dans le [tableau de bord d’état Azure](https://azure.microsoft.com/status/). Cela signifie que vous pouvez obtenir le service de mise à jour yum sans frais supplémentaires dans ces régions. Ces informations feront l’objet d’une mise à jour à l'avenir.

## Obtenir des mises à jour à partir d'un référentiel de mise à jour locale (par exemple, Red Hat Network Satellite)

Pour obtenir des mises à jour à partir d'un référentiel de mise à jour locale, vous devez disposer d’une licence Red Hat Cloud Access et d’un abonnement Red Hat à Azure.

Ensuite, vous devez annuler l'inscription de RHUI et recréer une inscription à votre infrastructure de mise à jour locale. Vous trouverez ci-dessous des instructions détaillées.

1.	Modifiez /etc/yum.repos.d/rh-cloud.repo et transformez tous les `enabled=1` en `enabled=0`. Par exemple :

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	Modifiez /etc/yum/pluginconf.d/rhnplugin.conf et transformez `enabled=0` en `enabled=1`.
3.	Ensuite, inscrivez-vous à Red Hat Network (RHN).

        rhn_register

    ou

        rhnreg_ks


> [AZURE.NOTE] Les transferts de données sortants sont facturés (voir la [tarification](http://azure.microsoft.com/pricing/details/data-transfers/)). Nous vous recommandons d'utiliser RHUI par défaut pour obtenir les mises à jour incrémentielles sans frais supplémentaires.

## Étapes suivantes
Maintenant que vous comprenez RHUI dans Azure, vous pouvez créer une image RHEL à partir d’[Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/) et utiliser `yum update` dans votre instance RHEL.

<!---HONumber=AcomDC_0224_2016-->