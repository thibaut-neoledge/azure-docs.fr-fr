---
title: "Considérations relatives à la sécurité de SQL Server dans Azure | Microsoft Docs"
description: "Cette rubrique fait référence à des ressources créées avec le modèle de déploiement classique, et fournit des indications générales pour la sécurisation de SQL Server s’exécutant sur une machine virtuelle Azure."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/15/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9fc96d70592bd55685ebbf1b80f6017b74f58925
ms.lasthandoff: 03/25/2017


---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure
Cette rubrique comprend des instructions de sécurité générales pour créer un accès sécurisé aux instances SQL Server dans une machine virtuelle Azure. Toutefois, pour garantir une meilleure protection de vos instances de base de données SQL Server dans Azure, nous vous recommandons d’implémenter les pratiques de sécurité locales classiques en plus des meilleures pratiques de sécurité de Azure.

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

Pour plus d’informations sur les pratiques de sécurité de SQL Server, voir [Meilleures pratiques de sécurité de SQL Server 2008 R2 - Tâches opérationnelles et administratives](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

Azure est conforme à plusieurs règlements et normes de l’industrie et vous permet de créer une solution conforme avec SQL Server exécuté dans un ordinateur virtuel. Pour plus d’informations sur les exigences réglementaires relatives à Azure, voir [Centre de gestion de la confidentialité Azure](https://azure.microsoft.com/support/trust-center/).

Voici la liste des recommandations de sécurité à prendre en compte lors de la configuration et de la connexion à l’instance de SQL Server dans une machine virtuelle Azure.

## <a name="considerations-for-managing-accounts"></a>Considérations relatives à la gestion des comptes ;
* Créez un seul compte d’administrateur local avec un nom différent de **Administrator**.
* Utilisez des mots de passe forts complexes pour tous vos comptes. Pour plus d’informations sur la création d’un mot de passe fort, consultez l’article [Conseils pour créer un mot de passe fort](http://windows.microsoft.com/en-us/windows-vista/Tips-for-creating-a-strong-password) .
* Par défaut, Azure sélectionne l’authentification Windows pendant l’installation de la machine virtuelle SQL Server. Par conséquent, la connexion **SA** est désactivée et un mot de passe est affecté par l’installation. Nous recommandons que la connexion **SA** ne soit ni utilisée ni activée. Voici d’autres stratégies applicables si vous souhaitez une connexion SQL :
  
  * Créez un compte SQL membre du groupe ADministrateurs système.
  * Si vous devez utiliser une connexion **SA** , activez-la, renommez-la et attribuez un nouveau mot de passe.
  * Les deux options citées précédemment exigent la modification du mode d’authentification en **Mode d’authentification SQL Server et Windows**. Pour plus d’informations, consultez [Modifier le mode d’authentification du serveur](https://msdn.microsoft.com/library/ms188670.aspx).

## <a name="considerations-for-securing-connections-to-azure-virtual-machine"></a>Considérations relatives à la sécurisation des connexions vers une machine virtuelle Azure :
* Envisagez d’utiliser le [réseau virtuel Azure](../../../virtual-network/virtual-networks-overview.md) pour gérer les ordinateurs virtuels au lieu de ports RDP publics.
* Utilisez un [groupe de sécurité réseau](../../../virtual-network/virtual-networks-nsg.md) (NSG) pour autoriser ou refuser le trafic réseau vers votre machine virtuelle. Si vous souhaitez utiliser un groupe de sécurité réseau et une ACL de point de terminaison déjà en place, supprimez d'abord l’ACL de point de terminaison. Pour en savoir plus sur cette procédure, consultez [Gestion des listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Si vous utilisez des points de terminaison, supprimez-les sur la machine virtuelle si vous n’en avez pas besoin. Pour obtenir des instructions sur l’utilisation de listes ACL avec des points de terminaison, voir [Gestion de l’ACL sur un point de terminaison](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint)
* Activez l’option de connexion chiffrée pour une instance du moteur de base de données SQL Server dans des machines virtuelles Azure. Configurez l’instance SQL Server avec un certificat signé. Pour plus d’informations, voir [Activation des connexions chiffrées dans le moteur de base de données](https://msdn.microsoft.com/library/ms191192.aspx) et [Syntaxe de la chaîne de connexion](https://msdn.microsoft.com/library/ms254500.aspx).
* Si vos machines virtuelles doivent être accessibles uniquement à partir d’un réseau spécifique, utilisez le Pare-feu Windows pour limiter l’accès à certaines adresses IP ou sous-réseaux.

## <a name="next-steps"></a>Étapes suivantes
Si vous vous intéressez également à l’optimisation des performances, voir [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-performance.md).

Pour d’autres rubriques relatives à l’utilisation de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).


