---
title: Forum Aux Questions (FAQ) concernant SQL Server sur les machines virtuelles Azure | Microsoft Docs
description: "Cet article fournit des réponses aux questions fréquemment posées sur l&quot;exécution de SQL Server sur les machines virtuelles Azure."
services: virtual-machines-windows
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/15/2016
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 8a506a4c9a304ebbb25d0e522f48f0eae9ca706c
ms.openlocfilehash: 1e1123f78ee61fa392dcd7eb1a649331a4fe1c53


---
# <a name="sql-server-on-azure-virtual-machines-faq"></a>Forum Aux Questions (FAQ) concernant SQL Server sur les machines virtuelles Azure
Cette rubrique fournit des réponses à la plupart des questions courantes sur l’exécution de [SQL Server sur les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)
1. **Comment créer une machine virtuelle Azure avec SQL Server ?**
   
    Il existe deux façons d'effectuer cette opération. La solution la plus simple consiste à créer une machine virtuelle qui inclut SQL Server. Pour obtenir un didacticiel sur l’inscription à Azure et la création d’une machine virtuelle SQL à partir du portail, consultez la rubrique [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Vous avez également la possibilité d’installer manuellement SQL Server sur une machine virtuelle et de réutiliser une licence locale avec [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. **Quelle est la différence entre les machines virtuelles SQL et le service SQL Database ?**
   
    Conceptuellement, l’exécution de SQL Server sur une machine virtuelle Azure n’est pas si différente de l’exécution de SQL Server dans un centre de données distant. En revanche, la [base de données SQL](../sql-database/sql-database-technical-overview.md) fournit une base de données relationnelle complète en tant que service. Avec SQL Database, vous n’avez pas besoin d’accéder aux machines qui hébergent vos bases de données. Pour obtenir une comparaison complète, consultez la rubrique [Choisir une option de SQL Server cloud : Base de données SQL Azure (PaaS) ou SQL Server sur des machines virtuelles Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).
3. **Comment migrer ma base de données SQL Server locale vers le cloud ?**
   
    Créez tout d’abord une machine virtuelle Azure avec une instance SQL Server. Puis migrez vos bases de données locales vers cette instance. Pour les stratégies de migration de données, consultez la rubrique [Migrer une base de données SQL Server vers SQL Server dans une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Puis-je modifier les fonctionnalités installées ou installer une seconde instance SQL Server sur la même machine virtuelle ?**
   
    Oui. Le support d’installation de SQL Server se trouve dans un dossier sur le lecteur **C** . Exécutez **Setup.exe** à partir de cet emplacement pour ajouter de nouvelles instances SQL Server ou pour modifier d’autres fonctionnalités SQL Server installées sur la machine.
5. **Comment mettre à niveau vers une nouvelle version/édition de SQL Server dans une machine virtuelle Azure ?**
   
    Il n'existe actuellement aucune mise à niveau sur place pour une instance SQL Server exécutée sur une machine virtuelle Azure. Créez une machine virtuelle Azure avec la version/édition de SQL Server souhaitée, puis migrez vos bases de données vers le nouveau serveur à l’aide des [techniques de migration de données](virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)standard.
6. **Comment installer ma copie sous licence de SQL Server sur une machine virtuelle Azure ?**
   
    Copiez le support d'installation de SQL Server dans la machine virtuelle Windows Server, puis installez SQL Server sur la machine virtuelle. Pour des raisons de licence, vous devez posséder [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).
7. **Faut-il payer les frais SQL d'une machine virtuelle si elle est utilisée uniquement comme machine de secours ou de basculement ?**
   
    Si vous créez la machine virtuelle SQL via la galerie, vous devez posséder une licence distincte pour la machine virtuelle SQL de secours et le tarif reste le même. Si vous installez SQL Server manuellement sur une machine virtuelle avec [License Mobility](https://azure.microsoft.com/pricing/license-mobility/), vous pouvez avoir une instance SQL passive gratuite disponible pour le basculement. Veuillez consulter les restrictions et la configuration requise.
8. **Comment les mises à jour et les service packs sont-ils appliqués sur une machine virtuelle SQL Server ?**
   
    Les machines virtuelles vous permettent de contrôler la machine hôte, y compris quand et comment appliquer les mises à jour. Pour le système d’exploitation, vous pouvez appliquer manuellement les mises à jour Windows, ou activer un service de planification appelé [Mise à jour corrective automatisée](virtual-machines-windows-classic-sql-automated-patching.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). La mise à jour corrective automatisée installe toutes les mises à jour importantes, y compris les mises à jour de SQL Server de cette catégorie. Les autres mises à jour SQL Server facultatives doivent être installées manuellement.
9. **Est-il possible de définir des configurations non affichées dans la galerie de machines virtuelles (par exemple, Windows 2008 R2 + SQL Server 2012) ?**
   
    Non. Pour les images de la galerie de machines virtuelles incluant SQL Server, vous devez sélectionner une des images fournies.
10. **Comment installer les outils SQL Data sur ma machine virtuelle Azure ?**
    
     Téléchargez et installez les outils SQL Data à partir de [Microsoft SQL Server Data Tools - Business Intelligence pour Visual Studio 2013 ](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Ressources
Pour obtenir une vue d’ensemble de SQL Server sur les machines virtuelles Azure, regardez la vidéo [Azure VM is the best platform for SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016). Une bonne présentation est également disponible à la rubrique [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Les autres ressources incluent :

* [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrating a Database to SQL Server on an Azure VM](virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Meilleures pratiques relatives aux performances de SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Modèles d'application et stratégies de développement pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Nov16_HO3-->


