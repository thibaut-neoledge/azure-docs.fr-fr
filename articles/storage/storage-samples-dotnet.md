---
title: Exemples de stockage Azure avec .NET| Microsoft Docs
description: "Affichez, téléchargez et exécutez des exemples de code et des applications pour Azure Storage. Découvrez la mise en route des exemples d’objets blob, de files d’attente, de tables et de fichiers qui utilisent des bibliothèques clientes de stockage .NET."
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 01/12/2017
ms.author: seguler
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d2b6b3d9483f230ad25ae47255a4f28c1a67e064
ms.lasthandoff: 03/29/2017


---
# <a name="azure-storage-samples-using-net"></a>Exemples de stockage Azure avec .NET

## <a name="net-sample-index"></a>Index des exemples .NET

Le tableau suivant fournit une vue d’ensemble de notre référentiel d’exemples et des scénarios traités dans chaque exemple. Cliquez sur les liens pour afficher l’exemple de code correspondant dans GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Point de terminaison</th><th style="font-size:110%">Scénario</th><th style="font-size:110%">Exemple de code</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Objet blob</b></td>
<td>Append Blob</td> 
<td><a href="https://msdn.microsoft.com/en-us/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.getappendblobreference.aspx">Exemple de méthode CloudBlobContainer.GetAppendBlobReference</a></td> 
</tr> 
<tr> 
<td>Objet blob de blocs</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Application web de la galerie photos d’Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Chiffrement côté client</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Exemples de chiffrement d’objets blob</a></td>
</tr> 
<tr> 
<td>Copie d'un objet blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Prise en main des objets blob</a></td>
</tr> 
<tr> 
<td>Create Container</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Application web de la galerie photos d’Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Delete Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Application web de la galerie photos d’Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Delete Container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Prise en main des objets blob</a></td>
</tr> 
<tr> 
<td>Métadonnées/propriétés/statistiques des objets blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Prise en main des objets blob</a></td>
</tr> 
<tr> 
<td>ACL/métadonnées/propriétés du conteneur</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Application web de la galerie photos d’Azure Blob Storage</a></td>
</tr> 
<tr> 
<td>Get Page Ranges</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Prise en main des objets blob</a></td>
</tr> 
<tr> 
<td>Objet blob/conteneur de bail</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Prise en main des objets blob</a></td>
</tr> 
<tr> 
<td>Objet blob/conteneur de liste</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Prise en main des objets blob</a></td>
</tr> 
<tr> 
<td>Objet blob de pages</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Prise en main des objets blob</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Prise en main des objets blob</a></td>
</tr>     
<tr> 
<td>Propriétés du service</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Prise en main des objets blob</a></td>
</tr>             
<tr> 
<td>Snapshot Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Sauvegarder des disques de machine virtuelle Azure avec des instantanés incrémentiels</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>File</b></td>
<td>Créer des partages/répertoires/fichiers</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Exemple de stockage de fichier .NET Stockage Azure</a></td> 
</tr>
<tr> 
<td>Créer des partages/répertoires/fichiers</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Prise en main du service de fichiers Azure dans .NET</a></td> 
</tr> 
<tr> 
<td>Propriétés/métadonnées du répertoire</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemple de stockage de fichier .NET Stockage Azure</a></td> 
</tr> 
<tr> 
<td>Télécharger des fichiers</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Exemple de stockage de fichier .NET Stockage Azure</a></td> 
</tr> 
<tr> 
<td>Propriétés/métadonnées/mesures de fichier</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemple de stockage de fichier .NET Stockage Azure</a></td> 
</tr> 
<tr> 
<td>Propriétés du service de fichiers</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemple de stockage de fichier .NET Stockage Azure</a></td> 
</tr> 
<tr> 
<td>Liste des répertoires et des fichiers</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Exemple de stockage de fichier .NET Stockage Azure</a></td> 
</tr>
<tr> 
<td>Partages de listes</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemple de stockage de fichier .NET Stockage Azure</a></td> 
</tr>
<tr> 
<td>Propriétés/métadonnées/statistiques de partage</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Exemple de stockage de fichier .NET Stockage Azure</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>File d'attente</b></td>
<td>Ajouter un message</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Prise en main du service de File d’attente Azure dans .NET</a></td> 
</tr> 
<tr> 
<td>Chiffrement côté client</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Chiffrement côté client de la file d’attente .NET Stockage Azure</a></td> 
</tr> 
<tr> 
<td>Créer des files d’attente</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Prise en main du service de File d’attente Azure dans .NET</a></td> 
</tr> 
<tr> 
<td>Supprimer le message/la file d’attente</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Prise en main du service de File d’attente Azure dans .NET</a></td> 
</tr> 
<tr> 
<td>Lire furtivement un message</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Prise en main du service de File d’attente Azure dans .NET</a></td> 
</tr> 
<tr> 
<td>ACL/métadonnées/statistiques de la file d’attente</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Prise en main du service de File d’attente Azure dans .NET</a></td> 
</tr> 
<tr> 
<td>Propriétés du service de File d’attente</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Prise en main du service de File d’attente Azure dans .NET</a></td> 
</tr> 
<tr> 
<td>Mise à jour de message</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Prise en main du service de File d’attente Azure dans .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Table</b></td>
<td>Créer une table</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gestion de l’accès concurrentiel avec Azure Storage - Exemple d’application</a></td> 
</tr> 
<tr> 
<td>Supprimer l’entité/la table</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Getting Started with Azure Table Storage in .NET (Prise en main de Stockage Table Azure dans .NET)</a></td> 
</tr> 
<tr> 
<td>Insertion/fusion/remplacement d’entité</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gestion de l’accès concurrentiel avec Azure Storage - Exemple d’application</a></td> 
</tr> 
<tr> 
<td>Query Entities</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Getting Started with Azure Table Storage in .NET (Prise en main de Stockage Table Azure dans .NET)</a></td> 
</tr> 
<tr> 
<td>Tables de requête</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Getting Started with Azure Table Storage in .NET (Prise en main de Stockage Table Azure dans .NET)</a></td> 
</tr> 
<tr> 
<td>ACL/propriétés de la table</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Getting Started with Azure Table Storage in .NET (Prise en main de Stockage Table Azure dans .NET)</a></td> 
</tr> 
<tr> 
<td>Update Entity</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Gestion de l’accès concurrentiel avec Azure Storage - Exemple d’application</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Bibliothèque d’exemples de code Azure

Pour voir la bibliothèque complète des exemples, consultez la bibliothèque d’[exemples de code Azure](https://azure.microsoft.com/resources/samples/?service=storage) pour trouver des exemples de stockage Azure que vous pouvez télécharger et exécuter localement. Les exemples de code fournis par la bibliothèque sont au format .zip. Vous pouvez également parcourir et cloner le dépôt GitHub pour chaque exemple.

[!INCLUDE [storage-dotnet-samples-include](../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Guides de prise en main

Consultez les guides suivants si vous recherchez des instructions sur l’installation et la prise en main des bibliothèques clientes de stockage Azure.

* [Prise en main du service BLOB Azure dans .NET](storage-dotnet-how-to-use-blobs.md)
* [Prise en main du service de File d’attente Azure dans .NET](storage-dotnet-how-to-use-queues.md)
* [Prise en main du service de Table Azure dans .NET](storage-dotnet-how-to-use-tables.md)
* [Prise en main du service de fichiers Azure dans .NET](storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les exemples pour d’autres langages :

* Java : [exemples de stockage Azure avec Java](storage-samples-java.md)
* Tous les autres langages : [exemples de stockage Azure](storage-samples.md)

