---
title: "Créer des certificats auto-signés pour les connexions de point à site : PowerShell : Azure | Microsoft Docs"
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
ms.date: 03/14/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: cb66edd0c0ff1f0b78232233719dd44329584c78
ms.lasthandoff: 03/15/2017


---
# <a name="create-a-self-signed-root-certificate-for-point-to-site-connections-using-powershell"></a>Créer un certificat auto-signé pour les connexions de point à site avec PowerShell

Les connexions de point à site utilisent des certificats pour l’authentification. Lorsque vous configurez une connexion de point à site, il vous faut charger la clé publique (fichier .cer) d’un certificat racine sur Azure. Cet article vous aide à créer un certificat racine auto-signé, exporter la clé publique et générer et installer des certificats clients.

> [!NOTE]
> La méthode makecert était auparavant recommandée pour créer les certificats racines auto-signés et générer des certificats clients pour les connexions de point à site. Vous pouvez désormais utiliser PowerShell pour créer ces certificats. L’un des avantages de PowerShell est de pouvoir créer des certificats SHA-2. 
>
>

## <a name="rootcert"></a>Créer un certificat racine auto-signé

Les étapes suivantes vous guideront dans la création d’un certificat racine auto-signé avec PowerShell. Windows 10 est requis pour suivre les étapes suivantes. Les applets de commande et les paramètres utilisés dans les étapes suivantes font partie du système d’exploitation Windows 10, et non d’une version de PowerShell.

1. Sur un ordinateur sous Windows 10, ouvrez une console Windows PowerShell avec élévation de privilèges.
2. Utilisez l’exemple suivant pour créer le certificat racine auto-signé. L’exemple suivant crée un certificat racine auto-signé nommé « P2SRootCert », automatiquement installé dans « Certificates-Current User\Personal\Certificates ». Vous pouvez afficher le certificat en ouvrant *certmgr.msc*.

        $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
        -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
        -HashAlgorithm sha256 -KeyLength 2048 `
        -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign

### <a name="cer"></a>Pour obtenir la clé publique

Les connexions de point à site requièrent le chargement de la clé publique (.cer) dans Azure. Les étapes suivantes vous aideront à exporter le fichier .cer pour votre certificat racine auto-signé.

1. Pour obtenir un fichier .cer du certificat, ouvrez **certmgr.msc**. Localisez le certificat racine auto-signé, généralement dans « Certificates - Curent User\Personal\Certificates » et cliquez avec le bouton droit. Cliquez sur **Toutes les tâches**, puis cliquez sur **Exporter**. Cette opération ouvre **l’Assistant Exportation de certificat**.
2. Dans l’assistant, cliquez sur **Suivant**. Sélectionnez **Non, ne pas exporter la clé privée**, puis cliquez sur **Suivant**.
3. Sur la page **Format de fichier d’exportation**, sélectionnez **Codé à base&64; X.509 (.cer).**, puis cliquez sur **Suivant**. 
4. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.
5. Cliquez sur **Terminer** pour exporter le certificat. Vous verrez **L’exportation a réussi**. Cliquez sur **OK** pour fermer l’assistant.

### <a name="to-export-a-self-signed-root-certificate-optional"></a>Pour exporter un certificat racine auto-signé (facultatif)
Vous souhaiterez peut-être exporter le certificat racine auto-signé et le stocker en toute sécurité. Si besoin est, vous pouvez l’installer ultérieurement sur un autre ordinateur et générer davantage de certificats clients ou exporter un autre fichier .cer.

Pour exporter le certificat racine auto-signé au format .pfx, sélectionnez le certificat racine et suivez les étapes décrites dans la section [Exporter un certificat client](#clientexport) pour l’exportation.

## <a name="clientcert"></a>Générer un certificat client

Chaque ordinateur client qui se connecte à un réseau virtuel avec une connexion de point à site doit avoir un certificat client installé. Vous générez un certificat client à partir du certificat racine auto-signé, puis l’exportez et l’installez. Si le certificat client n’est pas installé, l’authentification échoue. 

Les étapes suivantes vous guident dans la génération d’un certificat client à partir d’un certificat racine auto-signé. Vous pouvez générer plusieurs certificats clients à partir d’un même certificat racine. Lorsque vous générez des certificats clients suivant les étapes ci-dessous, le certificat client est automatiquement installé sur l’ordinateur que vous avez utilisé pour générer le certificat. Si vous souhaitez installer un certificat client sur un autre ordinateur client, vous pouvez exporter le certificat.

Windows 10 est requis pour suivre les étapes suivantes. Les applets de commande et les paramètres utilisés dans les étapes suivantes font partie du système d’exploitation Windows 10, et non d’une version de PowerShell.

### <a name="example-1"></a>Exemple 1

Cet exemple utilise la variable « $cert » déclarée dans la section précédente. Si vous avez fermé la console PowerShell après la création du certificat racine auto-signé, ou que vous créez des certificats clients supplémentaires dans une nouvelle session de console PowerShell, suivez les étapes décrites dans l’exemple 2.

Modifiez et exécutez l’exemple pour générer un certificat client. Si vous exécutez l’exemple suivant sans le modifier, le résultat est un certificat client nommé « P2SChildCert ».  Si vous souhaitez donner un autre nom au certificat enfant, modifiez la valeur CN. Ne modifiez pas la valeur TextExtension lors de l’exécution de cet exemple. Le certificat client que vous générez est automatiquement installé dans « Certificates - Current User\Personal\Certificates » sur votre ordinateur.

    New-SelfSignedCertificate -Type Custom -KeySpec Signature `
    -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 -KeyLength 2048 `
    -CertStoreLocation "Cert:\CurrentUser\My" `
    -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

### <a name="example-2"></a>Exemple 2

Si vous créez des certificats clients supplémentaires ou que vous n’utilisez pas la même session PowerShell que pour créer votre certificat racine auto-signé, suivez les étapes suivantes :

1. Identifiez le certificat racine auto-signé installé sur l’ordinateur. Cette applet de commande renvoie la liste des certificats installés sur votre ordinateur.

        Get-ChildItem -Path “Cert:\CurrentUser\My”

2. Recherchez le nom du sujet dans la liste renvoyée, puis copiez l’empreinte qui se trouve à côté dans un fichier texte. Dans l’exemple suivant, il y a deux certificats. Le nom CN est le nom du certificat racine auto-signé à partir duquel vous souhaitez générer un certificat enfant. Dans ce cas, « P2SRootCert ».

        Thumbprint                                Subject
        ----------                                -------
        AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
        7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert


3. Déclarez une variable pour le certificat racine avec l’empreinte de l’étape précédente. Remplacez THUMBPRINT par l’empreinte du certificat racine à partir duquel vous souhaitez générer un certificat enfant.

        $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"

    Par exemple, avec l’empreinte de P2SRootCert de l’étape précédente, la variable ressemblerait à ceci :

        $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
        

4.  Modifiez et exécutez l’exemple pour générer un certificat client. Si vous exécutez l’exemple suivant sans le modifier, le résultat est un certificat client nommé « P2SChildCert ». Si vous souhaitez donner un autre nom au certificat enfant, modifiez la valeur CN. Ne modifiez pas la valeur TextExtension lors de l’exécution de cet exemple. Le certificat client que vous générez est automatiquement installé dans « Certificates - Current User\Personal\Certificates » sur votre ordinateur.

        New-SelfSignedCertificate -Type Custom -KeySpec Signature `
        -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
        -HashAlgorithm sha256 -KeyLength 2048 `
        -CertStoreLocation "Cert:\CurrentUser\My" `
        -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

## <a name="clientexport"></a>Exporter un certificat client   

Lorsque vous générez un certificat client, il est automatiquement installé sur l’ordinateur que vous avez utilisé pour le générer. Si vous souhaitez installer le certificat client sur un autre ordinateur client, il vous faut exporter le certificat client généré.                              

1. Pour exporter un certificat client, ouvrez **certmgr.msc**. Les certificats clients que vous avez générés se trouvent par défaut dans « Certificates - Current User\Personal\Certificates ». Cliquez avec le bouton droit sur le certificat client à exporter, cliquez sur **Toutes les tâches**, puis sur **Exporter**. Cette opération ouvre **l’Assistant Exportation de certificat**.
2. Dans l’Assistant, cliquez sur **Suivant**, sélectionnez **Oui, exporter la clé privée**, puis cliquez sur **Suivant**.
3. Dans la page **Format de fichier d’exportation** , vous pouvez laisser les valeurs par défaut sélectionnées. Cliquez ensuite sur **Suivant**. 
4. Dans la page **Sécurité** , vous devez protéger la clé privée. Si vous choisissez d’utiliser un mot de passe, veillez à enregistrer ou à mémoriser celui que vous définissez pour ce certificat. Cliquez ensuite sur **Suivant**.
5. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.
6. Cliquez sur **Terminer** pour exporter le certificat.    

## <a name="install"></a>Installer un certificat client exporté

Si vous souhaitez créer une connexion P2S à partir d’un ordinateur client différent de celui que vous avez utilisé pour générer les certificats clients, vous devez installer un certificat client. Lorsque vous installez un certificat client, vous avez besoin du mot de passe créé lors de l’exportation du certificat client.

1. Recherchez le fichier *.pfx* et copiez-le sur l’ordinateur client. Sur l’ordinateur client, double-cliquez sur le fichier *.pfx* à installer. Laissez la zone **Emplacement du magasin** définie sur **Utilisateur actuel**, puis cliquez sur **Suivant**.
2. N’apportez aucune modification à la page **Fichier à importer** . Cliquez sur **Next**.
3. Dans la page **Protection de clé privée**, entrez le mot de passe du certificat si vous en avez utilisé un, ou vérifiez que le principal de sécurité qui installe le certificat est correct, puis cliquez sur **Suivant**.
4. Dans la page **Magasin de certificats**, laissez l’emplacement par défaut, puis cliquez sur **Suivant**.
5. Cliquez sur **Terminer**. Dans la page **Avertissement de sécurité** relative à l’installation du certificat, cliquez sur **Oui**. Vous pouvez cliquer sur « Oui » sans hésitation, car vous avez généré le certificat. Le certificat est désormais importé.

## <a name="next-steps"></a>Étapes suivantes
Poursuivez votre configuration point à site. 

* Pour connaître les étapes du modèle de déploiement **Resource Manager**, consultez la page [Configurer une connexion de point à site à un réseau virtuel](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Pour connaître les étapes du modèle de déploiement **Classic**, consultez la page [Configurer une connexion VPN de point à site à un réseau virtuel (Classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).


