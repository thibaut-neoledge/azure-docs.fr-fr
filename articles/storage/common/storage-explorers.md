---
title: "Outils pour l’utilisation du stockage Azure | Microsoft Docs"
description: "Liste des outils qui vous permettent d’afficher/d’interagir avec vos données Azure Storage."
services: storage
documentationcenter: 
author: dineshmurthy
manager: jahogg
editor: tysonn
ms.assetid: e4748642-98c4-437e-b0ed-4f9641c2e894
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: dineshmurthy
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: c57a300b54c62bbd43c73121c22adbc44cb94776
ms.contentlocale: fr-fr
ms.lasthandoff: 09/07/2017

---
# <a name="azure-storage-client-tools"></a>Outils clients d’Azure Storage
Souvent, les utilisateurs d’Azure Storage veulent être en mesure d’afficher/d’interagir avec leurs données à l’aide d’un outil client d’Azure Storage. Dans les tableaux ci-dessous, nous répertorions un certain nombre d’outils qui vous permettent de le faire. Nous avons placé un « X » dans chaque bloc si l’outil offre la possibilité d’énumérer et/ou d’accéder à l’abstraction des données. Le tableau indique également si l’outil est gratuit ou non. La mention « Version d’évaluation » indique qu’il existe une version d’évaluation gratuite, mais que le produit complet n’est pas gratuit. La mention « O/N » indique qu’il existe une version disponible gratuitement, ainsi qu’une version différente disponible à l’achat.

Ceci n’est qu’un aperçu des outils clients Azure Storage disponibles. Ces outils peuvent continuer à évoluer, ainsi que leurs fonctionnalités. S’il existe des corrections ou des mises à jour, veuillez laisser un commentaire pour nous le faire savoir. Il en va de même si vous connaissez des outils qui devraient apparaître dans cette liste : nous serions heureux de les ajouter.

**Outils clients de Microsoft Azure Storage**

<table>
  <tr>
    <th rowspan="2">Outil client d’Azure Storage</th>
    <th rowspan="2">Objet blob de blocs</th>
    <th rowspan="2">Objet blob de pages</th>
    <th rowspan="2">Append Blob</th>
    <th rowspan="2">Tables</th>
    <th rowspan="2">Files d’attente</th>
    <th rowspan="2">Fichiers</th>
    <th rowspan="2">Gratuit</th>
    <th colspan="4">Plateforme</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Portail Microsoft Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>O</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://storageexplorer.com/">Explorateur Stockage Microsoft Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>O</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Explorateur de serveurs Microsoft Visual Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>O</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**Outils clients tiers d’Azure Storage**

Nous n’avons pas vérifié les fonctionnalités ni la qualité des outils tiers suivants et leur inclusion dans la liste n’implique aucune garantie de la part de Microsoft.

<table>
  <tr>
    <th rowspan="2">Outil client d’Azure Storage</th>
    <th rowspan="2">Objet blob de blocs</th>
    <th rowspan="2">Objet blob de pages</th>
    <th rowspan="2">Append Blob</th>
    <th rowspan="2">Tables</th>
    <th rowspan="2">Files d’attente</th>
    <th rowspan="2">Fichiers</th>
    <th rowspan="2">Gratuit</th>
    <th colspan="4">Plateforme</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="http://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata : Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Version d’évaluation</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.cerebrata.com/products/azure-explorer/introduction">Cerabrata : Azure Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>O</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Explorateur de stockage web Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>O</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.cloudberrylab.com/explorer/microsoft-azure.aspx">CloudBerry Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>O/N</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gapotchenko.com/cloudcombine">Cloud Combine</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Version d’évaluation</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://clumsyleaf.com">ClumsyLeaf : AzureXplorer, CloudXplorer, TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>O</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gladinet.com/Azure-Storage/index.htm">Gladinet Cloud</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>Version d’évaluation</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

