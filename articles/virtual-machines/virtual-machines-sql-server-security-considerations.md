<properties 
	pageTitle="Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure"
	description="Fournit des conseils généraux pour la sécurisation de SQL Server s’exécutant sur une machine virtuelle Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="jroth"/>

# Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure

Cette rubrique comprend des instructions de sécurité générales pour créer un accès sécurisé aux instances SQL Server dans une machine virtuelle Azure. Toutefois, pour garantir une meilleure protection de vos instances de base de données SQL Server dans Azure, nous vous recommandons d’implémenter les pratiques de sécurité locales classiques en plus des meilleures pratiques de sécurité de Azure.

Pour plus d’informations sur les pratiques de sécurité de SQL Server, consultez la page [Meilleures pratiques de sécurité de SQL Server 2008 R2 - Tâches opérationnelles et administratives ](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

Azure est conforme à plusieurs règlements et normes de l’industrie et vous permet de créer une solution conforme avec SQL Server exécuté dans un ordinateur virtuel. Pour plus d’informations sur les exigences réglementaires relatives à Azure, consultez [Centre de gestion de la confidentialité Azure](http://azure.microsoft.com/support/trust-center/).

Voici la liste des recommandations de sécurité à prendre en compte lors de la configuration et de la connexion à l’instance de SQL Server dans une machine virtuelle Azure.

## Considérations relatives à la gestion des comptes ;

- Créez un seul compte d’administrateur local avec un nom différent de **Administrator**.

- Utilisez des mots de passe forts complexes pour tous vos comptes. Pour plus d’informations sur la création d’un mot de passe fort, consultez l’article [Créer des mots de passe forts](http://go.microsoft.com/fwlink/?LinkId=293596) dans le Centre de sécurité.

- Par défaut, Azure sélectionne l’authentification Windows pendant l’installation de la machine virtuelle SQL Server. Par conséquent, la connexion **SA** est désactivée et un mot de passe est affecté par l’installation. Nous recommandons que la connexion **SA** ne soit ni utilisée ni activée. Voici d’autres stratégies applicables si vous souhaitez une connexion SQL :
	- Créez un compte SQL avec des autorisations **CONTROL SERVER**.
	- Si vous devez utiliser une connexion **SA**, activez-la, renommez-la et attribuez un nouveau mot de passe.
	- Les deux options citées précédemment exigent la modification du mode d’authentification en Mode d’authentification SQL Server et Windows. Pour plus d’informations, consultez Modifier le mode d’authentification du serveur.

- Créez un compte SQL avec des autorisations CONTROL SERVER.

- Si vous devez utiliser une connexion SA, activez-la, renommez-la et attribuez un nouveau mot de passe.

- Les deux options citées précédemment exigent la modification du mode d’authentification en **Mode d’authentification SQL Server et Windows**. Pour plus d’informations, consultez [Modifier le mode d’authentification du serveur](https://msdn.microsoft.com/library/ms188670.aspx).

## Considérations relatives à la sécurisation des connexions vers une machine virtuelle Azure :

- Envisagez d’utiliser le [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) pour gérer les ordinateurs virtuels au lieu de ports RDP publics.

- Supprimez tous les points de terminaison sur l’ordinateur virtuel si vous ne les utilisez pas.

- Activez l’option de connexion chiffrée pour une instance du moteur de base de données SQL Server dans des machines virtuelles Azure. Configurez l’instance SQL Server avec un certificat signé. Pour plus d’informations, consultez [Activer les connexions chiffrées dans le moteur de base de données](https://msdn.microsoft.com/library/ms191192.aspx) et [Syntaxe de la chaîne de connexion](https://msdn.microsoft.com/library/ms254500.aspx).

- Si vos machines virtuelles doivent être accessibles uniquement à partir d’un réseau spécifique, utilisez le Pare-feu Windows pour limiter l’accès à certaines adresses IP ou sous-réseaux. Vous pouvez également envisager d’ajouter une liste de contrôle d’accès réseau (ACL) sur votre point de terminaison pour limiter le trafic uniquement aux clients que vous autorisez. Pour obtenir des instructions sur l’utilisation de listes ACL avec des points de terminaison, consultez la page [Gestion de l’ACL sur un point de terminaison](virtual-machines-set-up-endpoints.md#manage-the-acl-on-an-endpoint)

## Étapes suivantes

Si vous vous intéressez également à l'optimisation des performances, consultez la page [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-performance-best-practices.md).

Pour d’autres rubriques relatives à l’utilisation de SQL Server sur des machines virtuelles Azure, consultez la rubrique [SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=September15_HO1-->