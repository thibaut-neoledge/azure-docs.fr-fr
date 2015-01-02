<properties urlDisplayName="Introduction to Azure Storage" pageTitle="Introduction à Azure Storage | Microsoft Azure" metaKeywords="Get started  Azure storage introduction  Azure storage overview  Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage  Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage  Azure file storage  Azure file  Azure file share  Azure " description="An overview of Microsoft Azure Storage." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="Introduction to Microsoft Azure Storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="tamram" />

# Introduction à Microsoft Azure Storage

Cet article propose une introduction à Microsoft Azure Storage destinée aux développeurs, aux professionnels de l'informatique et aux décideurs économiques. Il vous permettra de découvrir :

- ce qu'est Azure Storage et comment vous pouvez en tirer parti dans vos applications cloud, mobiles, serveur et bureautiques ;
- quels types de données vous pouvez stocker avec les services Azure Storage : objets blob, tables, files d'attente et fichiers ;
- comment est géré l'accès à vos données dans Azure Storage ;
- comment vos données Azure Storage sont protégées par la redondance et la réplication ; 
- comment procéder ensuite pour créer votre première application Azure Storage.

## Présentation d'Azure Storage ##

Le cloud computing permet d'élaborer de nouveaux scénarios pour les applications qui exigent un stockage évolutif, durable et à haute disponibilité pour leurs données, et c'est la raison pour laquelle Microsoft a développé Azure Storage. Azure Storage permet aux développeurs de créer des applications à grande échelle pour prendre en charge de nouveaux scénarios, et fournit également une base de stockage pour Azure Virtual Machines, autre preuve de sa robustesse. 

Azure Storage est massivement évolutif, ce qui vous permet de stocker et de traiter des centaines de téraoctets de données pour prendre en charge les scénarios Big Data exigés par les analyses scientifiques ou financières et les applications multimédias. Vous pouvez également stocker les petites quantités de données requises pour un petit site Web professionnel. Quels que soient vos besoins, vous payez uniquement pour les données que vous stockez. Azure Storage stocke actuellement des dizaines de billions d'objets client uniques et traite en moyenne des millions de requêtes par seconde. 

L'élasticité d'Azure Storage vous permet de concevoir des applications pour un large public mondial et de mettre à l'échelle ces applications (la quantité de stockage et le nombre de transactions requises) en fonction des besoins. Vous payez uniquement pour ce que vous utilisez, et pour la durée d'utilisation.

Azure Storage utilise un système de partitionnement automatique qui équilibre automatiquement la charge représentée par vos données sur la base du trafic. Autrement dit, lorsque les demandes concernant votre application augmentent, Azure Storage alloue automatiquement les ressources appropriées pour y répondre. 

Azure Storage est accessible partout dans le monde, depuis n'importe quel type d'application, qu'elle soit exécutée dans le cloud, sur le bureau, sur un serveur local, sur un appareil mobile ou sur une tablette. Vous pouvez utiliser Azure Storage dans les scénarios mobiles où l'application stocke un sous-ensemble de données sur l'appareil et le synchronise avec un ensemble complet des données stocké dans le cloud.

Azure Storage prend en charge les clients avec divers systèmes d'exploitation (notamment Windows et Linux) et divers langages de programmation (notamment .NET, Java, et C++) pour un développement pratique. Azure Storage expose également les ressources de données par l'intermédiaire de simples API REST, disponibles pour tout client capable d'envoyer et de recevoir des données via HTTP/HTTPS.

Azure Premium Storage est à présent disponible en version préliminaire. Azure Premium Storage offre des performances élevées et une prise en charge à faible latence du disque pour des charges de travail d'E/S intensives exécutées sur Azure Virtual Machines. Avec Azure Premium Storage, vous pouvez associer plusieurs disques de données persistants à une machine virtuelle et les configurer afin qu'ils répondent à vos exigences de performance. Chaque disque de données est approvisionné par un disque SSD dans Azure Premium Storage pour une performance d'E/S maximale. Consultez la page [Stockage Premium : stockage hautes performances pour les charges de travail d'Azure Virtual Machine](http://go.microsoft.com/fwlink/?LinkId=521898) pour en savoir plus. 

## Présentation des services Azure Storage ##

Un compte de stockage Azure est un compte sécurisé qui vous donne accès aux services d'Azure Storage. Votre compte de stockage fournit les espaces de noms uniques pour vos ressources de stockage. Deux types de comptes de stockage sont disponibles :

- Un compte de stockage standard qui inclut le stockage d'objets blob, de tables, de files d'attente et de fichiers.
- Un compte de stockage premium qui, actuellement, prend uniquement en charge les disques d'Azure Virtual Machine. Azure Premium Storage est disponible sur demande via la [page de la version préliminaire d'Azure](/fr-fr/services/preview/).

Pour pouvoir créer un compte de stockage, vous devez posséder un abonnement Azure, c'est-à-dire un plan qui vous donne accès à divers services Azure. Un seul abonnement vous permet de créer jusqu'à 100 comptes de stockage uniques. Consultez la page [Tarification - Stockage](http://www.windowsazure.com/fr-fr/pricing/details/storage/) pour en savoir plus sur la tarification selon les volumes.

Pour la prise en main d'Azure, vous pouvez bénéficier d'une [version d'évaluation gratuite](/fr-fr/pricing/free-trial/). Lorsque vous décidez d'acheter un plan, vous avez le choix entre plusieurs [options d'achat](/fr-fr/pricing/purchase-options/). Si vous êtes [abonné à MSDN](/fr-fr/pricing/member-offers/msdn-benefits-details/), vous bénéficiez de crédits mensuels gratuits que vous pouvez utiliser avec les services Azure, y compris Azure Storage.

### Comptes de stockage standard

Un compte de stockage standard vous donne accès au stockage d'objets blob, de tables, de files d'attente et de fichiers :

- Le **stockage d'objets blob** stocke les données des fichiers. Un objet blob peut correspondre à n'importe quel type de données texte ou binaires, par exemple, un document, un fichier multimédia ou un programme d'installation d'application. 
- Le **stockage de tables** stocke les jeux de données structurés. Le stockage de tables est un magasin de données de clés-attributs NoSQL qui permet le développement rapide et l'accès rapide à de grosses quantités de données.
- Le **stockage de files d'attente** fournit une messagerie fiable pour le traitement du workflow et pour la communication entre les composants des services cloud.
- Le **stockage de fichiers (version préliminaire)** propose un stockage partagé pour les applications héritées utilisant le protocole SMB 2.1. Les machines virtuelles et les services cloud Microsoft Azure peuvent partager des données de fichiers entre plusieurs composants d'application grâce à des partages montés. Les applications locales peuvent accéder aux données de fichiers d'un partage via l'API REST du service de stockage de fichiers. Le stockage de fichiers est disponible sur demande via la [page de la version préliminaire d'Azure](/fr-fr/services/preview/). 

Chaque compte de stockage standard peut contenir jusqu'à 500 To de données d'objets blob, de files d'attente, de tables et de fichiers combinées. Consultez la page [Objectifs de performance et d'extensibilité d'Azure Storage](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx) pour en savoir plus sur la capacité d'un compte de stockage standard.

L'image ci-dessous affiche les relations entre les ressources de stockage Azure dans un compte de stockage standard :

![Azure Storage Resources](./media/storage-introduction/storage-concepts.png)

Une fois que vous êtes prêt à créer un compte de stockage standard, consultez la page [Création, gestion ou suppression d'un compte de stockage](../storage-create-storage-account/) pour en savoir plus.

### Comptes de stockage Premium (version préliminaire)

Actuellement, Azure Premium Storage prend uniquement en charge les disques d'Azure Virtual Machines. Azure Premium Storage est disponible sur demande via la [page de la version préliminaire d'Azure](/fr-fr/services/preview/). Pour une présentation détaillée d'Azure Premium Storage, consultez la page [Premium Storage : stockage hautes performances pour les charges de travail d'Azure Virtual Machine](http://go.microsoft.com/fwlink/?LinkId=521898).

## Stockage d'objets blob ##

Pour les utilisateurs qui doivent stocker de grandes quantités de données non structurées dans le cloud, le stockage d'objets blob offre une solution peu coûteuse et évolutive. Vous pouvez utiliser le stockage d'objets blob pour stocker les contenus suivants :

- Documents 
- Données sociales telles que photos, vidéos, musique et blogs
- Sauvegardes de fichiers, d'ordinateurs, de bases de données et d'appareils
- Images et textes pour applications Web
- Données de configuration pour applications cloud
- Big Data tels que journaux et autres jeux de données volumineux

Chaque objet blob est organisé dans un conteneur. Les conteneurs fournissent également un moyen utile d'affecter des stratégies de sécurité à des groupes d'objets. Un compte de stockage peut contenir un nombre quelconque de conteneurs, et un conteneur peut contenir un nombre quelconque d'objets blobs, jusqu'à la limite de capacité de 500 To du compte de stockage.  

Le stockage d'objets blob propose deux types d'objets blob : les objets blob de bloc et les objets blob de page (disques). Les objets blob de bloc sont optimisés pour la diffusion en continu et le stockage des objets cloud. Ils conviennent pour le stockage de documents, de fichiers multimédias, de sauvegardes, etc. La taille maximale d'un objet blob de bloc est de 200 Go. Les objets blob de page sont optimisés pour représenter les disques IaaS et prendre en charge les écritures aléatoires. Leur taille maximale est de 1 To. Un disque IaaS rattaché à un réseau de machines virtuelles Azure est un disque dur virtuel stocké en tant qu'objet blob de page.

Pour les jeux de données très volumineux où les contraintes du réseau rendent irréaliste le téléchargement de données vers/depuis le stockage d'objets blob via le réseau, vous pouvez expédier un disque dur à Microsoft pour importer ou exporter les données directement à partir du centre de données avec le [service Import/Export d'Azure](http://azure.microsoft.com/fr-fr/documentation/articles/storage-import-export-service/). Vous pouvez également copier des données d'objets blob dans votre compte de stockage ou à travers des comptes de stockage. 

## Stockage de tables ##

Les applications modernes exigent souvent des magasins de données avec plus d'évolutivité et de souplesse que ne l'exigeaient les précédentes générations de logiciels. Le stockage de tables offre un stockage hautement disponible et massivement évolutif, de telle sorte que votre application puisse être mise à l'échelle automatiquement pour répondre à la demande des utilisateurs. Le stockage de tables est le magasin de clés/attributs de Microsoft ; il a une conception sans schéma, ce en quoi il diffère des bases de données relationnelles classiques. Avec un magasin de données sans schéma, il est facile d'adapter vos données au fur et à mesure que les besoins de votre application évoluent. Facile à utiliser, le stockage de tables permet aux développeurs de créer rapidement des applications. L'accès aux données est rapide et peu coûteux pour tous les types d'applications.  Normalement, le stockage de tables est considérablement moins coûteux que le SQL traditionnel pour des volumes de données similaires.

Le stockage de tables est un magasin de clés/attributs : cela signifie que chaque valeur dans une table est stockée avec un nom de propriété typé. Le nom de propriété peut être utilisé pour filtrer et spécifier des critères de sélection. Une collection de propriétés et leurs valeurs constituent une entité. Dans la mesure où le stockage de tables est sans schéma, deux entités d'une même table contiennent différentes collections de propriétés, et ces propriétés peuvent être de différents types. 

Vous pouvez utiliser le stockage de tables pour stocker des jeux de données flexibles, par exemple, des données utilisateur pour des applications Web, des carnets d'adresses, des informations sur les périphériques et tout autre type de métadonnées requis par votre service.  Vous pouvez stocker un nombre quelconque d'entités dans une table, et un compte de stockage peut contenir un nombre quelconque de tables, jusqu'à la limite de capacité du compte de stockage.

Les développeurs peuvent gérer le stockage de tables et y accéder comme ils le font pour les objets blob et les files d'attente, à l'aide des protocoles REST standards, mais le stockage de tables prend également en charge un sous-ensemble du protocole OData, qui simplifie les fonctionnalités d'interrogation avancées et autorise les formats (XML) JSON et AtomPub.

Pour les applications Internet actuelles, les bases de données NoSQL telles que le stockage de tables offrent une alternative populaire aux bases de données relationnelles classiques. 

## Stockage de files d'attente ##

Lors de la conception d'applications pour la mise à l'échelle, des composants d'application sont souvent découplés, de sorte qu'ils peuvent être mis à l'échelle indépendamment. Le stockage de files d'attente offre une solution de messagerie fiable pour la communication asynchrone entre les composants d'application, qu'ils soient exécutés dans le cloud, sur le bureau, sur un serveur local ou sur un appareil mobile. Le stockage de files d'attente prend également en charge la gestion des tâches asynchrones et la création des workflows de processus. 

Un compte de stockage peut contenir un nombre quelconque de files d'attente. Une file d'attente peut contenir un nombre quelconque de messages, jusqu'à la limite de capacité du compte de stockage. La taille maximale des messages individuels est de 64 Ko.

## Stockage de fichiers (version préliminaire) ##

De nombreuses applications héritées reposent sur des partages de fichiers, une dépendance qui complique le déplacement de ces applications sur le cloud. Le stockage de fichiers offre des partages de fichiers sur le cloud permettant de migrer des applications héritées vers Azure rapidement, sans réécritures onéreuses. 

Les applications exécutées dans les machines virtuelles ou services cloud Azure peuvent monter un partage de stockage de fichiers pour accéder aux données de fichiers, tout comme une application de bureau monterait un partage SMB classique. Un nombre illimité de composants d'application peuvent montrer un partage de fichiers et y accéder simultanément.

Un partage de stockage de fichiers étant un partage de fichiers SMB 2.1 standard, les applications exécutées dans Azure peuvent accéder aux données du partage grâce à des API d'E/S de système de fichiers. Les développeurs peuvent ainsi tirer profit de leur code et compétences actuels pour migrer les applications existantes. Les professionnels de l'informatique peuvent utiliser des applets de commande PowerShell pour créer, monter et gérer les partages de stockage de fichiers dans le cadre de l'administration des applications Azure.

Comme les autres services de stockage Azure, le stockage de fichiers expose une API REST pour l'accès aux données d'un partage. Les applications locales peuvent appeler l'API REST de stockage de fichiers pour accéder aux données d'un partage de fichiers. Ainsi, une entreprise peut choisir de migrer certaines applications héritées vers Azure tout en continuant à exécuter d'autres applications au sein de leurs locaux. Le montage d'un partage de fichiers est possible uniquement pour les applications exécutées dans Azure. Une application locale ne peut accéder au partage de fichiers que via l'API REST.

Les applications distribuées peuvent également utiliser le stockage de fichiers pour stocker et partager des données d'application, et des outils de développement et de test. Par exemple, une application peut stocker des fichiers de configuration et des données de diagnostic (telles que des journaux, mesures et vidages sur incident) dans un partage de stockage de fichiers afin de les rendre disponibles pour plusieurs machines virtuelles ou rôles. Les développeurs et les administrateurs peuvent stocker des utilitaires dont ils ont besoin pour générer ou générer une application dans un partage de stockage de fichiers disponible pour tous les composants, plutôt que de les installer sur chaque machine virtuelle ou instance de rôle.


## Accès aux ressources d'objets blob, de tables, de files d'attente et de fichiers ##

Par défaut, seul le propriétaire du compte de stockage peut accéder aux ressources dans le compte de stockage. Pour la sécurité de vos données, chaque demande concernant les ressources de votre compte doit être authentifiée. L'authentification s'appuie sur un modèle de clé partagée. Les objets blob peuvent également être configurés pour prendre en charge l'authentification anonyme. 

Deux clés d'accès privées sont attribuées à votre compte de stockage lors de sa création. Elles sont utilisées pour l'authentification. L'existence de deux clés permet de s'assurer que votre application reste disponible lorsque la pratique courante de gestion des clés de sécurité vous amène à régénérer régulièrement les clés.

Si vous devez autoriser un accès contrôlé à vos ressources de stockage pour les utilisateurs, vous pouvez créer une [signature d'accès partagé](../storage-dotnet-shared-access-signature-part-1/). Une signature d'accès partagé est un jeton pouvant être ajouté à une URL qui permet un accès délégué à un conteneur, un objet blob, une table ou une file d'attente. Quiconque possède le jeton peut accéder à la ressource qu'il désigne avec les autorisations qu'il spécifie, pendant son délai de validité. Le stockage de fichiers Azure ne prend pas actuellement en charge les signatures d'accès partagé.

Enfin, vous pouvez spécifier qu'un conteneur et ses objets blob, ou un objet blob particulier, sont disponibles pour l'accès public. Lorsque vous indiquez qu'un conteneur ou un objet blob est public, n'importe qui peut le lire de manière anonyme ; aucune authentification n'est requise.  Les conteneurs et les objets blob publics sont utiles pour exposer des ressources telles que des médias et des documents hébergés sur des sites Web.  Afin de réduire la latence du réseau pour un public mondial, vous pouvez mettre en cache les données d'objets blob utilisées par des sites Web avec le réseau de distribution de contenu (CDN) Azure.

## Réplication pour la durabilité et la haute disponibilité ##

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

## Tarification ##

La facturation d'Azure Storage aux clients se base sur quatre facteurs : la capacité de stockage utilisée, l'option de réplication sélectionnée, le nombre de demandes concernant le service et l'acheminement des données. 

La capacité de stockage fait référence à l'unité de compte de stockage que vous utilisez pour stocker des données. Le coût d'un simple stockage de vos données est déterminé par la quantité de données que vous stockez et leur type de réplication. Chaque opération de lecture et d'écriture vis-à-vis d'Azure Storage effectue également une demande auprès du service. L'acheminement des données fait référence aux données transférées hors d'une région Microsoft Azure. Lorsque les données de votre compte de stockage sont utilisées par une application qui n'est pas exécutée dans la même région, que cette application soit un service cloud ou un autre type d'application, vous êtes facturé pour l'acheminement des données (pour les services Windows Azure, vous pouvez grouper vos données et services dans les mêmes centres de données pour réduire ou éliminer les frais de traitement et d'acheminement des données). 

La page [Tarification - Stockage](/fr-fr/pricing/details/storage/) fournit des informations de tarification détaillées pour les capacités de stockage, la réplication et les transactions. La page [Tarification - Transferts de données](/fr-fr/pricing/details/data-transfers/) fournit des informations de tarification détaillées concernant l'acheminement des données. Vous pouvez utiliser la [calculatrice de la tarification d'Azure Storage.](/fr-fr/pricing/calculator/?scenario=data-management) to help estimate your costs.

## Développement dans Azure Storage ##

Azure Storage expose les ressources de stockage via une [API REST] (http://msdn.microsoft.com/library/windowsazure/dd179355.aspx) qui peut être appelée par n'importe quel langage capable de créer des requêtes HTTP/HTTPS. Par ailleurs, Azure Storage offre des bibliothèques de programmation pour plusieurs langages populaires. Ces bibliothèques simplifient l'utilisation d'Azure Storage sous de nombreux aspects en gérant des détails tels que l'invocation synchrone et asynchrone, le traitement par lots des opérations, la gestion des exceptions, les nouvelles tentatives automatiques, le comportement opérationnel, etc. Des bibliothèques sont actuellement disponibles pour les langages et les plateformes suivants (d'autres sont à l'étude) :

- [.NET](http://go.microsoft.com/fwlink/?LinkID=390731)
- [Code natif](http://msdn.microsoft.com/library/dn495438.aspx)
- [Java/Android](/fr-fr/develop/java/)
- [Node.js](/fr-fr/develop/nodejs/)
- [PHP](/fr-fr/develop/php/)
- [Ruby](/fr-fr/develop/ruby/)
- [Python](/fr-fr/develop/python/)
- [PowerShell](http://msdn.microsoft.com/library/dn495240.aspx)

## Étapes suivantes##

Pour la prise en main d'Azure Storage, explorez les ressources suivantes :

### Téléchargements

- [Package NuGet d'Azure Storage - Bibliothèques clientes pour .NET, Windows Phone et Windows Runtime](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Outils et Kit de développement logiciel (SDK) d'Azure](http://azure.microsoft.com/fr-fr/downloads/)
- [Émulateur de stockage Azure](http://www.microsoft.com/en-in/download/details.aspx?id=43709)

### Code source

- [Bibliothèques Microsoft Azure Storage pour .NET](https://github.com/Azure/azure-storage-net)

### HDInsight

- [Documentation d'Azure Storage](/fr-fr/documentation/services/storage/)
- [Référence de l'API REST des services d'Azure Storage](http://msdn.microsoft.com/fr-fr/library/dd179355.aspx)
- [Référence de l'outil en ligne de commande AzCopy](http://azure.microsoft.com/fr-fr/documentation/articles/storage-use-azcopy/)

<h3>Pour les utilisateurs de PowerShell</h3>
- [Cmdlets Azure Storage](http://msdn.microsoft.com/fr-fr/library/azure/dn806401.aspx)

<h3>Pour les développeurs .NET</h3>

- [Référence de bibliothèque cliente .NET](http://msdn.microsoft.com/fr-fr/library/wa_storage_30_reference_home.aspx)
- [Utilisation du stockage d'objets blob à partir de .NET](../storage-dotnet-how-to-use-blobs/)
- [Utilisation du stockage de tables à partir de .NET](../storage-dotnet-how-to-use-tables/)
- [Utilisation du service de stockage de files d'attente à partir de .NET](../storage-dotnet-how-to-use-queues/)

<h3>Pour les développeurs Java/Android</h3>

- [Référence de bibliothèque cliente JAVA]()
- [Utilisation du stockage d'objets blob à partir de Java/Android](../storage-java-how-to-use-blob-storage/)
- [Utilisation du stockage de tables à partir de Java/Android](../storage-java-how-to-use-table-storage/)
- [Utilisation du stockage de files d'attente à partir de Java/Android](../storage-java-how-to-use-queue-storage/)

<h3>Pour les développeurs Node.js</h3>

- [Utilisation du stockage d'objets blob à partir de Node.js](../storage-nodejs-how-to-use-blob-storage/)
- [Utilisation du stockage de tables à partir de Node.js](../storage-nodejs-how-to-use-table-storage/)
- [Utilisation du stockage de files d'attente à partir de Node.js](../storage-nodejs-how-to-use-queues/)

<h3>Pour les développeurs PHP</h3>

- [Utilisation du stockage d'objets blob à partir de PHP](../storage-php-how-to-use-blobs/)
- [Utilisation du stockage de tables à partir de PHP](../storage-php-how-to-use-table-storage/)
- [Utilisation du stockage de files d'attente à partir de PHP](../storage-php-how-to-use-queues/)

<h3>Pour les développeurs Ruby</h3>

- [Utilisation du stockage d'objets blob à partir de Ruby](../storage-ruby-how-to-use-blob-storage/)
- [Utilisation du stockage de tables à partir de Ruby](../storage-ruby-how-to-use-table-storage/)
- [Utilisation du stockage de files d'attente à partir de Ruby](../storage-ruby-how-to-use-queue-storage/)

<h3>Pour les développeurs Python</h3>

- [Utilisation du stockage d'objets blob à partir de Python](../storage-python-how-to-use-blob-storage/)
- [Utilisation du stockage de tables à partir de Python](../storage-python-how-to-use-table-storage/)
- [Utilisation du stockage de files d'attente à partir de Python](../storage-python-how-to-use-queue-storage/)

<!--HONumber=35_1-->
