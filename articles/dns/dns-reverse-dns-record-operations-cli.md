<properties 
   pageTitle="Gestion des enregistrements DNS inversés pour vos services à l’aide de l’interface de ligne de commande Azure dans Resource Manager | Microsoft Azure"
   description="Gestion des enregistrements DNS inversés ou des enregistrements PTR pour les services Azure à l’aide de l’interface de ligne de commande Azure dans Resource Manager"
   services="DNS"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/22/2016"
   ms.author="joaoma" />

# Gestion des enregistrements DNS inversés pour vos services à l’aide de l’interface de ligne de commande Azure

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]<BR>[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](dns-reverse-dns-record-operations-classic-ps.md).


## Ajout d’un enregistrement DNS inversé à une adresse IP publique existante
Vous pouvez ajouter un enregistrement DNS inversé à une adresse IP publique existante avec la commande azure network public-ip set.

	azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

## Création d’une adresse IP publique avec un enregistrement DNS inversé
Vous pouvez ajouter une nouvelle adresse IP publique avec la propriété DNS inversée spécifiée avec la commande azure network public-ip create.

	azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com. "
 
## Affichage d’un enregistrement DNS inversé pour une adresse IP publique existante
Vous pouvez afficher la valeur configurée pour une adresse IP publique existante avec la commande azure network public-ip show.

	azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS 

## Suppression d’un enregistrement DNS inversé d’une adresse IP publique existante
Vous pouvez supprimer un enregistrement DNS inversé d’une adresse IP publique existante avec la commande azure network public-ip set. Pour ce faire, définissez la valeur de la propriété ReverseFqdn sur une valeur vide.

	azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “” 

## Validation des enregistrement DNS inversés 
Pour vous assurer qu’un tiers ne soit pas en mesure de créer des mappages d’enregistrements DNS inversés vers vos domaines DNS, Azure autorise uniquement la création d’un enregistrement DNS inversé lorsque l’une des conditions suivantes est vraie :

- La valeur « ReverseFqdn » est identique à « Fqdn » pour la ressource d’adresse IP publique pour laquelle elle a été spécifiée, ou la valeur « Fqdn » pour n’importe quelle adresse IP publique dans le même abonnement, par exemple, « ReverseFqdn » correspond à « contosoapp1.northus.cloudapp.azure.com. ».

- La valeur « ReverseFqdn » explicite correspond au nom ou à l’adresse IP de l’adresse IP publique pour laquelle elle a été spécifiée, ou la valeur « Fqdn » ou adresse IP de n’importe quelle adresse IP publique dans le même abonnement, par exemple, « ReverseFqdn » correspond à « app1.contoso.com. » qui est un alias CName pour « contosoapp1.northus.cloudapp.azure.com. » Les contrôles de validation sont exécutés uniquement lorsque la propriété DNS inversée pour une adresse IP publique est définie ou modifiée. Aucune nouvelle validation périodique n’est effectuée.

[AZURE.INCLUDE [FORUM AUX QUESTIONS](../../includes/dns-reverse-dns-record-operations-faq-include.md)]

<!---------HONumber=AcomDC_0309_2016-->