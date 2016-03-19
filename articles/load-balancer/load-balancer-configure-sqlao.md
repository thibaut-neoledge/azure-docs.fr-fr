<properties 
   pageTitle="Configuration de l'équilibrage de charge pour SQL Always On | Microsoft Azure"
   description="Configuration de l'équilibrage de charge pour fonctionner avec SQL Alway On et procédure d’exploitation de Powershell pour créer l'équilibrage de charge pour l'implémentation de SQL"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/21/2015"
   ms.author="joaoma" />

# Configuration de l'équilibrage de charge pour SQL Always On

Les groupes de disponibilité Always On de SQL Server peuvent maintenant être exécutés avec l’équilibrage de charge interne (ILB). Le groupe de disponibilité constitue la solution phare de SQL Server pour une haute disponibilité et la récupération d’urgence. L’écouteur du groupe de disponibilité permet aux applications clientes de se connecter en toute transparence au réplica principal, quel que soit le nombre de réplicas dans la configuration.

Le nom (DNS) de l'écouteur est mappé vers une adresse IP de l’équilibrage de charge et l’équilibrage de charge Azure dirige le trafic entrant vers le serveur principal du jeu de réplicas uniquement.


Vous pouvez utiliser le support de l'équilibrage de charge pour les points de terminaison (écouteur) de SQL Server AlwaysOn. Vous avez désormais un contrôle sur l'accessibilité de l'écouteur et vous pouvez choisir l'adresse IP de l’équilibrage de charge à partir d'un sous-réseau spécifique dans votre réseau virtuel.

À l'aide de l'ILB sur l'écouteur, le point de terminaison du serveur SQL (par exemple, Server=tcp:ListenerName,1433;Database=DatabaseName) est accessible uniquement via :

des services et machines virtuelles des mêmes services de réseau virtuel, des machines virtuelles à partir des services de réseau local et des machines virtuelles connectées entre les réseaux virtuels

![ILB\_SQLAO\_NewPic](./media/load-balancer-configure-sqlao/sqlao1.jpg)


L’équilibrage de charge interne ne peut être configuré qu’avec PowerShell.


## Ajout d’un équilibrage de charge interne au service 

### Étape 1.

Dans l'exemple suivant, nous allons configurer un réseau virtuel qui contient un sous-réseau appelé « Sous-réseau-1 » :

	Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc

Étape 2.

## Ajout des points de terminaison avec une charge équilibrée pour l'ILB sur chaque machine virtuelle

	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –
	DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

 	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

Dans l'exemple ci-dessus, la machine virtuelle 2 est appelée « sqlsvc1 » et « sqlsvc2 » en cours d'exécution dans le service de cloud « Sqlsvc ». Après avoir créé l'équilibre de charge avec le commutateur « DirectServerReturn », vous allez ajouter des points de terminaison d'équilibrage de charge pour permettre à SQL de configurer les écouteurs pour les groupes de disponibilité.

Vous trouverez davantage d’informations sur la création d’un SQL AlwaysOn [dans le déploiement de SQL AlwaysOn, à l’aide du modèle Azure Resource Manager](virtual-machines-workload-template-sql-alwayson.md) ou [à l’aide de la galerie de portail](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).


## Voir aussi

[Prise en main de la configuration d’un équilibrage de charge sur Internet](load-balancer-internet-getstarted.md)

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-internal-getstarted.md)

[Configuration d’un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=AcomDC_1223_2015-->