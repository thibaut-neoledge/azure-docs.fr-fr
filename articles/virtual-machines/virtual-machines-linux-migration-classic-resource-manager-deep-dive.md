<properties
	pageTitle="Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager | Microsoft Azure"
	description="Cet article présente une étude technique approfondie de la migration de ressources prise en charge par la plateforme de l’environnement Classic vers Azure Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="mahthi"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="kasing"/>

# Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager

Dans cet article, nous procédons à une étude technique approfondie de la migration au niveau des ressources et des fonctionnalités pour vous aider à comprendre la manière dont la plateforme effectue la migration des ressources d’un modèle de déploiement Classic vers un modèle de déploiement Azure Resource Manager. Pour en savoir plus, consultez l’article d’annonce de service [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md).

## Instructions détaillées concernant la migration au niveau des ressources et des fonctionnalités

Les représentations Classic et Resource Manager des ressources sont décrites dans la table suivante. Les fonctionnalités et ressources non répertoriées dans ce tableau ne sont pas prises en charge actuellement.

| Représentation Classic | Représentation Resource Manager | Remarques détaillées | | |
|--------------------------------------------------------|-------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| Nom du service cloud | Nom DNS | Pendant la migration, nous créons un nouveau groupe de ressources pour chaque service cloud avec le modèle de nommage `<cloudservicename>-migrated`. Ce groupe de ressources contient toutes les ressources. Le nom du service cloud devient un nom DNS associé à l’adresse IP publique. | | |
| Machine virtuelle | Machine virtuelle | Les propriétés spécifiques à la machine virtuelle sont inchangées après la migration. Notez que certaines informations osProfile, notamment le nom de l’ordinateur, ne sont pas stockées dans le modèle de déploiement Classic et restent vides après la migration. | | |
| Ressources de disque attachées à une machine virtuelle | Disques implicites attachés à une machine virtuelle | Les disques ne sont pas modélisés comme ressources de niveau supérieur dans le modèle de déploiement Resource Manager. Ils font l’objet d’une migration sous forme de disques implicites sous la machine virtuelle. À ce stade, nous prenons en charge uniquement les disques attachés à une machine virtuelle. Pour effectuer la migration, les machines virtuelles du modèle de déploiement Ressource Manager peuvent maintenant utiliser des comptes de stockage Classic. Ceci facilite la migration des disques vers le modèle Resource Manager sans aucune mise à jour. | | |
| Extensions de machine virtuelle | Extensions de machine virtuelle | Toutes les extensions de ressource font l’objet d’une migration depuis le déploiement Classic, à l’exception des extensions XML. | | |
| Certificats de machine virtuelle | Certificats dans Azure Key Vault | Si un service cloud contient des certificats de service, la plateforme crée un coffre de clés Azure pour chaque service cloud et y déplace les certificats. Les machines virtuelles sont mises à jour pour référencer les certificats du coffre de clés. | | |
| Configuration WinRM | Configuration WinRM sous osProfile | La configuration de Windows Remote Management est déplacée sans être modifiée dans le cadre de la migration. | | |
| Propriété de groupe à haute disponibilité | Ressource de groupe à haute disponibilité | La spécification des groupes à haute disponibilité était une propriété de la machine virtuelle dans le modèle de déploiement Classic. Les groupes à haute disponibilité deviennent une ressource de niveau supérieur dans le cadre de la migration. Notez que nous ne prenons pas en charge les configurations suivantes : plusieurs groupes à haute disponibilité par service cloud, ou un ou plusieurs groupes à haute disponibilité en même temps que des machines virtuelles ne figurant dans aucun groupe à haute disponibilité d’un service cloud. | | |
| Configuration réseau sur une machine virtuelle | Interface réseau principale | La configuration réseau sur une machine virtuelle est représentée sous forme de ressource d’interface réseau principale après la migration. Dans le cas des machines virtuelles qui ne figurent pas dans un réseau virtuel, l’adresse IP interne change lors de la migration. | | |
| Plusieurs interfaces réseau sur une machine virtuelle | Interfaces réseau | Si plusieurs interfaces réseau sont associées à une machine virtuelle, chaque interface réseau devient une ressource de niveau supérieur dans le cadre de la migration vers le modèle de déploiement Resource Manager, ainsi que toutes les propriétés. | | |
| Jeux de points de terminaison soumis à l’équilibrage de charge | Équilibrage de charge | Dans le modèle de déploiement Classic, la plateforme a affecté un équilibreur de charge implicite à chaque service cloud. Pendant la migration, nous créons une autre ressource d’équilibreur de charge, et le jeu de points de terminaison d’équilibrage de charge devient des règles d’équilibreur de charge. | | |
| Règles NAT entrantes | Règles NAT entrantes | Les points de terminaison d’entrée définis sur la machine virtuelle sont convertis en règles NAT (Network Access Translation) de trafic entrant sous l’équilibreur de charge lors de la migration. | | |
| Adresse IP virtuelle | Adresse IP publique avec nom DNS | L’adresse IP virtuelle devient une adresse IP publique et est associée à l’équilibreur de charge. | | |
| Réseau virtuel | Réseau virtuel | Le réseau virtuel fait l’objet d’une migration vers le modèle de déploiement Resource Manager avec toutes ses propriétés. Un nouveau groupe de ressources est créé avec le nom `-migrated`. Prenez note des [configurations non prises en charge](virtual-machines-windows-migration-classic-resource-manager.md). | | |
| IP réservées | Adresse IP publique avec méthode d’allocation statique | La migration des IP réservées associées à l’équilibreur de charge est effectuée en même temps que la migration du service cloud ou de la machine virtuelle. La migration des IP réservées non associées n’est pas prise en charge actuellement. | | |
| Adresse IP publique par machine virtuelle | Adresse IP publique avec méthode d’allocation dynamique | L’adresse IP publique associée à la machine virtuelle est convertie en ressource d’adresse IP publique avec la méthode d’allocation statique. | | |
| Groupes de sécurité réseau | Groupes de sécurité réseau | Les groupes de sécurité réseau (NSG) associés à un sous-réseau sont clonés dans le cadre de la migration vers le modèle de déploiement Resource Manager. Notez que le NSG du modèle de déploiement Classic n’est pas supprimé lors de la migration. Toutefois, les opérations du plan de gestion relatives au NSG sont bloquées pendant le processus de migration. | | |
| Serveurs DNS | Serveurs DNS | La migration des serveurs DNS associés à un réseau virtuel ou à la machine virtuelle est effectuée dans le cadre de la migration des ressources correspondantes avec toutes les propriétés. | | |
| Itinéraires définis par l’utilisateur (UDR) | Itinéraires définis par l’utilisateur (UDR) | Les Itinéraires définis par l’utilisateur associés à un sous-réseau sont clonés dans le cadre de la migration vers le modèle de déploiement Resource Manager. Notez que l’itinéraire défini par l’utilisateur dans le modèle de déploiement Classic n’est pas supprimé lors de la migration. Toutefois, les opérations du plan de gestion relatives à l’itinéraire défini par l’utilisateur sont bloquées pendant le processus de migration. | | |
| Propriété de transfert IP sur la configuration réseau d’une machine virtuelle | Propriété de transfert IP sur la carte d’interface réseau (NIC) | La propriété de transfert IP sur une machine virtuelle est convertie en propriété sur l’interface réseau pendant la migration. | | |
| Équilibreur de charge avec plusieurs adresses IP | Équilibreur de charge avec plusieurs ressources d’adresses IP publiques | Chaque adresse IP publique associée à l’équilibreur de charge est convertie en ressource d’adresse IP publique et associée à l’équilibreur de charge après la migration. | | |
| Noms DNS internes sur la machine virtuelle | Noms DNS internes sur la carte réseau | Pendant la migration, les suffixes DNS internes pour les machines virtuelles sont migrés vers une propriété en lecture seule nommée « InternalDomainNameSuffix » sur la carte réseau. Le suffixe reste inchangé après la migration, et la résolution des machines virtuelles doit continuer à fonctionner comme auparavant. | | |

## Illustration d’une procédure pas à pas de migration simple

Les exemples de captures d’écran suivants sont une représentation d’un service cloud avec une machine virtuelle (ne figurant pas dans un réseau virtuel) après la phase de préparation.

![Capture d’écran illustrant la représentation Classic après la préparation](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png) ![Capture d’écran illustrant la représentation Resource Manager après la préparation](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## Étapes suivantes

À présent que vous savez en quoi consiste la migration des ressources IaaS Classic vers Resource Manager, vous pouvez commencer à effectuer la migration des ressources.

- [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Cloner une machine virtuelle Classic vers Azure Resource Manager à l’aide de scripts PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0824_2016-->