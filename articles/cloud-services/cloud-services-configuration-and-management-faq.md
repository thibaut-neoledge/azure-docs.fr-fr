---
title: "Questions fréquentes (FAQ) concernant les problèmes de configuration et de gestion pour Microsoft Azure Cloud Services | Microsoft Docs"
description: "Cet article répertorie les questions fréquentes sur la configuration et la gestion pour Microsoft Azure Cloud Services."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 7d7676be26a8b68ab9fda18388e2b8cd5ed5f60b
ms.contentlocale: fr-fr
ms.lasthandoff: 06/15/2017


---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problèmes de configuration et de gestion pour Azure Cloud Services : questions fréquentes (FAQ)

Cet article comprend des questions fréquentes sur les problèmes de configuration et de gestion pour [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Vous pouvez également consulter la page [Taille de services cloud](cloud-services-sizes-specs.md) pour obtenir des informations sur la taille.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-add-nosniff-to-my-website"></a>Comment ajouter des directives « nosniff » à mon site web ?
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

## <a name="how-do-i-customize-iis-for-a-web-role"></a>Comment personnaliser IIS pour un rôle web ?
Utilisez le script de démarrage IIS à partir de l’article [tâches courantes de démarrage](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

## <a name="i-cannot-scale-beyond-x-instances"></a>Je ne parviens pas à mettre à l’échelle au-delà de X instances
Le nombre de cœurs que vous pouvez utiliser est limité dans votre abonnement Azure. La mise à l’échelle ne fonctionnera pas si vous avez utilisé tous les cœurs disponibles. Par exemple, si vous avez une limite de 100 cœurs, cela signifie que vous pouvez avoir 100 instances de machine virtuelle A1 pour votre service cloud ou 50 instances de machine virtuelle A2.

## <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Comment implémenter un accès en fonction du rôle pour les services cloud ?
Les services cloud ne prennent pas en charge le modèle de contrôle d’accès en fonction du rôle (RBAC), car il ne s’agit pas d’un service Azure Resource Manager.

Consultez [Comparaison entre RBAC Azure et les administrateur d’abonnements classiques](../active-directory/role-based-access-control-what-is.md#azure-rbac-vs-classic-subscription-administrators).

## <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Comment définir le délai d’inactivité d’Azure Load Balancer ?
Vous pouvez spécifier le délai d’expiration dans votre fichier de définition de service (csdef) comme ceci :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Pour plus d’informations, consultez [Nouveau : délai d’inactivité configurable pour Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/).

## <a name="can-microsoft-internal-engineers-rdp-to-cloud-service-instances-without-permission"></a>Les ingénieurs internes Microsoft peuvent-ils se connecter à des instances de service cloud sans autorisation via RDP ?
Microsoft suit un processus strict qui ne permet pas à ses ingénieurs internes de se connecter à votre service cloud via RDP sans une autorisation écrite (par courrier électronique ou toute autre communication écrite) de la part du propriétaire ou de son représentant.

## <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Pourquoi la chaîne de certificats du certificat SSL de mon service cloud est-elle incomplète ?
Nous recommandons aux clients d’installer la chaîne de certificats complète (certificat feuille, certificats intermédiaires et certificat racine) au lieu du seul certificat feuille. Quand vous installez uniquement le certificat feuille, vous faites confiance à Windows pour générer la chaîne de certificats en parcourant la liste CTL. Si Azure ou Windows Update rencontre des problèmes réseau ou DNS intermittents pendant que Windows tente de valider le certificat, celui-ci peut être considéré comme non valide. En installant la chaîne de certificats complète, ce problème peut être évité. L’article du blog intitulé [How to install a chained SSL certificate](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) (Comment installer un certificat SSL chaîné) explique comment effectuer cette opération.

## <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Comment associer une adresse IP statique à mon service cloud ?
Pour configurer une adresse IP statique, vous devez créer une adresse IP réservée. Cette adresse IP réservée peut être associée à un nouveau service cloud ou à un déploiement existant. Pour plus d’informations, consultez les documents suivants :
* [Comment créer une adresse IP réservée](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Réserver l’adresse IP d’un service cloud existant](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Associer une adresse IP réservée à un nouveau service cloud](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Associer une adresse IP réservée à un déploiement en cours d’exécution](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Associer une adresse IP réservée à un service cloud à l’aide d’un fichier de configuration de service](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

## <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Quelle est la limite de quota de mon service cloud ?
Consultez [Limites spécifiques des services](../azure-subscription-service-limits.md#subscription-limits).

## <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Pourquoi le lecteur de la machine virtuelle de mon service cloud dispose-t-il d’aussi peu d’espace disque ?
Il s’agit d’un comportement normal qui ne devrait pas causer de problèmes à votre application. La journalisation est activée pour le lecteur % des machines virtuelles Azure PaaS, ce qui dans l’absolu a pour effet de consommer normalement le double d’espace que les fichiers. Cependant, cela ne pose pas de problème, et ce pour plusieurs raisons.

La taille du lecteur %approot% est calculée selon la formule <taille du fichier .cspkg + taille maximale du journal > + marge d’espace libre> ou est égale à 1,5 Go, la valeur la plus grande étant retenue. La taille de votre machine virtuelle n’a pas d’incidence sur ce calcul. (La taille de machine virtuelle affecte uniquement la taille du lecteur C: temporaire.) 

L’écriture sur le lecteur %approot% n’est pas prise en charge. Si vous écrivez sur la machine virtuelle Azure, vous devez le faire dans une ressource LocalStorage temporaire (ou une autre option, telle que le Stockage Blob, Azure Files, etc..). Autrement dit, la quantité d’espace libre dans le dossier %approot % n’est pas significative. Pour savoir avec certitude si votre application écrit ou non sur le lecteur %approot%, vous pouvez toujours laisser votre service s’exécuter pendant quelques jours et comparer sa taille avant et après. 

Azure n’écrit rien sur le lecteur %approot%. Une fois le disque dur virtuel créé à partir de votre fichier .cspkg et monté dans la machine virtuelle Azure, votre application est la seule à pouvoir écrire sur ce lecteur. 

Les paramètres de journal n’étant pas configurables, vous ne pouvez pas les désactiver.

## <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Quelles fonctionnalités et capacités IPS/ID et DDOS de base trouve-t-on sur Azure ?
Azure propose des fonctionnalités IPS/IDS sur les serveurs physiques des centres de données pour assurer une protection contre les menaces. Par ailleurs, les clients peuvent déployer des solutions de sécurité tierces, telles que des pare-feu d’applications web, des pare-feu réseau, des logiciels anti-programmes malveillants, des systèmes de détection et de prévention des intrusions (IDS/IPS), etc. Pour plus d’informations, consultez [Protégez vos données et vos biens en respectant les normes internationales de sécurité](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft surveille en continu les serveurs, les réseaux et les applications pour détecter les menaces. L’approche concertée de la gestion des menaces d’Azure utilise également la détection d’intrusion, la prévention des attaques par déni de service distribué (DDoS), le test de pénétration, l’analytique des comportements, la détection d’anomalies et le Machine Learning pour renforcer constamment ses défenses et réduire les risques. Microsoft Antimalware pour Azure protège les services cloud et les machines virtuelles Azure. Vous avez la possibilité de déployer des solutions de sécurité tierces supplémentaires, telles que des pare-feu d’applications web, des pare-feu réseau, des logiciels anti-programmes malveillants, des systèmes de détection et de prévention des intrusions (IDS/IPS), etc.

## <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Pourquoi IIS cesse-t-il d’écrire dans le répertoire des journaux ?
Vous avez épuisé le quota de stockage local réservé à l’écriture dans le répertoire des journaux. Pour corriger ce problème, vous avez trois solutions :
* Activez les diagnostics pour IIS et déplacez régulièrement les diagnostics sur le Stockage Blob.
* Supprimez manuellement les fichiers journaux du répertoire de journalisation.
* Augmentez la limite de quota pour les ressources locales.

Pour plus d’informations, consultez les documents suivants :
* [Stocker et afficher des données de diagnostic dans le stockage Azure](cloud-services-dotnet-diagnostics-storage.md)
* [Le service Journaux IIS cesse d’écrire dans le service cloud](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>À quoi sert le « certificat de chiffrement Windows Azure Tools pour les extensions » ?
Ces certificats sont créés automatiquement chaque fois qu’une extension est ajoutée au service cloud. Il s’agit le plus souvent de l’extension WAD ou RDP, mais il peut s’agit d’autres extensions, notamment Antimalware ou Log Collector. Ces certificats sont utilisés uniquement pour chiffrer et déchiffrer la configuration privée de l’extension. Dans la mesure où la date d’expiration n’est jamais vérifiée, l’expiration du certificat ne pose pas de problème. 

Vous pouvez ignorer ces certificats. Si vous voulez nettoyer les certificats, vous pouvez essayer de tous les supprimer. Azure génère une erreur si vous essayez de supprimer un certificat en cours d’utilisation.

## <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Comment générer une demande de signature de certificat sans se connecter à l’instance via le protocole RDP ?
Consultez le document d’instructions suivant :

>[Obtention d’un certificat pour une utilisation avec Windows Azure Web Sites (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Notez qu’une demande de signature de certificat consiste simplement en un fichier texte. Il N’EST PAS nécessaire de le créer à partir de la machine sur laquelle le certificat est finalement utilisé. Même si ce document est écrit pour un service d’application (App Service), la création d’une demande de signature de certificat est générique et s’applique aussi aux services cloud.

