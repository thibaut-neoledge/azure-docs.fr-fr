---
title: Abonnement et comptes pour les machines virtuelles Windows dans Azure | Microsoft Docs
description: "Découvrez les principales instructions de conception et d’implémentation pour les abonnements et les comptes sur Azure."
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 761fa847-78b0-4078-a33a-d95d198d1029
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 233116deaaaf2ac62981453b05c4a5254e836806
ms.openlocfilehash: 09c87e78aaccae4f70b2ff61cfc2a0a174dbdfaf


---
# <a name="azure-subscription-and-accounts-guidelines"></a>Instructions pour les abonnements et les comptes Azure
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Cet article se concentre sur la compréhension de l’approche de la gestion des abonnements et des comptes lorsque votre environnement et votre base d’utilisateurs augmentent.

## <a name="implementation-guidelines-for-subscriptions-and-accounts"></a>Instructions d’implémentation pour les abonnements et les comptes
Décisions :

* Quel est l’ensemble d’abonnements et de comptes dont vous avez besoin pour héberger votre charge de travail ou votre infrastructure informatique ?
* Comment détailler la hiérarchie pour l’adapter à votre organisation ?

Tâches :

* Définissez la hiérarchie logique de votre organisation que vous souhaitez gérer à partir d’un niveau d’abonnement.
* Pour faire correspondre cette hiérarchie logique, définissez les comptes nécessaires et les abonnements sous chaque compte.
* Créez l’ensemble d’abonnements et de comptes à l’aide de votre convention d’affectation de noms.

## <a name="subscriptions-and-accounts"></a>Abonnements et comptes
Pour utiliser Azure, vous avez besoin d’un ou de plusieurs abonnements Azure. Des ressources telles que des machines virtuelles ou des réseaux virtuels existent dans le contexte de ces abonnements.

* Les clients d’entreprises disposent généralement d’une inscription d’entreprise, qui est la ressource principale dans la hiérarchie et est associée à un ou plusieurs comptes.
* Pour les particuliers et les clients sans inscription d’entreprise, la ressource principale est le compte.
* Les abonnements sont associés à des comptes, et chaque compte peut être associé à un ou plusieurs abonnements. Azure enregistre les informations de facturation au niveau de l’abonnement.

La relation compte/abonnement étant limitée à deux niveaux de hiérarchie, il est important d'aligner la convention d'affectation de noms des comptes et des abonnements sur les besoins liés à la facturation. Par exemple, si une entreprise multinationale utilise Azure, elle peut choisir d’avoir un seul compte par région et des abonnements gérés au niveau régional :

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

Par exemple, vous pouvez utiliser la structure suivante :

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

Si une région décide d’avoir plusieurs abonnements associés à un groupe spécifique, la convention d’affectation de noms doit comprendre un code pour les données supplémentaires dans le nom du compte ou de l’abonnement. Cette organisation permet le transfert de données de facturation pour générer de nouveaux niveaux de hiérarchie lors de l’établissement de rapports de facturation :

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

L’organisation peut ressembler à l’exemple suivant :

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

Nous fournissons une facturation détaillée au moyen d’un fichier téléchargeable, pour un compte unique ou pour tous les comptes liés à un accord d’entreprise.

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]




<!--HONumber=Jan17_HO5-->


