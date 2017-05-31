---
title: "Créer et exporter des certificats pour les connexions de point à site : PowerShell : Azure | Microsoft Docs"
description: "Cet article décrit les étapes permettant de créer un certificat racine auto-signé, d’exporter la clé publique et de générer des certificats clients avec PowerShell sous Windows 10."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 1bfcb8683669770d6dd927cbde53a683bbc1d56e
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-powershell"></a>Générer et exporter des certificats pour les connexions de point à site à l’aide de PowerShell

Cet article vous explique comment créer un certificat racine auto-signé et générer des certificats clients. Cet article ne contient ni instruction de configuration de point à site ni questions fréquentes relatives aux connexions de point à site. Pour plus d’informations, choisissez l’un des articles de la liste suivante :

> [!div class="op_single_selector"]
> * [Création de certificat auto-signés - PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Création de certificat auto-signés - Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Configuration d’une connexion de point à site à un réseau virtuel - Resource Manager - Portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Configuration d’une connexion point à site à un réseau virtuel - Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Configuration d’une connexion de point à site à un réseau virtuel - Classic - Portail Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Les connexions de point à site utilisent des certificats pour l’authentification. Lorsque vous configurez une connexion de point à site, il vous faut charger la clé publique (fichier .cer) d’un certificat racine sur Azure. De plus, les certificats clients doivent être générés à partir du certificat racine et installés sur chaque ordinateur client qui se connecte au réseau virtuel. Le certificat client permet au client de s’authentifier.


> [!NOTE]
> Vous devez suivre les étapes de cet article sur un ordinateur exécutant Windows 10. Les applets de commande PowerShell qui sont requises pour générer des certificats font partie du système d’exploitation Windows 10 et ne fonctionnent pas sur d’autres versions de Windows. Ces applets de commande sont suffisantes pour générer les certificats. Une fois les certificats générés, vous pouvez les installer sur n’importe quel système d’exploitation client pris en charge. Si vous n’avez pas accès à un ordinateur Windows 10, vous pouvez utiliser makecert pour générer des certificats. Toutefois, les certificats générés à l’aide de makecert, même s’ils sont compatibles avec le type de point à site, ne sont pas SHA-2. Pour obtenir des instructions sur makecert, consultez [Créer des certificats à l’aide de makecert](vpn-gateway-certificates-point-to-site-makecert.md). Les certificats que vous créez à l’aide de PowerShell ou makecert peuvent être installés sur n’importe quel [système d’exploitation client pris en charge](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq), pas seulement sur le système d’exploitation que vous avez utilisé pour les créer.
> 
>

## <a name="rootcert"></a>Créer un certificat racine auto-signé

Les étapes suivantes vous montrent comment créer un certificat racine auto-signé avec PowerShell sur Windows 10. Les applets de commande et les paramètres utilisés dans les étapes suivantes font partie du système d’exploitation Windows 10, et non d’une version de PowerShell. Cela ne signifie pas que les certificats que vous créez ne peuvent être installés que sur Windows 10. Ils peuvent être installés sur n’importe quel client pris en charge. Pour plus d’informations sur les clients pris en charge, consultez le [Forum Aux Questions sur les connexions point à site](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq).

1. Sur un ordinateur sous Windows 10, ouvrez une console Windows PowerShell avec élévation de privilèges.
2. Utilisez l’exemple suivant pour créer le certificat racine auto-signé. L’exemple suivant crée un certificat racine auto-signé nommé « P2SRootCert », automatiquement installé dans « Certificates-Current User\Personal\Certificates ». Vous pouvez afficher le certificat en ouvrant *certmgr.msc* ou *Gérer les certificats utilisateur*.

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

### <a name="cer"></a>Exporter la clé publique (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Le fichier exported.cer doit être chargé dans Azure. Pour obtenir des instructions, consultez [Configurer une connexion de point à site](vpn-gateway-howto-point-to-site-rm-ps.md#upload).

### <a name="export-the-self-signed-root-certificate-and-public-key-to-store-it-optional"></a>Exporter le certificat racine auto-signé et la clé privée pour la stocker (facultatif)

Vous souhaiterez peut-être exporter le certificat racine auto-signé et le stocker en toute sécurité. Si besoin est, vous pouvez l’installer ultérieurement sur un autre ordinateur et générer davantage de certificats clients ou exporter un autre fichier .cer. Pour exporter le certificat racine auto-signé au format .pfx, sélectionnez le certificat racine et suivez les étapes décrites dans la section [Exporter un certificat client](#clientexport).

## <a name="clientcert"></a>Générer un certificat client

Chaque ordinateur client qui se connecte à un réseau virtuel avec une connexion de point à site doit avoir un certificat client installé. Vous générez un certificat client à partir du certificat racine auto-signé, puis l’exportez et l’installez. Si le certificat client n’est pas installé, l’authentification échoue. 

Les étapes suivantes vous guident dans la génération d’un certificat client à partir d’un certificat racine auto-signé. Vous pouvez générer plusieurs certificats clients à partir d’un même certificat racine. Lorsque vous générez des certificats clients suivant les étapes ci-dessous, le certificat client est automatiquement installé sur l’ordinateur que vous avez utilisé pour générer le certificat. Si vous souhaitez installer un certificat client sur un autre ordinateur client, vous pouvez exporter le certificat.

Vous devez utiliser Windows 10 pour pouvoir générer des certificats clients à l’aide de la procédure PowerShell suivante. Les applets de commande et les paramètres utilisés dans les étapes suivantes font partie du système d’exploitation Windows 10, et non d’une version de PowerShell. Cela ne signifie pas que les certificats que vous créez ne peuvent être installés que sur Windows 10. Pour plus d’informations sur les clients pris en charge, consultez le [Forum Aux Questions sur les connexions point à site](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq).

### <a name="example-1"></a>Exemple 1

Cet exemple utilise la variable « $cert » déclarée dans la section précédente. Si vous avez fermé la console PowerShell après la création du certificat racine auto-signé, ou que vous créez des certificats clients supplémentaires dans une nouvelle session de console PowerShell, suivez les étapes décrites dans [l’exemple 2](#ex2).

Modifiez et exécutez l’exemple pour générer un certificat client. Si vous exécutez l’exemple suivant sans le modifier, le résultat est un certificat client nommé « P2SChildCert ».  Si vous souhaitez donner un autre nom au certificat enfant, modifiez la valeur CN. Ne modifiez pas la valeur TextExtension lors de l’exécution de cet exemple. Le certificat client que vous générez est automatiquement installé dans « Certificates - Current User\Personal\Certificates » sur votre ordinateur.

```powershell
New-SelfSignedCertificate -Type Custom -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Exemple 2

Si vous créez des certificats clients supplémentaires ou que vous n’utilisez pas la même session PowerShell que pour créer votre certificat racine auto-signé, suivez les étapes suivantes :

1. Identifiez le certificat racine auto-signé installé sur l’ordinateur. Cette applet de commande renvoie la liste des certificats installés sur votre ordinateur.

  ```powershell
  Get-ChildItem -Path “Cert:\CurrentUser\My”
  ```
2. Recherchez le nom du sujet dans la liste renvoyée, puis copiez l’empreinte qui se trouve à côté dans un fichier texte. Dans l’exemple suivant, il y a deux certificats. Le nom CN est le nom du certificat racine auto-signé à partir duquel vous souhaitez générer un certificat enfant. Dans ce cas, « P2SRootCert ».

        Thumbprint                                Subject

        AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
        7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert


3. Déclarez une variable pour le certificat racine avec l’empreinte de l’étape précédente. Remplacez THUMBPRINT par l’empreinte du certificat racine à partir duquel vous souhaitez générer un certificat enfant.

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
  ```

  Par exemple, avec l’empreinte numérique pour P2SRootCert de l’étape précédente, la variable ressemble à ceci :

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
  ```    

4.  Modifiez et exécutez l’exemple pour générer un certificat client. Si vous exécutez l’exemple suivant sans le modifier, le résultat est un certificat client nommé « P2SChildCert ». Si vous souhaitez donner un autre nom au certificat enfant, modifiez la valeur CN. Ne modifiez pas la valeur TextExtension lors de l’exécution de cet exemple. Le certificat client que vous générez est automatiquement installé dans « Certificates - Current User\Personal\Certificates » sur votre ordinateur.

  ```powershell
  New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="clientexport"></a>Exporter un certificat client   

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]
    

## <a name="install"></a>Installer un certificat client exporté

[!INCLUDE [Install client certificate](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Poursuivez votre configuration point à site. 

* Pour connaître les étapes du modèle de déploiement **Resource Manager**, consultez la page [Configurer une connexion de point à site à un réseau virtuel](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Pour connaître les étapes du modèle de déploiement **Classic**, consultez la page [Configurer une connexion VPN de point à site à un réseau virtuel (Classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).
