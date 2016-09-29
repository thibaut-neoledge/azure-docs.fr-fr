<properties
   pageTitle="Comparaison d’Azure Data Lake Store avec Azure Storage Blob | Microsoft Azure"
   description="Comparaison d’Azure Data Lake Store avec Azure Storage Blob"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/15/2016"
   ms.author="nitinme"/>

# Comparaison d’Azure Data Lake Store et d’Azure Blob Storage

Le tableau de cet article résume les différences entre Azure Data Lake Store et Azure Blob Storage pour certains aspects essentiels du traitement des données volumineuses. Azure Blob Storage est un magasin d’objets extensible généraliste conçu pour s’adapter à une large gamme de scénarios de stockage. Azure Data Lake Store est un référentiel hyperscale optimisé pour les charges de travail d’analyse des données volumineuses.

| | Azure Data Lake Store | un stockage Azure Blob |
|----|-----------------------|--------------------|
| Objectif | Stockage optimisé pour les charges de travail d’analyse de données volumineuses | Magasin d’objets polyvalent adapté à un large éventail de scénarios de stockage |
| Cas d'utilisation | Données par lots, interactives, d’analyse de diffusion en continu et d’apprentissage machine (par exemple, fichiers journaux, données IoT, données sur le parcours de navigation, jeux de données volumineux) | N’importe quel type de données texte ou binaires, par exemple données d’application principale, de sauvegarde, de stockage de médias pour la diffusion en continu, et d’usage général |
| Concepts clés | Le compte Data Lake Store contient des dossiers, qui contiennent des données stockées sous forme de fichiers | Le compte de stockage a des conteneurs, qui possèdent des données sous la forme d’objets BLOB |
| Structure | Système de fichiers hiérarchique | Magasin d’objets avec espace de noms plat |
| API | API REST sur HTTPS | API REST sur HTTP/HTTPS |
| API côté serveur | [API REST compatible WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) | [API REST d’Azure Blob Storage](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Client de système de fichiers Hadoop | Oui | Oui |
| Opérations de données - authentification | Basées sur les [Identités Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md) | Basées sur les secrets partagés - [Clés d’accès au compte](../storage/storage-create-storage-account.md#manage-your-storage-account) et [Clés de signature d’accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md). |
| Opérations de données - protocole d’authentification | OAuth 2.0. Les appels doivent contenir un JWT (jeton web JSON) valide émis par Azure Active Directory | Code d’authentification de message basé sur hachage (HMAC). Les appels doivent contenir un hachage SHA-256 codé en Base64 sur une partie de la requête HTTP. |
| Opérations de données - autorisation | Listes de contrôle d’accès (ACL) POSIX. Les ACL basées sur les identités Active Directory Azure peuvent être définies aux niveaux fichier et dossier. | Pour l’autorisation au niveau des comptes – Utiliser les [clés d’accès au compte](../storage/storage-create-storage-account.md#manage-your-storage-account)<br>Pour l’autorisation de compte, de conteneur ou d’objet blob - Utiliser les [clés de signature d’accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md) |
| Opérations de données - Audit | Disponible. Pour plus d’informations, voir [ici](data-lake-store-diagnostic-logs.md). | Disponible |
| Chiffrement des données au repos | Transparent, côté serveur (prochainement)<ul><li>Avec des clés gérées par le service</li><li>Avec des clés gérées par le client dans Azure Key Vault</li></ul>| <ul><li>Transparent, côté serveur</li> <ul><li>Avec des clés gérées par le service</li><li>Avec des clés gérées par le client dans Azure KeyVault (disponible prochainement)</li></ul><li>Chiffrement côté client</li></ul> |
| Opérations de gestion (par exemple, créer un compte) | [Le contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-what-is.md) (RBAC) fournit par Azure pour la gestion des comptes ; | [Le contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-what-is.md) (RBAC) fournit par Azure pour la gestion des comptes ; |
| Kits de développement logiciel pour développeur | .NET, Java, Node.js | .NET, Java, Python, Node.js, C++, Ruby |
| Performances des charges de travail d’analyse | Optimisation des performances pour les charges de travail d’analyses parallèles. Débit et nombre d’E/S par seconde élevés. | Non optimisé pour les charges de travail d’analyse |
| Limites de taille | Aucune limite de taille pour les comptes, les fichiers ou le nombre de fichiers | Limites spécifiques documentées [ici](../azure-subscription-service-limits.md#storage-limits) |
| Géo-redondance | Localement redondant (plusieurs copies des données dans une région Azure) | Localement redondant (LRS), globalement redondant (GRS), accès en lecture redondant globalement (RA-GRS). Pour plus d’informations, voir [ici](../storage/storage-redundancy.md) |
| État du service | Version préliminaire publique | Mise à la disposition générale |
| Disponibilité régionale | Voir [ici](https://azure.microsoft.com/regions/#services)| Voir [ici](https://azure.microsoft.com/regions/#services) |
| Prix | Consultez la page [Tarification](https://azure.microsoft.com/pricing/details/data-lake-store/)| Consultez la page [Tarification](https://azure.microsoft.com/pricing/details/storage/) |

### Étapes suivantes

- [Présentation d’Azure Data Lake Store](data-lake-store-overview.md)
- [Prise en main de Data Lake Store](data-lake-store-get-started-portal.md)

<!---HONumber=AcomDC_0914_2016-->