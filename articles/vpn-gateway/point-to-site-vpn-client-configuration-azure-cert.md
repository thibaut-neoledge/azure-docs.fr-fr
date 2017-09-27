---
title: "Create and install P2S VPN client configuration files for Azure certificate authentication: PowerShell: Azure (Créer et installer des fichiers de configuration du client VPN pour des connexions P2S : Azure PowerShell) | Microsoft Docs"
description: "Cet article vous aide à créer et à installer un fichier de configuration du client VPN pour des connexions P2S utilisant une authentification par certificat."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: cd5608ceda95a0bcca4416d2836c3deb3eb51e00
ms.contentlocale: fr-fr
ms.lasthandoff: 09/22/2017

---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Créer et installer des fichiers de configuration du client VPN avec des configurations d’authentification par certificat de connexions P2S Azure natives

Les fichiers de configuration du client VPN se trouvent dans un fichier zip. Les fichiers de configuration fournissent les paramètres nécessaires à un client VPN IKEv2 Windows ou Mac natif pour se connecter à un réseau virtuel en point à site avec une authentification par certificat Azure native.

### <a name="workflow"></a>Workflow P2S

  1. Configurez la passerelle VPN Azure pour une connexion P2S.
  2. Générez le certificat racine et les certificats client. Chargez la clé publique du certificat racine dans Azure et installez les certificats client sur les appareils client. Les certificats sont utilisés pour authentifier les utilisateurs qui se connectent.
  3. Obtenez la configuration du client VPN pour l’option d’authentification de votre choix et utilisez-la pour installer le client VPN sur vos appareils Windows et Mac. Cela vous permet de vous connecter à des réseaux virtuels Azure via une connexion point à site.

>[!NOTE]
>Les fichiers de configuration du client sont spécifiques à la configuration VPN du réseau virtuel. Si vous apportez des modifications à la configuration du VPN en point à site après avoir généré les fichiers de configuration du client VPN, comme le type de protocole ou d’authentification du VPN, veillez à générer de nouveaux fichiers de configuration du client VPN pour vos appareils utilisateurs.
>
>

## <a name="generate"></a>Générer les fichiers de configuration du client VPN

Avant de commencer, veillez à ce que tous les utilisateurs qui se connectent disposent d’un certificat valide installer sur l’appareil utilisateur. Pour plus d’informations sur l’installation d’un certificat client, consultez [Installer un certificat client](point-to-site-how-to-vpn-client-install-azure-cert.md).

1. Lorsque vous générez les fichiers de configuration du client VPN, la valeur de la méthode « -AuthenticationMethod » est « EapTls ». Générez les fichiers de configuration du client VPN à l’aide de la commande suivante :

  ```powershell
  New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -VirtualNetworkGatewayName "VNet1GW" -AuthenticationMethod "EapTls"
  ```
2. La commande précédente retourne un lien à utiliser pour télécharger les fichiers de configuration du client. Copiez et collez ce lien dans un navigateur web pour télécharger le fichier « VpnClientConfiguration.zip ». Décompressez le fichier pour afficher les dossiers suivants :

  * Les dossiers **WindowsAmd64** et **WindowsX86**, dans lesquels se trouvent respectivement les packages de programme d’installation pour Windows 32 bits et Windows 64 bits. Le package d’installation **WindowsAmd64** est pour tous les clients Windows 64 bits pris en charge, pas seulement Amd.
  * Le dossier **Générique**, dans lequel se trouvent les informations générales utilisées pour créer la configuration de votre client VPN. Ignorez ce dossier. Le dossier Générique est fourni uniquement si les protocoles IKEv2 ou SSTP+IKEv2 ont été configurés sur la passerelle. S’il n’y a que le protocole SSTP qui a été configuré, alors le dossier Générique n’apparaîtra pas.

### <a name="to-retrieve-client-configuration-files"></a>Pour récupérer les fichiers de configuration du client VPN

Si vous avez déjà généré les fichiers du client VPN et que vous avez besoin de les récupérer, vous pouvez utiliser la cmdlet « Get-AzureRmVpnClientConfiguration ». La cmdlet « Get-AzureRmVpnClientConfiguration » retourne un URL (lien) depuis lequel vous pouvez télécharger le fichier VpnClientConfiguration.zip. Si vous avez apporté des modifications à votre configuration VPN P2S, comme le type de protocole ou d’authentification VPN, elle ne se met pas à jour automatiquement. Vous devez exécuter la cmdlet « New-AzureRmVpnClientConfiguration » pour recréer la configuration.

Pour récupérer les fichiers de configuration du client générés précédemment, utilisez l’exemple suivant :

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -VirtualNetworkGatewayName "VNet1GW"
```
 
## <a name="installwin"></a>Installer un package de configuration du client VPN Windows

Vous pouvez utiliser le même package de configuration du client VPN sur chaque ordinateur client Windows, tant que la version correspond à l’architecture du client. Pour obtenir la liste des systèmes d’exploitation clients pris en charge, consultez la section Point à site de la [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md#P2S).

Suivez les étapes suivantes pour configurer le client VPN Windows natif pour une authentification par certificat :

1. Sélectionnez les fichiers de configuration du client VPN qui correspondent à l’architecture de l’ordinateur Windows. Pour une architecture de processeur 64 bits, choisissez le package du programme d’installation « VpnClientSetupAmd64 ». Pour une architecture de processeur 32 bits, choisissez le package du programme d’installation « VpnClientSetupX86 ». 
2. Double-cliquez sur le package pour lancer l’installation. Si une fenêtre contextuelle SmartScreen s’affiche, cliquez sur **Plus d’infos**, puis sur **Exécuter quand même**.
3. Sur l’ordinateur client, accédez à **Paramètres réseau**, puis cliquez sur **VPN**. La connexion VPN indique le nom du réseau virtuel auquel elle se connecte. 

## <a name="installmac"></a>Installer une configuration du client VPN Mac (OSX)

Vous devez créer une configuration du client VPN distincte pour chaque appareil Mac connecté au réseau virtuel Azure. Vous ne pouvez pas réutiliser les mêmes fichiers de configuration sur plusieurs appareils Mac. Les appareils Mac nécessitent la spécification des certificats utilisateur dans les fichiers de configuration du client VPN. Le dossier **Générique** contient toutes les informations requises pour créer une configuration du client VPN. Ce dossier contient les fichiers suivants :

* Le fichier **VpnSettings.xml**, qui contient d’importants paramètres tels que l’adresse et le type de tunnel du serveur. 
* Le fichier **VpnServerRoot.cer**, qui contient le certificat racine requis pour valider la passerelle VPN Azure lors de la configuration de la connexion P2S.

Suivez les étapes suivantes pour configurer le client VPN Mac natif pour une authentification par certificat :

1. Importez le certificat racine **VpnServerRoot** sur votre Mac. Vous pouvez les importer en copiant les fichiers sur votre Mac, puis en double-cliquant dessus.  
Cliquez sur **Ajouter** pour importer.

  ![ajouter le certificat](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
2. Ouvrez la boîte de dialogue **Réseau** dans **Préférences réseau**, puis cliquez sur l’icône **+** pour créer un profil de connexion du client VPN pour une connexion P2S au réseau virtuel Azure.

  La valeur **Interface** est « VPN » et la valeur **VPN Type** est « IKEv2 ». Spécifiez un nom pour le profil dans le champ **Nom du service**, puis cliquez sur **Créer** pour créer le profil de connexion du client VPN.

  ![réseau](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
3. Dans le dossier **Générique**, depuis le fichier **VpnSettings.xml**, copiez la valeur de la balise **VpnServer**. Collez cette valeur dans les champs **Adresse du serveur** et **ID distant** du profil.

  ![informations du serveur](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
4. Cliquez sur **Paramètres d’authentification** et sélectionnez **Certificat**. 

  ![paramètres d’authentification](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
5. Cliquez sur **Sélectionner** pour choisir le certificat que vous souhaitez utiliser pour l’authentification.

  ![certificat](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
6. **Choisir une identité** affiche une liste de certificats à choisir. Sélectionnez le certificat approprié, puis cliquez sur **Continuer**.

  ![identité](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
7. Dans le champ **ID local**, spécifiez le nom du certificat (renseigné dans l’étape 5). Dans cet exemple, nous lui avons donné le nom « ikev2Client.com ». Cliquez ensuite sur le bouton **Appliquer** pour enregistrer les modifications.

  ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
8. Dans la boîte de dialogue **Réseau**, cliquez sur **Appliquer** pour enregistrer toutes les modifications. Cliquez ensuite sur **Connexion** pour lancer la connexion P2S au réseau virtuel Azure.

## <a name="next-steps"></a>Étapes suivantes

Revenez à l’article pour [terminer la configuration P2S](vpn-gateway-howto-point-to-site-rm-ps.md).
