---
title: "Résoudre les problèmes concernant la connexion de point à site Azure | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes de connexion de point à site."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: c9d9b099bf71c5b1e51a11e3b62779bbc8767fd6
ms.contentlocale: fr-fr
ms.lasthandoff: 06/29/2017

---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Résolution des problèmes de connexion de point à site Azure

Cet article répertorie les problèmes de connexion de point à site courants que vous pouvez rencontrer. Il décrit également les causes probables de ces problèmes, ainsi que les solutions possibles.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Erreur du client VPN : le certificat est introuvable

### <a name="symptom"></a>Symptôme

Lorsque vous essayez de vous connecter au réseau virtuel Microsoft Azure à l’aide du client VPN, vous recevez le message d’erreur suivant :

**Impossible de trouver un certificat qui peut être utilisé avec le protocole EAP (Extensible Authentication Protocol). (Erreur 798)**

### <a name="cause"></a>Cause :

Ce problème se produit si le certificat client est absent de **Certificats - Utilisateur actuel\Personnel\Certificats**.

### <a name="solution"></a>Solution

Vérifiez que le certificat client est bien installé dans le magasin de certificats (Certmgr.msc), à l’emplacement suivant :
 
**Certificats - Utilisateur actuel\Personnel\Certificats**

Pour en savoir plus sur la façon d’installer le certificat client, consultez la page [Générer et exporter des certificats pour les connexions de point à site](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Lorsque vous importez le certificat client, ne sélectionnez pas l’option **Activer la protection renforcée par clé privée**.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Erreur du client VPN : le message reçu était inattendu ou formaté de façon incorrecte

### <a name="symptom"></a>Symptôme

Lorsque vous essayez de vous connecter au réseau virtuel à l’aide du client VPN, vous recevez le message d’erreur suivant :

**Erreur du client VPN : le message reçu était inattendu ou formaté de façon incorrecte. (Erreur 0x80090326)**

### <a name="cause"></a>Cause :

Ce problème se produit si la clé publique du certificat racine n’est pas téléchargée dans la passerelle VPN Microsoft Azure ou si la clé est endommagée ou a expiré.

### <a name="solution"></a>Solution

Pour résoudre ce problème, vérifiez l’état du certificat racine dans le portail Azure. Vous pourrez voir si le certificat a été révoqué ou non. S’il n’est pas révoqué, essayez de supprimer le certificat racine et de le télécharger une nouvelle fois. Pour en savoir plus, consultez la section [Créer des certificats](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Erreur du client VPN : une chaîne de certificats a été traitée mais s’est terminée 

### <a name="symptom"></a>Symptôme 

Lorsque vous essayez de vous connecter au réseau virtuel Azure à l’aide du client VPN, vous recevez le message d’erreur suivant :

**Une chaîne de certificats a été traitée mais s’est terminée par un certificat racine qui n’est pas approuvé par le fournisseur d’approbation**

### <a name="solution"></a>Solution

1. Assurez-vous que les certificats suivants se trouvent au bon emplacement :

    | Certificat | Emplacement |
    | ------------- | ------------- |
    | AzureClient.pfx  | Utilisateur actuel\Personnel\Certificats |
    | Azuregateway-*GUID*.cloudapp.net  | Utilisateur actuel\Autorités de certification racines de confiance|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Ordinateur local\Autorités de certification racines de confiance|

2. Si les certificats se trouvent déjà dans cet emplacement, essayez de les supprimer et de les réinstaller. Vous pourrez trouver le certificat **azuregateway-*GUID*.cloudapp.net** dans le package de configuration du client VPN, téléchargé à partir du portail Azure. Vous pouvez vous servir des programmes d’archivage de fichiers afin d’extraire les fichiers du package.

##  <a name="error-file-download-error-target-uri-is-not-specified"></a>Erreur : « erreur de téléchargement du fichier. L’URI cible n’est pas spécifiée »

### <a name="symptom"></a>Symptôme

Vous recevez le message d’erreur suivant :

**Erreur de téléchargement du fichier. L’URI cible n’est pas spécifiée**

### <a name="cause"></a>Cause : 

Ce problème se produit lorsque le type de passerelle est incorrecte. 

### <a name="solution"></a>Solution

Le type de passerelle VPN doit être défini sur la valeur **VPN**, tandis que le type de VPN doit être défini sur la valeur **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed-8007026f"></a>Erreur du client VPN : échec du script VPN personnalisé Azure (8007026f)

### <a name="symptom"></a>Symptôme

Lorsque vous essayez de vous connecter au réseau virtuel Azure à l’aide du client VPN, vous recevez le message d’erreur suivant :

**Échec du script personnalisé (pour mettre à jour votre table de routage) (8007026f).**

### <a name="cause"></a>Cause :

Ce problème peut se produire si vous essayez d’ouvrir la connexion VPN de point à site à l’aide d’un raccourci.

### <a name="solution"></a>Solution 

Ouvrez directement le package VPN au lieu de l’ouvrir à partir d’un raccourci.

## <a name="cannot-install-the-vpn-client"></a>Impossible d’installer le client VPN

### <a name="cause"></a>Cause : 

Afin de faire confiance à la passerelle VPN pour votre réseau virtuel, un certificat supplémentaire est nécessaire. Le certificat est inclus dans le package de configuration du client VPN, généré à partir du portail Azure.

### <a name="solution"></a>Solution

Extrayez le package de configuration du client VPN. Vous trouverez un fichier .cer. Installer le certificat dans les **Autorités de certification racines de confiance** du **Compte d’ordinateur** :

1. Ouvrez mmc.exe.
2. Ajoutez le composant logiciel enfichable **Certificats**.
3. Sélectionnez le compte **Ordinateur** pour l’ordinateur local.
4. Cliquez avec le bouton droit sur le nœud **Autorités de certification racines de confiance**. Cliquez sur **All-Task** (Toutes les tâches) > **Importer**, puis naviguez vers le fichier .cer extrait du package de configuration du client VPN.
5. Redémarrez l'ordinateur. 
6. Essayez d’installer le client VPN.

## <a name="azure-portal-error-failed-to-save-vpn-gateway-data-is-invalid"></a>Erreur du portail Azure : échec de l’enregistrement. Les données de la passerelle VPN ne sont pas valides

### <a name="symptom"></a>Symptôme

Lorsque vous essayez d’enregistrer les modifications apportées à la passerelle VPN dans le portail Azure, vous recevez le message d’erreur suivant :

**Échec de l’enregistrement de la passerelle de réseau virtuel &lt;nom de la passerelle&gt;. Erreur : les données du certificat &lt;ID de certificat&gt; ne sont pas valides.**

### <a name="cause"></a>Cause : 

Ce problème peut se produire si la clé publique de certificat racine que vous avez téléchargée contient des caractères non valides, comme un espace.

### <a name="solution"></a>Solution

Vérifiez que les données du certificat ne contiennent pas de caractères non valides, comme des sauts de ligne (retours chariot). La valeur entière doit être sur une longue ligne. Le texte ci-après est un extrait du certificat :

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-vpn-gateway-resource-name-is-invalid"></a>Erreur du portail Azure : échec de l’enregistrement de la passerelle VPN. Le nom de la ressource n’est pas valide

### <a name="symptom"></a>Symptôme

Lorsque vous essayez d’enregistrer les modifications apportées à la passerelle VPN dans le portail Azure, vous recevez le message d’erreur suivant : 

**Échec de l’enregistrement de la passerelle de réseau virtuel &lt;nom de la passerelle&gt;. Erreur : le nom de la ressource &lt;nom du certificat à télécharger&gt; n’est pas valide**.

### <a name="cause"></a>Cause :

Ce problème se produit car le nom du certificat contient des caractères non valides, comme un espace. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Erreur du portail Azure : téléchargement du fichier de package VPN (Erreur 503)

### <a name="symptom"></a>Symptôme

Lorsque vous essayez de télécharger le package de configuration du client VPN, vous recevez le message d’erreur suivant :

**Impossible de télécharger le fichier. Détails de l’erreur : erreur 503. Le serveur est occupé.**
 
### <a name="solution"></a>Solution

Cette erreur peut être due à un problème réseau temporaire. Patientez quelques minutes, puis essayez à nouveau de télécharger le package VPN.

## <a name="azure-vpn-gateway-upgrade-all-p2s-clients-are-unable-to-connect"></a>Mise à niveau de la passerelle VPN Azure, aucun client P2S ne peut se connecter

### <a name="cause"></a>Cause :

Si le certificat a atteint plus de 50 % de sa durée de vie, il est restauré.

### <a name="solution"></a>Solution

Pour résoudre ce problème, créez des certificats et redistribuez-les aux clients VPN. 

## <a name="too-many-vpn-clients-connected-at-once"></a>Trop de clients VPN sont connectés

Chaque passerelle VPN autorise un nombre de connexions maximal de 128.  Vous pouvez voir le nombre total de clients connectés dans le portail Azure.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-route-table"></a>Le VPN de point à site ajoute incorrectement un itinéraire pour 10.0.0.0/8 à la table de routage

### <a name="symptom"></a>Symptôme

Lorsque vous composez la connexion VPN sur le client de point à site, il est prévu que le client ajoute un itinéraire vers le Réseau virtuel Azure. De plus, le service d’Assistance IP ajoute un itinéraire pour le sous-réseau des clients VPN. 

Toutefois, si la plage de client VPN appartient à un plus petit sous-réseau 10.0.0.0/8 tel que 10.0.12.0/24, au lieu d’un itinéraire pour 10.0.12.0/24, alors un itinéraire « incorrect » pour 10.0.0.0/8 est ajouté, dont la priorité est plus élevée. 

Cela arrête la connectivité avec d’autres réseaux locaux pouvant appartenir à un autre sous-réseau dans la plage 10.0.0.0/8, comme 10.50.0.0/24 qui ne possède pas d’itinéraire spécifique. 

### <a name="cause"></a>Cause :

Ce comportement est lié aux clients Windows. Lorsqu’il utilise le protocole PPP IPCP, le client obtient l’adresse IP de l’interface de tunnel à partir du serveur (passerelle VPN dans ce cas). Cependant, à cause de la limitation du protocole, le client ne possède pas de masque de sous-réseau. Étant donné qu’il n’existe aucun autre moyen de l’obtenir, le client essaie de deviner le masque de sous-réseau en se basant sur la classe de l’adresse IP de l’interface de tunnel. 

Par conséquent, un itinéraire est ajouté sur la base du mappage statique suivant : 

Si l’adresse appartient à la classe A --> appliquer la valeur /8

Si l’adresse appartient à la classe B --> appliquer la valeur /16

Si l’adresse appartient à la classe C --> appliquer la valeur /24

##  <a name="vpn-client-cannot-access-network-file-shares"></a>Les clients VPN ne peuvent pas accéder aux partages de fichiers réseau

### <a name="symptom"></a>Symptôme

Le client VPN s’est connecté au réseau Azure. Toutefois, le client ne peut pas accéder aux partages réseau.

### <a name="cause"></a>Cause :

Le protocole SMB est utilisé pour l’accès au partage de fichiers. L’échec se produit lorsque les informations d’identification de la session sont ajoutées par le client VPN, au moment où la connexion est établie. Une fois la connexion établie, le client est forcé d’utiliser les informations d’identification en cache pour l’authentification Kerberos. Ce processus lance les requêtes sur le centre de distribution de clés (un contrôleur de domaine) afin d’obtenir un jeton. Étant donné que les clients se connectent à partir d’Internet, ils ne sont peut-être pas en mesure d’atteindre le contrôleur de domaine. Par conséquent, les clients ne peuvent pas basculer de Kerberos à NTLM. 

Il ne sera demandé au client de renseigner ses informations d’identification que s’il possède un certificat valide (avec SAN = UPN) émis par le domaine auquel le client est associé, et seulement si le client est physiquement connecté au réseau avec domaine. Dans ce cas, le client tente d’utiliser le certificat et d’atteindre le contrôleur de domaine. Ensuite, KDC retourne une erreur « KDC_ERR_C_PRINCIPAL_UNKNOWN ».  Cette erreur force le client à basculer vers NTLM. 

### <a name="solution"></a>Solution

Pour contourner le problème, désactivez la mise en cache des informations d’identification de domaine à partir de la sous-clé de Registre suivante : 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-vpn-client"></a>Impossible de trouver la connexion VPN de point à site dans Windows après la réinstallation du client VPN

### <a name="symptom"></a>Symptôme

Supprimez la connexion VPN de point à site, puis réinstallez le client VPN. Dans ce cas, la connexion VPN n’est pas configurée correctement. Vous ne voyez pas la connexion VPN dans les paramètres **Connexions réseau** dans Windows.

### <a name="solution"></a>Solution

Pour résoudre le problème, supprimez les anciens fichiers de configuration du client VPN à partir de **C:\Utilisateurs\Nomdel’utilisateur\AppData\Roaming\Microsoft\Network\Connections**, puis exécutez à nouveau le programme d’installation du client VPN.
