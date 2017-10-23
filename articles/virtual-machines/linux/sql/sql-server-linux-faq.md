---
title: Forum Aux Questions (FAQ) concernant SQL Server sur les machines virtuelles Linux Azure | Microsoft Docs
description: "Cet article fournit des réponses aux questions fréquemment posées sur l’exécution de SQL Server sur les machines virtuelles Linux Azure."
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 10/05/2017
ms.author: jroth
ms.openlocfilehash: a001ae116e33e0b7be4431b0bc4c8bb319f4e801
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Forum Aux Questions (FAQ) concernant SQL Server sur les machines virtuelles Linux Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Cette rubrique fournit des réponses à la plupart des questions courantes sur l’exécution de [SQL Server sur les machines virtuelles Linux Azure](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Cette rubrique se concentre sur les problèmes spécifiques de SQL Server sur les machines virtuelles Linux. Si vous exécutez SQL Server sur des machines virtuelles Windows, consultez le [Forum Aux Questions Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)

1. **Comment faire pour créer une machine virtuelle Linux Azure avec SQL Server ?**

   La solution la plus simple consiste à créer une machine virtuelle Linux qui inclut SQL Server. Pour obtenir un didacticiel sur l’inscription à Azure et la création d’une machine virtuelle SQL à partir du portail, consultez la rubrique [Approvisionnement d’une machine virtuelle Linux SQL Server dans le portail Azure](provision-sql-server-linux-virtual-machine.md). Vous avez également la possibilité d’installer manuellement SQL Server sur une machine virtuelle avec une version sous licence gratuite (Developer ou Express) ou en réutilisant une licence locale. Si vous utilisez votre propre licence, vous devez posséder [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Comment mettre à niveau vers une nouvelle version/édition de SQL Server dans une machine virtuelle Azure ?**

   Il n'existe actuellement aucune mise à niveau sur place pour une instance SQL Server exécutée sur une machine virtuelle Azure. Créez une machine virtuelle Azure avec la version/édition de SQL Server souhaitée, puis migrez vos bases de données vers le nouveau serveur à l’aide des [techniques de migration de données standard](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

1. **Comment installer ma copie sous licence de SQL Server sur une machine virtuelle Azure ?**

   Commencez par créer une machine virtuelle uniquement Linux. Puis exécutez les [étapes d’installation de SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) pour votre distribution Linux. À moins que vous n’installiez l’une des éditions sous licence gratuite de SQL Server, vous devez aussi disposer d’une licence SQL Server et de [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Existe-t-il des images de machine virtuelle Linux BYOL (apportez votre propre licence) pour SQL Server ?**

   À ce stade, il n’existe aucune image de machine virtuelle Linux BYOL pour SQL Server. Toutefois, vous pouvez installer manuellement SQL Server sur une machine virtuelle Linux uniquement comme indiqué dans les questions précédentes.

1. **Puis-je modifier une machine virtuelle pour utiliser ma propre licence SQL Server si elle a été créée à partir de l’une des images de la galerie avec paiement à l’utilisation ?**

   Non. Vous ne pouvez pas passer du mode de licence avec paiement à la minute à l’utilisation de votre propre licence. Vous devez créer une machine virtuelle Linux, installer SQL Server et faire migrer vos données. Consultez la question précédente pour plus d’informations sur l’apport de votre propre licence.

1. **Quels sont les packages SQL Server associés également installés ?**

   Pour voir les packages SQL Server installés par défaut sur les machines virtuelles Linux SQL Server, consultez [Packages installés](sql-server-linux-virtual-machines-overview.md#packages).

1. **Les solutions de haute disponibilité SQL Server sont-elles prises en charge sur les machines virtuelles Azure ?**

   Pas pour l'instant. Les groupes de disponibilité AlwaysOn et le clustering de basculement requièrent une solution de clustering dans Linux, telle que Pacemaker. Les distributions Linux prises en charge pour SQL Server ne gèrent pas les modules complémentaires de haute disponibilité dans le cloud.

1. **Pourquoi ne puis-je pas configurer une machine virtuelle SQL Server RHEL ou SLES avec un abonnement Azure associé à une limite de dépense ?**

   Les machines virtuelles RHEL et SLES exigent un abonnement sans aucune limite de dépense et un mode de paiement vérifié (généralement une carte de crédit) associé à l’abonnement. Si vous configurez une machine virtuelle RHEL ou SLES sans supprimer la limite de dépense, votre abonnement est désactivé et toutes les machines virtuelles/tous les services sont arrêtés. Si vous parvenez à cette situation, pour réactiver l’abonnement, [supprimez la limite de dépense](https://account.windowsazure.com/subscriptions). Vos crédits restants sont restaurés pour le cycle de facturation en cours, mais un surcoût pour l’image de machine virtuelle RHEL ou SLES est débité sur votre carte de crédit si vous choisissez de redémarrer votre abonnement et de continuer à l’exécuter.

## <a name="resources"></a>Ressources

**Machines virtuelles Linux** :

* [Vue d’ensemble de SQL Server sur une machine virtuelle Linux](sql-server-linux-virtual-machines-overview.md)
* [Approvisionner une machine virtuelle Linux SQL Server](provision-sql-server-linux-virtual-machine.md)
* [Documentation relative à SQL Server sur Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Machines virtuelles Windows** :

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Approvisionner une machine virtuelle Windows SQL Server](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Forum Aux Questions (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)