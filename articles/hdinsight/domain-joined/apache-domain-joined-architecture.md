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
ms.date: 09/21/2017
ms.author: saurinsh
ms.openlocfilehash: 9deb5e4dbd925c4fdc523d8d21afbcfbf85947b8
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planifier des clusters Hadoop Azure joints à un domaine dans HDInsight

Le cluster Hadoop traditionnel est un cluster à un seul utilisateur. Il convient à la plupart des entreprises qui font appel à des équipes d’application plus petites pour créer leurs charges de travail de données importantes. À mesure qu’Hadoop gagne en popularité, nombre d’entreprises font la transition vers un modèle dans lequel les clusters sont gérés par les équipes informatiques et partagés par plusieurs équipes d’application. Ainsi, les fonctionnalités impliquant des clusters multi-utilisateur font partie des fonctionnalités les plus demandées dans Azure HDInsight.

Au lieu de créer une authentification et une autorisation multi-utilisateurs qui lui soient propres, HDInsight s’appuie sur le fournisseur d’identité le plus courant : Active Directory (AD). Dans AD, la puissante fonctionnalité de sécurité permet de gérer l’autorisation multi-utilisateur dans HDInsight. En intégrant HDInsight à AD, vous pouvez communiquer avec les clusters au moyen de vos informations d’identification AD. HDInsight mappe un utilisateur AD à un utilisateur Hadoop local afin que tous les services exécutés sur HDInsight (Ambari, serveur Hive, Ranger, serveur Spark Thrift, etc.) fonctionnent de manière transparente pour l’utilisateur authentifié.

## <a name="integrate-hdinsight-with-active-directory"></a>Intégrer HDInsight avec Active Directory

Quand vous intégrez HDInsight avec Active Directory, les nœuds du cluster HDInsight sont joints à un domaine AD. HDInsight crée des principaux pour les services Hadoop s’exécutant sur le cluster et les place dans une unité d’organisation spécifiée dans le domaine. HDInsight crée également des mappages DNS inverses dans le domaine pour les adresses IP des nœuds qui sont joints au domaine.

Il y a deux options de déploiement pour Active Directory :
* **[Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md)** : ce service fournit un domaine Active Directory managé qui est compatible avec Windows Server Active Directory. Microsoft effectue la gestion, les mises à jour correctives et la surveillance du domaine AD. Vous pouvez déployer votre cluster sans vous préoccuper de la maintenance des contrôleurs de domaine. Les utilisateurs, les groupes et les mots de passe sont synchronisés à partir de votre annuaire Azure Active Directory, ce qui permet aux utilisateurs de se connecter au cluster à l’aide de leurs informations d’identification professionnelles.

* **Domaine Windows Server Active Directory sur des machines virtuelles Azure IaaS** : cette option vous permet de déployer et gérer votre propre domaine Windows Server Active Directory sur des machines virtuelles Azure IaaS. 

Vous pouvez obtenir cette configuration en utilisant plusieurs architectures. Vous pouvez choisir parmi les architectures suivantes :


### <a name="hdinsight-integrated-with-an-azure-ad-domain-services-managed-ad-domain"></a>HDInsight intégré à un domaine AD managé par Azure AD Domain Services
Vous pouvez déployer un domaine managé [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS). Azure AD DS fournit un domaine Active Directory managé dans Azure, qui est géré, mis à jour et surveillé par Microsoft. Il crée deux contrôleurs de domaine pour la haute disponibilité et inclut des services DNS. Vous pouvez ensuite intégrer le cluster HDInsight dans ce domaine managé. Avec cette option de déploiement, vous n’avez pas à vous soucier de la gestion, des mises à jour correctives, de la mise à jour et de la surveillance des contrôleurs de domaine.

![Topologie du cluster HDInsight joint à un domaine](./media/apache-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Prérequis pour l’intégration avec Azure AD Domain Services :

* [Provisionner un domaine managé par Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started.md).
* Créer une [unité d’organisation](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) dans laquelle vous placez les machines virtuelles du cluster HDInsight et les principaux de service utilisés par le cluster.
* Configurer [LDAPS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) en même temps qu’Azure AD DS. Le certificat utilisé pour configurer LDAPS doit être émis par une autorité de certification publique (ce ne doit pas être un certificat auto-signé).
* Créer les zones DNS inverses dans le domaine managé pour la plage d’adresses IP du sous-réseau HDInsight (par exemple 10.2.0.0/24 dans l’image précédente).
* Configurer la [synchronisation des hachages de mot de passe nécessaires pour l’authentification NTLM et Kerberos](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) entre Azure AD et le domaine managé Azure AD DS.
* Un compte de service ou un compte d’utilisateur sont nécessaires. Utilisez l’un de ces comptes pour créer le cluster HDInsight. Ce compte doit disposer des autorisations suivantes :

    - Autorisations de création des objets de principal de service et des objets machine dans l’unité d’organisation
    - Autorisations de création de règles de proxy DNS inverses
    - Autorisations pour joindre des machines au domaine Azure AD


### <a name="hdinsight-integrated-with-windows-server-ad-running-on-azure-iaas"></a>HDInsight intégré à Windows Server AD fonctionnant sur Azure IaaS

Vous pouvez déployer le rôle Windows Server Active Directory Domain Services sur une ou plusieurs machines virtuelles dans Azure et promouvoir ensuite ces machines en contrôleurs de domaine. Ces machines virtuelles de contrôleur de domaine peuvent être déployées avec le modèle de déploiement Resource Manager dans le même réseau virtuel que le cluster HDInsight. Si les contrôleurs de domaine sont déployés dans un autre réseau virtuel, vous devez connecter ces deux réseaux virtuels en créant un [appairage de réseau virtuel à réseau virtuel](../../virtual-network/virtual-network-create-peering.md). 

[Informations supplémentaires sur le déploiement de Windows Server Active Directory sur des machines virtuelles Azure](../../active-directory/virtual-networks-windows-server-active-directory-virtual-machines.md)

![Topologie du cluster HDInsight joint à un domaine](./media/apache-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Dans cette architecture, vous ne pouvez pas utiliser Azure Data Lake Store avec le cluster HDInsight.


Prérequis pour l’intégration de Windows Server Active Directory sur des machines virtuelles Azure :

* Vous devez créer une [unité d’organisation](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md) dans laquelle vous placerez les machines virtuelles du cluster HDInsight et les principaux du service utilisés par le cluster.
* Les [protocoles LDAP (Lightweight Directory Access Protocol)](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) doivent être configurés pour communiquer avec AD. Le certificat utilisé pour configurer le protocole LDAPS doit être un certificat réel (et non un certificat auto-signé).
* Les zones DNS inverses doivent être créées dans le domaine correspondant à la plage d’adresses IP du sous-réseau HDInsight (par exemple 10.2.0.0/24 dans l’image précédente).
* Un compte de service ou un compte d’utilisateur sont nécessaires. Utilisez l’un de ces comptes pour créer le cluster HDInsight. Ce compte doit disposer des autorisations suivantes :

    - Autorisations de création des objets de principal de service et des objets machine dans l’unité d’organisation
    - Autorisations de création de règles de proxy DNS inverses
    - Autorisations pour joindre des machines au domaine Active Directory


## <a name="next-steps"></a>Étapes suivantes
* Pour configurer un cluster HDInsight joint à un domaine, consultez l’article [Configurer des clusters HDInsight joints à un domaine](apache-domain-joined-configure.md).
* Pour gérer des clusters HDInsight joints à un domaine, consultez l’article [Gestion des clusters HDInsight joints à un domaine](apache-domain-joined-manage.md).
* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez l’article [Configure Hive policies for domain-joined HDInsight clusters (Configurer des stratégies Hive pour les clusters HDInsight joints à un domaine)](apache-domain-joined-run-hive.md).
* Pour exécuter des requêtes Hive en utilisant SSH sur des clusters HDInsight joints au domaine, voir [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
