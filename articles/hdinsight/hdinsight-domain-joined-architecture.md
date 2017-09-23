---
title: "Architecture de cluster Azure HDInsight joint à un domaine | Microsoft Docs"
description: "Découvrez comment planifier les clusters HDInsight joints à un domaine."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: saurinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 7e34f47f09466a40993b4cc797ff1cad2bdaeafe
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017

---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planifier des clusters Hadoop Azure joints à un domaine dans HDInsight

Le cluster Hadoop traditionnel est un cluster à un seul utilisateur. Il convient à la plupart des entreprises qui font appel à des équipes d’application plus petites pour créer leurs charges de travail de données importantes. À mesure qu’Hadoop gagne en popularité, nombre d’entreprises font la transition vers un modèle dans lequel les clusters sont gérés par les équipes informatiques et partagés par plusieurs équipes d’application. Ainsi, les fonctionnalités impliquant des clusters multi-utilisateur font partie des fonctionnalités les plus demandées dans Azure HDInsight.

Au lieu de créer une authentification et une autorisation multi-utilisateurs qui lui soient propres, HDInsight s’appuie sur le fournisseur d’identité le plus courant : Active Directory (AD). Dans AD, la puissante fonctionnalité de sécurité permet de gérer l’autorisation multi-utilisateur dans HDInsight. En intégrant HDInsight à AD, vous pouvez communiquer avec les clusters au moyen de vos informations d’identification AD. HDInsight mappe un utilisateur AD à un utilisateur Hadoop local afin que tous les services exécutés sur HDInsight (Ambari, serveur Hive, Ranger, serveur Spark Thrift, etc.) fonctionnent de manière transparente pour l’utilisateur authentifié.

## <a name="integrate-hdinsight-with-ad-and-ad-on-iaas-vm"></a>Intégrer HDInsight à AD et AD sur la machine virtuelle IaaS

En intégrant HDInsight à Azure AD ou AD sur la machine virtuelle IaaS, les nœuds du cluster HDInsight sont joints à un domaine. HDInsight crée les principaux pour les services Hadoop s’exécutant sur le cluster et les place dans une unité d’organisation spécifiée dans Azure AD ou AD sur la machine virtuelle IaaS. HDInsight crée également des mappages DNS inverses dans le domaine pour les adresses IP des nœuds qui sont joints au domaine.

Vous pouvez obtenir cette configuration en utilisant plusieurs architectures. Vous pouvez choisir parmi les architectures suivantes :

**HDInsight intégré à AD fonctionnant sur Azure IaaS**

Il s’agit de l’architecture la plus simple pour intégrer HDInsight avec Active Directory. Le contrôleur de domaine AD s’exécute sur une machine virtuelle (ou plusieurs) dans Azure. En général, ces machines virtuelles se trouvent sur un réseau virtuel. Vous configurez un autre réseau virtuel pour le cluster HDInsight. Pour que HDInsight puisse voir Active Directory, vous devez apparier ces réseaux virtuels à l’aide de l’[homologation de réseau virtuel à réseau virtuel](../virtual-network/virtual-network-create-peering.md). Si vous créez le service Active Directory dans ARM, vous pouvez ensuite créer Active Directory et HDInsight dans le même réseau virtuel sans avoir besoin de procéder à une homologation. 

![Topologie du cluster HDInsight joint à un domaine](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Dans cette architecture, vous ne pouvez pas utiliser Azure Data Lake Store avec le cluster HDInsight.


Conditions préalables pour Active Directory :

* Vous devez créer une [unité d’organisation](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) dans laquelle vous placerez les machines virtuelles du cluster HDInsight et les principaux du service utilisés par le cluster.
* Les [protocoles LDAP (Lightweight Directory Access Protocol)](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) doivent être configurés pour communiquer avec AD. Le certificat utilisé pour configurer le protocole LDAPS doit être un certificat réel (et non un certificat auto-signé).
* Les zones DNS inverses doivent être créées dans le domaine correspondant à la plage d’adresses IP du sous-réseau HDInsight (par exemple 10.2.0.0/24 dans l’image précédente).
* Un compte de service ou un compte d’utilisateur sont nécessaires. Utilisez l’un de ces comptes pour créer le cluster HDInsight. Ce compte doit disposer des autorisations suivantes :

    - Autorisations de création des objets de principal de service et des objets machine dans l’unité d’organisation
    - Autorisations de création de règles de proxy DNS inverses
    - Autorisations pour joindre des machines au domaine Active Directory

**HDInsight intégré à Azure AD dans le cloud uniquement**

Pour Azure AD dans le cloud uniquement, configurez un contrôleur de domaine pour que HDInsight puisse être intégré à Azure AD. Pour cela, vous devez utiliser [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS). Azure AD DS crée des machines contrôleurs de domaine dans le cloud et fournit leurs adresses IP. Il crée deux contrôleurs de domaine pour la haute disponibilité.

Pour le moment, Azure AD DS existe uniquement dans les réseaux virtuels classiques. Il est uniquement accessible à l’aide du portail Azure Classic. Le réseau virtuel HDInsight existe dans le portail Azure, qui doit être mis en lien avec le réseau virtuel classique à l’aide de l’homologation des réseaux virtuels.

> [!NOTE]
> L’homologation entre un réseau virtuel classique et un réseau virtuel Azure Resource Manager exige que les deux réseaux virtuels soient dans la même région et sous le même abonnement Azure.

![Topologie du cluster HDInsight joint à un domaine](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Composants requis pour Azure AD :

* Vous devez créer une [unité d’organisation](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) dans laquelle vous placerez les machines virtuelles du cluster HDInsight et les principaux du service utilisés par le cluster.
* Le protocole [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) doit être paramétré lorsque vous configurez Azure AD DS. Le certificat utilisé pour configurer le protocole LDAPS doit être un certificat réel (et non un certificat auto-signé).
* Les zones DNS inverses doivent être créées dans le domaine correspondant à la plage d’adresses IP du sous-réseau HDInsight (par exemple 10.2.0.0/24 dans l’image précédente).
* Les [hachages de mot de passe](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) doivent être synchronisés entre Azure AD et Azure AD DS.
* Un compte de service ou un compte d’utilisateur sont nécessaires. Utilisez l’un de ces comptes pour créer le cluster HDInsight. Ce compte doit disposer des autorisations suivantes :

    - Autorisations de création des objets de principal de service et des objets machine dans l’unité d’organisation
    - Autorisations de création de règles de proxy DNS inverses
    - Autorisations pour joindre des machines au domaine Azure AD

## <a name="next-steps"></a>Étapes suivantes
* Pour configurer un cluster HDInsight joint à un domaine, consultez l’article [Configurer des clusters HDInsight joints à un domaine](hdinsight-domain-joined-configure.md).
* Pour gérer des clusters HDInsight joints à un domaine, consultez l’article [Gestion des clusters HDInsight joints à un domaine](hdinsight-domain-joined-manage.md).
* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez l’article [Configure Hive policies for domain-joined HDInsight clusters (Configurer des stratégies Hive pour les clusters HDInsight joints à un domaine)](hdinsight-domain-joined-run-hive.md).
* Pour exécuter des requêtes Hive en utilisant SSH sur des clusters HDInsight joints au domaine, voir [Utiliser SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

