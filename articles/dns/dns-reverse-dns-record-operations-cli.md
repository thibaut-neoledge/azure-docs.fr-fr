---
title: "Gérer les enregistrements DNS inversés pour vos services Azure à l’aide de l’interface CLI Azure | Microsoft Docs"
description: "Gestion des enregistrements DNS inversés ou des enregistrements PTR pour les services Azure à l’aide de l’interface de ligne de commande Azure dans Resource Manager"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c655707e-1156-4893-b163-0b228ffd25d2
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: 0f8bc125855bc5a5b67fde5b0b742c73b5da7610
ms.openlocfilehash: b7c91d7c13884a00bb06a7cfed55a59b357f7d18


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli"></a>Guide pratique pour gérer les enregistrements DNS inversés pour vos services Azure à l’aide de l’interface CLI Azure

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [dns-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

Pour plus d’informations sur le modèle de déploiement classique, consultez [Guide pratique pour gérer les enregistrements DNS inversés pour vos services Azure (classique) à l’aide d’Azure PowerShell](dns-reverse-dns-record-operations-classic-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validation des enregistrement DNS inversés
Pour vous assurer qu’un tiers ne soit pas en mesure de créer des mappages d’enregistrements DNS inversés vers vos domaines DNS, Azure autorise uniquement la création d’un enregistrement DNS inversé lorsque l’une des conditions suivantes est vraie :

* La valeur « ReverseFqdn » est identique à « Fqdn » pour la ressource d’adresse IP publique pour laquelle elle a été spécifiée, ou la valeur « Fqdn » pour n’importe quelle adresse IP publique dans le même abonnement, par exemple, « ReverseFqdn » correspond à « contosoapp1.northus.cloudapp.azure.com. ».
* La valeur « ReverseFqdn » explicite correspond au nom ou à l’adresse IP de l’adresse IP publique pour laquelle elle a été spécifiée, ou la valeur « Fqdn » ou adresse IP de n’importe quelle adresse IP publique dans le même abonnement, par exemple, « ReverseFqdn » correspond à « app1.contoso.com. » qui est un alias CName pour « contosoapp1.northus.cloudapp.azure.com ».

Les contrôles de validation sont exécutés uniquement lorsque la propriété DNS inversée pour une adresse IP publique est définie ou modifiée. Aucune nouvelle validation périodique n’est effectuée.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Ajout d’un DNS inversé à des adresses IP publiques existantes
Vous pouvez ajouter un DNS inversé à une adresse IP publique existante avec la commande azure network public-ip set :

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

Si vous souhaitez ajouter un DNS inversé à une adresse IP publique existante qui ne comporte pas encore de nom DNS, vous devez également spécifier un nom DNS. Vous pouvez effectuer cet ajout à l’aide de la commande azure network public-ip set :

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Création d’une adresse IP publique avec un DNS inversé
Vous pouvez ajouter une nouvelle adresse IP publique avec la propriété DNS inversée spécifiée avec la commande azure network public-ip create :

    azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Affichage d’un DNS inversé pour des adresses IP publiques existantes
Vous pouvez afficher la valeur configurée pour une adresse IP publique existante avec la commande azure network public-ip show :

    azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Suppression d’un enregistrement DNS inversé d’une adresse IP publique existante
Vous pouvez supprimer un DNS inversé d’une adresse IP publique existante avec la commande azure network public-ip set. Pour ce faire, définissez la valeur de la propriété ReverseFqdn sur une valeur vide :

    azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “”

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]




<!--HONumber=Nov16_HO3-->


