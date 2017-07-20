---
title: "Lier un certificat SSL existant à des applications web Azure | Microsoft Docs"
description: "Découvrez comment lier un certificat SSL personnalisé à votre application web, un backend d’application mobile ou une application API dans Azure App Service."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: f344ef59c3d6f9f99989a37e78f161b8be948916
ms.contentlocale: fr-fr
ms.lasthandoff: 06/26/2017

---
# <a name="bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>Lier un certificat SSL existant à des applications web Azure

Azure Web Apps fournit un service d’hébergement hautement évolutif et appliquant des mises à jour correctives automatiquement. Ce didacticiel vous montre comment lier un certificat SSL personnalisé acheté auprès d’une autorité de certification approuvée pour [Azure Web Apps](app-service-web-overview.md). Lorsque vous aurez terminé, vous serez en mesure d’accéder à votre application web au niveau du point de terminaison HTTPS de votre domaine DNS personnalisé.

![Application Web avec certificat SSL personnalisé](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Mettre à jour le niveau de tarification de votre application
> * Lier votre certificat SSL personnalisé à App Service
> * Appliquer le protocole HTTPS à votre application
> * Automatiser la liaison de certificat SSL avec des scripts

> [!NOTE]
> Si vous avez besoin d’un certificat SSL personnalisé, vous pouvez en obtenir un directement dans le portail Azure et le lier à votre application web. Suivez le [didacticiel Certificats App Service](web-sites-purchase-ssl-web-site.md).

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel :

- [Création d’une application App Service](/azure/app-service/)
- [Mappage d’un nom DNS personnalisé à une application web](app-service-web-tutorial-custom-domain.md)
- Acquisition d’un certificat SSL auprès d’une autorité de certification approuvée

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Conditions requises pour le certificat SSL

Pour l’utiliser dans App Service, un certificat doit remplir toutes les conditions suivantes :

* Être signé par une autorité de certification approuvée
* Être exporté sous la forme d’un fichier PFX protégé par mot de passe
* Contenir une clé privée d’au moins 2048 bits de long
* Contenir tous les certificats intermédiaires dans la chaîne de certificats

> [!NOTE]
> Les **certificats de chiffrement à courbe elliptique (ECC)** sont compatibles avec App Service, mais ce sujet sort du cadre de cet article. Consultez votre autorité de certification sur les étapes à suivre pour créer des certificats ECC.

## <a name="prepare-your-web-app"></a>Préparation de votre application web

Pour lier un certificat SSL personnalisé à votre application web, votre [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) doit se trouver dans le niveau **De base**, **Standard** ou **Premium**. Au cours de cette étape, vous allez vous assurer que votre application web se trouve dans le niveau de tarification pris en charge.

### <a name="log-in-to-azure"></a>Connexion à Azure

Ouvrez le [portail Azure](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Accès à votre application web

Dans le menu de gauche, cliquez sur **App Services** puis sur le nom de votre application web.

![Sélectionner de l’application web](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Vous accédez à la page de gestion de votre application web.  

### <a name="check-the-pricing-tier"></a>Vérification du niveau tarifaire

Dans la navigation de gauche de la page de votre application web, accédez à la section **Paramètres** et sélectionnez **Monter en puissance (plan App Service)**.

![Menu Monter en puissance](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Vérifiez que votre application ne se trouve pas dans le niveau **Gratuit** ou **Partagé**. Le niveau actuel de votre application web est encadré d’un rectangle bleu foncé.

![Vérification du niveau de tarification](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

Le SSL personnalisé n’est pas pris en charge aux niveaux **Gratuit** et **Partagé**. Si vous avez besoin de monter en puissance, consultez la section ci-après. Sinon, fermez la page **Choisir votre niveau de tarification** et passez à [Charger et lier votre certificat SSL](#upload).

### <a name="scale-up-your-app-service-plan"></a>Évolution de votre plan App Service

Sélectionnez l’un des niveaux **De base**, **Standard** ou **Premium**.

Cliquez sur **Sélectionner**.

![Sélection du niveau tarifaire](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

Lorsque la notification suivante s’affiche, cela signifie que la montée en charge est terminée.

![Notification de montée en puissance](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Liaison de votre certificat SSL

Vous êtes prêt à charger votre certificat SSL dans votre application web.

### <a name="merge-intermediate-certificates"></a>Fusionner les certificats intermédiaires

Si votre autorité de certification vous donne plusieurs certificats dans la chaîne, vous devez les fusionner dans l’ordre. 

Pour ce faire, ouvrez chaque certificat reçu dans un éditeur de texte. 

Créez un fichier pour le certificat fusionné, appelé _mergedcertificate.crt_. Dans un éditeur de texte, copiez le contenu de chaque certificat dans ce fichier. L’ordre de vos certificats doit être conforme au modèle suivant :

```
-----BEGIN CERTIFICATE-----
<your Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportation du certificat vers PFX

Exportez votre certificat SSL fusionné avec la clé privée ayant servi à générer votre demande de certificat.

Si vous avez généré votre demande de certificat à l’aide d’OpenSSL, vous avez créé un fichier de clé privée. Pour exporter votre certificat au format PFX, exécutez la commande suivante : Remplacez les espaces réservés _&lt;private-key-file>_ et  _&lt;merged-certificate-file>_.

```
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Lorsque vous y êtes invité, définissez un mot de passe d’exportation. Vous allez utiliser ce mot de passe lors du chargement de votre certificat SSL dans App Service.

Si vous avez utilisé IIS ou _Certreq.exe_ pour générer votre demande de certificat, installez le certificat sur votre ordinateur local, puis [exportez le certificat au format PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>Chargement de votre certificat SSL

Pour charger votre certificat SSL, cliquez sur **Certificats SSL** dans le volet de navigation gauche de votre application web.

Cliquez sur **Charger le certificat**.

Dans **Fichier de certificat PFX**, sélectionnez votre fichier PFX. Dans **Mot de passe du certificat**, tapez le mot de passe que vous avez créé lors de l’exportation du fichier PFX.

Cliquez sur **Télécharger**.

![Téléchargement d’un certificat](./media/app-service-web-tutorial-custom-ssl/upload-certificate.png)

Lorsque App Service finit de charger votre certificat, celui-ci apparaît dans la page **Certificats SSL**.

![Certificat chargé](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Liaison de votre certificat SSL

Dans la section **Liaisons SSL**, cliquez sur **Ajouter une liaison**.

Dans la page **Ajouter une liaison SSL**, utilisez les listes déroulantes pour sélectionner le nom de domaine à sécuriser et le certificat à utiliser.

> [!NOTE]
> Si vous avez chargé votre certificat mais que vous ne voyez pas le ou les noms de domaine dans la liste déroulante **Nom d’hôte**, essayez d’actualiser la page du navigateur.
>
>

Dans **Type SSL**, choisissez d’utiliser **[l’indication du nom du serveur (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ou le protocole SSL basé sur IP.

- **SSL basé sur SNI** : plusieurs liaisons SSL basées sur SNI peuvent être ajoutées. Cette option permet de sécuriser plusieurs domaines sur la même adresse IP avec plusieurs certificats SSL. La plupart des navigateurs actuels (y compris Internet Explorer, Chrome, Firefox et Opera) prennent en charge SNI (plus d’informations sur la prise en charge des navigateurs dans [Indication du nom du serveur](http://wikipedia.org/wiki/Server_Name_Indication)).
- **SSL basé sur IP** : une seule liaison SSL basée sur IP peut être ajoutée. Cette option permet de sécuriser une adresse IP publique dédiée avec un seul certificat SSL. Pour sécuriser plusieurs domaines, vous devez tous les sécuriser en utilisant le même certificat SSL. Cette option est sélectionnée par défaut pour la liaison SSL.

Cliquez sur **Ajouter une liaison**.

![Liaison d’un certificat SSL](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

Lorsque App Service finit de charger votre certificat, celui-ci apparaît dans les sections **Liaisons SSL**.

![Certificat lié à une application web](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Nouveau mappage d’un enregistrement pour SSL IP

Si vous n’utilisez pas un SSL basé sur IP dans votre application web, passez à [Tester HTTPS pour votre domaine personnalisé](#test).

Par défaut, votre application web utilise une adresse IP publique partagée. Dès que vous liez un certificat avec SSL basé sur IP, App Service crée une adresse IP dédiée pour votre application web.

Si vous avez mappé un enregistrement A à votre application web, mettez à jour le registre de domaine avec cette nouvelle adresse IP dédiée.

La page **Domaine personnalisé** de votre application web est mise à jour avec la nouvelle adresse IP dédiée. [Copiez cette adresse IP](app-service-web-tutorial-custom-domain.md#info), puis [mappez à nouveau l’enregistrement A](app-service-web-tutorial-custom-domain.md#map-an-a-record) à cette nouvelle adresse IP.

<a name="test"></a>

## <a name="test-https"></a>Test du protocole HTTPS

Il ne reste plus maintenant qu’à vous assurer que HTTPS fonctionne pour votre domaine personnalisé. Dans différents navigateurs, accédez à `https://<your.custom.domain>` pour vérifier qu’il fournit votre application web.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Si votre application web permet de voir les erreurs de validation de certificat, vous utilisez probablement un certificat auto-signé.
>
> Si ce n’est pas le cas, vous pouvez avoir oublié des certificats intermédiaires lorsque vous avez exporté votre certificat vers le fichier PFX.

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Appliquer le protocole HTTPS

App Service n’appliquant *pas* le protocole HTTPS, tout le monde peut accéder à votre application à l’aide de HTTP. Pour appliquer HTTPS à votre application web, définissez une règle de réécriture dans le fichier _web.config_ de votre application web. App Service utilise ce fichier, quelle que soit l’infrastructure de langage de votre application web.

> [!NOTE]
> Certaines redirections de requête sont propres au langage. ASP.NET MVC peut utiliser le filtre [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) au lieu de la règle de réécriture dans le fichier _web.config_.

Si vous êtes un développeur .NET, vous connaissez probablement ce fichier. Il se trouve à la racine de votre solution.

Ou, si vous développez avec PHP, Node.js, Python ou Java, il est possible que ce fichier ait été généré en votre nom dans App Service.

Connectez-vous au point de terminaison FTP de votre application web en suivant les instructions fournies dans [Déployer votre application dans Azure App Service avec FTP/S](app-service-deploy-ftp.md).

Ce fichier doit se trouver dans _/home/site/wwwroot_. Dans le cas contraire, créez un fichier _web.config_ dans ce dossier avec le code XML suivant :

```xml   
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <!-- BEGIN rule ELEMENT FOR HTTPS REDIRECT -->
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
        <!-- END rule ELEMENT FOR HTTPS REDIRECT -->
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

Pour un fichier _web.config_ existant, copiez l’intégralité de l’élément `<rule>` dans l’élément `configuration/system.webServer/rewrite/rules` de votre fichier _web.config_. Si d’autres éléments `<rule>` sont présents dans votre fichier _web.config`<rule>`, placez_  avant les autres éléments `<rule>`.

Cette règle renvoie un HTTP 301 (redirection permanente) vers le protocole HTTPS chaque fois que l’utilisateur envoie une requête HTTP à votre application web. Par exemple, elle redirige de `http://contoso.com` vers `https://contoso.com`.

Pour plus d'informations sur le module Réécriture d'URL d'IIS, consultez la documentation sur la [Réécriture d'URL](http://www.iis.net/downloads/microsoft/url-rewrite) .

## <a name="enforce-https-for-web-apps-on-linux"></a>Mettre en œuvre HTTPS pour Web Apps sous Linux

App Service sous Linux n’appliquant *pas* le protocole HTTPS, tout le monde peut accéder à votre application web à l’aide de HTTP. Pour appliquer HTTPS à votre application web, définissez une règle de réécriture dans le fichier _.htaccess_ de votre application web. 

Connectez-vous au point de terminaison FTP de votre application web en suivant les instructions fournies dans [Déployer votre application dans Azure App Service avec FTP/S](app-service-deploy-ftp.md).

Dans _/home/site/wwwroot_, créez un fichier _.htaccess_ avec le code suivant :

```
RewriteEngine On
RewriteCond %{HTTP:X-ARR-SSL} ^$
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```

Cette règle renvoie un HTTP 301 (redirection permanente) vers le protocole HTTPS chaque fois que l’utilisateur envoie une requête HTTP à votre application web. Par exemple, elle redirige de `http://contoso.com` vers `https://contoso.com`.

## <a name="automate-with-scripts"></a>Automatisation à l’aide de scripts

Vous pouvez automatiser les liaisons SSL de votre application web à l’aide de scripts, en utilisant [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Interface de ligne de commande Azure

La commande suivante charge un fichier PFX exporté et obtient l’empreinte.

```bash
thumbprint=$(az appservice web config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

La commande suivante ajoute une liaison SSL basée sur SNI à l’aide de l’empreinte de la commande précédente.

```bash
az appservice web config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

### <a name="azure-powershell"></a>Azure PowerShell

La commande suivante charge un fichier PFX exporté et ajoute une liaison SSL basée sur SNI.

```PowerShell
New-AzureRmWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Mettre à jour le niveau de tarification de votre application
> * Lier votre certificat SSL personnalisé à App Service
> * Appliquer le protocole HTTPS à votre application
> * Automatiser la liaison de certificat SSL avec des scripts

Passez au didacticiel suivant pour découvrir comment utiliser un réseau de distribution de contenu Azure.

> [!div class="nextstepaction"]
> [Ajouter un réseau de distribution de contenu (CDN) à un Azure App Service](app-service-web-tutorial-content-delivery-network.md)

