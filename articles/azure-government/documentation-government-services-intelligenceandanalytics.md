---
title: Intelligence et analyse Azure Governement | Microsoft Docs
description: This provides a comparision of features and guidance on developing applications for Azure Government
services: Azure-Government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
editor: 
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: 0302a44bb42200c07a313bf2862479c1722af8c4
ms.openlocfilehash: 2a8769f7a611fb22e39db166d3416728f57cbbf4


---
# <a name="azure-government-intelligence--analytics"></a>Intelligence et analyse Azure Governement
Cet article décrit les services d’intelligence et d’analyse, les variations et les éléments à prendre en compte pour l’environnement Azure Government.

## <a name="azure-hdinsight"></a>Azure HDInsight
HDInsight est mis à la disposition générale dans Azure Government.

### <a name="variations"></a>Variantes
Actuellement, les fonctionnalités HDInsight suivantes ne sont pas disponibles dans Azure Government.

* HDInsight n’est pas disponible sur Windows.
* Azure Data Lake Store n’est actuellement pas disponible dans Azure Government. Stockage Blob Azure est la seule option de stockage disponible pour l’instant.
* L’intégration d’Azure Active Directory Domain Services n’est pas encore disponible. Pour créer des clusters Hadoop sécurisés sans Active Directory Domain Services, sélectionnez un des scénarios d’installation suivants :

1. HDINSIGHT INTÉGRÉ À ACTIVE DIRECTORY SUR AZURE IAAS

  Il s’agit là de l’architecture la plus simple pour intégrer HDInsight avec Active Directory. Le diagramme de l’architecture est fourni ci-dessous. Dans cette architecture, votre contrôleur de domaine Active Directory s’exécute sur une machine virtuelle (ou plusieurs) dans Azure. Normalement, ces machines virtuelles se trouvent dans un réseau virtuel. Vous pouvez configurer un réseau virtuel dans lequel vous pouvez placer votre cluster HDInsight. Pour qu’HDInsight puisse voir l’annuaire Active Directory, vous devrez mettre en lien ces réseaux virtuels à l’aide de [l’homologation des réseaux virtuels] (https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal). 
  
  Conditions préalables qui doivent être configurées dans l’annuaire Active Directory
     * Vous devez créer l’unité organisationnelle dans laquelle vous souhaitez placer les machines virtuelles du cluster HDInsight et les principaux du service utilisés par le cluster. 
     * LDAPS doit être configuré de manière à communiquer avec Active Directory. Le certificat utilisé pour installer LDAPS doit être un certificat réel (et non un certificat auto-signé).
     * Les zones DNS inverses doivent être créées sur le domaine de la plage d’adresses IP du sous-réseau HDI.
     * Un compte de service ou un compte d’utilisateur est nécessaire. Il sera utilisé pour créer le cluster HDInsight. Ce compte doit disposer des autorisations suivantes :
        * Autorisations de création des objets de principal de service et des objets ordinateur dans l’unité organisationnelle.
        * Autorisations de création de règles de proxy DNS inverses.
        * Autorisations pour joindre des ordinateurs au domaine Active Directory.
        
2. HDINSIGHT INTÉGRÉ À UN ANNUAIRE ACTIVE DIRECTORY LOCAL VIA LA CONFIGURATION DU VPN
  
  Cette architecture est similaire à l’architecture 1. La seule différence est que, dans ce cas, votre annuaire Active Directory est local et que le lien entre HDInsight et Active Directory se fait via une [connexion VPN entre Azure et le réseau local] (https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction). Le diagramme d’architecture de cette installation est présenté ci-dessous. 
  
  Conditions préalables qui doivent être entrées dans l’annuaire Active Directory local
     * Vous devez créer l’unité organisationnelle dans laquelle vous souhaitez placer les machines virtuelles du cluster HDInsight et les principaux du service utilisés par le cluster.
     * LDAPS doit être configuré de manière à communiquer avec Active Directory. Le certificat utilisé pour installer LDAPS doit être un certificat réel (et non un certificat auto-signé).
     * Les zones DNS inverses doivent être créées sur le domaine de la plage d’adresses IP du sous-réseau HDI.
     * Un compte de service ou un compte d’utilisateur est nécessaire. Il sera utilisé pour créer le cluster HDInsight. Ce compte doit disposer des autorisations suivantes :
        * Autorisations de création des objets de principal de service et des objets ordinateur dans l’unité organisationnelle.
        * Autorisations de création de règles de proxy DNS inverses.
        * Autorisations pour joindre des ordinateurs au domaine Active Directory.


Les URL de Log Analytics sont différentes dans Azure Government :

| Type de service | Azure (public) | Azure Government |
| --- | --- | --- |
| Cluster HDInsight | \*.azurehdinsight.net | \*.azurehdinsight.us |

Pour plus d’informations, consultez la [documentation publique sur Azure HDInsight](../hdinsight/hdinsight-hadoop-introduction.md).


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government</a>.



<!--HONumber=Jan17_HO2-->


