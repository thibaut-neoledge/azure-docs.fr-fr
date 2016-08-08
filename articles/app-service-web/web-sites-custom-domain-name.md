<properties
	pageTitle="Mapper un nom de domaine personnalisé à une application Azure"
	description="Découvrez comment mapper un nom de domaine personnalisé (domaine personnel) à votre application dans Azure App Service."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"
	tags="top-support-issue"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="cephalin"/>

# Mapper un nom de domaine personnalisé à une application Azure

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Cet article vous indique comment mapper manuellement un nom de domaine personnalisé à votre application web, à votre backend d’application mobile ou à votre application API dans [Azure App Service](../app-service/app-service-value-prop-what-is.md).

Votre application est fournie avec un sous-domaine unique d’azurewebsites.net. Par exemple, si votre application se nomme **contoso**, son nom de domaine est donc **contoso.azurewebsites.net**. Toutefois, vous pouvez mapper un nom de domaine personnalisé à l’application afin que son URL, par exemple `www.contoso.com`, reflète votre marque.

>[AZURE.NOTE] Consultez les [forums Azure](https://azure.microsoft.com/support/forums/) pour y trouver l’aide des experts Azure. Pour obtenir un niveau de support encore supérieur, accédez au [site Support Azure](https://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## Acheter un nouveau domaine personnalisé sur le portail Azure

Si vous n’avez pas déjà acheté un nom de domaine personnalisé, vous pouvez en acheter un et le gérer directement dans les paramètres de votre application dans le [portail Azure](https://portal.azure.com). Cette option facilite le mappage d’un domaine personnalisé à votre application, qu’elle utilise ou non [Azure Traffic Manager](web-sites-traffic-manager-custom-domain-name.md).

Pour connaître les instructions à suivre, voir l’article [Acheter et configurer un nom de domaine personnalisé dans Azure App Service](custom-dns-web-site-buydomains-web-app.md).

## Mapper un domaine personnalisé acheté en externe

Si vous avez déjà acheté un domaine personnalisé auprès de [DNS Azure](https://azure.microsoft.com/services/dns/) ou d’un fournisseur tiers, la procédure de mappage du domaine personnalisé à votre application compte trois étapes principales :

1. [*(Un enregistrement uniquement)* Obtenez l’adresse IP de l’application](#vip).
1. [Activez le nom de domaine personnalisé de votre application Azure](#enable).
    - **Où** : le [portail Azure](https://portal.azure.com).
    - **Pourquoi** : pour que votre application sache répondre aux requêtes effectuées auprès du nom de domaine personnalisé.
2. [Créez les enregistrements DNS qui mappent votre domaine à votre application](#dns).
    - **Où** : l’outil de gestion de votre bureau d’enregistrement de domaines (par ex. DNS Azure, www.godaddy.com, etc.).
    - **Pourquoi** : pour que votre bureau d’enregistrement de domaines sache résoudre le domaine personnalisé souhaité à votre application Azure.
3. [Vérifiez la propagation DNS](#verify).

### Types de domaine que vous pouvez mapper

Azure App Service vous permet de mapper les catégories suivantes de domaines personnalisés à votre application.

- **Domaine racine** : nom du domaine que vous avez réservé auprès du bureau d’enregistrement de domaines (représenté généralement par l’enregistrement d’hôte `@`). Par exemple, **contoso.com**.
- **Sous-domaine** : tout domaine se trouvant sous votre domaine racine. Par exemple, **www.contoso.com** (représenté par l’enregistrement d’hôte `www`). Vous pouvez mapper différents sous-domaines d’un même domaine racine à différentes applications dans Azure.
- **Domaine avec un caractère générique** : [tout sous-domaine dont le nom DNS à l’extrême gauche est `*`](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (par exemple, les enregistrements d’hôte `*` et `*.blogs`). Par exemple, ***.contoso.com**.

### Types d’enregistrement DNS que vous pouvez utiliser

Selon vos besoins, vous pouvez utiliser deux types d’enregistrement DNS standard pour mapper votre domaine personnalisé :

- [A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) : mappe directement votre nom de domaine personnalisé à l’adresse IP virtuelle de l’application Azure.
- [CNAME](https://en.wikipedia.org/wiki/CNAME_record) : mappe votre nom de domaine personnalisé au nom de domaine Azure de votre application, **&lt;*nom\_application*>. azurewebsites.net**.

L’avantage de l’enregistrement CNAME est qu’il persiste malgré les modifications de l’adresse IP. L’adresse IP virtuelle de votre application peut être modifiée si vous supprimez et recréez votre application ou si vous repassez d’un niveau tarifaire plus élevé au niveau **Partagé**. Malgré une modification de ce type, un enregistrement CNAME reste valide tandis qu’un enregistrement A doit être mis à jour.

Le didacticiel vous montre les procédures à suivre pour utiliser les enregistrements A et CNAME.

<a name="vip"></a>
## Étape 1. *(Un enregistrement uniquement)* Obtenir l’adresse IP de l’application
Pour mapper un nom de domaine personnalisé à l’aide d’un enregistrement A, vous avez besoin de l’adresse IP de votre application Azure. Si vous préférez mapper à l’aide d’un enregistrement CNAME, ignorez cette étape et passez à la section suivante.

1.	Connectez-vous au [portail Azure](https://portal.azure.com).
2.	Cliquez sur **App Services** dans le menu de gauche.
4.	Cliquez sur votre application, puis sur **Paramètres** > **Domaines personnalisés et SSL** > **Apporter des domaines externes**.
5.	Dans **Noms de domaine**, tapez votre nom de domaine personnalisé.
6.  Prenez note de l’adresse IP pour l’utiliser ultérieurement.
7.  Laissez ce panneau du portail ouvert. Vous retournerez dans celui-ci une fois que vous aurez créé les enregistrements DNS.

<a name="dns"></a>
## Étape 2. Créer les enregistrements DNS

Connectez-vous à votre bureau d'enregistrement de domaine et utilisez son utilitaire pour ajouter un enregistrement A ou CNAME. Comme l’interface utilisateur de chaque bureau d’enregistrement est légèrement différente, vous devez donc consulter la documentation de votre fournisseur. Voici néanmoins quelques recommandations générales.

1.	Trouvez la page de gestion des enregistrements DNS. Recherchez la mention **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**. Vous trouverez généralement un lien en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**.
2.	Recherchez un lien vous permettant d’ajouter ou de modifier des enregistrements DNS. Il est probablement répertorié en tant que lien de configuration de **fichier de zone**, **d’enregistrements DNS** ou **avancé**.
3.  Créez l’enregistrement et enregistrez vos modifications.
    - [Les instructions relatives à un enregistrement A sont ici](#a).
    - [Les instructions relatives à un enregistrement CNAME sont ici](#cname).

<a name="a"></a>
### Créer un enregistrement A

Pour utiliser un enregistrement A à mapper à l’adresse IP de votre application Azure, vous devez créer un enregistrement A et un enregistrement CNAME. L’enregistrement A concerne la résolution DNS proprement dite, et l’enregistrement CNAME permet à Azure de vérifier que vous possédez le nom de domaine personnalisé.

Votre enregistrement A doit être configuré comme suit (@ représente généralement le domaine racine) :
 
<table cellspacing="0" border="1">
  <tr>
    <th>Exemple de nom de domaine complet</th>
    <th>Hôte/Nom/Nom d’hôte</th>
    <th>Valeur</th>
  </tr>
  <tr>
    <td>contoso.com (racine)</td>
    <td>@</td>
    <td>Adresse IP de [l’étape 1]()</td>
  </tr>
  <tr>
    <td>www.contoso.com (sous-domaine)</td>
    <td>www</td>
    <td>Adresse IP de [l’étape 1]()</td>
  </tr>
  <tr>
    <td>*.contoso.com (caractère générique)</td>
    <td>*</td>
    <td>Adresse IP de [l’étape 1]()</td>
  </tr>
</table>

L’enregistrement CNAME supplémentaire adopte la convention qui mappe de awverify.&lt;*sous-domaine*>.&lt;*domaine\_racine*> à awverify.&lt;*sous-domaine*>.azurewebsites.net. Voir les exemples ci-dessous :

<table cellspacing="0" border="1">
  <tr>
    <th>Exemple de nom de domaine complet</th>
    <th>Hôte/Nom/Nom d’hôte</th>
    <th>Valeur</th>
  </tr>
  <tr>
    <td>contoso.com (racine)</td>
    <td>awverify</td>
    <td>awverify.&lt;<i>nom_application</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (sous-domaine)</td>
    <td>awverify.www</td>
    <td>awverify.www.&lt;<i>nom_application</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>*.contoso.com (caractère générique)</td>
    <td>awverify</td>
    <td>awverify.&lt;<i>nom_application</i>>.azurewebsites.net</td>
  </tr>
</table>

<a name="cname"></a>
### Créer un enregistrement CNAME

Si vous utilisez un enregistrement CNAME à mapper au nom de domaine par défaut de votre application Azure, vous n’avez pas besoin d’un enregistrement CNAME supplémentaire, comme c’est le cas avec un enregistrement A.

Votre enregistrement CNAME doit être configuré comme suit (@ représente généralement le domaine racine) :

<table cellspacing="0" border="1">
  <tr>
    <th>Exemple de nom de domaine complet</th>
    <th>Hôte/Nom/Nom d’hôte</th>
    <th>Valeur</th>
  </tr>
  <tr>
    <td>contoso.com (racine)</td>
    <td>@</td>
    <td>&lt;<i>nom_application</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (sous-domaine)</td>
    <td>www</td>
    <td>&lt;<i>nom_application</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>*.contoso.com (caractère générique)</td>
    <td>*</td>
    <td>&lt;<i>nom_application</i>>.azurewebsites.net</td>
  </tr>
</table>


>[AZURE.NOTE] Vous pouvez utiliser Azure DNS pour héberger les enregistrements de domaine nécessaires pour votre application web. Pour configurer votre domaine personnalisé et créer vos enregistrements, dans le système Azure DNS, consultez [Création d’enregistrements DNS personnalisés pour une application web](../dns/dns-web-sites-custom-domain.md).

<a name="enable"></a>
## Étape 3. Activer le nom de domaine personnalisé de votre application

De retour dans le panneau **Apporter des domaines externes** du portail Azure (voir [l’étape 1](#vip)), vous devez ajouter à la liste le nom de domaine complet (FQDN) de votre domaine personnalisé.

1.	Retournez dans le panneau **Apporter des domaines externes** du portail Azure.

2.	Ajoutez le nom de domaine complet de votre domaine personnalisé à la liste (par exemple, **www.contoso.com**).

    >[AZURE.NOTE] Comme Azure tente de vérifier ici le nom de domaine que vous utilisez, veillez à ce qu’il s’agisse du nom de domaine pour lequel vous avez créé un enregistrement DNS à [l’étape 2](#dns). Si vous êtes sûr qu’il s’agit du même nom, continuez.

6.  Cliquez sur **Enregistrer**.

7.  Une fois le nouveau nom de domaine personnalisé configuré, accédez à votre nom de domaine personnalisé dans un navigateur. À présent, vous devez voir votre application s’exécuter ainsi que votre nom de domaine personnalisé.

<a name="verify"></a>
## Vérifier la propagation DNS

Une fois les étapes de configuration terminées, la propagation des modifications peut prendre un certain temps, suivant votre fournisseur DNS. Vous pouvez vérifier que la propagation DNS fonctionne normalement à partir du site [http://digwebinterface.com/](http://digwebinterface.com/). Une fois sur le site, spécifiez les noms d’hôte dans la zone de texte et cliquez sur **Dig**. Consultez les résultats pour vérifier si les modifications récentes ont pris effet.

![](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE] La propagation des entrées DNS peut prendre 48 heures (parfois plus). Même si vous avez tout configuré correctement, vous devez faire preuve de patience.

## Étapes suivantes

Pour plus d'informations, consultez [Prise en main d’Azure DNS](../dns/dns-getstarted-create-dnszone.md) et [Domaine délégué vers Azure DNS](../dns/dns-domain-delegation.md)

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.


<!-- Anchors. -->
[Overview]: #overview
[DNS record types]: #dns-record-types
[Find the virtual IP address]: #find-the-virtual-ip-address
[Create the DNS records]: #create-the-dns-records
[Enable the domain name on your web app]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!---HONumber=AcomDC_0727_2016-->