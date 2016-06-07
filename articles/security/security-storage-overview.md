<properties
   pageTitle="Vue d’ensemble des fonctionnalités de sécurité d’Azure Storage | Microsoft Azure"
   description="Azure Storage est la solution de stockage cloud pour les applications récentes qui s’appuient sur la durabilité, la disponibilité et l’extensibilité pour répondre aux besoins des clients. Cet article fournit une vue d’ensemble des principales fonctionnalités de sécurité Azure pouvant être utilisées avec Azure Storage."
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/19/2016"
   ms.author="terrylan"/>

# Vue d’ensemble des fonctionnalités de sécurité d’Azure Storage

Azure Storage est la solution de stockage cloud pour les applications récentes qui s’appuient sur la durabilité, la disponibilité et l’extensibilité pour répondre aux besoins des clients. Azure Storage fournit un ensemble complet de fonctionnalités de sécurité :

- Le compte de stockage peut être sécurisé à l’aide du contrôle d’accès en fonction du rôle et d’Azure Active Directory.
- Les données peuvent être sécurisées en transit entre une application et Azure au moyen du chiffrement côté client, de HTTPS ou de SMB 3.0.
- Les données peuvent être définies de façon à être automatiquement chiffrées du moment où elles sont écrites dans Azure Storage en utilisant Storage Service Encryption.
- Les disques de système d’exploitation et de données utilisés par les machines virtuelles peuvent être définis de façon à être chiffrés à l’aide d’Azure Disk Encryption.
- Il est possible d’accorder un accès délégué aux objets de données d’Azure Storage en utilisant des signatures d’accès partagé.

Cet article fournit une vue d’ensemble de chacune de ces fonctionnalités de sécurité pouvant être utilisées avec Azure Storage. Les liens renvoient à des articles qui fournissent des informations détaillées complémentaires sur chaque fonctionnalité.

Pour une étude plus détaillée de la sécurité dans Azure Storage, consultez le [Guide de sécurité Azure Storage](https://azure.microsoft.com/documentation/articles../storage/storage-security-guide.md).

Voici les principales fonctionnalités abordées dans cet article :

- Contrôle d’accès en fonction du rôle
- Gestion des clés de compte de stockage
- Accès délégué aux objets de stockage
- Chiffrement en transit
- Chiffrement au repos et chiffrement Service Storage
- Analyse du stockage

## Contrôle d’accès en fonction du rôle

Vous pouvez sécuriser un compte de stockage en utilisant le contrôle d’accès en fonction du rôle (RBAC). Chaque abonnement Azure est associé à un locataire Azure Active Directory (AD). Les utilisateurs, groupes et applications de ce répertoire peuvent gérer les ressources de l’abonnement Azure. Ces droits d’accès sont octroyés en attribuant le rôle RBAC approprié aux utilisateurs, groupes et applications, dans une étendue donnée.

Vous pouvez utiliser RBAC pour accorder l’accès aux opérations de gestion de votre compte Azure Storage. La fonctionnalité RBAC vous permet de contrôler les utilisateurs autorisés à :

- gérer le compte de stockage concerné ;
- lire les clés de compte de stockage et utiliser ces clés pour accéder aux objets blob, aux files d’attente, aux tables et aux fichiers ;
- régénérer les clés de stockage.

En savoir plus :

- [Contrôle d’accès en fonction du rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md)

## Gestion des clés de compte de stockage

Lorsque vous créez un compte de stockage, Azure génère deux clés d'accès de stockage de 512 bits, qui servent à l'authentification lors de l'accès au compte de stockage. En fournissant deux clés d'accès de stockage, Azure vous permet de régénérer les clés sans interrompre votre service de stockage ni l'accès à ce service. Vous pouvez conserver des connexions au compte de stockage à l’aide d’une clé d’accès lorsque vous régénérez l’autre clé.

Les clés de compte de stockage, combinées au nom du compte, permettent d’accéder aux objets de données stockés dans le compte de stockage, notamment les objets blob, les entités d’une table, les messages de file d’attente et les fichiers se trouvant sur un partage de fichiers Azure. L’utilisation de RBAC vous permet de contrôler l’accès aux clés de compte de stockage qui contrôlent l’accès aux objets de données proprement dits.

En savoir plus :

- [À propos des comptes Azure Storage](../storage/storage-create-storage-account.md)

## Accès délégué aux objets de stockage

Une signature d’accès partagé (SAP) est une chaîne contenant un jeton de sécurité qui peut être associé à un URI et vous permet de déléguer l’accès aux objets de stockage et de spécifier des restrictions telles que les autorisations et la plage des dates/heures d’accès.

La distribution de votre clé de compte de stockage revient à partager les clés de votre royaume de stockage. L’accès accordé est complet. Avec une signature d’accès partagé, vous pouvez donner à un client uniquement les autorisations requises pour une durée limitée. Vous pouvez :

- accorder l’accès à des objets blob, conteneurs, messages de file d’attente, fichiers et tables. Avec les tables, vous pouvez en fait accorder l’autorisation d’accéder à un ensemble d’entités dans la table en spécifiant les plages de clés de partition et de ligne auxquelles vous souhaitez que l’utilisateur ait accès.
- spécifier que les demandes effectuées à l’aide d’une signature d’accès partagé doivent être limitées à une certaine adresse IP ou plage d’adresses IP externe à Azure ;
- exiger que les demandes soient effectuées à l’aide d’un protocole spécifique (HTTPS ou HTTP/HTTPS). Cela signifie que, si vous souhaitez uniquement autoriser le trafic HTTPS, vous pouvez affecter la valeur HTTPS uniquement au protocole requis et le trafic HTTP sera bloqué.

En savoir plus :

- [Présentation du modèle SAP](../storage/storage-dotnet-shared-access-signature-part-1.md)

## Chiffrement en transit
Le chiffrement en transit est un mécanisme de protection des données transmises sur des réseaux. Azure Storage vous permet de sécuriser les données à l’aide de diverses fonctionnalités :

- Le chiffrement au niveau du transport (HTTPS, par exemple) lorsque vous transférez des données vers ou depuis Azure Storage.
- Le chiffrement câblé, par exemple le chiffrement SMB 3.0 pour les partages de fichiers Azure.
- Le chiffrement côté client, pour chiffrer les données avant leur transfert vers Storage et les déchiffrer après leur transfert à partir de Storage.

### Chiffrement au niveau du transport

Pour garantir la sécurité de vos données Azure Storage , vous pouvez chiffrer les données entre le client et Azure Storage. Vous pouvez [activer HTTPS](../app-service-web/web-sites-configure-ssl-certificate.md) lors de l’appel des API REST ou de l’accès aux objets dans le stockage. Vous pouvez également utiliser les [signatures d’accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md) (SAS) pour déléguer l’accès aux objets Azure Storage. Celles-ci incluent un paramètre « protocole » facultatif, qui vous permet d’imposer l’utilisation exclusive du protocole HTTPS avec les signatures d’accès partagé. Cela permet de s’assurer que toute personne qui envoie des liens avec des jetons SAP utilisera systématiquement le protocole approprié.

### Chiffrement câblé pour l’accès au partage de fichiers

SMB 3.0 prend en charge le chiffrement et peut être utilisé avec Windows Server 2012 R2, Windows 8, Windows 8.1 et Windows 10, ce qui rend possibles les connexions d’accès entre régions et même les accès sur le bureau.

Les partages de fichiers Azure peuvent être utilisés avec les machines virtuelles Linux. Notez que, comme le client SMB de Linux ne prend pas encore en charge le chiffrement, l’accès est autorisé uniquement dans la même région Azure. Le chiffrement pour Linux sera prochainement pris en charge. Quand ce chiffrement sera pris en charge, vous pourrez accéder aux partage de fichiers Azure sur Linux de la même manière que sur Windows, en bénéficiant d’un chiffrement de niveau réseau identique.

En savoir plus :

- [Utilisation du stockage de fichiers Azure avec Linux](../storage/storage-how-to-use-files-linux.md)
- [Prise en main d’Azure File Storage sur Windows](../storage/storage-dotnet-how-to-use-files.md)
- [Stockage de fichiers dans Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)

### Chiffrement côté client

Le chiffrement côté client vous permet de garantir la sécurité de vos données pendant leur transfert entre une application cliente et Azure Storage. Les données sont chiffrées avant d’être transférées vers Azure Storage. Quand vous récupérez les données d’Azure Storage, les données sont déchiffrées seulement après leur réception côté client. Même si les données sont chiffrées quand elles sont en transit sur le réseau, vous pouvez utiliser également le protocole HTTPS pour réduire les erreurs réseau ayant un impact sur l’intégrité des données.

En savoir plus :

- [Client-Side Encryption for Microsoft Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/) (Chiffrement côté client pour Microsoft Azure Storage)
- [Cloud security controls series: Encrypting Data in Transit](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/) (Série consacrée aux contrôles de sécurité dans le cloud : chiffrement des données en transit)

## Chiffrement au repos

Trois fonctionnalités Azure fournissent un chiffrement des données « au repos ».

- Storage Service Encryption
- Chiffrement côté client
- Azure Disk Encryption

### Storage Service Encryption

Storage Service Encryption vous permet de demander que le service de stockage chiffre automatiquement les données lors de leur écriture dans Azure Storage. Les données lues à partir d’Azure Storage sont déchiffrées par le service de stockage avant d’être renvoyées. Grâce à ce processus, vous sécurisez vos données sans avoir à modifier le code existant ni à ajouter du code dans les applications.

[Azure Storage Service Encryption](https://azure.microsoft.com/services/storage/) est disponible pour [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/). Pour plus d’informations sur les autres types de stockage Azure, consultez les pages [File](https://azure.microsoft.com/services/storage/files/), [Disk (Premium Storage)](https://azure.microsoft.com/services/storage/premium-storage/), [Table](https://azure.microsoft.com/services/storage/tables/) et [Queue](https://azure.microsoft.com/services/storage/queues/).

En savoir plus :

- [Azure Storage Service Encryption pour les données au repos](../storage/storage-service-encryption.md)

### Chiffrement côté client

Nous avons déjà mentionné le chiffrement côté client en abordant la question du chiffrement en transit. Cette fonctionnalité vous permet de chiffrer par programmation vos données dans une application cliente avant de les envoyer via le réseau vers Azure Storage, et de les déchiffrer par programmation une fois que vous les avez récupérées d’Azure Storage.

Elle fournit le chiffrement en transit, mais également le chiffrement au repos. Même si les données sont chiffrées quand elles sont en transit, vous pouvez utiliser le protocole HTTPS, car HTTPS inclut des vérifications de l’intégrité des données qui contribuent à réduire les erreurs réseau ayant un impact sur l’intégrité des données.

En savoir plus :

- [Chiffrement côté client et Azure Key Vault pour Microsoft Azure Storage](../storage/storage-client-side-encryption.md)

### Azure Disk Encryption

Azure Disk Encryption pour les machines virtuelles vous permet de répondre aux exigences de conformité et de sécurité des organisations en chiffrant vos disques de machine virtuelle (y compris disques d’amorçage et disques de données) avec des clés et stratégies que vous pouvez contrôler dans votre [coffre de clés Azure](https://azure.microsoft.com/services/key-vault/).

Azure Disk Encryption pour les machines virtuelles est compatible avec les systèmes d’exploitation Linux et Windows. Cette fonctionnalité utilise également le coffre de clés pour vous aider à protéger, gérer et contrôler l’utilisation de vos clés de chiffrement de disque. Toutes les données contenues dans les disques de vos machines virtuelles sont chiffrées au repos à l’aide d’une technologie de chiffrement standard dans vos comptes Azure Storage. La solution Disk Encryption pour Windows est basée sur le [chiffrement de lecteur Microsoft BitLocker](https://technet.microsoft.com/library/cc732774.aspx) et la solution Linux est basée sur le chiffrement [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

En savoir plus :

- [Azure Disk Encryption pour des machines virtuelles IaaS Windows et Linux](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## Analyse du stockage

Pour chaque compte de stockage, vous pouvez activer Azure Storage Analytics pour effectuer la journalisation et stocker les données de métriques. Cet outil est idéal si vous souhaitez vérifier les mesures de performances ou résoudre des problèmes de performances d’un compte de stockage.

Les journaux d’analyse du stockage vous permettent de voir un autre élément de données : la méthode d’authentification utilisée par un utilisateur lors de l’accès au stockage. Par exemple, avec Blob Storage, vous pouvez voir s’il a utilisé une signature d’accès partagé ou les clés de compte de stockage, ou si l’objet blob qui a fait l’objet d’un accès est public.

En savoir plus :

- [Analyse du stockage](../storage/storage-analytics.md)
- [Format du journal de l’analyse de stockage](https://msdn.microsoft.com/library/azure/hh343259.aspx)
- [Surveiller un compte de stockage dans le portail Azure](../storage/storage-monitor-storage-account.md)
- [Résolution des problèmes de bout en bout avec les métriques et la journalisation Azure, AzCopy et Message Analyzer](../storage/storage-e2e-troubleshooting.md)

<!---HONumber=AcomDC_0525_2016-->