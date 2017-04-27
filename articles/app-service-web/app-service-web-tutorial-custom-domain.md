---
title: "Mapper un nom DNS personnalisé à une application web Azure | Microsoft Docs"
description: "Découvrez comment ajouter un nom de domaine DNS personnalisé (p. ex. domaine personnel) à une application web, au back-end d’une application mobile ou à une application API dans Azure App Service."
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
ms.date: 03/29/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 25ddbd0c89ec7eee8eb240cf111245bb8b5add48
ms.lasthandoff: 04/04/2017


---
# <a name="map-a-custom-dns-name-to-an-azure-web-app"></a>Mapper un nom DNS personnalisé à une application web Azure

Ce didacticiel vous explique comment mapper un nom DNS personnalisé (p. ex. domaine personnel) à votre application web, back-end d’une application mobile ou application API dans [Azure App Service](../app-service/app-service-value-prop-what-is.md). 

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Ce didacticiel suit l’exemple de scénario de mappage de deux noms DNS à une application dans App Service :

- `contoso.com` - un domaine racine. Vous allez utiliser un [enregistrement A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) pour le mapper à Azure. 
- `www.contoso.com` - un sous-domaine de `contoso.com`. Vous pouvez utiliser un [enregistrement A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) ou un [enregistrement CNAME](https://en.wikipedia.org/wiki/CNAME_record).

Ce didacticiel vous montre également comment mapper un [DNS générique](https://en.wikipedia.org/wiki/Wildcard_DNS_record) à App Service (p. ex. `*.contoso.com`).

## <a name="before-you-begin"></a>Avant de commencer

Étant donné que ce didacticiel vous montre comment mapper un nom DNS à Azure App Service, vous devez disposer d’un accès administratif à la page de configuration DNS de votre fournisseur de domaine (p. ex. GoDaddy). Par exemple, pour ajouter un mappage pour `contoso.com` et `www.contoso.com`, vous devez être en mesure de configurer les entrées DNS pour `contoso.com`.

Si vous n’avez pas encore inscrit de domaine auprès d’un fournisseur de domaine, vous pouvez simplement [acheter un domaine directement à partir d’Azure et le mapper à votre application](custom-dns-web-site-buydomains-web-app.md).

## <a name="step-1---prepare-your-app"></a>Étape 1 - Préparation de votre application
Pour mapper un nom DNS personnalisé, votre [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) doit se trouver dans le niveau **Partagé** ou supérieur. Au cours de cette étape, vous allez vous assurer que votre application Azure se trouve dans le niveau de tarification pris en charge.

### <a name="log-in-to-azure"></a>Connexion à Azure

Ouvrez le portail Azure. 

Pour ce faire, connectez-vous au portail : [https://portal.azure.com](https://portal.azure.com) avec votre compte Azure.

### <a name="navigate-to-your-app"></a>Accès à votre application
Dans le menu de gauche, cliquez sur **App Service**, puis cliquez sur le nom de votre application Azure.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Vous accédez au _panneau_ de votre application (une page du portail qui s’ouvre horizontalement).  

### <a name="check-the-pricing-tier"></a>Vérification du niveau tarifaire
Sur la page **Vue d’ensemble** qui s’ouvre par défaut, vérifiez que votre application ne se trouve pas dans le niveau **Gratuit**.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Les DNS personnalisés ne sont pas pris en charge dans le niveau **Gratuit**. Si vous avez besoin de faire évoluer votre plan, consultez la section ci-après. Sinon, passez à [l’étape 2]().

### <a name="scale-up-your-app-service-plan"></a>Évolution de votre plan App Service

Pour faire évoluer votre plan, cliquez sur **Monter en puissance (plan App Service)** dans le volet gauche.

Sélectionnez le niveau que vous souhaitez. Par exemple, sélectionnez **Partagé**. Lorsque vous êtes prêt, cliquez sur **Sélectionner**.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Lorsque la notification ci-dessous s’affiche, cela signifie que l’opération est terminée.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="info"></a>

## <a name="step-2---get-mapping-information"></a>Étape 2 - Obtention des informations de mappage

Au cours de cette étape, vous allez obtenir les informations dont vous aurez besoin pour créer ultérieurement les enregistrements DNS. 

### <a name="open-the-custom-domain-ui"></a>Ouverture de l’interface utilisateur du domaine personnalisé

Dans le panneau de votre application, cliquez sur **Domaines personnalisés** dans le menu. 

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

### <a name="copy-the-mapping-information"></a>Copie des informations de mappage

Sur la page **Domaines personnalisés**, copiez **l’adresse IP** de l’application et son nom d’hôte par défaut sous **Noms d’hôtes affectés au site**.

Vous aurez besoin ultérieurement de l’adresse IP pour le mappage de l’enregistrement A et du nom d’hôte par défaut pour le mappage de l’enregistrement CNAME.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

## <a name="step-3---add-dns-records"></a>Étape 3 - Ajout d’enregistrements DNS 

Au cours de cette étape, vous allez ajouter les enregistrements DNS dont vous avez besoin pour mapper votre DNS personnalisé à votre application. Vous effectuez cette étape avec votre fournisseur de domaine.

### <a name="access-dns-records-with-domain-provider"></a>Accès aux enregistrements DNS avec le fournisseur de domaine

Commencez par vous connecter au site web de votre fournisseur de domaine.

Recherchez ensuite la page de gestion des enregistrements DNS. Chaque fournisseur de domaine ayant sa propre interface d’enregistrements DNS, vous devez consulter la documentation de votre fournisseur. En général, vous devez rechercher la mention **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**. Vous trouvez généralement un lien en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**. 

Recherchez ensuite un lien vous permettant de gérer des enregistrements DNS. Ce lien peut être nommé **Fichier de zone**, **Enregistrements DNS** ou **Configuration avancée**.

La capture d’écran suivante est un exemple de ce à quoi votre page d’enregistrements DNS peut ressembler :

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

Dans la capture d’écran de l’exemple, vous cliquez sur **Ajouter** pour créer un enregistrement. Certains fournisseurs ont différents liens pour ajouter divers types d’enregistrements. Là encore, consultez la documentation de votre fournisseur.

> [!NOTE]
> Pour certains fournisseurs, tels que GoDaddy, les modifications apportées aux enregistrements DNS n’entrent en vigueur qu’une fois que vous avez cliqué sur un lien **Enregistrer les modifications** distinct après leur création. 
>
>

<a name="a"></a>

### <a name="create-an-a-record"></a>Créer un enregistrement A

Dans l’exemple de ce didacticiel, vous souhaitez ajouter un enregistrement A pour le domaine racine, `contoso.com`. 

Pour mapper un enregistrement A à votre application, App Service a besoin de _deux_ enregistrements DNS :

- Un enregistrement **A** pour effectuer un mappage vers l’adresse IP de votre application.
- Un enregistrement **TXT** pour effectuer un mappage vers le nom d’hôte par défaut de votre application. Cet enregistrement permet à App Service de vérifier que vous êtes propriétaire du domaine personnalisé que vous souhaitez mapper.

Le tableau suivant vous montre comment configurer un mappage d’enregistrement A pour les types de domaine pris en charge (`@` représente généralement le domaine racine). 

<table cellspacing="0" border="1">
  <tr>
    <th>Type de domaine</th>
    <th>Type d’enregistrement</th>
    <th>Host</th>
    <th>Valeur</th>
  </tr>
  <tr>
    <td rowspan="2" align="left">Domaine racine<br>(p. ex. <code>contoso.com</code>)</td>
    <td>Un </td>
    <td>@</td>
    <td>Adresse IP de <a href="#info">l’étape 2</a></td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>@</td>
    <td>Hôte par défaut de <a href="#info">l’étape 2</a></td>
  </tr>
  <tr>
    <td rowspan="2" align="left">Sous-domaine<br>(p. ex. <code>www.contoso.com</code>)</td>
    <td>Un </td>
    <td>www</td>
    <td>Adresse IP de <a href="#info">l’étape 2</a></td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>www</td>
    <td>Hôte par défaut de <a href="#info">l’étape 2</a></td>
  </tr>
  <tr>
    <td rowspan="2" align="left">Domaine générique<br>(p. ex. <code>*.contoso.com</code>)</td>
    <td>Un </td>
    <td>\*</td>
    <td>Adresse IP de <a href="#info">l’étape 2</a></td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>\*</td>
    <td>Hôte par défaut de <a href="#info">l’étape 2</a></td>
  </tr>
</table>

Pour l’exemple de domaine `contoso.com`, votre page d’enregistrements DNS doit ressembler à la capture d’écran suivante :

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="cname"></a>

### <a name="create-a-cname-record"></a>Créer un enregistrement CNAME
Dans l’exemple de ce didacticiel, vous souhaitez ajouter un enregistrement CNAME pour le domaine racine, `contoso.com`. 

Contrairement au [mappage d’un enregistrement A](#a), mapper un enregistrement CNAME à votre application ne nécessite pas d’enregistrements supplémentaires.

> [!IMPORTANT]
> Nous vous recommandons d’utiliser un mappage CNAME. Si vous supprimez et recréez votre application, ou si vous repassez d’un niveau d’hébergement dédié au niveau **Partagé**, l’adresse IP virtuelle de votre application peut être modifiée. Un mappage CNAME reste valide suite à une modification de ce genre, alors qu’un mappage A est potentiellement invalidé par une nouvelle adresse IP. 
>
> Toutefois, _ne créez pas_ d’enregistrement CNAME pour votre domaine racine (c’est-à-dire « d’enregistrement racine »). Pour plus d’informations, consultez l’article [Why can’t a CNAME record be used at the root domain (Pourquoi un enregistrement CNAME ne peut-il pas être utilisé dans le domaine racine)](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain).
> Pour mapper un domaine racine à votre application Azure, utilisez plutôt un [enregistrement A](#a) .
> 
> 

Le tableau suivant vous montre comment configurer un mappage d’enregistrement CNAME pour les types de domaine pris en charge.

Configurez votre enregistrement CNAME comme suit.

<table cellspacing="0" border="1">
  <tr>
    <th>Type de domaine</th>
    <th>Hôte CNAME</th>
    <th>Valeur CNAME</th>
  </tr>
  <tr>
    <td>Sous-domaine<br>(p. ex. <code>www.contoso.com</code>)</td>
    <td>www</td>
    <td>Hôte par défaut de <a href="#info">l’étape 2</a></td>
  </tr>
  <tr>
    <td>Domaine générique<br>(p. ex. <code>*.contoso.com</code>)</td>
    <td>\*</td>
    <td>Hôte par défaut de <a href="#info">l’étape 2</a></td>
  </tr>
</table>

Pour l’exemple de domaine `www.contoso.com`, votre page d’enregistrements DNS doit ressembler à la capture d’écran suivante :

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

<a name="enable"></a>

## <a name="step-4---enable-the-custom-dns-in-your-app"></a>Étape 4 - Activation du DNS personnalisé dans votre application

Vous êtes maintenant prêt à ajouter vos noms DNS configurés (p. ex. `contoso.com` et `www.contoso.com`) à votre application.

De retour sur la page **Domaines personnalisés** de votre application dans le portail Azure (voir [l’étape 1](#info)), vous devez ajouter à la liste le nom de domaine complet de votre domaine personnalisé.

### <a name="add-the-a-hostname-to-your-app"></a>Ajout du nom d’hôte A à votre application

Cliquez sur l’icône **+** en regard de **Ajouter un nom d’hôte**.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Tapez le nom de domaine complet pour lequel vous avez configuré l’enregistrement A précédemment (p. ex `contoso.com`), puis cliquez sur **Valider**.

Si vous avez raté une étape ou fait une faute de frappe à un endroit, une erreur de vérification peut apparaître au bas de la page.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/verification-error.png)

Dans le cas contraire, le bouton **Ajouter un nom d’hôte** est activé. 

Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **Enregistrement A (exemple.com)**.

Cliquez sur **Ajouter un nom d’hôte** pour ajouter le nom DNS à votre application.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Un certain temps peut être nécessaire pour que votre nouveau nom d’hôte soit reflété sur la page **Domaines personnalisés** de votre application. Essayez d’actualiser le navigateur pour mettre à jour les données.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

<a name="add-cname"></a>
### <a name="add-the-cname-hostname-to-your-app"></a>Ajout du nom d’hôte CNAME à votre application

Cliquez sur l’icône **+** en regard de **Ajouter un nom d’hôte**.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Tapez le nom de domaine complet pour lequel vous avez configuré l’enregistrement CNAME précédemment (p. ex `www.contoso.com`), puis cliquez sur **Valider**.

Si vous avez raté une étape ou fait une faute de frappe à un endroit, une erreur de vérification peut apparaître au bas de la page.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

Dans le cas contraire, le bouton **Ajouter un nom d’hôte** est activé. 

Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **Enregistrement A (exemple.com)**.

Cliquez sur **Ajouter un nom d’hôte** pour ajouter le nom DNS à votre application.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Un certain temps peut être nécessaire pour que votre nouveau nom d’hôte soit reflété sur la page **Domaines personnalisés** de votre application. Essayez d’actualiser le navigateur pour mettre à jour les données.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

### <a name="step-5---test-in-browser"></a>Étape 5 - Test dans le navigateur

Dans votre navigateur, accédez aux noms DNS que vous avez configurés précédemment (`contoso.com` et `www.contoso.com`).

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="more-resources"></a>Autres ressources

[Acheter et configurer un nom de domaine personnalisé dans Azure App Service](custom-dns-web-site-buydomains-web-app.md)

