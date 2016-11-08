---
title: Forum aux questions sur Cloud Services | Microsoft Docs
description: Forum aux questions (FAQ) sur Cloud Services.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''

ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: adegeo

---
# Forum aux questions sur Cloud Services
Cet article répond à certaines questions fréquemment posées sur Microsoft Azure Cloud Services. Vous pouvez également visiter le [FAQ du support Azure](http://go.microsoft.com/fwlink/?LinkID=185083) pour obtenir des informations de support et de tarification générale Azure. Vous pouvez également consulter la page [Taille de services cloud](cloud-services-sizes-specs.md) pour obtenir des informations sur la taille.

## Certificats
### Où dois-je installer mon certificat ?
* **My** Certificat d’application avec une clé privée (*.pfx, *.p12).
* **CA** Tous vos certificats intermédiaires sont stockés dans ce magasin (stratégie et autorités de certification secondaires).
* **ROOT** Magasin de l’autorité de certification racine (par conséquent, il s’agit de l’emplacement de votre certificat principal de l’autorité de certification racine.

### Impossible de supprimer les certificats expirés
Azure vous empêche de supprimer un certificat s’il est en cours d’utilisation. Vous devez soit supprimer le déploiement qui utilise le certificat, soit mettre à jour le déploiement avec un autre certificat ou avec un certificat que vous avez renouvelé.

### Suppression d’un certificat ayant expiré
Vous pouvez utiliser l’applet de commande PowerShell [Remove-AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) pour supprimer un certificat, à condition qu’il n’est pas utilisé.

### Mes certificats Microsoft Azure Service Management for Extensions ont expiré
Ces certificats sont créés chaque fois qu’une extension, par exemple l’extension du Bureau à distance, est ajoutée au service cloud. Ces certificats sont utilisés uniquement pour le chiffrement et le déchiffrement de la configuration privée de l’extension. L’expiration de ces certificats n’a aucune importance. La date d’expiration n’est pas vérifiée.

### Les certificats que j’ai supprimés continuent à s’afficher
Ces certificats continuent de s’afficher en raison d’un outil que vous utilisez, comme Visual Studio. À chaque fois que vous vous reconnectez avec un outil qui utilise un certificat, il sera à nouveau téléchargé dans Azure.

### Mes certificats ne cessent de disparaître
Chaque nouveau cycle de l’instance de machine virtuelle entraîne la perte de toutes les modifications locales. Utilisez une [tâche de démarrage](cloud-services-startup-tasks.md) pour installer les certificats sur la machine virtuelle à chaque démarrage du rôle.

### Impossible de trouver les certificats de gestion dans le portail
Les [certificats de gestion](../azure-api-management-certs.md) sont uniquement disponibles dans le portail Azure Classic. Le portail Azure actuel n’utilise pas de certificats de gestion.

### Comment puis-je désactiver un certificat de gestion ?
Les [certificats de gestion](../azure-api-management-certs.md) ne peuvent pas être désactivés. Vous les supprimez via le portail Azure Classic lorsque vous ne souhaitez plus les utiliser.

### Comment créer un certificat SSL pour une adresse IP spécifique ?
Suivez les instructions du [didacticiel de création de certificat](cloud-services-certs-create.md). Utilisez l’adresse IP en tant que nom DNS.

## Résolution de problèmes
### Impossible de réserver une adresse IP dans un service cloud à plusieurs adresses IP virtuelles
Tout d’abord, vérifiez que l’instance de machine virtuelle pour laquelle vous tentez de réserver l’adresse IP est activée. Ensuite, assurez-vous que vous utilisez des adresses IP réservées aussi bien pour les déploiements intermédiaires que pour les déploiements de production. **Ne** modifiez pas les paramètres pendant la mise à niveau du déploiement.

<!---HONumber=AcomDC_0914_2016-->