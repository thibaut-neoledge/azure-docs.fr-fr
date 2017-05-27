---
title: "Mapper un nom DNS personnalisé existant à des applications web Azure | Microsoft Docs"
description: "Découvrez comment ajouter un nom de domaine DNS personnalisé existant (domaine personnel) à une application web, au serveur principal d’une application mobile ou à une application API dans Azure App Service."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/04/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 000440fb2c38eadc0ffdcab84a3c23bb034e834f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Mapper un nom DNS personnalisé existant à des applications web Azure

Ce didacticiel vous montre comment mapper un nom DNS personnalisé existant à des [applications web Azure](app-service-web-overview.md). 

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Mapper un sous-domaine (par exemple, `www.contoso.com`) à l’aide d’un enregistrement CNAME
> * Mapper un domaine racine (par exemple, `contoso.com`) à l’aide d’un enregistrement A
> * Mapper un domaine générique (par exemple, `*.contoso.com`) à l’aide d’un enregistrement CNAME
> * Automatiser le mappage de domaine à l’aide de scripts

Vous pouvez utiliser un **enregistrement CNAME** ou un **enregistrement A** pour mapper un nom DNS personnalisé à App Service.

> [!NOTE]
> Nous vous recommandons d’utiliser un enregistrement CNAME pour tous les noms DNS personnalisés, à l’exception d’un domaine racine (par exemple, `contoso.com`). 
> 
> 

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel, vous devez accéder à votre registre DNS pour votre fournisseur de domaine (tel que GoDaddy) et disposer des autorisations pour modifier la configuration de votre domaine. 

Par exemple, pour ajouter des entrées DNS pour `contoso.com` et `www.contoso.com`, vous devez pouvoir configurer les paramètres DNS du domaine racine `contoso.com`. 

> [!NOTE]
> Si vous n’avez pas de nom de domaine existant, suivez le [didacticiel sur le domaine App Service](custom-dns-web-site-buydomains-web-app.md) pour acheter un domaine à l’aide du portail Azure. 
>
>

## <a name="prepare-your-app"></a>Préparation de votre application
Pour mapper un nom DNS personnalisé, votre [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) doit être un niveau payant (**Partagé**, **De base**, **Standard** ou **Premium**). Au cours de cette étape, vous allez vous assurer que votre application App Service se trouve dans le niveau de tarification pris en charge.

### <a name="sign-in-to-azure"></a>Connexion à Azure

Ouvrez le portail Azure. Pour ce faire, connectez-vous au portail : [https://portal.azure.com](https://portal.azure.com) avec votre compte Azure.

### <a name="navigate-to-your-app"></a>Accès à votre application
Dans le menu de gauche, cliquez sur **App Services**, puis cliquez sur le nom de votre application.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Vous êtes maintenant dans le panneau de gestion de votre application App Service (_panneau_ : page de portail qui s’ouvre horizontalement).  

### <a name="check-the-pricing-tier"></a>Vérification du niveau tarifaire

Dans la navigation gauche du panneau de l’application, faites défiler jusqu’à la section **Paramètres** et sélectionnez **Monter en puissance (plan App Service)**.

![Menu Monter en puissance](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Vérifiez que votre application ne se trouve pas dans le niveau **Gratuit**. Le niveau actuel de votre application est encadré d’un rectangle bleu foncé. 

![Vérifier le niveau de tarification](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Les DNS personnalisés ne sont pas pris en charge dans le niveau **Gratuit**. Si vous avez besoin de faire évoluer votre plan, consultez la section ci-après. Sinon, fermez le panneau **Choisir votre niveau de tarification** et passez à [Mapper un enregistrement CNAME](#cname) ou [Mapper un enregistrement A](#a).

### <a name="scale-up-your-app-service-plan"></a>Évolution de votre plan App Service

Sélectionnez l’un des niveaux payants (**Partagé**, **De base**, **Standard** ou **Premium**). 

Cliquez sur **Sélectionner**.

![Vérifier le niveau de tarification](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Lorsque la notification ci-dessous s’affiche, cela signifie que l’opération est terminée.

![Confirmation d’opération de mise à l’échelle](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>Mapper un enregistrement CNAME

Dans l’exemple de ce didacticiel, vous souhaitez ajouter un enregistrement CNAME pour le sous-domaine `www` (`www.contoso.com`). 

### <a name="access-dns-records-with-domain-provider"></a>Accès aux enregistrements DNS avec le fournisseur de domaine

Connectez-vous d’abord au site web de votre fournisseur de domaine.

Trouvez la page de gestion des enregistrements DNS. Chaque fournisseur de domaine ayant sa propre interface d’enregistrements DNS, vous devez consulter la documentation de votre fournisseur. Recherchez la mention **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**. 

Vous trouvez généralement un lien en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**. Recherchez ensuite un lien vous permettant de gérer des enregistrements DNS. Ce lien peut être nommé **Fichier de zone**, **Enregistrements DNS** ou **Configuration avancée**.

La capture d’écran suivante est un exemple d’une page d’enregistrements DNS :

![Exemple de page d’enregistrements DNS](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

Dans la capture d’écran de l’exemple, vous cliquez sur **Ajouter** pour créer un enregistrement. Certains fournisseurs ont différents liens pour ajouter divers types d’enregistrements. Là encore, consultez la documentation de votre fournisseur.

> [!NOTE]
> Pour certains fournisseurs tels que GoDaddy, les modifications apportées aux enregistrements DNS n’entrent en vigueur que lorsque vous cliquez sur un lien **Enregistrer les modifications** distinct. 
>
>

### <a name="create-the-cname-record"></a>Créer un enregistrement CNAME

Ajoutez un enregistrement CNAME pour mapper un sous-domaine au nom d’hôte par défaut de votre application (`<app_name>.azurewebsites.net`).

Pour l’exemple de domaine `www.contoso.com`, votre enregistrement CNAME doit pointer le nom `www` vers `<app_name>.azurewebsites.net`.

Votre page d’enregistrements DNS doit ressembler à la capture d’écran suivante :

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>Activer le mappage d’enregistrement CNAME dans votre application

Vous êtes maintenant prêt à ajouter votre nom DNS configuré à votre application.

Dans la navigation gauche du panneau de l’application, cliquez sur **Domaines personnalisés**. 

![Menu Domaines personnalisés](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Dans le panneau **Domaines personnalisés** de votre application, vous devez ajouter le nom DNS personnalisé complet (`www.contoso.com`) à la liste.

Cliquez sur l’icône **+** en regard de **Ajouter un nom d’hôte**.

![Ajouter un nom d’hôte](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Tapez le nom de domaine complet pour lequel vous avez configuré l’enregistrement CNAME précédemment (par exemple `www.contoso.com`), puis cliquez sur **Valider**.

Dans le cas contraire, le bouton **Ajouter un nom d’hôte** est activé. 

Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **Enregistrement CNAME (exemple.com)**.

Cliquez sur **Ajouter un nom d’hôte** pour ajouter le nom DNS à votre application.

![Ajouter un nom DNS à l’application](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Un certain temps peut être nécessaire pour que votre nouveau nom d’hôte soit reflété sur la page **Domaines personnalisés** de votre application. Essayez d’actualiser le navigateur pour mettre à jour les données.

![Enregistrement CNAME ajouté](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Si vous avez raté une étape ou fait une faute de frappe à un endroit, une erreur de vérification peut apparaître au bas de la page.

![Erreur de vérification](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>Mapper un enregistrement A

Dans l’exemple de ce didacticiel, vous souhaitez ajouter un enregistrement A pour le domaine racine, `contoso.com`. 

<a name="info"></a>

### <a name="copy-your-apps-ip-address"></a>Copier l’adresse IP de votre application

Pour mapper un enregistrement A, vous avez besoin de l’adresse IP externe de votre application. Celle-ci se trouve dans le panneau **Domaines personnalisés**.

Dans la navigation gauche du panneau de l’application, cliquez sur **Domaines personnalisés**. 

![Menu Domaines personnalisés](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Dans la page **Domaines personnalisés**, copiez l’adresse IP de l’application.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="access-dns-records-with-domain-provider"></a>Accès aux enregistrements DNS avec le fournisseur de domaine

Connectez-vous d’abord au site web de votre fournisseur de domaine.

Trouvez la page de gestion des enregistrements DNS. Chaque fournisseur de domaine ayant sa propre interface d’enregistrements DNS, vous devez consulter la documentation de votre fournisseur. Recherchez la mention **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**. 

Vous trouvez généralement un lien en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**. Recherchez ensuite un lien vous permettant de gérer des enregistrements DNS. Ce lien peut être nommé **Fichier de zone**, **Enregistrements DNS** ou **Configuration avancée**.

La capture d’écran suivante est un exemple d’une page d’enregistrements DNS :

![Exemple de page d’enregistrements DNS](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

Dans la capture d’écran de l’exemple, vous cliquez sur **Ajouter** pour créer un enregistrement. Certains fournisseurs ont différents liens pour ajouter divers types d’enregistrements. Là encore, consultez la documentation de votre fournisseur.

> [!NOTE]
> Pour certains fournisseurs tels que GoDaddy, les modifications apportées aux enregistrements DNS n’entrent en vigueur que lorsque vous cliquez sur un lien **Enregistrer les modifications** distinct. 
>
>

<a name="create-a"></a>

### <a name="create-the-a-record"></a>Créer l’enregistrement A

Pour mapper un enregistrement A à votre application, App Service a besoin de **deux** enregistrements DNS :

- Un enregistrement **A** pour effectuer un mappage vers l’adresse IP de votre application.
- Un enregistrement **TXT** pour effectuer un mappage vers le nom d’hôte par défaut de votre application `<app_name>.azurewebsites.net`. Cet enregistrement permet à App Service de vérifier que vous êtes propriétaire du domaine personnalisé que vous souhaitez mapper.

Pour l’exemple de domaine `www.contoso.com`, créez les enregistrements A et TXT d’après le tableau suivant (`@` représente généralement le domaine racine). 

| Type d’enregistrement | Host | Valeur |
| - | - | - |
| A | `@` | Adresse IP de [Copier l’adresse IP de votre application](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

Votre page d’enregistrements DNS doit ressembler à la capture d’écran suivante :

![Page Enregistrements DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-your-app"></a>Activer le mappage d’enregistrement A dans votre application

Vous êtes maintenant prêt à ajouter votre nom DNS configuré à votre application.

De retour sur la page **Domaines personnalisés** de votre application dans le portail Azure, vous devez ajouter à la liste le nom DNS personnalisé complet (`contoso.com`).

Cliquez sur l’icône **+** en regard de **Ajouter un nom d’hôte**.

![Ajouter un nom d’hôte](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Tapez le nom de domaine complet pour lequel vous avez configuré l’enregistrement A précédemment (par exemple `contoso.com`), puis cliquez sur **Valider**.

Dans le cas contraire, le bouton **Ajouter un nom d’hôte** est activé. 

Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **Enregistrement A (exemple.com)**.

Cliquez sur **Ajouter un nom d’hôte** pour ajouter le nom DNS à votre application.

![Ajouter un nom DNS à l’application](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Un certain temps peut être nécessaire pour que votre nouveau nom d’hôte soit reflété sur la page **Domaines personnalisés** de votre application. Essayez d’actualiser le navigateur pour mettre à jour les données.

![Enregistrement A ajouté](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

Si vous avez raté une étape ou fait une faute de frappe à un endroit, une erreur de vérification peut apparaître au bas de la page.

![Erreur de vérification](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>Mapper un domaine générique

Vous pouvez également mapper un [DNS générique](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (par exemple, `*.contoso.com`) à votre application App Service. 

La procédure suivante vous montre comment mapper le domaine générique `*.contoso.com` à l’aide d’un enregistrement CNAME. 

### <a name="access-dns-records-with-domain-provider"></a>Accès aux enregistrements DNS avec le fournisseur de domaine

Connectez-vous d’abord au site web de votre fournisseur de domaine.

Trouvez la page de gestion des enregistrements DNS. Chaque fournisseur de domaine ayant sa propre interface d’enregistrements DNS, vous devez consulter la documentation de votre fournisseur. Recherchez la mention **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**. 

Vous trouvez généralement un lien en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**. Recherchez ensuite un lien vous permettant de gérer des enregistrements DNS. Ce lien peut être nommé **Fichier de zone**, **Enregistrements DNS** ou **Configuration avancée**.

La capture d’écran suivante est un exemple d’une page d’enregistrements DNS :

![Exemple de page d’enregistrements DNS](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

Dans la capture d’écran de l’exemple, vous cliquez sur **Ajouter** pour créer un enregistrement. Certains fournisseurs ont différents liens pour ajouter divers types d’enregistrements. Là encore, consultez la documentation de votre fournisseur.

> [!NOTE]
> Pour certains fournisseurs tels que GoDaddy, les modifications apportées aux enregistrements DNS n’entrent en vigueur que lorsque vous cliquez sur un lien **Enregistrer les modifications** distinct. 
>
>

### <a name="create-the-cname-record"></a>Créer un enregistrement CNAME

Ajoutez un enregistrement CNAME pour mapper un nom générique au nom d’hôte par défaut de votre application (`<app_name>.azurewebsites.net`).

Pour l’exemple de domaine `*.contoso.com`, votre enregistrement CNAME doit pointer le nom `*` vers `<app_name>.azurewebsites.net`.

Votre page d’enregistrements DNS doit ressembler à la capture d’écran suivante :

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>Activer le mappage d’enregistrement CNAME dans votre application

Vous pouvez maintenant ajouter n’importe quel sous-domaine qui correspond à votre nom générique.

Pour l’exemple de nom générique `*.contoso.com`, vous pouvez maintenant ajouter `sub1.contoso.com` et `sub2.contoso.com`. 

Dans la navigation gauche du panneau de l’application, cliquez sur **Domaines personnalisés**. 

![Menu Domaines personnalisés](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Cliquez sur l’icône **+** en regard de **Ajouter un nom d’hôte**.

![Ajouter un nom d’hôte](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Tapez le nom de domaine complet d’un sous-domaine qui correspond à votre domaine générique (par exemple, `sub1.contoso.com`), puis cliquez sur **Valider**.

Dans le cas contraire, le bouton **Ajouter un nom d’hôte** est activé. 

Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **Enregistrement CNAME (exemple.com)**.

Cliquez sur **Ajouter un nom d’hôte** pour ajouter le nom DNS à votre application.

![Ajouter un nom DNS à l’application](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Un certain temps peut être nécessaire pour que votre nouveau nom d’hôte soit reflété sur la page **Domaines personnalisés** de votre application. Essayez d’actualiser le navigateur pour mettre à jour les données.

Vous pouvez cliquer de nouveau sur l’icône **+** pour ajouter un autre nom d’hôte qui correspond à votre domaine générique.

Par exemple, ajoutez `sub2.contoso.com` à l’aide de la procédure ci-dessus.

![Enregistrement CNAME ajouté](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>Test dans le navigateur

Dans votre navigateur, accédez aux noms DNS que vous avez configurés précédemment (`contoso.com` et `www.contoso.com`).

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="automate-with-scripts"></a>Automatiser des tâches à l’aide de scripts

Vous pouvez automatiser la gestion des domaines personnalisés à l’aide de scripts, en utilisant [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview). 

### <a name="azure-cli"></a>Interface de ligne de commande Azure 

La commande suivante ajoute un nom DNS personnalisé configuré à une application App Service. 

```bash 
az appservice web config hostname add \
    --webapp <app_name> \
    --resource-group <resourece_group_name> \ 
    --name <fully_qualified_domain_name> 
``` 

Pour plus d’informations, consultez [Mapper un nom de domaine personnalisé à une application web](scripts/app-service-cli-configure-custom-domain.md) 

### <a name="azure-powershell"></a>Azure PowerShell 

La commande suivante ajoute un nom DNS personnalisé configuré à une application App Service. 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resourece_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

Pour plus d’informations, consultez [Attribuer un domaine personnalisé à une application web](scripts/app-service-powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Mapper un sous-domaine à l’aide d’un enregistrement CNAME
> * Mapper un domaine racine à l’aide d’un enregistrement A
> * Mapper un domaine générique à l’aide d’un enregistrement CNAME
> * Automatiser le mappage de domaine à l’aide de scripts

Passez au didacticiel suivant pour découvrir comment lier un certificat SSL personnalisé à une application web.

> [!div class="nextstepaction"]
> [Lier un certificat SSL personnalisé existant à des applications web Azure](app-service-web-tutorial-custom-ssl.md)

