<properties 
   pageTitle="Vue d’ensemble de la sécurité dans Data Lake Store | Microsoft Azure" 
   description="Comprendre en quoi Azure Data Lake Store est un magasin sécurisé de Big Data" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/18/2016"
   ms.author="nitinme"/>

# Sécurité dans Azure Data Lake Store

Un grand nombre d’entreprises tirent parti de l’analyse du Big Data pour bénéficier de connaissances professionnelles afin de prendre des décisions éclairées. Ces entreprises peuvent évoluer dans un environnement complexe et réglementé, avec un nombre croissant d’utilisateurs divers. Il est donc essentiel pour ces entreprises de s’assurer que les données critiques sont stockées en toute sécurité avec un niveau correct d’accès accordé aux utilisateurs concernés. Azure Data Lake Store tient compte de ces exigences de sécurité. Dans cet article, vous allez découvrir les fonctionnalités de sécurité d’Azure Data Lake Store (ADLS), notamment :

* Authentification
* Autorisation
* Isolement réseau
* Protection des données
* Audit

 
## Authentification et gestion des identités

L’authentification est le processus par lequel les utilisateurs prouvent leur identité lors de l’interaction avec Data Lake Store ou avec les services se connectant à Data Lake Store. Pour l’authentification et la gestion des identités, Data Lake Store utilise [Azure Active Directory](../active-directory/active-directory-whatis.md) (AAD), solution complète pour la gestion des identités et des accès dans le cloud qui simplifie la gestion des utilisateurs et des groupes.

Aujourd’hui, chaque abonnement Azure peut être associé à un répertoire Azure Active Directory. Seuls les utilisateurs et les identités de service définis dans ce répertoire peuvent accéder à votre Data Lake Store à l’aide du portail Azure, des outils de ligne de commande ou des applications clientes créées à l’aide du Kit de développement logiciel (SDK) Data Lake Store. Les principaux avantages de l’utilisation d’Azure Active Directory en tant que mécanisme de contrôle d’accès centralisé sont les suivants :

* Il simplifie la gestion du cycle de vie des identités. L’identité d’un utilisateur ou d’un service (identité de principal du service) peut être rapidement créée et révoquée en supprimant ou en désactivant simplement le compte dans le répertoire.

* Il prend en charge [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) qui fournit une couche supplémentaire de sécurité pour les connexions et les transactions utilisateur.

* Il permet aux utilisateurs de s’authentifier à partir de n’importe quel client à l’aide d’un protocole open standard comme OAuth et OpenID.

* Il permet la fédération avec les services de répertoire d’entreprise et les fournisseurs d’identité cloud.

## Autorisation et contrôle d’accès

Une fois qu’un utilisateur est authentifié par AAD pour accéder à Azure Data Lake Store, les contrôles d’autorisation accèdent aux autorisations pour Data Lake Store. Data Lake Store sépare l’autorisation pour les activités relatives aux comptes et aux données de la manière suivante.

* [Le contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-what-is.md) (RBAC) fournit par Azure pour la gestion des comptes ;
* ACL POSIX pour accéder aux données dans le magasin.

### Utilisation de RBAC pour la gestion des comptes

Il existe quatre rôles de base définis par défaut. Elles autorisent différentes opérations sur un compte Data Lake Store via le portail, les applets de commande PowerShell et les API REST. Les rôles **Propriétaire** et **Collaborateur** autorisent un large éventail de fonctions d’administration sur le compte. Vous pouvez ajouter les utilisateurs qui interagissent uniquement avec les données au rôle **Lecteur**.

![Rôles RBAC](./media/data-lake-store-security-overview/rbac-roles.png "Rôles RBAC")

Notez que, bien que l’objectif de l’affectation de ces rôles soit la gestion des comptes, en fonction d’un rôle spécifique, elle peut également avoir des implications sur les autorisations d’accès aux données. Pour les opérations que l’utilisateur peut effectuer sur le système de fichiers, vous devrez utiliser des listes de contrôle d’accès (ACL). Voici un résumé des droits d’administration et des droits d’accès aux données pour ces rôles.

| contrôleur | Droits de gestion | Droits d’accès aux données | Explication |
|--------------------------|---------------------------------|--------------------|-------------|
| Aucun rôle affecté | Aucun | Régi par ACL | Dans de tels cas, les utilisateurs ne peuvent pas utiliser le portail Azure ou les applets de commande Azure PowerShell pour parcourir Data Lake Store. Ces utilisateurs devront uniquement compter sur les outils de ligne de commande. |
| Propriétaire | Tout | Tout | Le propriétaire est un super utilisateur, donc le rôle de propriétaire vous permet de gérer tous les éléments et offre un accès complet aux données | 
| Lecteur | Lecture seule | Régi par ACL | Le lecteur peut tout afficher en matière de gestion de comptes, notamment quel utilisateur est affecté à quel rôle, mais ne peut pas apporter de modifications |
| Collaborateur | Tout, sauf ajouter et supprimer des rôles | Régi par ACL | Le collaborateur peut gérer d’autres aspects d’un compte, tels que la création/gestion des alertes, le déploiement, etc. Un collaborateur ne peut pas ajouter ou supprimer des rôles |
| Administrateur de l’accès utilisateur | Ajouter et supprimer des rôles | Régi par ACL | L’administrateur de l’accès utilisateur vous permet de gérer l’accès des utilisateurs aux comptes. |

Pour obtenir des instructions, consultez la page [Affecter les utilisateurs ou les groupes de sécurité aux comptes Azure Data Lake Store](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### Utilisation des ACL pour les opérations sur les systèmes de fichiers

Azure Data Lake Store est un système de fichiers hiérarchique comme HDFS qui prend en charge les [ACL POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists) pour autoriser la lecture (r), l’écriture (w) et l’exécution (x) des droits d’accès aux ressources accordés au propriétaire, au groupe propriétaire et à d’autres utilisateurs/groupes. Dans la version d’évaluation publique de Data Lake Store (version actuelle), les ACL sont activées uniquement sur le dossier racine, ce qui signifie que les ACL que vous appliquez au dossier racine sont également applicables à tous les dossiers/fichiers enfants. Dans les versions à venir, vous pourrez définir des ACL sur tout fichier ou dossier.

Il est vivement recommandé de définir des ACL pour de nombreux utilisateurs à l’aide de [groupes de sécurité](../active-directory/active-directory-accessmanagement-manage-groups.md). Regroupez les utilisateurs dans un groupe de sécurité, puis affectez les ACL du fichier et du dossier à ce groupe de sécurité. Cela se révèle utile lors de la fourniture de l’accès personnalisé, car il existe une limite : vous ne pouvez ajouter que neuf entrées maximum dans le cadre d’un accès personnalisé. Consultez la page [Affecter des utilisateurs ou un groupe de sécurité comme ACL au système de fichiers Azure Data Lake Store](data-lake-store-secure-data.md#filepermissions) pour plus d’informations sur la sécurisation des données stockées dans Data Lake Store à l’aide de groupes de sécurité AAD.

![Lister les accès standard et personnalisés](./media/data-lake-store-security-overview/adl.acl.2.png "Lister les accès standard et personnalisés")

## Isolement réseau

Azure Data Lake Store vous permet de mieux verrouiller l’accès à votre magasin de données au niveau du réseau. Vous pouvez activer le pare-feu et définir une plage d’adresses IP pour vos clients approuvés. Une fois qu’il est activé, seuls les clients qui possédant des adresses IP dans la plage définie peuvent se connecter au magasin.

![Paramètres de pare-feu et accès IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Paramètres de pare-feu et adresse IP")

## Protection des données

Les organisations veulent garantir que leurs données critiques sont sécurisées tout au long de leur cycle de vie. Pour les données en transit, Data Lake Store utilise le protocole normalisé TLS (Transport Layer Security) pour sécuriser les données entre le client et la sécurité de Data Lake.

La protection des données pour les données au repos sera également disponible dans les versions ultérieures.

## Journaux d’audit et de diagnostic

Vous pouvez utiliser les journaux d’audit ou de diagnostic selon si vous recherchez des journaux sur des activités liées à la gestion ou des activités liées aux données.

*  Les activités liées à la gestion utilisent les API d’Azure Resource Manager et sont affichées dans le portail Azure via les journaux d’audit.
*  Les activités liées aux données utilisent les API WebHDFS et sont signalées dans le portail via les journaux de diagnostic.

### Journaux d’audit

Pour respecter les réglementations, les organisations peuvent avoir besoin de pistes d’audit adéquates si elles doivent examiner un incident en profondeur. Data Lake Store est doté de fonctionnalités intégrées de surveillance et d’audit où toutes les activités de gestion de compte sont consignées.

Pour des pistes d’audit de gestion de compte, vous pouvez afficher et choisir les colonnes à consigner, et également exporter des journaux d’audit dans Azure Storage.

![Journaux d’audit](./media/data-lake-store-security-overview/audit-logs.png "Journaux d’audit")

### Journaux de diagnostic

Vous pouvez activer les pistes d’audit d’accès aux données à partir du portail Azure (**Paramètres de diagnostic**) et fournir un compte Azure Blob Storage dans lequel les journaux seront stockés.

![Journaux de diagnostic](./media/data-lake-store-security-overview/diagnostic-logs.png "Journaux de diagnostic")

Une fois que vous avez activé les paramètres de diagnostic, vous pouvez consulter les journaux dans l’onglet **Journaux de diagnostic**.

## Résumé

Les clients professionnels exigent une plateforme cloud d’analyse des données sécurisée et facile à utiliser. Azure Data Lake Store a été conçu pour répondre à ces exigences avec la gestion des identités et l’authentification via l’intégration d’Azure Active Directory, l’autorisation basée sur les ACL, l’isolement réseau, le chiffrement des données en transit et au repos (à venir) et l’audit.

Si vous souhaitez voir de nouvelles fonctionnalités incluses dans Data Lake Store, envoyez-nous vos commentaires sur notre [forum UserVoice](https://feedback.azure.com/forums/327234-data-lake).

## Voir aussi

- [Présentation d’Azure Data Lake Store](data-lake-store-overview.md)
- [Prise en main de Data Lake Store](data-lake-store-get-started-portal.md)
- [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0720_2016-->