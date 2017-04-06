---
title: "FAQ sur les rôles Services cloud Azure | Microsoft Docs"
description: "Forum aux questions sur les Services cloud Azure. Répond à certaines questions courantes sur les certificats, les rôles web et les rôles de travail."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 7287cb1709b7c863cd046edfb995e23455398ec2
ms.lasthandoff: 03/25/2017


---
# <a name="cloud-services-faq"></a>Forum aux questions sur Cloud Services
Cet article répond à certaines questions fréquemment posées sur Microsoft Azure Cloud Services. Vous pouvez également visiter le [FAQ du support Azure](http://go.microsoft.com/fwlink/?LinkID=185083) pour obtenir des informations de support et de tarification générale Azure. Vous pouvez également consulter la page [Taille de services cloud](cloud-services-sizes-specs.md) pour obtenir des informations sur la taille.

## <a name="certificates"></a>Certificats
### <a name="where-should-i-install-my-certificate"></a>Où dois-je installer mon certificat ?
* **My**  
  Certificat d’application avec clé privée (\*.pfx, \*.p12).
* **CA**  
  Tous vos certificats intermédiaires sont stockés dans ce magasin (stratégie et autorités de certification secondaires).
* **ROOT**  
  Magasin de l’autorité de certification racine (par conséquent, il s’agit de l’emplacement de votre certificat principal de l’autorité de certification racine.

### <a name="i-cant-remove-expired-certificate"></a>Impossible de supprimer les certificats expirés
Azure vous empêche de supprimer un certificat s’il est en cours d’utilisation. Vous devez soit supprimer le déploiement qui utilise le certificat, soit mettre à jour le déploiement avec un autre certificat ou avec un certificat que vous avez renouvelé.

### <a name="delete-an-expired-certificate"></a>Suppression d’un certificat ayant expiré
Vous pouvez utiliser l’applet de commande PowerShell [Remove-AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) pour supprimer un certificat, à condition qu’il n’est pas utilisé.

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>Mes certificats Windows Azure Service Management for Extensions ont expiré
Ces certificats sont créés chaque fois qu’une extension, par exemple l’extension du Bureau à distance, est ajoutée au service cloud. Ces certificats sont utilisés uniquement pour le chiffrement et le déchiffrement de la configuration privée de l’extension. L’expiration de ces certificats n’a aucune importance. La date d’expiration n’est pas vérifiée.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>Les certificats que j’ai supprimés continuent à s’afficher
Ces certificats continuent de s’afficher en raison d’un outil que vous utilisez, comme Visual Studio. À chaque fois que vous vous reconnectez avec un outil qui utilise un certificat, il sera à nouveau téléchargé dans Azure.

### <a name="my-certificates-keep-disappearing"></a>Mes certificats ne cessent de disparaître
Chaque nouveau cycle de l’instance de machine virtuelle entraîne la perte de toutes les modifications locales. Utilisez une [tâche de démarrage](cloud-services-startup-tasks.md) pour installer les certificats sur la machine virtuelle à chaque démarrage du rôle.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>Impossible de trouver les certificats de gestion dans le portail
Les [certificats de gestion](../azure-api-management-certs.md) sont uniquement disponibles dans le portail Azure Classic. Le portail Azure actuel n’utilise pas de certificats de gestion. 

### <a name="how-can-i-disable-a-management-certificate"></a>Comment puis-je désactiver un certificat de gestion ?
[certificats de gestion](../azure-api-management-certs.md) ne peuvent pas être désactivés. Vous les supprimez via le portail Azure Classic lorsque vous ne souhaitez plus les utiliser.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>Comment créer un certificat SSL pour une adresse IP spécifique ?
Suivez les instructions du [didacticiel de création de certificat](cloud-services-certs-create.md). Utilisez l’adresse IP en tant que nom DNS.

## <a name="security"></a>Sécurité
### <a name="disable-ssl-30"></a>Désactiver SSL 3.0
Pour désactiver SSL 3.0 et utiliser la sécurité TLS, créez une tâche de démarrage qui est décrite dans ce billet de blog : https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/

### <a name="add-nosniff-to-your-website"></a>Ajouter **nosniff** à votre site web
Pour empêcher les clients de détecter les types MIME, ajoutez un paramètre au fichier *web.config*.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Vous pouvez également ajouter ce paramètre dans IIS. Utilisez la commande suivante avec l’article [tâches courantes de démarrage](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="customize-iis-for-a-web-role"></a>Personnaliser IIS pour un rôle web
Utilisez le script de démarrage IIS à partir de l’article [tâches courantes de démarrage](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

## <a name="scaling"></a>Mise à l'échelle
### <a name="i-cannot-scale-beyond-x-instances"></a>Je ne parviens pas à mettre à l’échelle au-delà de X instances
Le nombre de cœurs que vous pouvez utiliser est limité dans votre abonnement Azure. La mise à l’échelle ne fonctionnera pas si vous avez utilisé tous les cœurs disponibles. Par exemple, si vous avez une limite de 100 cœurs, cela signifie que vous pouvez avoir 100 instances de machine virtuelle A1 pour votre service cloud ou 50 instances de machine virtuelle A2.

## <a name="networking"></a>Mise en réseau
### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Impossible de réserver une adresse IP dans un service cloud à plusieurs adresses IP virtuelles
Tout d’abord, vérifiez que l’instance de machine virtuelle pour laquelle vous tentez de réserver l’adresse IP est activée. Ensuite, assurez-vous que vous utilisez des adresses IP réservées aussi bien pour les déploiements intermédiaires que pour les déploiements de production. **Ne** modifiez pas les paramètres pendant la mise à niveau du déploiement.

## <a name="remote-desktop"></a>Bureau à distance
### <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>Comment établir un Bureau à distance lorsque je possède un groupe de sécurité réseau ?
Ajoutez une règle au groupe de sécurité réseau qui transfère le port **20000**.

