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
ms.date: 04/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 5bbdd1db655c080b4372f6728bb47207757209e4
ms.lasthandoff: 04/27/2017


---
# <a name="bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>Lier un certificat SSL existant à des applications web Azure

Ce didacticiel vous montre comment lier un certificat SSL personnalisé acheté auprès d’une autorité de certification approuvée pour [Azure Web Apps](app-service-web-overview.md). Lorsque vous aurez terminé, vous serez en mesure d’accéder à votre application web au niveau du point de terminaison HTTPS de votre domaine DNS personnalisé.

![Application Web avec certificat SSL personnalisé](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!TIP]
> Si vous avez besoin d’un certificat SSL personnalisé, vous pouvez en obtenir un directement dans le portail Azure et le lier à votre application web. Suivez le [didacticiel Certificats App Service](web-sites-purchase-ssl-web-site.md). 
>
> 

## <a name="before-you-begin"></a>Avant de commencer
Avant de suivre les instructions de ce didacticiel, vous devez avoir réalisé les étapes suivantes :

- [Création d’une application App Service](/azure/app-service/)
- [Mappage d’un nom DNS personnalisé à une application web](web-sites-custom-domain-name.md)
- Acquisition d’un certificat SSL auprès d’une autorité de certification approuvée

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Conditions requises pour le certificat SSL

Pour utiliser votre certificat dans le App Service, votre certificat doit remplir les conditions suivantes :

* Être signé par une autorité de certification approuvée
* Être exporté sous la forme d’un fichier PFX protégé par mot de passe
* Contenir une clé privée d’au moins 2048 bits de long
* Contenir tous les certificats intermédiaires dans la chaîne de certificats

> [!NOTE]
> **certificats de chiffrement à courbe elliptique (ECC)** peuvent fonctionner avec App Service, mais ce sujet n’est pas abordé dans cet article. Consultez votre autorité de certification sur les étapes à suivre pour créer des certificats ECC.
> 
>

## <a name="prepare-your-web-app"></a>Préparation de votre application web
Pour lier un certificat SSL personnalisé à votre application web, votre [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) doit se trouver dans le niveau **De base**, **Standard** ou **Premium**. Au cours de cette étape, vous allez vous assurer que votre application web se trouve dans le niveau de tarification pris en charge.

### <a name="log-in-to-azure"></a>Connexion à Azure

Ouvrez le portail Azure. Pour ce faire, connectez-vous au portail : [https://portal.azure.com](https://portal.azure.com) avec votre compte Azure.

### <a name="navigate-to-your-web-app"></a>Accès à votre application web
Dans le menu de gauche, cliquez sur **App Services**, puis cliquez sur le nom de votre application web.

![Sélectionner de l’application web](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Vous êtes maintenant dans le panneau de gestion de votre application web (_panneau_ : page de portail qui s’ouvre horizontalement).  

### <a name="check-the-pricing-tier"></a>Vérification du niveau tarifaire

Dans la navigation gauche du panneau de l’application web, faites défiler jusqu’à la section **Paramètres** et sélectionnez **Monter en puissance (plan App Service)**.

![Menu Monter en puissance](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Vérifiez que votre application ne se trouve pas dans le niveau **Gratuit** ou **Partagé**. Le niveau actuel de votre application web est encadré d’un rectangle bleu foncé. 

![Vérification du niveau de tarification](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

Le SSL personnalisé n’est pas pris en charge dans les niveaux **Gratuit** et **Partagé**. Si vous avez besoin de faire évoluer votre plan, consultez la section ci-après. Sinon, fermez le panneau **Choisir votre niveau de tarification** et passez à [Charger et lier votre certificat SSL](#upload).

### <a name="scale-up-your-app-service-plan"></a>Évolution de votre plan App Service

Sélectionnez l’un des niveaux **De base**, **Standard** ou **Premium**. 

Cliquez sur **Sélectionner**.

![Sélection du niveau tarifaire](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

Lorsque la notification ci-dessous s’affiche, cela signifie que l’opération est terminée.

![Notification de montée en puissance](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Liaison de votre certificat SSL

Vous êtes prêt à charger votre certificat SSL dans votre application web. 

### <a name="export-certificate-to-pfx"></a>Exportation du certificat vers PFX

Vous devez exporter votre certificat SSL personnalisé avec la clé privée ayant servi à générer votre demande de certificat.

Si vous avez généré votre demande de certificat à l’aide d’OpenSSL, vous avez créé une clé privée. Pour exporter votre certificat vers PFX, exécutez la commande suivante :

```bash
openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
```

Si vous avez utilisé IIS ou `Certreq.exe` pour générer votre demande de certificat, installez d’abord le certificat sur votre ordinateur local, puis exportez-le vers PFX en suivant les étapes de la section [Exportation d’un certificat avec la clé privée](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>Chargement de votre certificat SSL

Pour charger votre certificat SSL, cliquez sur **Certificats SSL** dans le volet de navigation gauche de votre application web.

Cliquez sur **Charger le certificat**.

Dans **fichier de certificat PFX**, sélectionnez votre fichier PFX. Dans **Mot de passe du certificat**, tapez le mot de passe que vous avez créé lors de l’exportation du fichier PFX.

Cliquez sur **Télécharger**.

![Téléchargement d’un certificat](./media/app-service-web-tutorial-custom-ssl/upload-certificate.png)

Lorsque App Service finit de charger votre certificat, celui-ci apparaît dans la page **Certificats SSL**.

![Certificat chargé](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Liaison de votre certificat SSL

Vous devriez maintenant voir votre certificat chargé dans la page **Certificat SSL** .

Dans la section **Liaisons SSL**, cliquez sur **Ajouter une liaison**.

Dans le panneau **Ajouter une liaison SSL** , utilisez les listes déroulantes pour sélectionner le nom de domaine à sécuriser, ainsi que le certificat à utiliser. 

Dans **Type SSL**, choisissez d’utiliser **[l’indication du nom du serveur (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ou le protocole SSL basé sur IP.
   
- **SSL basé sur SNI** : plusieurs liaisons SSL basées sur SNI peuvent être ajoutées. Cette option permet de sécuriser plusieurs domaines sur la même adresse IP avec plusieurs certificats SSL. La plupart des navigateurs actuels (y compris Internet Explorer, Chrome, Firefox et Opera) prennent en charge SNI (plus d’informations sur la prise en charge des navigateurs dans [Indication du nom du serveur](http://wikipedia.org/wiki/Server_Name_Indication)).
- **SSL basé sur IP** : une seule liaison SSL basée sur IP peut être ajoutée. Cette option permet de sécuriser une adresse IP publique dédiée avec un seul certificat SSL. Pour sécuriser plusieurs domaines, vous devez tous les sécuriser en utilisant le même certificat SSL. Cette option est sélectionnée par défaut pour la liaison SSL. 

Cliquez sur **Ajouter une liaison**.

![Liaison d’un certificat SSL](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

Lorsque App Service finit de charger votre certificat, celui-ci apparaît dans les sections **Liaisons SSL**.

![Certificat lié à une application web](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Nouveau mappage d’un enregistrement pour SSL IP

Si vous n’utilisez pas un SSL basé sur IP dans votre application web, passez à [Tester HTTPS pour votre domaine personnalisé](#test). 

Par défaut, votre application web utilise une adresse IP publique partagée. Dès que vous liez un certificat avec SSL basé sur IP, App Service crée une nouvelle adresse IP dédiée pour votre application web.

Si vous avez mappé un enregistrement A à votre application web, mettez à jour le registre de domaine avec cette nouvelle adresse IP dédiée.

La page **Domaine personnalisé** de votre application web est mise à jour avec la nouvelle adresse IP dédiée. [Copiez cette adresse IP](app-service-web-tutorial-custom-domain.md#info), puis [mappez à nouveau l’enregistrement A](app-service-web-tutorial-custom-domain.md#create-the-a-record) à cette nouvelle adresse IP.

<a name="test"></a>

## <a name="test-https"></a>Test du protocole HTTPS
Il ne reste plus maintenant qu’à vous assurer que HTTPS fonctionne pour votre domaine personnalisé. Dans différents navigateurs, accédez à `https://<your.custom.domain>` pour vérifier qu’il fournit votre application web.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Si votre application web permet de voir les erreurs de validation de certificat, vous utilisez probablement un certificat auto-signé.
>
> Si ce n’est pas le cas, vous pouvez avoir oublié des certificats intermédiaires lorsque vous avez exporté votre certificat vers le fichier PFX. 
>
>

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Appliquer le protocole HTTPS
Si vous souhaitez toujours autoriser l’accès HTTP à votre application web, ignorez cette étape. 

App Service n’appliquant *pas* le protocole HTTPS, tout le monde peut accéder à votre application à l’aide de HTTP. Pour appliquer le protocole HTTPS pour votre application web, vous pouvez définir une règle de réécriture dans le fichier `web.config` pour votre application web. App Service utilise ce fichier, quelle que soit l’infrastructure de langage de votre application web.

> [!NOTE]
> Certaines redirections de requête sont propres au langage. ASP.NET MVC peut utiliser le filtre [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) au lieu de la règle de réécriture dans `web.config` (voir [Déployer une application ASP.NET MVC 5 sécurisée vers une application web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)).
> 
> 

Si vous êtes un développeur .NET, vous connaissez probablement ce fichier. Il se trouve à la racine de votre solution.

Ou, si vous développez avec PHP, Node.js, Python ou Java, il est possible que ce fichier ait été généré en votre nom dans App Service.

Connectez-vous au point de terminaison FTP de votre application web en suivant les instructions fournies dans [Déployer votre application dans Azure App Service avec FTP/S](app-service-deploy-ftp.md). 

Ce fichier doit se trouver dans `/home/site/wwwroot`. Dans le cas contraire, créez un `web.config` dans ce dossier avec le code XML suivant :

```xml   
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
        <!-- END rule TAG FOR HTTPS REDIRECT -->
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

Pour un `web.config` existant, il suffit de copier l’intégralité de la balise `<rule>` dans l’élément `web.config` de votre `configuration/system.webServer/rewrite/rules`. S’il existe d’autres balises `<rule>` dans votre `web.config`, placez la balise `<rule>` copiée avant les autres balises `<rule>`.

Cette règle renvoie un HTTP 301 (redirection permanente) vers le protocole HTTPS chaque fois que l’utilisateur envoie une requête HTTP à votre application web. Par exemple, elle redirige de `http://contoso.com` vers `https://contoso.com`.

Pour plus d'informations sur le module Réécriture d'URL d'IIS, consultez la documentation sur la [Réécriture d'URL](http://www.iis.net/downloads/microsoft/url-rewrite) .

## <a name="automate-with-scripts"></a>Automatisation à l’aide de scripts

Vous pouvez automatiser les liaisons SSL de votre application web à l’aide de scripts, en utilisant [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Interface de ligne de commande Azure

La commande suivante charge un fichier PFX exporté et obtient l’empreinte. 

```bash
thumprint=$(az appservice web config ssl upload --certificate-file <path_to_PFX_file> \
--certificate-password <PFX_password> --name <app_name> --resource-group <resource_group_name> \
--query thumbprint --output tsv)
```

La commande suivante ajoute une liaison SSL basée sur SNI à l’aide de l’empreinte de la commande précédente.

```bash
az appservice web config ssl bind --certificate-thumbprint $thumbprint --ssl-type SNI \
--name <app_name> --resource-group <resource_group_name>
```

### <a name="azure-powershell"></a>Azure PowerShell

La commande suivante charge un fichier PFX exporté et ajoute une liaison SSL basée sur SNI.

```PowerShell
New-AzureRmWebAppSSLBinding -WebAppName <app_name> -ResourceGroupName <resource_group_name> -Name <dns_name> `
-CertificateFilePath <path_to_PFX_file> -CertificatePassword <PFX_password> -SslState SniEnabled
```
## <a name="more-resources"></a>Autres ressources
* [Centre de gestion de la confidentialité Microsoft Azure](/support/trust-center/security/)
* [Options de configuration déverrouillées dans Sites Web Azure](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
* [Activer la journalisation des diagnostics](web-sites-enable-diagnostic-log.md)
* [Configurer des applications web dans Azure App Service](web-sites-configure.md)

