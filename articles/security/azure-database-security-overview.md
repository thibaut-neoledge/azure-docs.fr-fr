---
title: "Vue d’ensemble de la sécurité des bases de données Azure | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble des fonctionnalités de sécurité des bases de données Azure."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: TomSh
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 036ce3dce28e7951bb39754c4351661fae85f06c
ms.contentlocale: fr-fr
ms.lasthandoff: 08/09/2017

---

# <a name="azure-database-security-overview"></a>Vue d’ensemble de la sécurité des bases de données Azure

La sécurité est une préoccupation majeure lors de la gestion de bases de données, et elle a toujours été une priorité pour Azure SQL Database. Azure SQL Database prend en charge la sécurité de connexion avec des règles de pare-feu et un chiffrement de connexion. Il prend en charge l’authentification avec nom d’utilisateur et mot de passe, ainsi que l’authentification Azure Active Directory qui utilise des identités gérées par Azure Active Directory. L’autorisation utilise le contrôle d’accès en fonction du rôle.

Azure SQL Database prend en charge le chiffrement en chiffrant et déchiffrant en temps réel les bases de données, les sauvegardes associées et les fichiers journaux des transactions au repos sans que cela nécessite de modifications de l’application.

Microsoft offre des méthodes supplémentaires pour chiffrer des données d’entreprise :

-   Le chiffrement au niveau des cellules permet de chiffrer des colonnes voire des cellules de données spécifiques avec des clés de chiffrement différentes.
-   S’il vous faut un module de sécurité matériel ou si vous devez gérer la hiérarchie des clés de chiffrement de manière centralisée, consultez l’article de blog relatif à l’utilisation d’Azure Key Vault avec SQL Server dans une machine virtuelle Azure.
-   La solution Always Encrypted (actuellement en préversion) rend le chiffrement transparent pour les applications, et permet aux clients de chiffrer les données sensibles dans les applications clientes sans partager les clés de chiffrement avec SQL Database.

L’audit Azure SQL Database permet aux entreprises d’enregistrer des événements dans un journal d'audit dans Stockage Azure. Cette fonction s’intègre également dans Microsoft Power BI, afin de faciliter la création d’analyses et de rapports approfondis.

 Les bases de données SQL Azure peuvent être étroitement sécurisées afin de satisfaire la plupart des exigences réglementaires ou de sécurité, dont HIPAA, ISO 27001/27002 et PCI DSS Niveau 1. Une liste actualisée des certifications de conformité de sécurité est disponible sur le [site du Centre de confidentialité Azure](http://azure.microsoft.com/support/trust-center/services/).

Cet article présente les principes fondamentaux de la sécurisation de Microsoft Azure SQL Database pour les données structurées, tabulaires et relationnelles. Plus spécifiquement, cet article vous offre un aperçu sur les ressources dédiées à la protection des données, au contrôle d’accès et à la surveillance proactive.

Cet article présentant une vue d’ensemble de la sécurité des bases de données Azure se concentre sur les points suivants :

-   Protection des données
-   Contrôle d’accès
-   Surveillance proactive
-   Gestion centralisée de la sécurité
-   Place de marché Azure

## <a name="protect-data"></a>Protection des données

SQL Database sécurise vos données grâce au chiffrement : il utilise [Transport Layer Security](https://support.microsoft.com/kb/3135244) pour les données en mouvement, [Transparent Data Encryption](http://go.microsoft.com/fwlink/?LinkId=526242) pour les données au repos et [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) pour les données en cours d’utilisation.

Dans cette section, nous parlons de :

-   Chiffrement en mouvement
-   Chiffrement au repos
-   Chiffrement en cours d’utilisation (client)

Vous pouvez également utiliser les méthodes de chiffrement des données suivantes :

-   [chiffrement au niveau des cellules](https://msdn.microsoft.com/library/ms179331.aspx) permet de chiffrer des colonnes spécifiques, voire des cellules de données, avec des clés de chiffrement différentes.
-   S’il vous faut un module de sécurité matériel ou si vous devez gérer la hiérarchie des clés de chiffrement de manière centralisée, consultez l’article de blog relatif au [coffre de clés Microsoft Azure avec SQL Server dans une machine virtuelle Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="encryption-in-motion"></a>Chiffrement en mouvement

Un problème courant pour toutes les applications client/serveur est le besoin de confidentialité lorsque des données se déplacent sur des réseaux publics et privés. Si les données se déplaçant sur un réseau ne sont pas chiffrées, il existe un risque qu’elles soient capturées et volées par des utilisateurs non autorisés. Lorsque vous utilisez des services de base de données, vous devez vous assurer que les données sont chiffrées entre le client et le serveur de base de données, ainsi qu’entre les serveurs de base de données qui communiquent entre eux et avec des applications intermédiaires.

Un problème lorsque vous administrez un réseau est la sécurisation des données échangées entre les applications via un réseau non approuvé. Vous pouvez utiliser [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) pour authentifier les serveurs et clients, puis pour chiffrer les messages entre les parties authentifiées.

Dans le processus d’authentification, un client TLS/SSL envoie un message à un serveur TLS/SSL qui répond avec les informations dont il a besoin pour s’authentifier. Le client et le serveur effectuent un échange supplémentaire de clés de session, puis la boîte de dialogue d’authentification se ferme. Une fois l’authentification terminée, les communications SSL sécurisées peuvent commencer entre le serveur et le client à l’aide de clés de chiffrement symétriques établies pendant le processus d’authentification.

Toutes les connexions à la base de données SQL Microsoft Azure doivent être chiffrées à tout moment (via SSL/TLS) lorsque les données sont « en transit » depuis et vers la base de données. SQL Azure utilise TLS/SSL pour authentifier les serveurs et les clients, puis pour chiffrer les messages entre les parties authentifiées. Dans la chaîne de connexion de votre application, vous devez spécifier des paramètres permettant de chiffrer la connexion et non d’approuver le certificat de serveur (le système effectue cette opération pour vous lorsque vous copiez la chaîne de connexion hors du portail Azure Classic). Dans le cas contraire, la connexion ne vérifie pas l’identité du serveur et peut être la cible d’attaques de type MITM (« Man-In-The-Middle »). Pour le pilote ADO.NET, par exemple, ces paramètres de chaîne de connexion sont les suivants : Encrypt=True et TrustServerCertificate=False.

### <a name="encryption-at-rest"></a>Chiffrement au repos
Vous pouvez prendre plusieurs précautions pour sécuriser la base de données telles que la conception d’un système sécurisé, le chiffrement de ressources confidentielles et la création d'un pare-feu autour des serveurs de base de données. Toutefois, dans un scénario où le support physique (par exemple, les lecteurs ou les bandes de sauvegarde) est volé, une personne malveillante peut juste restaurer ou attacher la base de données et consulter les données.

Une solution consiste à chiffrer les données sensibles dans la base de données et à protéger les clés utilisées pour chiffrer les données avec un certificat. Cela empêche toute personne dépourvue des clés d'utiliser les données, mais ce type de protection doit être planifié.

Pour résoudre ce problème, SQL Server et SQL Azure prennent en charge la fonctionnalité [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde). La fonctionnalité Transparent Data Encryption chiffre les fichiers de données SQL Server et Azure SQL Database. Il s’agit de ce qu’on appelle le chiffrement de données au repos.

La fonctionnalité Transparent Data Encryption d’Azure SQL Database protège le système contre toute menace d’activité malveillante, en effectuant un chiffrement et un déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions au repos, sans qu’il soit nécessaire de modifier l’application.  

Le chiffrement transparent des données chiffre le stockage d’une base de données entière à l’aide d’une clé symétrique appelée clé de chiffrement de base de données. Dans SQL Database, la clé de chiffrement de base de données est protégée par un certificat de serveur intégré. Le certificat de serveur intégré est unique pour chaque serveur de base de données SQL.

Si une base de données est dans un relation GeoDR, elle est protégée par une clé différente sur chaque serveur. Si deux bases de données sont connectées au même serveur, elles partagent le même certificat intégré. Microsoft alterne automatiquement ces certificats au moins tous les 90 jours. Pour obtenir une description générale du chiffrement transparent des données, consultez [Chiffrement transparent des données (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Chiffrement en cours d’utilisation (client)

La plupart des violations de données impliquent le vol de données critiques telles que des numéros de carte de crédit ou des informations d’identification personnelle. Les bases de données peuvent contenir des trésors d’informations sensibles. Ils peuvent contenir des données personnelles de clients, des informations confidentielles sur la concurrence et des éléments de propriété intellectuelle. Les pertes ou vols de données, en particulier de données client, peuvent entraîner une dégradation de l’image de marque, un déficit de compétitivité, des amendes lourdes, voire des actions en justice.

![Toujours chiffré](./media/azure-databse-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) est une fonctionnalité conçue pour protéger les données sensibles, telles que des numéros de carte bancaire ou des numéros d’identification (par exemple, des numéros de sécurité sociale), stockées dans Azure SQL Database ou des bases de données SQL Server. Always Encrypted permet aux clients de chiffrer les données sensibles des applications clientes et de ne jamais divulguer les clés de chiffrement au moteur de base de données (SQL Database ou SQL Server).

Always Encrypted sépare les utilisateurs qui sont propriétaires des données (et peuvent les consulter) des utilisateurs qui gèrent les données (mais n’y ont pas accès). Elle veille à ce que les administrateurs de base de données locaux, les opérateurs de base de données cloud ou d’autres utilisateurs disposant de privilèges élevés mais non autorisés, ne puissent pas accéder aux données chiffrées.

Par ailleurs, Always Encrypted rend le chiffrement transparent pour les applications. Un pilote prenant en charge Always Encrypted est installé sur l’ordinateur client, qui peut automatiquement chiffrer et déchiffrer les données sensibles dans l’application cliente. Le pilote chiffre les données figurant dans les colonnes sensibles avant de les transmettre au moteur de base de données, et réécrit automatiquement les requêtes de façon à préserver leur sémantique pour l’application. De même, le pilote déchiffre de manière transparente les données stockées dans les colonnes de base de données chiffrées contenues dans les résultats de requête.

## <a name="access-control"></a>Contrôle d’accès
Pour assurer la sécurité, SQL Database contrôle l’accès avec des règles de pare-feu qui limitent la connectivité par adresse IP, des mécanismes d’authentification qui obligent les utilisateurs à prouver leur identité, et des mécanismes d’autorisation qui les restreignent à certaines actions et données.

### <a name="database-access"></a>Accès à la base de données

La protection des données commence avec le contrôle de l’accès à celles-ci. Le centre de données hébergeant vos données gère l’accès physique, tandis que vous pouvez configurer un pare-feu pour gérer la sécurité au niveau de la couche réseau. Vous pouvez également contrôler l’accès en configurant des connexions pour l’authentification, et définir des autorisations pour les rôles serveur et base de données.

Dans cette section, nous parlons de :

-   Pare-feu et règles de pare-feu
-   Authentification
-   Autorisation

#### <a name="firewall-and-firewall-rules"></a>Pare-feu et règles de pare-feu

Microsoft Azure SQL Database fournit un service de base de données relationnelle pour Azure et d’autres applications basées sur Internet. Pour aider à protéger vos données, le pare-feu empêche tout accès à votre serveur de base de données jusqu’à ce que vous spécifiiez les ordinateurs qui disposent d’autorisations. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande. Pour plus d’informations, consultez l’article [Vue d’ensemble des règles de pare-feu d’Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

Le service [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) n’est disponible que via le port TCP 1433. Pour accéder à une base de données SQL depuis votre ordinateur, vérifiez que le pare-feu de votre ordinateur client autorise les communications TCP sortantes sur le port 1433. Si elles ne sont pas nécessaire pour les autres applications, bloquez les connexions entrantes sur le port TCP 1433.

#### <a name="authentication"></a>Authentification

L’authentification de base de données SQL fait référence au processus de validation de votre identité lorsque vous vous connectez à la base de données. Une base de données SQL prend en charge deux types d’authentification :

-   **Authentification SQL :** un seul compte de connexion est créé lors de la création d’une instance SQL logique. Il est appelé compte abonné de SQL Database. Ce compte se connecte en utilisant l’[Authentification SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (nom d’utilisateur et mot de passe). Ce compte est un administrateur sur l’instance de serveur logique et sur toutes les bases de données utilisateur associées à cette instance. Les autorisations du compte abonné ne peuvent pas être restreintes. Un seul de ces comptes peut exister.
-   **Authentification Azure Active Directory :** l’[Authentification Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) est un mécanisme de connexion à Microsoft Azure SQL Database et à SQL Data Warehouse en utilisant des identités d’Azure Active Directory (Azure AD). Cela vous permet de gérer de manière centralisée les identités des utilisateurs de base de données.

![Authentification](./media/azure-databse-security-overview/azure-database-fig2.png)

 Avantages de l’authentification Azure Active Directory :
  - Elle fournit une alternative à l’authentification SQL Server.
  - Elle contribue également à arrêter la prolifération des identités utilisateurs sur les serveurs de base de données et permet une rotation du mot de passe dans un emplacement unique.
  - Vous pouvez gérer les autorisations de base de données à l’aide de groupes (Azure Active Directory) externes.
  - Il peut éliminer le stockage des mots de passe en activant l’authentification intégrée Windows et les autres formes d’authentification prises en charge par Azure Active Directory.

#### <a name="authorization"></a>Autorisation
Le terme [autorisation](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) fait référence aux actions qu’un utilisateur peut exécuter au sein d’Azure SQL Database. Celle-ci sont contrôlées par les [appartenances au rôles](https://msdn.microsoft.com/library/ms189121) et les [autorisations au niveau objet](https://msdn.microsoft.com/library/ms191291.aspx) de la base de données de votre compte d’utilisateur. L’autorisation est le processus de détermination des ressources sécurisables auxquelles un principal peut accéder, ainsi que des opérations autorisées pour ces ressources.

### <a name="application-access"></a>Accès aux applications

Dans cette section, nous parlons de :

-   Masquage des données dynamiques
-   Sécurité au niveau des lignes

#### <a name="dynamic-data-masking"></a>Masquage des données dynamiques
Un conseiller de service dans un centre d'appel peut identifier les appelants à l’aide de quelques chiffres de leur numéro de sécurité sociale ou de carte de crédit, mais ces éléments de données ne doivent pas lui être entièrement divulgués .

Une règle de masquage peut être définie pour ne faire apparaître que les quatre derniers chiffres du numéro de sécurité sociale ou de carte de crédit dans l'ensemble de résultats de chaque requête.

![Masquage des données dynamiques](./media/azure-databse-security-overview/azure-database-fig3.png)

Autre exemple, un masque de données approprié peut être défini pour protéger les informations d'identification personnelle (PII), de telle sorte qu'un développeur puisse interroger les environnements de production à des fins de dépannage sans aller à l'encontre des réglementations de conformité.

[SQL Database Dynamic Data Masking](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limite l’exposition des données sensibles en les masquant aux utilisateurs sans privilèges. Le masquage des données dynamiques est pris en charge pour la version V12 de la base de données SQL Azure.

Le [masquage dynamique des données](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) contribue à empêcher tout accès non autorisé aux données sensibles en vous permettant d’indiquer la quantité de données sensibles à révéler avec un impact minimal sur la couche application. Il s’agit d’une fonctionnalité de sécurité basée sur des stratégies qui masque les données sensibles dans le jeu de résultats d’une requête dans les champs de la base de données désignés. Les données de la base de données ne sont pas modifiées.


> [!Note]
> Le masquage des données dynamiques peut être configuré par l’administrateur de base de données Azure, l’administrateur de serveur ou le responsable de la sécurité.

#### <a name="row-level-security"></a>Sécurité au niveau des lignes
Une autre exigence de sécurité courante pour les bases de données mutualisées est la [Sécurité au niveau des lignes](https://msdn.microsoft.com/library/dn765131.aspx). Cette fonctionnalité permet aux clients de contrôler l’accès aux lignes d’une table de base de données en fonction des caractéristiques de l’utilisateur qui exécute une requête (par exemple, appartenance à un groupe ou contexte d’exécution).

![Sécurité au niveau des lignes](./media/azure-databse-security-overview/azure-database-fig4.png)

La logique de restriction d’accès se situe dans la couche de base de données plutôt qu’à l’écart des données d’une autre couche d’application. Le système de base de données applique les restrictions d’accès chaque fois qu’un accès aux données est tenté à partir d’un quelconque niveau. Cela rend votre système de sécurité plus fiable et robuste en réduisant sa surface d’exposition.

La sécurité au niveau des lignes introduit le contrôle d’accès en fonction d’un prédicat. Elle opère une évaluation basée sur un prédicat, centralisée et flexible, qui peut prendre en compte des métadonnées ou tout autre critère que l’administrateur juge approprié. Le prédicat est utilisé en tant que critère pour déterminer si, en fonction des attributs de l’utilisateur, celui-ci dispose ou non de l’accès approprié aux données. Un contrôle d’accès en fonction d’une étiquette peut être implémenté en utilisant un contrôle d’accès en fonction d’un prédicat.

## <a name="proactive-monitoring"></a>Surveillance proactive
SQL Database sécurise vos données en fournissant des fonctionnalités d’**audit** et de **détection des menaces**.

### <a name="auditing"></a>Audit
L’audit SQL Database augmente votre capacité à acquérir une compréhension des événements et modifications qui se produisent au sein de la base de données, dont les mises à jour et les requêtes sur les données.

L’[audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) suit les événements de base de données et les consigne dans un journal d’audit dans votre compte Stockage Azure. L’audit peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données et à découvrir des discordances et anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité. L’audit permet et facilite le respect de normes de conformité, mais il ne garantit pas cette conformité.

L’audit de bases de données SQL permet :

-   **La rétention** d’une piste d’audit d’événements sélectionnés. Définissez les catégories d'actions et d'événements de base de données à auditer.
-   **La génération de rapports** sur les activités de la base de données. Vous pouvez utiliser des rapports préconfigurés et un tableau de bord pour une prise en main rapide de la génération de rapports d'activités et d'événements.
-   **L'analyse** des rapports. Vous pouvez repérer les événements suspects, les activités inhabituelles et les tendances.

Il existe deux méthodes d’audit :

-   **Audit d’objets blob** : les journaux sont écrits dans Stockage Blob Azure. Il s’agit d’une méthode d’audit plus récente, qui fournit des performances plus élevées, prend en charge l’audit avec une granularité plus élevée au niveau des objets et est plus économique.
-   **Audit des tables** : les journaux sont écrits dans Stockage Table Azure.

### <a name="threat-detection"></a>Détection de menaces
La [détection des menaces Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) détecte des activités suspectes indiquant des menaces de sécurité potentielles. La détection des menaces vous permet de réagir à des événements suspects se produisant dans la base de données, telles des injections de code SQL. Elle génère des alertes et autorise l’utilisation de l’audit Azure SQL Database pour explorer les événements suspects.

![Détection des menaces](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Par exemple, l’injection SQL représente l’un des problèmes de sécurité auxquels sont le plus exposées les applications web, et est utilisée pour cibler les applications pilotées par des données. Les pirates exploitent les vulnérabilités des applications pour injecter des instructions SQL nuisibles dans les champs de saisie d’application afin de violer ou modifier les données contenues dans la base de données.

Les responsables de la sécurité ou autres administrateurs désignés peuvent obtenir une notification immédiate concernant les activités suspectes qui interviennent sur la base de données. Chaque notification contient des détails sur l’activité suspecte et fournit des recommandations pour vous aider à étudier et corriger la menace.        

## <a name="centralized-security-management"></a>Gestion centralisée de la sécurité

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) vous aide à vous empêcher, détecter et répondre aux menaces. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) vous aide à protéger les données dans SQL Database en fournissant une visibilité de la sécurité de l’ensemble de vos serveurs et bases de données. Avec Security Center, vous pouvez :

-   définir des stratégies pour l’audit et le chiffrement de SQL Database ;
-   surveiller la sécurité des ressources SQL Database dans tous vos abonnements ;
-   identifier et corriger rapidement les problèmes de sécurité ;
-   intégrer les alertes de la [détection de menaces Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
-   Le Centre de sécurité prend en charge l’accès en fonction du rôle.

## <a name="azure-marketplace"></a>Azure Marketplace

La Place de marché Azure est un marché en ligne d'applications et de services, qui permet aux startups et aux éditeurs de logiciels indépendants de proposer leurs solutions aux clients Azure dans le monde entier.
La Place de marché Azure rassemble en une seule plateforme les écosystèmes partenaires de Microsoft Azure afin de mieux répondre aux besoins de nos clients et partenaires. Pour jeter un coup d’œil aux produits de sécurité de base de données disponibles sur la Place de marché Azure, cliquez [ici](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la manière de [Sécuriser votre base de données SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
- En savoir plus sur [Azure Security Center et SQL Database](https://docs.microsoft.com/azure/security-center/security-center-sql-database).
- En savoir plus sur la [Détection de menaces pour les bases de données SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
- En savoir plus sur la manière de [Résoudre les problèmes de performances et optimiser votre base de données](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial). 

