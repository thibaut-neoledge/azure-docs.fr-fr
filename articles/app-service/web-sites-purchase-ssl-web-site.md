---
title: "Ajouter un certificat SSL à votre application Azure App Service | Microsoft Docs"
description: "Découvrez comment ajouter un certificat SSL à votre application App Service."
services: app-service
documentationcenter: .net
author: ahmedelnably
manager: stefsch
editor: cephalin
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: apurvajo
ms.openlocfilehash: 191dd7240ad15b4936a72bc27a2d0162350f3afb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Acheter et configurer un certificat SSL pour votre service Azure App Service

Dans ce didacticiel, vous allez sécuriser votre application web en achetant un certificat SSL pour votre **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)**, en le stockant dans [Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) de manière sécurisée, et en l’associant à un domaine personnalisé.

## <a name="step-1---log-in-to-azure"></a>Étape 1 : Se connecter à Azure

Connectez-vous au portail Azure à l’adresse http://portal.azure.com.

## <a name="step-2---place-an-ssl-certificate-order"></a>Étape 2 : Passer une commande de certificat SSL

Vous pouvez passer une commande de certificat SSL en créant un [Certificat App Service](https://portal.azure.com/#create/Microsoft.SSL) dans le **portail Azure**.

![Création du certificat](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Entrez un **nom** convivial pour votre certificat SSL et entrez le **nom de domaine**.

> [!NOTE]
> Il s’agit de l’une des parties les plus critiques du processus d’achat. Veillez à entrer un nom d’hôte correct (domaine personnalisé) que vous voulez protéger avec ce certificat. **PAS** le nom d’hôte avec WWW. 
>

Sélectionnez votre **abonnement**, **groupe de ressources** et **référence (SKU) de certificat**

> [!WARNING]
> Les certificats App Service peuvent uniquement être utilisés par d’autres services App Service au sein du même abonnement.  
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>Étape 3 : Stocker le certificat dans Azure Key Vault

> [!NOTE]
> [Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) est un service Azure qui permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud.
>

Une fois l’achat du certificat SSL terminé, vous devez ouvrir le panneau des ressources [Certificats App Service](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders).

![insérer une image de prêt à stocker dans KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

Vous remarquerez que l’état du certificat est **« Émission en attente »** , car vous devez effectuer quelques étapes supplémentaires avant de commencer à utiliser ce certificat.

Cliquez sur **Configuration du certificat** dans le panneau Propriétés du certificat, puis sur **Étape 1 : Stocker** pour stocker ce certificat dans Azure Key Vault.

Dans le panneau **État de Key Vault**, cliquez sur **Référentiel Key Vault** pour choisir un coffre de clés existant destiné à stocker ce certificat **OU Créer un coffre de clés** pour créer un coffre de clés dans les mêmes abonnement et groupe de ressources.

> [!NOTE]
> Azure Key Vault engendre peu de frais pour le stockage de ce certificat.
> Pour plus d’informations, consultez **[Tarification d’Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)**.
>

Une fois que le référentiel Key Vault où stocker le certificat est sélectionné, l’option **Stocker** doit indiquer que l’opération a réussi.

![insérer une image de réussite du stockage dans KV](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>Étape 4 : Vérifier la propriété du domaine

> [!NOTE]
> Il existe 3 types de vérification du domaine pris en charge par les certificats App Service : domaine, e-mail et manuelle. Elles sont décrites plus en détail dans la [section Avancé](#advanced).

Dans le panneau **Configuration du certificat** utilisé à l’étape 3, cliquez sur **Étape 2 : Vérifier**.

**Vérification du domaine** Il s’agit du processus le plus pratique **seulement si** vous avez **[acheté votre domaine personnalisé à partir d’Azure App Service.](custom-dns-web-site-buydomains-web-app.md)**
Cliquez sur le bouton **Vérifier** pour terminer cette étape.

![insérer une image de vérification du domaine](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

Après avoir cliqué sur **Vérifier**, utilisez le bouton **Actualiser** jusqu’à ce que l’option **Vérifier** indique que l’opération a réussi.

![insérer une image de réussite de la vérification dans KV](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>Étape 5 : Attribuer un certificat à une application App Service

> [!NOTE]
> Avant de suivre les étapes de cette section, vous devez avoir associé un nom de domaine personnalisé à votre application. Pour plus d’informations, consultez la page **[Configuration d’un nom de domaine personnalisé pour une application web.](app-service-web-tutorial-custom-domain.md)**
>

Dans le **[portail Azure](https://portal.azure.com/)**, cliquez sur l’option **App Service** sur le côté gauche de la page.

Cliquez sur le nom de votre application à laquelle vous voulez attribuer ce certificat.

Dans les **Paramètres**, cliquez sur **Certificats SSL**.

Cliquez sur **Importer un certificat App Service**, puis sélectionnez le certificat que vous venez d’acheter.

![insérer une image d’importation de certificat](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Dans la section **Liaisons SSL**, cliquez sur **Ajouter des liaisons** et utilisez les listes déroulantes pour sélectionner le nom de domaine à sécuriser à l’aide du protocole SSL, ainsi que le certificat à utiliser. Vous pouvez également indiquer si vous voulez utiliser **[l’indication du nom du serveur (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ou le protocole SSL basé sur IP.

![insérer une image de liaisons SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

Cliquez sur **Ajouter une liaison** pour enregistrer les modifications et activer SSL.

> [!NOTE]
> Si vous avez sélectionné **SSL basé sur IP** et que votre domaine personnalisé est configuré à l’aide d’un enregistrement A, vous devez effectuer les étapes supplémentaires suivantes. Elles sont décrites plus en détail dans la [section Avancé](#Advanced).

À ce stade, vous devez être en mesure de visiter votre application en utilisant `HTTPS://` à la place de `HTTP://` pour vérifier que le certificat a été configuré correctement.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>Étape 6 : Tâches de gestion

### <a name="azure-cli"></a>Interface de ligne de commande Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>Avancé

### <a name="verifying-domain-ownership"></a>Vérifier la propriété du domaine

Il existe 2 autres types de vérification du domaine pris en charge par les certificats App Service : e-mail et manuelle.

#### <a name="mail-verification"></a>Vérification par e-mail

Un e-mail de vérification a déjà été envoyé aux adresses e-mail associées à ce domaine personnalisé.
Pour terminer l’étape de vérification par e-mail, ouvrez l’e-mail, puis cliquez sur le lien de vérification.

![insérer une image de vérification par e-mail](./media/app-service-web-purchase-ssl-web-site/KVVerifyEmailSuccess.png)

Si vous avez besoin de renvoyer l’e-mail de vérification, cliquez sur le bouton **Renvoyer le message**.

#### <a name="manual-verification"></a>Vérification manuelle

> [!IMPORTANT]
> Vérification de page web HTML (fonctionne uniquement avec la référence du certificat standard)
>

1. Créez un fichier HTML nommé **« starfield.html »**

1. Le contenu de ce fichier doit être le nom exact du jeton de vérification du domaine. (Vous pouvez copier le jeton à partir du panneau d’état de la vérification du domaine.)

1. Chargez ce fichier à la racine du serveur web qui héberge votre domaine `/.well-known/pki-validation/starfield.html`

1. Cliquez sur **« Actualiser »** pour mettre à jour l’état du certificat une fois la vérification terminée. Cette vérification peut prendre quelques minutes.

> [!TIP]
> Effectuez la vérification dans un terminal utilisant `curl -G http://<domain>/.well-known/pki-validation/starfield.html`. La réponse doit contenir le `<verification-token>`.

#### <a name="dns-txt-record-verification"></a>Vérification d’enregistrement TXT DNS

1. À l’aide de votre Gestionnaire DNS, créez un enregistrement TXT sur le sous-domaine `@` avec une valeur égale au jeton de vérification du domaine.
1. Cliquez sur **« Actualiser »** pour mettre à jour l’état du certificat une fois la vérification terminée.

> [!TIP]
> Vous devez créer un enregistrement TXT sur `@.<domain>` avec la valeur `<verification-token>`.

### <a name="assign-certificate-to-app-service-app"></a>Attribuer un certificat à une application App Service

Si vous avez sélectionné **SSL basé sur IP** et que votre domaine personnalisé est configuré à l’aide d’un enregistrement A, vous devez effectuer les étapes supplémentaires suivantes :

Une fois la liaison SSL basée sur IP configurée, une adresse IP dédiée est attribuée à votre application. Vous trouverez cette adresse IP sur la page des **domaines personnalisés** sous les paramètres de votre application, juste au-dessus de la section **Noms d’hôtes**. Elle est répertoriée en tant qu’**Adresse IP externe**

![insérer une image d’IP SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

Cette adresse IP est différente de celle utilisée précédemment pour configurer l’enregistrement A de votre domaine. Si vous utilisez le protocole SSL basé sur SNI ou que vous n’utilisez pas SSL, aucune adresse n’est indiquée pour cette entrée.

À l’aide des outils fournis par votre bureau d’enregistrement, modifiez l’enregistrement A de votre nom de domaine personnalisé de manière à ce qu’il pointe vers l’adresse IP spécifiée lors de l’étape précédente.

## <a name="rekey-and-sync-the-certificate"></a>Renouveler la clé du certificat et le synchroniser

Si vous avez besoin de renouveler la clé de votre certificat, sélectionnez l’option **Recréer la clé et synchroniser** à partir du panneau **Propriétés du certificat**.

Cliquez sur le bouton **Renouveler la clé** pour lancer le processus. Ce processus peut prendre de 1 à 10 minutes.

![insérer une image de renouvellement de clé SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Le renouvellement de la clé de votre certificat remplace le certificat par un nouveau certificat émis par l’autorité de certification.

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter un réseau de distribution de contenu](app-service-web-tutorial-content-delivery-network.md)