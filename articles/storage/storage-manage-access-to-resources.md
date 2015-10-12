<properties 
	pageTitle="Gestion de l’accès anonyme aux conteneurs et aux objets blob | Microsoft Azure" 
	description="Découvrez comment rendre des conteneurs et des objets disponibles via un accès anonyme." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="jdial" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/28/2015" 
	ms.author="micurd;tamram"/>

# Gestion de l’accès aux ressources d’Azure Storage

## Vue d'ensemble

Par défaut, seul le propriétaire du compte de stockage peut accéder aux ressources de stockage de ce compte. Si votre service ou application doit mettre ces ressources à disposition d’autres clients sans partager votre clé d’accès, vous disposez des options suivantes pour autoriser l’accès :

- Vous pouvez définir les autorisations d’un conteneur afin de permettre un accès en lecture anonyme au conteneur et à ses objets blob. L’accès en lecture anonyme n’est disponible que pour les conteneurs et objets blob. 

- Vous pouvez exposer une ressource via une signature d’accès partagé, ce qui vous permet de déléguer un accès limité à un conteneur, un objet blob, une table, une file d’attente, un partage de fichiers ou un fichier, en spécifiant l’intervalle pendant lequel les ressources seront disponibles et les autorisations dont bénéficiera le client.

- Vous pouvez utiliser une stratégie d’accès stockée pour gérer les signatures d’accès partagé d’un conteneur ou de ses objets blob, d’une file d’attente, d’une table ou d’un partage de fichiers et de ses fichiers. La stratégie d’accès stockée vous donne un contrôle supplémentaire sur vos signatures d’accès partagé et permet également de les révoquer simplement.

## Accorder à des utilisateurs anonymes des autorisations d’accès aux conteneurs et objets blob

Par défaut, un conteneur et tous les objets blob qu'il contient sont accessibles uniquement par le propriétaire du compte de stockage. Pour fournir aux utilisateurs anonymes des autorisations de lecture sur un conteneur et ses objets blob, vous pouvez configurer les autorisations du conteneur afin d’autoriser l’accès public. Les utilisateurs anonymes peuvent lire les objets blob d’un conteneur accessible publiquement sans avoir à authentifier la demande.

Les conteneurs fournissent les options suivantes pour gérer leur accès :

- **Accès public en lecture** : les données de conteneur et d’objet blob peuvent être lues via une demande anonyme. Les clients peuvent énumérer les objets blob à l’intérieur du conteneur via une demande anonyme, mais ne peuvent pas énumérer les conteneurs dans le compte de stockage.

- **Accès public en lecture pour les objets blob uniquement :** les données d’objets blob à l’intérieur de ce conteneur peuvent être lues via une demande anonyme, mais les données du conteneur ne sont pas disponibles. Les clients ne peuvent pas énumérer les objets blob à l’intérieur du conteneur via une demande anonyme.

- **Pas d’accès public en lecture :** les données de conteneur et d’objet blob ne peuvent être lues que par le propriétaire du compte.

>[AZURE.NOTE]Si votre service requiert un contrôle plus précis sur les ressources d'objet blob ou si vous souhaitez accorder des autorisations pour des opérations autres que les opérations de lecture, vous pouvez utiliser une signature d'accès partagé pour que la ressource soit accessible aux utilisateurs.

## Fonctionnalités accessibles aux utilisateurs anonymes

Le tableau suivant indique les opérations pouvant être appelées par les utilisateurs anonymes quand la liste de contrôle d’accès (ACL, Access Control List) est définie pour autoriser l’accès public.

| Opération REST | Autorisation avec accès en lecture public complet | Autorisation avec accès en lecture public pour les objets blob uniquement |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| List Containers | Propriétaire uniquement | Propriétaire uniquement |
| Create Container | Propriétaire uniquement | Propriétaire uniquement |
| Get Container Properties | Tout | Propriétaire uniquement |
| Get Container Metadata | Tout | Propriétaire uniquement |
| Set Container Metadata | Propriétaire uniquement | Propriétaire uniquement |
| Get Container ACL | Propriétaire uniquement | Propriétaire uniquement |
| Set Container ACL | Propriétaire uniquement | Propriétaire uniquement |
| Delete Container | Propriétaire uniquement | Propriétaire uniquement |
| List Blobs | Tout | Propriétaire uniquement |
| Put Blob | Propriétaire uniquement | Propriétaire uniquement |
| Get Blob | Tout | Tout |
| Get Blob Properties | Tout | Tout |
| Set Blob Properties | Propriétaire uniquement | Propriétaire uniquement |
| Get Blob Metadata | Tout | Tout |
| Set Blob Metadata | Propriétaire uniquement | Propriétaire uniquement |
| Put Block | Propriétaire uniquement | Propriétaire uniquement |
| Get Block List (blocs validés uniquement) | Tout | Tout |
| Get Block List (blocs non validés uniquement ou tous les blocs) | Propriétaire uniquement | Propriétaire uniquement |
| Put Block List | Propriétaire uniquement | Propriétaire uniquement |
| Delete Blob | Propriétaire uniquement | Propriétaire uniquement |
| Copie d'un objet blob | Propriétaire uniquement | Propriétaire uniquement |
| Snapshot Blob | Propriétaire uniquement | Propriétaire uniquement |
| Lease Blob | Propriétaire uniquement | Propriétaire uniquement |
| Put Page | Propriétaire uniquement | Propriétaire uniquement |
| Get Page Ranges | Tout | Tout |


## Voir aussi

- [Authentification pour les services de stockage Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Signatures d'accès partagé : présentation du modèle SAP](storage-dotnet-shared-access-signature-part-1.md)
- [Délégation de l'accès avec une signature d'accès partagé](https://msdn.microsoft.com/library/azure/ee395415.aspx) 

<!---HONumber=Oct15_HO1-->