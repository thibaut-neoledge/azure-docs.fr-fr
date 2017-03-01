---
title: "Prise en charge du pare-feu Azure DocumentDB et contrôle d’accès IP | Microsoft Docs"
description: "Découvrez comment utiliser les stratégies de contrôle d’accès IP pour la prise en charge du pare-feu dans les comptes de base de données Azure DocumentDB."
keywords: "contrôle d’accès IP, prise en charge du pare-feu"
services: documentdb
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: ankshah
translationtype: Human Translation
ms.sourcegitcommit: 08cac64a6b08266f78bca03f1139a13e9686ebc3
ms.openlocfilehash: 819602cda932ea698287724e307ebbd73f1af988


---
# <a name="documentdb-firewall-support"></a>Prise en charge du pare-feu dans DocumentDB
Pour sécuriser les données stockées dans un compte de base de données Azure DocumentDB, DocumentDB assure la prise en charge d’un [modèle d’autorisation](https://msdn.microsoft.com/library/azure/dn783368.aspx) basé sur une clé secrète, qui utilise un code d’authentification de message basé sur le hachage (HMAC) à forte intégrité. Le modèle d’autorisations basé sur un secret, DocumentDB, prend désormais en charge les contrôles d’accès basés sur une stratégie IP pour la prise en charge du pare-feu entrant. Ce modèle est très semblable aux règles de pare-feu d’un système de base de données traditionnel et renforce la sécurité du compte de base de données DocumentDB. Avec ce modèle, vous pouvez désormais configurer un compte de base de données DocumentDB pour qu’il soit accessible uniquement à partir d’un ensemble d’ordinateurs et/ou de services cloud approuvés. L’accès aux ressources de DocumentDB à partir de ces ensembles d’ordinateurs et de services approuvés nécessite toujours que l’appelant présente un jeton d’autorisation valide.

## <a name="ip-access-control-overview"></a>Vue d’ensemble du contrôle d’accès IP
Par défaut, un compte de base de données DocumentDB est accessible depuis l’Internet public tant que la demande est accompagnée d’un jeton d’autorisation valide. Pour configurer le contrôle d’accès basé sur la stratégie IP, l’utilisateur doit fournir le jeu d’adresses IP ou des plages d’adresses IP au format CIDR pour être ajouté à la liste d’adresses IP clients autorisées pour un compte de base de données donné. Une fois que cette configuration est appliquée, toutes les demandes provenant d’ordinateurs ne figurant pas sur cette liste autorisée seront bloquées par le serveur.  Le flux de traitement de connexion du contrôle d’accès basé sur IP est décrit dans le diagramme suivant.

![Diagramme illustrant le processus de connexion du contrôle d’accès basé sur IP](./media/documentdb-firewall-support/documentdb-firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>Connexions à partir de services cloud
Dans Azure, les services cloud sont une méthode très courante d’hébergement de la logique de service de couche intermédiaire à l’aide de DocumentDB. Pour autoriser l’accès à un compte de base de données DocumentDB à partir d’un service cloud, l’adresse IP publique de ce dernier doit être ajoutée à la liste d’adresses IP autorisées de votre compte de base de données DocumentDB en [configurant la stratégie de contrôle d’accès IP](#configure-ip-policy).  Cela garantit que toutes les instances de rôle des services cloud ont accès à votre compte de base de données DocumentDB. Vous pouvez récupérer des adresses IP pour vos services cloud dans le portail Azure, comme illustré dans la capture d’écran suivante.

![Capture d’écran illustrant l’adresse IP publique pour un service cloud affichée dans le portail Azure](./media/documentdb-firewall-support/documentdb-public-ip-addresses.png)

Lorsque vous faites évoluer votre service cloud en ajoutant des instances de rôle supplémentaires, ces nouvelles instances auront automatiquement accès au compte de base de données DocumentDB, car ils font partie du même service cloud.

## <a name="connections-from-virtual-machines"></a>Connexions à partir de machines virtuelles
Des [machines virtuelles](https://azure.microsoft.com/services/virtual-machines/) ou des [jeux de mise à l’échelle de machine virtuelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) peuvent également être utilisés pour héberger les services de couche intermédiaire à l’aide de DocumentDB.  Pour configurer le compte de base de données DocumentDB afin qu’il autorise l’accès à partir de machines virtuelles, les adresses IP publiques de la machine virtuelle ou du groupe de machines virtuelles identiques doivent figurer parmi les adresses IP autorisées de votre compte de base de données DocumentDB en [configurant la stratégie de contrôle d’accès IP](#configure-ip-policy). Vous pouvez récupérer des adresses IP pour des machines virtuelles dans le portail Azure, comme illustré dans la capture d’écran suivante.

![Capture d’écran illustrant une adresse IP publique pour une machine virtuelle affichée dans le portail Azure](./media/documentdb-firewall-support/documentdb-public-ip-addresses-dns.png)

Lorsque vous ajoutez des instances de machine virtuelle supplémentaires au groupe, elles disposent automatiquement d’un accès à votre compte de base de données DocumentDB.

## <a name="connections-from-the-internet"></a>Connexions à partir d’Internet
Lorsque vous accédez à un compte de base de données DocumentDB à partir d’un ordinateur sur Internet, l’adresse IP ou la plage d’adresses IP de l’ordinateur doit être ajoutée à la liste d’adresses IP autorisées pour le compte de base de données DocumentDB. 

## <a name="a-idconfigure-ip-policya-configuring-the-ip-access-control-policy"></a><a id="configure-ip-policy"></a> Configuration de la stratégie de contrôle d’accès IP
La stratégie de contrôle d’accès IP peut être définie par programmation avec [Azure CLI](documentdb-automation-resource-manager-cli.md), [Azure PowerShell](documentdb-manage-account-with-powershell.md) ou [l’API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) en mettant à jour la propriété `ipRangeFilter`. Les plages/adresses IP doivent être séparées par des virgules et ne doivent pas contenir d’espaces. Exemple : « 13.91.6.132,13.91.6.1/24 ». Lors de la mise à jour de votre compte de base de données avec ces méthodes, veillez à renseigner toutes les propriétés afin d’éviter le rétablissement des paramètres par défaut.

> [!NOTE]
> En activant une stratégie de contrôle d’accès IP pour votre compte de base de données DocumentDB, tous les accès à votre compte de base de données DocumentDB à partir d’ordinateurs ne figurant pas sur la liste de plages d’adresses IP autorisées sont bloqués. En vertu de ce modèle, la navigation dans le plan de données à partir du portail sera également bloquée pour assurer l’intégrité du contrôle d’accès.

## <a name="troubleshooting-the-ip-access-control-policy"></a>Dépannage de la stratégie de contrôle d’accès IP
### <a name="portal-operations"></a>Opérations du portail
En activant une stratégie de contrôle d’accès IP pour votre compte de base de données DocumentDB, tous les accès à votre compte de base de données DocumentDB à partir d’ordinateurs ne figurant pas sur la liste de plages d’adresses IP autorisées sont bloqués. En vertu de ce modèle, la navigation dans le plan de données à partir du portail sera également bloquée pour assurer l’intégrité du contrôle d’accès. 

### <a name="sdk--rest-api"></a>Kit de développement logiciel (SDK) et API REST
Pour des raisons de sécurité, l’accès via le Kit de développement logiciel (SDK) ou l’API REST à partir d’ordinateurs ne figurant pas dans la liste autorisée renverra une réponse générique 404 Introuvable, ainsi que des détails supplémentaires. Consultez la liste d’adresses IP autorisées configurée pour votre compte de base de données DocumentDB afin de vérifier que la configuration de la stratégie appropriée est appliquée à votre compte de base de données DocumentDB.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les conseils sur les performances relatives au réseau, consultez [Conseils relatifs aux performances](documentdb-performance-tips.md).




<!--HONumber=Feb17_HO1-->


