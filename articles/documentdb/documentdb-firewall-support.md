---
title: Prise en charge du pare-feu dans DocumentDB | Microsoft Docs
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
ms.date: 10/17/2016
ms.author: ankshah; kraman
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eb3c5c2adbaedc4bfb1e68f26b88079aeabe50f5


---
# <a name="documentdb-firewall-support"></a>Prise en charge du pare-feu dans DocumentDB
Pour sécuriser les données stockées dans un compte de base de données Azure DocumentDB, DocumentDB assure la prise en charge d’un [modèle d’autorisation](https://msdn.microsoft.com/library/azure/dn783368.aspx) basé sur une clé secrète, qui utilise un code d’authentification de message basé sur le hachage (HMAC) à forte intégrité. Le modèle d’autorisations basé sur un secret, DocumentDB, prend désormais en charge les contrôles d’accès basés sur une stratégie IP pour la prise en charge du pare-feu entrant. Ce modèle est très semblable aux règles de pare-feu d’un système de base de données traditionnel et renforce la sécurité du compte de base de données DocumentDB. Avec ce modèle, vous pouvez désormais configurer un compte de base de données DocumentDB pour qu’il soit accessible uniquement à partir d’un ensemble d’ordinateurs et/ou de services cloud approuvés. L’accès aux ressources de DocumentDB à partir de ces ensembles d’ordinateurs et de services approuvés nécessite toujours que l’appelant présente un jeton d’autorisation valide.

## <a name="ip-access-control-overview"></a>Vue d’ensemble du contrôle d’accès IP
Par défaut, un compte de base de données DocumentDB est accessible depuis l’Internet public tant que la demande est accompagnée d’un jeton d’autorisation valide. Pour configurer le contrôle d’accès basé sur la stratégie IP, l’utilisateur doit fournir le jeu d’adresses IP ou des plages d’adresses IP au format CIDR pour être ajouté à la liste d’adresses IP clients autorisées pour un compte de base de données donné. Une fois que cette configuration est appliquée, toutes les demandes provenant d’ordinateurs ne figurant pas sur cette liste autorisée seront bloquées par le serveur.  Le flux de traitement de connexion du contrôle d’accès basé sur IP est décrit dans le diagramme suivant.

![Diagramme illustrant le processus de connexion du contrôle d’accès basé sur IP](./media/documentdb-firewall-support/documentdb-firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>Connexions à partir de services cloud
Dans Azure, les services cloud sont une méthode très courante d’hébergement de la logique de service de couche intermédiaire à l’aide de DocumentDB. Pour autoriser l’accès à un compte de base de données DocumentDB à partir d’un service cloud, l’adresse IP publique du service cloud doit être ajoutée à la liste d’adresses IP autorisées associées à votre compte de base de données DocumentDB en [contactant le support Azure](#configure-ip-policy).  Cela garantit que toutes les instances de rôle des services cloud ont accès à votre compte de base de données DocumentDB. Vous pouvez récupérer des adresses IP pour vos services cloud dans le portail Azure, comme illustré dans la capture d’écran suivante. 

![Capture d’écran illustrant l’adresse IP publique pour un service cloud affichée dans le portail Azure](./media/documentdb-firewall-support/documentdb-public-ip-addresses.png)

Lorsque vous faites évoluer votre service cloud en ajoutant des instances de rôle supplémentaires, ces nouvelles instances auront automatiquement accès au compte de base de données DocumentDB, car ils font partie du même service cloud.

## <a name="connections-from-virtual-machines"></a>Connexions à partir de machines virtuelles
Des [machines virtuelles](https://azure.microsoft.com/services/virtual-machines/) ou des [jeux de mise à l’échelle de machine virtuelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) peuvent également être utilisés pour héberger les services de couche intermédiaire à l’aide de DocumentDB.  Pour configurer le compte de base de données DocumentDB afin d’autoriser l’accès à partir de machines virtuelles, les adresses IP publiques de la machine virtuelle ou du jeu de mise à l’échelle de machine virtuelle doivent être configurées comme une adresse IP autorisée pour votre compte de base de données DocumentDB en [contactant le support Azure](#configure-ip-policy). Vous pouvez récupérer des adresses IP pour des machines virtuelles dans le portail Azure, comme illustré dans la capture d’écran suivante.

![Capture d’écran illustrant une adresse IP publique pour une machine virtuelle affichée dans le portail Azure](./media/documentdb-firewall-support/documentdb-public-ip-addresses-dns.png)

Lorsque vous ajoutez des instances de machine virtuelle supplémentaires au groupe, elles disposent automatiquement d’un accès à votre compte de base de données DocumentDB.

## <a name="connections-from-the-internet"></a>Connexions à partir d’Internet
Lorsque vous accédez à un compte de base de données DocumentDB à partir d’un ordinateur sur Internet, l’adresse IP ou la plage d’adresses IP de l’ordinateur doit être ajoutée à la liste d’adresses IP autorisées pour le compte de base de données DocumentDB. 

## <a name="a-idconfigure-ip-policya-configuring-the-ip-access-control-policy"></a><a id="configure-ip-policy"></a> Configuration de la stratégie de contrôle d’accès IP
Utilisez le portail Azure pour créer une demande auprès du [Support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) afin d’activer la stratégie de contrôle d’accès IP sur votre compte de base de données.

1. Dans le panneau [Aide + Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade),sélectionnez **Nouvelle demande de support**.
2. Dans le panneau **Nouvelle demande de support**, cliquez sur **De base**.
3. Dans le panneau **De base**, sélectionnez les éléments suivants :
   * **Type de problème** : Quota
   * **Abonnement** : l’abonnement associé au compte dans lequel ajouter la stratégie de contrôle d’accès IP.
   * **Type de quota** : DocumentDB
   * **Plan de support** : Support quota - Inclus.
4. Dans le panneau **Problème**, procédez comme suit :
   * **Gravité** : sélectionnez C - impact minimal
   * **Détails** : copiez le texte suivant dans la zone, et indiquez les noms et adresses IP de votre compte : « Je souhaiterais activer la prise en charge du pare-feu pour mon compte de base de données DocumentDB. Compte de base de données : *indiquez les noms des comptes*. Adresses/Plages d’adresses IP autorisées : *indiquez l’adresse/la plage d’adresses IP au format CIDR, par exemple 13.91.6.132, 13.91.6.1/24*. »
   * Cliquez sur **Next**. 
5. Dans le panneau **Informations de contact**, renseignez vos coordonnées et cliquez sur **Créer**. 

Une fois votre demande reçue, le contrôle d’accès IP doit être activé dans les 24 heures. Vous serez averti une fois la demande terminée.

![Capture d’écran des panneaux Aide + Support](./media/documentdb-firewall-support/documentdb-firewall-support-request-access.png)

![Capture d’écran du panneau Problème](./media/documentdb-firewall-support/documentdb-firewall-support-request-access-ticket.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>Dépannage de la stratégie de contrôle d’accès IP
### <a name="portal-operations"></a>Opérations du portail
En activant une stratégie de contrôle d’accès IP pour votre compte de base de données DocumentDB, tous les accès à votre compte de base de données DocumentDB à partir d’ordinateurs ne figurant pas sur la liste de plages d’adresses IP autorisées sont bloqués. En vertu de ce modèle, la navigation dans le plan de données à partir du portail sera également bloquée pour assurer l’intégrité du contrôle d’accès. 

### <a name="sdk--rest-api"></a>Kit de développement logiciel (SDK) et API REST
Pour des raisons de sécurité, l’accès via le Kit de développement logiciel (SDK) ou l’API REST à partir d’ordinateurs ne figurant pas dans la liste autorisée renverra une réponse générique 404 Introuvable, ainsi que des détails supplémentaires. Consultez la liste d’adresses IP autorisées configurée pour votre compte de base de données DocumentDB afin de vérifier que la configuration de la stratégie appropriée est appliquée à votre compte de base de données DocumentDB.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les conseils sur les performances relatives au réseau, consultez [Conseils relatifs aux performances](documentdb-performance-tips.md).




<!--HONumber=Nov16_HO3-->


