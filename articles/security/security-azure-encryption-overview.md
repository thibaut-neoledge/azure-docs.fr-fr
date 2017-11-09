---
title: "Vue d’ensemble du chiffrement Azure | Documents Microsoft"
description: "En savoir plus sur les différentes options de chiffrement dans Azure"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.openlocfilehash: 752568747ab96bc0a9c7fc5f24ff28c3bce4e09f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-encryption-overview"></a>Vue d’ensemble du chiffrement Azure

Cet article fournit une vue d’ensemble de l’utilisation du chiffrement dans Microsoft Azure Document. Il couvre les principales zones de chiffrement, y compris le chiffrement au repos, le chiffrement en vol et la gestion des clés avec Key Vault. Chaque section inclut des liens vers des informations plus détaillées.

## <a name="encryption-of-data-at-rest"></a>Chiffrement des données au repos

Les données au repos incluent des informations qui se trouvent dans un stockage persistant sur un support physique, sous n’importe quel format numérique. Cela inclut les fichiers sur un support magnétique ou optique, les données archivées et les sauvegardes de données. Microsoft Azure offre une variété de solutions de stockage de données en fonction des besoins, y compris le stockage sur fichier, disque, objet blob et table. Microsoft fournit également le chiffrement pour protéger [Azure SQL Database](../sql-database/sql-database-technical-overview.md), [CosmosDB](../cosmos-db/introduction.md) et Azure Data Lake.

Le chiffrement des données au repos est disponible pour les modèles de cloud Software-as-a-Service (SaaS), Platform-as-a-Service (PaaS) et Infrastructure-as-a-Service (IaaS) Azure. Ce document résume et fournit des ressources pour vous aider à utiliser les options de chiffrement Azure.

Pour plus de discussion sur le mode de chiffrement de données au repos dans Azure, consultez le document intitulé [Chiffrement au repos des données Azure](azure-security-encryption-atrest.md)

## <a name="azure-encryption-models"></a>Modèles de chiffrement Azure

Azure prend en charge plusieurs modèles de chiffrement, y compris le chiffrement côté serveur à l’aide de clés gérées par le service, de clés de gérées par le client dans Azure Key Vault ou à l’aide de clés gérées par le client sur du matériel de contrôlé par le client. Le chiffrement côté client vous permet de gérer et de stocker des clés locales ou dans un autre emplacement sûr.

### <a name="client-side-encryption"></a>chiffrement côté client

Le chiffrement côté client est effectué en dehors d’Azure. Le chiffrement côté client inclut :

- les données chiffrées par une application qui s’exécute dans le centre de données du client ou par une application de service
- Les données sont déjà chiffrées lorsqu’elles sont reçues par Azure.

Avec le chiffrement côté client, le fournisseur de services cloud n’a pas accès aux clés de chiffrement et ne peut pas déchiffrer ces données. Vous conservez un contrôle total des clés.

### <a name="server-side-encryption"></a>Chiffrement côté serveur

Les trois modèles de chiffrement côté serveur offrent différentes caractéristiques de gestion de clés, qui peuvent être choisies selon vos besoins.

- Les **Clés gérées par le service** fournissent une combinaison de contrôle et de fonctionnalités avec une faible surcharge

- Les **Clés gérées par le client** vous offrent le contrôle sur les clés, y compris la possibilité d’apporter vos propres clés (BYOK) ou d’en générer de nouvelles.

- Les **Clés gérées par le service dans ccustomer-controlledhardware** vous permettent de gérer les clés de votre référentiel propriétaire qui se trouve en dehors du contrôle de Microsoft. Il s’agit de l’option Héberger vos propres clés (HYOK). Toutefois, la configuration est complexe et la plupart des services Azure ne prennent pas en charge ce modèle.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Les machines virtuelles Windows et Linux peuvent être protégées à l’aide de [Azure Disk Encryption](azure-security-disk-encryption.md), qui utilise la technologie [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) et Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) pour protéger des disques du système d’exploitation et des disques de données avec un chiffrement de volume complet.

Les clés et secrets de chiffrement sont sauvegardés dans votre abonnement [Azure Key Vault](../key-vault/key-vault-whatis.md). Vous pouvez sauvegarder et restaurer des machines virtuelles chiffrées avec la configuration de clé de chiffrement à clé (KEK) à l’aide du service Azure Backup.

### <a name="azure-storage-service-encryption"></a>Chiffrement du service de stockage Azure

Les données au repos dans le stockage Azure (objet blob et fichier) peuvent être chiffrées dans les scénarios côté serveur et côté client.

[Azure Storage Service Encryption](../storage/storage-service-encryption.md) (SSE) peut chiffrer automatiquement les données avant qu’elles ne soient stockées et les déchiffre automatiquement lorsque vous les récupérez, rendant le processus entièrement transparent pour les utilisateurs. Le chiffrement du service de stockage utilise un [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, qui constitue un des plus forts chiffrements par bloc disponible et gère le chiffrement, le déchiffrement et la gestion des clés de manière transparente.

### <a name="client-side-encryption-of-azure-blobs"></a>Chiffrement côté client des objets blob Azure

Le chiffrement côté client d’objets blob Azure peut être effectué de différentes façons.

Vous pouvez utiliser la bibliothèque cliente de stockage Azure pour le package NuGet de .NET pour chiffrer les données dans vos applications clientes avant de le télécharger vers le stockage Azure.

Pour en savoir plus et télécharger la bibliothèque cliente de stockage Azure pour le package NuGet de .NET, consultez le document intitulé [Stockage Windows Azure 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage)

Lorsque vous utilisez le chiffrement côté client avec Azure Key Vault, vos données sont chiffrées à l’aide d’une clé de chiffrement de contenu (CEK) symétrique à usage unique qui est générée par le client du kit SDK du stockage Azure. La clé CEK est chiffrée à l’aide d’une clé de chiffrement de clé (KEK), qui peut être une clé symétrique ou une paire de clés asymétriques. Vous pouvez la gérer localement ou la stocker dans Azure Key Vault. Les données chiffrées sont ensuite téléchargées sur le service de stockage Azure.

Pour en savoir plus sur le chiffrement côté client avec Azure Key Vault et bien démarrer avec le guide pratique, consultez le document intitulé [Didacticiel : chiffrer et déchiffrer les objets blob dans le stockage Microsoft Azure à l’aide d’Azure Key Vault](../storage/storage-encrypt-decrypt-blobs-key-vault.md)

Enfin, vous pouvez également utiliser la bibliothèque cliente de stockage Azure pour Java et effectuer un chiffrement côté client avant de charger des données dans le stockage Azure et pour déchiffrer les données lorsque de son téléchargement vers le client. La bibliothèque prend également en charge l’intégration à [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour la gestion des clés de compte de stockage.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Chiffrement des données au repos avec la base de données Azure SQL

[Azure SQL Database](../sql-database/sql-database-technical-overview.md) est une base de données relationnelle à usage général de Microsoft Azure qui prend en charge des structures telles que les données relationnelles, JSON, les données spatiales et XML. Azure SQL prend en charge le chiffrement côté serveur via la fonctionnalité de chiffrement transparent des données (TDE) et le chiffrement côté client via la fonction Always Encrypted.

#### <a name="transparent-data-encryption"></a>Chiffrement transparent des données

Le [chiffrement transparent des données TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) est utilisé pour chiffrer les fichiers d’enregistrement [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../sql-database/sql-database-technical-overview.md) et [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) en temps réel à l’aide d’une clé de chiffrement de base de données (DEK) stockée dans l’enregistrement de démarrage de la base de données pour une disponibilité lors de la récupération.

Le chiffrement transparent des données (TDE) protège les données et les fichiers journaux, à l’aide d’algorithmes de chiffrement AES et 3DES. Le chiffrement du fichier de base de données est effectué au niveau de la page ; les pages dans une base de données chiffrée sont chiffrées avant d’être écrites sur le disque et sont déchiffrées lorsqu’elles sont lues en mémoire. TDE est désormais activé par défaut sur les nouvelles bases de données Azure SQL.

#### <a name="always-encrypted"></a>Always Encrypted

La fonction [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) dans Azure SQL vous permet de chiffrer les données dans les applications clientes avant de les stocker dans la base de données Azure SQL et permet d’activer la délégation de l’administration de bases de données locales à des tiers et de séparer ceux qui possèdent et peuvent afficher les données et ceux qui les gèrent mais ne doivent pas y accéder.

#### <a name="cellcolumn-level-encryption"></a>Chiffrement au niveau des colonnes/cellules

Azure SQL Database vous permet d’appliquer un chiffrement symétrique à une colonne de données à l’aide de Transact-SQL. Il s’agit du [chiffrement au niveau des cellules ou chiffrement au niveau des colonnes](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) (CLE), car vous pouvez l’utiliser pour chiffrer les colonnes ou même des cellules de données spécifiques avec différentes clés de chiffrement. Cela vous offre la possibilité d’un chiffrement plus granulaire que le chiffrement transparent des données, qui chiffre les données dans les pages.

Le CLE dispose de fonctions intégrées que vous pouvez utiliser pour chiffrer les données à l’aide des clés symétriques ou asymétriques, avec la clé publique d’un certificat ou une phrase secrète à l’aide de 3DES.

### <a name="cosmos-db-database-encryption"></a>Chiffrement de base de données Azure Cosmos DB

[Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) est une base de données multi-modèles distribuée par Microsoft au niveau mondial. Les données utilisateur stockées dans le stockage non volatile (disques SSD) Cosmos DB sont chiffrées par défaut ; Il n’existe aucun contrôle pour les activer ou les désactiver. Le chiffrement au repos est implémenté à l’aide d’un certain nombre de technologies de sécurité, notamment des systèmes de stockage de clés sécurisés, des réseaux chiffrés et des API de chiffrement. Les clés de chiffrement sont gérées par Microsoft et font l’objet d’une rotation par le biais de directives internes de Microsoft.

### <a name="at-rest-encryption-in-azure-data-lake"></a>Chiffrement au repos dans Azure Data Lake

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) est un référentiel de l’entreprise pour tous les types de données collectées dans un seul emplacement avant toute définition formelle de spécifications ou schémas. Azure Data Lake Store prend en charge « par défaut » un chiffrement transparent de données au repos défini lors de la création de votre compte. Par défaut, Data Lake Store gère les clés pour vous, mais vous avez la possibilité de les gérer vous-même.

Trois types de clés sont utilisées dans le chiffrement et le déchiffrement des données : la clé de chiffrement principale (MEK), la clé de chiffrement de données (DEK) et la clé de chiffrement de blocs (BEK). La clé MEK permet de chiffrer la clé DEK, stockée sur un support persistant et a clé BEK est dérivée de la clé DEK et du bloc de données. Si vous gérez vos propres clés, vous pouvez procéder à la rotation de la clé MEK.

## <a name="encryption-of-data-in-transit"></a>Chiffrement des données en transit

Azure offre plusieurs mécanismes pour protéger la confidentialité des données lorsqu’elles transitent d’un emplacement à un autre.

### <a name="tlsssl-encryption-in-azure"></a>Chiffrement TLS/SSL dans Azure

Microsoft utilise le protocole [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) pour protéger les données lorsqu’elles sont en déplacement entre les services cloud et les clients. Les centres de données de Microsoft négocient une connexion TLS avec les systèmes clients qui se connectent aux services Azure. TLS fournit une authentification forte, la confidentialité et l’intégrité des messages (activation de la détection de falsification, l’interception et la falsification des messages), l’interopérabilité, la flexibilité, la facilité de déploiement et l’utilisation des algorithmes.

[Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) protège les connexions entre les systèmes clients des clients et les services cloud de Microsoft par des clés uniques. Les connexions utilisent également les longueurs de clés de chiffrement RSA de 2 048 bits. Cette combinaison rend difficile pour une personne l’interception et l’accès aux données en transit.

### <a name="azure-storage-transactions"></a>Transactions de stockage Azure

Si vous interagissez avec le stockage Azure via le portail Azure, toutes les transactions se produisent via HTTPS. L’API de stockage REST par le biais de HTTPS peut également être utilisée pour interagir avec le stockage Azure. Vous pouvez appliquer l’utilisation du protocole HTTPS lorsque vous appelez les API REST pour qu’elles accèdent aux objets dans les comptes de stockage en activant l’option Transfert sécurisé requis pour le compte de stockage.

Les signatures d’accès partagé ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)), qui peuvent être utilisées pour déléguer l’accès aux objets de stockage Azure, incluent une option pour spécifier que seul le protocole HTTPS est autorisé avec les signatures d’accès partagé. Cela permet de garantir que toute personne envoyant des liens avec des jetons SAS utilise le protocole approprié.

[SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption) utilisé pour accéder à Azure File Shares prend en charge le chiffrement et est disponible dans Windows Server 2012 R2, Windows 8, Windows 8.1 et Windows 10, ce qui rend possible l’accès entre les régions et même l’accès sur le bureau.

Le chiffrement côté client chiffre les données avant qu’elles soient envoyées au stockage Azure, afin qu’elles soient chiffrées lorsqu’elles transitent sur le réseau.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Chiffrement SMB sur les réseaux virtuels Azure 

[SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) dans des machines virtuelles Azure exécutant Windows Server 2012 et ultérieur vous donne la possibilité de sécuriser les transferts en chiffrant les données en transit via des réseaux virtuels Azure, pour vous protéger contre la falsification et l’écoute électronique des données. Les administrateurs peuvent activer le chiffrement SMB pour l’ensemble du serveur ou juste des partages spécifiques.

Par défaut, une fois le chiffrement SMB activé pour un partage ou un serveur, seuls les clients SMB 3 sont autorisés à accéder aux partages chiffrés.

## <a name="in-transit-encryption-in-azure-virtual-machines"></a>Chiffrement en transit dans les machines virtuelles Azure

Les données en transit vers, depuis et entre les machines virtuelles Azure exécutant Windows sont chiffrées de différentes manières, selon la nature de la connexion.

### <a name="rdp-sessions"></a>Sessions RDP

Vous pouvez vous connecter et ouvrir une session sur une machine virtuelle Azure à l’aide de la [Remote Desktop Protocol](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) (RDP) depuis un ordinateur client Windows ou depuis un Mac avec un client RDP installé. Les données en transit sur le réseau dans les sessions RDP peuvent être protégées par le TLS.

Vous pouvez également utiliser le Bureau à distance pour vous connecter à une machine virtuelle Linux dans Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Sécuriser l’accès aux machines virtuelles Linux avec SSH

Vous pouvez utiliser [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH) pour vous connecter aux machines virtuelles Linux s’exécutant dans Azure pour la gestion à distance. SSH est un protocole de connexion chiffré qui permet d'ouvrir des sessions en toute sécurité à travers des connexions non sécurisées. Il s’agit du protocole de connexion par défaut pour les machines virtuelles Linux hébergées dans Azure. À l’aide de clés SSH pour l’authentification, vous éliminez le besoin de mots de passe pour vous connecter. SSH utilise une paire de clés publique/privée (chiffrement asymétrique) pour l’authentification.

## <a name="azure-vpn-encryption"></a>Chiffrement VPN Azure

Vous pouvez vous connecter à Azure via un réseau privé virtuel qui crée un tunnel sécurisé pour protéger la confidentialité des données envoyées sur le réseau.

### <a name="azure-vpn-gateway"></a>Passerelle VPN Azure

La [passerelle VPN Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) permet d’envoyer un trafic chiffré entre votre réseau virtuel et votre emplacement local sur une connexion publique ou d’envoyer un trafic entre des réseaux virtuels.

Le VPN de site à site utilise [IPsec](https://en.wikipedia.org/wiki/IPsec) pour le chiffrement du transport. Les passerelles VPN Azure utilisent un ensemble de propositions par défaut. Vous pouvez configurer des passerelles VPN Azure pour utiliser une stratégie IPsec/IKE personnalisée avec des algorithmes de chiffrement spécifiques et des avantages clés, plutôt que des ensembles de stratégies Azure par défaut.

### <a name="point-to-site-vpn"></a>VPN de point à site

Les VPN de point à site autorise l’accès à des ordinateurs de clients individuels à un réseau virtuel Azure. Le protocole SSTP [(Secure Socket Tunneling Protocol)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) est utilisé pour créer le tunnel VPN et peut traverser les pare-feu (le tunnel apparaît en tant que connexion HTTPS). Puis-je utiliser ma propre AC racine PKI interne pour une connectivité de point à site.

Vous pouvez configurer une connexion VPN de point à site à un réseau virtuel à l’aide du portail Azure avec l’authentification par certificat ou PowerShell.

Pour en savoir plus sur les connexions VPN de point à site vers des réseaux virtuels Azure, consultez : [Configurer une connexion de point à site à un réseau virtuel à l’aide de l’authentification par certificat : portail Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) et

[Configurer une connexion de point à site vers un réseau virtuel à l’aide d’une authentification par certificat : PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpn"></a>VPN de site à site 

Une connexion de passerelle VPN de site à site permet de connecter votre réseau local à un réseau virtuel Azure via un tunnel VPN IPsec/IKE (IKEv1 ou IKEv2). Ce type de connexion requiert un périphérique VPN local disposant d’une adresse IP publique exposée en externe.

Vous pouvez configurer une connexion VPN de site à site à un réseau virtuel à l’aide du portail Azure PowerShell ou l’interface de ligne de commande Azure (CLI).

Veuillez lire ces informations supplémentaires :

[Créer une connexion de site à site dans le portail Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Créer une connexion de site à site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Créer un réseau virtuel avec une connexion VPN de site à site à l’aide de l’interface CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-azure-data-lake"></a>Chiffrement en transit dans Azure Data Lake

Les données en transit (ou données en mouvement) sont également toujours chiffrées dans Data Lake Store. Outre le chiffrement des données avant le stockage sur un support permanent, les données sont également toujours sécurisées en transit à l’aide du protocole HTTPS. HTTPS est le seul protocole pris en charge pour les interfaces REST Data Lake Store.

Pour en savoir plus sur le chiffrement des données en transit dans Azure Data Lake, consultez le document intitulé [Chiffrement des données dans Azure Data Lake Store.](../data-lake-store/data-lake-store-encryption.md)

## <a name="key-management-with-azure-key-vault"></a>Gestion des clés dans Azure Key Vault

Sans protection appropriée et la gestion des clés, le chiffrement est rendu inutilisable. Azure Key Vault est la solution recommandée de Microsoft qui permet de gérer et de contrôler l’accès aux clés de chiffrement utilisées par les services de cloud. Les autorisations d’accès aux clés peuvent être attribuées aux services ou aux utilisateurs via des comptes Azure Active Directory.

Azure Key Vault soulage les entreprises de la nécessité de configurer, de corriger et de tenir à jour des modules de sécurité matériels (HSM) et des logiciels de gestion de clés. Avec Azure Key Vault, Microsoft ne voit jamais vos clés et les applications n’en ont pas d’accès direct ; vous conservez le contrôle. Vous pouvez également importer ou générer des clés dans les modules HSM.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la sécurité d’Azure](security-get-started-overview.md)
- [Vue d’ensemble de la sécurité réseau d’Azure](security-network-overview.md)
- [Vue d’ensemble de la sécurité des bases de données d’Azure](azure-database-security-overview.md)
- [Vue d’ensemble de la sécurité des machines virtuelles d’Azure](security-virtual-machines-overview.md)
- [Chiffrement des données au repos](azure-security-encryption-atrest.md)
- [Meilleures pratiques en matière de chiffrement et de sécurité des données](azure-security-data-encryption-best-practices.md)
