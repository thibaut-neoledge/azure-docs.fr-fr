<properties
	pageTitle="Forum Aux Questions (FAQ) concernant SQL Server sur les machines virtuelles Azure | Microsoft Azure"
	description="Cet article fournit des réponses aux questions fréquemment posées sur l'exécution de SQL Server sur les machines virtuelles Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/15/2016"
	ms.author="jroth"/>

# Forum Aux Questions (FAQ) concernant SQL Server sur les machines virtuelles Azure

Cette rubrique fournit des réponses à la plupart des questions courantes sur l’exécution de [SQL Server sur les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

## Forum Aux Questions (FAQ)

1. **Comment créer une machine virtuelle Azure avec SQL Server ?**

	Il existe deux façons d'effectuer cette opération. La solution la plus simple consiste à créer une machine virtuelle qui inclut SQL Server. Pour obtenir un didacticiel sur l'inscription à Azure et la création d'une machine virtuelle SQL à partir du portail, consultez la rubrique [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md). Vous avez également la possibilité d’installer manuellement SQL Server sur une machine virtuelle et de réutiliser une licence locale avec [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Quelle est la différence entre les machines virtuelles SQL et le service SQL Database ?**

	Conceptuellement, l’exécution de SQL Server sur une machine virtuelle Azure n’est pas si différente de l’exécution de SQL Server dans un centre de données distant. En revanche, [SQL Database](../sql-database/sql-database-technical-overview.md) fournit une base de données relationnelle complète en tant que service. Avec SQL Database, vous n’avez pas besoin d’accéder aux machines qui hébergent vos bases de données. Pour obtenir une comparaison complète, consultez la rubrique [Choisir une option de SQL Server cloud : Base de données SQL Azure (PaaS) ou SQL Server sur des machines virtuelles Azure (IaaS)](../sql-database/media/data-management-azure-sql-database-and-sql-server-iaas.md).

1. **Comment migrer ma base de données SQL Server locale vers le cloud ?**

	Créez tout d’abord une machine virtuelle Azure avec une instance SQL Server. Puis migrez vos bases de données locales vers cette instance. Pour les stratégies de migration de données, consultez la rubrique [Migrer une base de données SQL Server vers SQL Server dans une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md).

2. **Puis-je modifier les fonctionnalités installées ou installer une seconde instance SQL Server sur la même machine virtuelle ?**

	Oui. Le support d'installation de SQL Server se trouve dans un dossier sur le lecteur **C**. Exécutez **Setup.exe** depuis cet emplacement pour ajouter de nouvelles instances SQL Server ou pour modifier d’autres fonctionnalités SQL Server installées sur la machine.

3. **Comment mettre à niveau vers une nouvelle version/édition de SQL Server dans une machine virtuelle Azure ?**

	Il n'existe actuellement aucune mise à niveau sur place pour une instance SQL Server exécutée sur une machine virtuelle Azure. Créez une machine virtuelle Azure avec la version/édition de SQL Server souhaitée, puis migrez vos bases de données vers le nouveau serveur à l'aide des [techniques de migration de données](virtual-machines-windows-migrate-sql.md) standard.

4. **Comment installer ma copie sous licence de SQL Server sur une machine virtuelle Azure ?**

	Copiez le support d'installation de SQL Server dans la machine virtuelle Windows Server, puis installez SQL Server sur la machine virtuelle. Pour des raisons de licence, vous devez posséder [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).

5. **Faut-il payer les frais SQL d'une machine virtuelle si elle est utilisée uniquement comme machine de secours ou de basculement ?**

	Si vous créez la machine virtuelle SQL via la galerie, vous devez posséder une licence distincte pour la machine virtuelle SQL de secours et le tarif reste le même. Si vous installez SQL Server manuellement sur une machine virtuelle avec [License Mobility](https://azure.microsoft.com/pricing/license-mobility/), vous pouvez avoir une instance SQL passive gratuite disponible pour le basculement. Veuillez consulter les restrictions et la configuration requise.

6. **Comment les mises à jour et les service packs sont-ils appliqués sur une machine virtuelle SQL Server ?**

	Les machines virtuelles vous permettent de contrôler la machine hôte, y compris quand et comment appliquer les mises à jour. Pour le système d'exploitation, vous pouvez appliquer manuellement les mises à jour Windows, ou activer un service de planification appelé [Mise à jour corrective automatisée](virtual-machines-windows-classic-sql-automated-patching.md). La mise à jour corrective automatisée installe toutes les mises à jour importantes, y compris les mises à jour de SQL Server de cette catégorie. Les autres mises à jour SQL Server facultatives doivent être installées manuellement.

7. **Est-il possible de définir des configurations non affichées dans la galerie de machines virtuelles (par exemple, Windows 2008 R2 + SQL Server 2012) ?**

	Non. Pour les images de la galerie de machines virtuelles incluant SQL Server, vous devez sélectionner une des images fournies.

9. **Comment installer les outils SQL Data sur ma machine virtuelle Azure ?**

	Téléchargez et installez les outils SQL Data à partir de [Microsoft SQL Server Data Tools - Business Intelligence pour Visual Studio 2013](https://www.microsoft.com/fr-FR/download/details.aspx?id=42313).

## Ressources

Pour obtenir une vue d'ensemble de SQL Server sur les machines virtuelles Azure, regardez la vidéo [Azure VM is the best platform for SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016). Une bonne présentation est également disponible à la rubrique [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

Les autres ressources incluent :

- [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md)
- [Migrating a Database to SQL Server on an Azure VM](virtual-machines-windows-migrate-sql.md)
- [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
- [Meilleures pratiques relatives aux performances de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
- [Modèles d'application et stratégies de développement pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

<!---HONumber=AcomDC_0427_2016-->