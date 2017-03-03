---
title: "Inverser les enregistrements DNS pour vos services Azure classiques à l’aide de PowerShell | Microsoft Docs"
description: "Azure DNS vous autorise à gérer des enregistrements DNS inversés ou des enregistrements PTR pour vos services Azure à l’aide de PowerShell dans le modèle de déploiement classique."
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9c24d176-6bce-4277-a14c-80fe44a20a87
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: 399cfcad5c17cdb12a4063e11e1ff353e88e56a7
ms.lasthandoff: 02/10/2017


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>Guide pratique pour gérer les enregistrements DNS inversés pour vos services Azure (classiques) à l’aide d’Azure PowerShell

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Découvrez comment [effectuer ces étapes à l’aide du modèle Resource Manager](dns-reverse-dns-record-operations-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validation des enregistrement DNS inversés
Pour vous assurer qu’un tiers ne soit pas en mesure de créer des mappages d’enregistrements DNS inversés vers vos domaines DNS, Azure autorise uniquement la création d’un enregistrement DNS inversé lorsque l’une des conditions suivantes est vraie :

* Le nom de domaine complet du DNS inversé est le nom du service cloud pour lequel il a été spécifié, ou n’importe quel nom de service cloud dans le même abonnement, par exemple, le DNS inversé est « contosoapp1.cloudapp.net. ».
* Le nom de domaine complet du DNS inversé explicite correspond au nom ou à l’adresse IP du service cloud pour lequel il a été spécifié, ou n’importe quel nom ou adresse IP de service cloud dans le même abonnement, par exemple, le DNS inversé est « app1.cloudapp.net ». », qui est un alias CName pour contosoapp1.cloudapp.net.

Les contrôles de validation sont exécutés uniquement lorsque la propriété DNS inversée pour un service cloud est définie ou modifiée. Aucune nouvelle validation périodique n’est effectuée.

## <a name="add-reverse-dns-to-existing-cloud-services"></a>Ajout d’un DNS inversé aux services cloud existants
Vous pouvez ajouter un DNS inversé à un service cloud existant à l’aide de l’applet de commande « Set-AzureService » :

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

## <a name="create-a-cloud-service-with-reverse-dns"></a>Création d’un service cloud avec un DNS inversé
Vous pouvez ajouter un nouveau service cloud avec la propriété DNS inversée spécifiée à l’aide de l’applet de commande « Set-AzureService » :

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

## <a name="view-reverse-dns-for-existing-cloud-services"></a>Affichage d’un DNS inversé pour les services cloud existants
Vous pouvez afficher la valeur configurée pour un service cloud existant à l’aide de l’applet de commande « Get-AzureService » :

```powershell
Get-AzureService "contosoapp1"
```

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>Suppression d’un DNS inversé des services cloud existants
Vous pouvez supprimer une propriété DNS inversée d’un service cloud existant à l’aide de l’applet de commande « Set-AzureService ». Pour ce faire, définissez la valeur de la propriété DNS inversée sur une valeur vide :

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]


