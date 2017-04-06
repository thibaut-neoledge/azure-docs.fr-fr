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
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 69378496fb7e4176243d36950e7270809248d2bb
ms.lasthandoff: 03/25/2017


---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planifier des clusters Hadoop Azure joints à un domaine dans HDInsight

Le cluster Hadoop traditionnel est un cluster à un seul utilisateur. Il convient à la plupart des entreprises qui font appel à des équipes d’application plus petites pour créer leurs charges de travail de données importantes. À mesure qu’Hadoop gagne en popularité, nombre d’entreprises font la transition vers un modèle dans lequel les clusters sont gérés par les équipes informatiques et partagés par plusieurs équipes d’application. Ainsi, les fonctionnalités impliquant des clusters multi-utilisateur font partie des fonctionnalités les plus demandées dans Azure HDInsight.

Au lieu de créer sa propre authentification et sa propre autorisation multi-utilisateur, HDInsight utilise le fournisseur d’identité le plus courant : Azure Active Directory (Azure AD). Dans Azure AD, la puissante fonctionnalité de sécurité permet de gérer l’autorisation multi-utilisateur dans HDInsight. En intégrant HDInsight à Azure AD, vous pouvez communiquer avec les clusters à l’aide de vos informations d’identification Azure AD. HDInsight mappe un utilisateur Azure AD à un utilisateur Hadoop local afin que tous les services exécutés sur HDInsight (Ambari, serveur Hive, Ranger, serveur Spark Thrift, etc.) fonctionnent de manière transparente pour l’utilisateur authentifié.

## <a name="integrate-hdinsight-with-azure-ad"></a>Intégrer HDInsight à Azure AD

En intégrant HDInsight à Azure AD, les nœuds du cluster HDInsight sont joints au domaine Azure AD. HDInsight crée des principaux pour les services Hadoop s’exécutant sur le cluster et les place dans une unité d’organisation spécifiée dans Azure AD. HDInsight crée également des mappages DNS inverses dans le domaine Azure AD pour les adresses IP des nœuds qui sont joints au domaine.

Vous pouvez obtenir cette configuration en utilisant plusieurs architectures. Vous pouvez choisir parmi les architectures suivantes :

**HDInsight intégré à Azure AD exécuté sur Azure IaaS**

Il s’agit de l’architecture la plus simple pour intégrer HDInsight à Azure AD. Le contrôleur de domaine Azure AD s’exécute sur une (ou plusieurs) machines virtuelles (VM) dans Azure. En général, ces machines virtuelles se trouvent sur un réseau virtuel. Vous configurez un autre réseau virtuel pour le cluster HDInsight. Pour que HDInsight puisse voir Azure AD, vous devez mettre en lien ces réseaux virtuels à l’aide de l’[homologation des réseaux virtuels](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md).

![Topologie du cluster HDInsight joint à un domaine](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Dans cette architecture, vous ne pouvez pas utiliser Azure Data Lake Store avec le cluster HDInsight.


Composants requis pour Azure AD :

* Vous devez créer une [unité d’organisation](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) dans laquelle vous placerez les machines virtuelles du cluster HDInsight et les principaux du service utilisés par le cluster.
* Les [protocoles LDAP (Lightweight Directory Access Protocol)](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) doivent être configurés pour communiquer avec Azure AD. Le certificat utilisé pour configurer le protocole LDAPS doit être un certificat réel (et non un certificat auto-signé).
* Les zones DNS inverses doivent être créées dans le domaine correspondant à la plage d’adresses IP du sous-réseau HDInsight (par exemple 10.2.0.0/24 dans l’image précédente).
* Un compte de service ou un compte d’utilisateur sont nécessaires. Utilisez l’un de ces comptes pour créer le cluster HDInsight. Ce compte doit disposer des autorisations suivantes :

    - Autorisations de création des objets de principal de service et des objets machine dans l’unité d’organisation
    - Autorisations de création de règles de proxy DNS inverses
    - Autorisations pour joindre des machines au domaine Active Directory

**HDInsight intégré à Azure AD dans le cloud uniquement**

Pour Azure AD dans le cloud uniquement, configurez un contrôleur de domaine pour que HDInsight puisse être intégré à Azure AD. Pour ce faire, utilisez [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS). Azure AD DS crée des machines contrôleurs de domaine dans le cloud et fournit leurs adresses IP. Il crée deux contrôleurs de domaine pour la haute disponibilité.

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

**HDInsight intégré à un annuaire Active Directory local via VPN**

Cette architecture est semblable à l’architecture « HDInsight intégré à Azure AD exécuté sur Azure IaaS ». La seule différence est qu’Azure AD est local et que le lien entre HDInsight et Azure AD se fait via une [connexion VPN entre Azure et le réseau local](../expressroute/expressroute-introduction.md).

![Topologie du cluster HDInsight joint à un domaine](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_3.png)

> [!NOTE]
> Dans cette architecture, vous ne pouvez pas utiliser Azure Data Lake Store avec le cluster HDInsight.

Composants requis pour Azure AD :

* Vous devez créer une [unité d’organisation](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) dans laquelle vous placerez les machines virtuelles du cluster HDInsight et les principaux du service utilisés par le cluster.
* Le protocole [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) doit être configuré pour communiquer avec Azure AD. Le certificat utilisé pour configurer le protocole LDAPS doit être un certificat réel (et non un certificat auto-signé).
* Les zones DNS inverses doivent être créées dans le domaine correspondant à la plage d’adresses IP du sous-réseau HDInsight (par exemple 10.2.0.0/24 dans l’image précédente).
* Un compte de service ou un compte d’utilisateur sont nécessaires. Utilisez l’un de ces comptes pour créer le cluster HDInsight. Ce compte doit disposer des autorisations suivantes :

    - Autorisations de création des objets de principal de service et des objets machine dans l’unité d’organisation
    - Autorisations de création de règles de proxy DNS inverses
    - Autorisations pour joindre des machines au domaine Azure AD

**HDInsight intégré à un annuaire Active Directory local synchronisé avec Azure AD**

Cette architecture est semblable à l’architecture « HDInsight intégré à Azure AD dans le cloud uniquement ». La seule différence est que l’annuaire Active Directory local est synchronisé avec Azure AD. Configurez un contrôleur de domaine dans le cloud pour que HDInsight puisse être intégré à Azure AD. Pour ce faire, utilisez [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md). Azure AD DS crée des machines contrôleurs de domaine dans le cloud et fournit leurs adresses IP. Il crée deux contrôleurs de domaine pour la haute disponibilité.

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
    - Autorisations pour joindre des machines au domaine Active Directory

**HDInsight intégré à un annuaire Azure AD non paramétré par défaut (recommandé uniquement pour les tests et le développement)**

Cette architecture est semblable à l’architecture « HDInsight intégré à Azure AD dans le cloud uniquement ». Pour la plupart des entreprises, l’accès administrateur à Azure AD est limité à certaines personnes. Par conséquent, si vous souhaitez procéder à une preuve de concept ou tenter de créer un cluster joint à un domaine, il peut être judicieux de créer une instance Azure AD dans l’abonnement au lieu d’attendre qu’un administrateur configure les composants préalables requis sur Azure AD. Comme il s’agit d’une instance Azure AD que vous avez créée, vous disposez de toutes les autorisations d’accès à Azure AD pour configurer Azure AD DS.

Azure AD DS crée des machines contrôleurs de domaine dans le cloud et fournit leurs adresses IP. Il crée deux contrôleurs de domaine pour la haute disponibilité.

Comme Azure AD DS existe uniquement dans les réseaux virtuels classiques, vous devez accéder au portail Azure Classic et créer un réseau virtuel classique configurer Azure AD DS. Le réseau virtuel HDInsight existe dans le portail Azure, qui doit être mis en lien avec le réseau virtuel classique à l’aide de l’homologation des réseaux virtuels.

> [!NOTE]
> L’homologation entre le réseau virtuel classique et le réseau virtuel Azure Resource Manager exige que les deux réseaux virtuels soient dans la même région et sous le même abonnement Azure.

![Topologie du cluster HDInsight joint à un domaine](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Composants requis pour Azure AD :

* Vous devez créer une [unité d’organisation](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) dans laquelle vous placerez les machines virtuelles du cluster HDInsight et les principaux du service utilisés par le cluster.
* Le protocole [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) doit être paramétré lorsque vous configurez Azure AD DS. Vous pouvez créer un [certificat auto-signé](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) pour configurer LDAPS. Toutefois, pour utiliser un certificat auto-signé, vous devez demander une exception auprès de <a href="mailto:hdipreview@microsoft.com">hdipreview@microsoft.com</a>.
* Les zones DNS inverses doivent être créées dans le domaine correspondant à la plage d’adresses IP du sous-réseau HDInsight (par exemple 10.2.0.0/24 dans l’image précédente).
* Les [hachages de mot de passe](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) doivent être synchronisés entre Azure AD et Azure AD DS.
* Un compte de service ou un compte d’utilisateur sont nécessaires. Utilisez l’un de ces comptes pour créer le cluster HDInsight. Ce compte doit disposer des autorisations suivantes :

    - Autorisations de création des objets de principal de service et des objets machine dans l’unité d’organisation
    - Autorisations de création de règles de proxy DNS inverses
    - Autorisations pour joindre des ordinateurs au domaine Azure Active Directory

## <a name="next-steps"></a>Étapes suivantes
* Pour configurer un cluster HDInsight joint à un domaine, consultez l’article [Configurer des clusters HDInsight joints à un domaine](hdinsight-domain-joined-configure.md).
* Pour gérer des clusters HDInsight joints à un domaine, consultez l’article [Gestion des clusters HDInsight joints à un domaine](hdinsight-domain-joined-manage.md).
* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez l’article [Configure Hive policies for domain-joined HDInsight clusters (Configurer des stratégies Hive pour les clusters HDInsight joints à un domaine)](hdinsight-domain-joined-run-hive.md).
* Pour exécuter des requêtes Hive en utilisant SSH sur des clusters HDInsight joints au domaine, voir [Utiliser SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

