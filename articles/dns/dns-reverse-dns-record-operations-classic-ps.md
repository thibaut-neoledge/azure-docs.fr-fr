<properties
   pageTitle="Gestion des enregistrements DNS inversés pour vos services à l’aide de PowerShell dans le modèle de déploiement classique | Microsoft Azure"
   description="Gestion des enregistrements DNS inversés ou des enregistrements PTR pour vos services Azure à l’aide de PowerShell dans le modèle de déploiement classique. "
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/05/2016"
   ms.author="smalone" />

# Gestion des enregistrements DNS inversés pour vos services (classiques) à l’aide de PowerShell

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)] <BR> [AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)] <BR> [AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] Découvrez comment [effectuer ces étapes à l’aide du modèle Resource Manager](dns-reverse-dns-record-operations-ps.md).

## Validation des enregistrement DNS inversés
Pour vous assurer qu’un tiers ne soit pas en mesure de créer des mappages d’enregistrements DNS inversés vers vos domaines DNS, Azure autorise uniquement la création d’un enregistrement DNS inversé lorsque l’une des conditions suivantes est vraie :

- Le nom de domaine complet du DNS inversé est le nom du service cloud pour lequel il a été spécifié, ou n’importe quel nom de service cloud dans le même abonnement, par exemple, le DNS inversé est « contosoapp1.cloudapp.net. ».
- Le nom de domaine complet du DNS inversé explicite correspond au nom ou à l’adresse IP du service cloud pour lequel il a été spécifié, ou n’importe quel nom ou adresse IP de service cloud dans le même abonnement, par exemple, le DNS inversé est « app1.cloudapp.net. », qui est un alias CName pour contosoapp1.cloudapp.net.

Les contrôles de validation sont exécutés uniquement lorsque la propriété DNS inversée pour un service cloud est définie ou modifiée. Aucune nouvelle validation périodique n’est effectuée.

## Ajout d’un DNS inversé aux services cloud existants
Vous pouvez ajouter un DNS inversé à un service cloud existant à l’aide de l’applet de commande « Set-AzureService » :

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## Création d’un service cloud avec un DNS inversé
Vous pouvez ajouter un nouveau service cloud avec la propriété DNS inversée spécifiée à l’aide de l’applet de commande « Set-AzureService » :

	PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## Affichage d’un DNS inversé pour les services cloud existants
Vous pouvez afficher la valeur configurée pour un service cloud existant à l’aide de l’applet de commande « Get-AzureService » :

	PS C:\> Get-AzureService "contosoapp1"

## Suppression d’un DNS inversé des services cloud existants
Vous pouvez supprimer une propriété DNS inversée d’un service cloud existant à l’aide de l’applet de commande « Set-AzureService ». Pour ce faire, définissez la valeur de la propriété DNS inversée sur une valeur vide :

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [FORUM AUX QUESTIONS](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]

<!---HONumber=AcomDC_0907_2016-->