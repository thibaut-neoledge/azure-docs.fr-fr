<properties
	pageTitle="Configurer un nom de domaine personnalisé dans Azure App Service"
	description="Apprenez à utiliser un nom de domaine personnalisé avec une application web dans Azure App Service."
	services="app-service\web"
	documentationCenter=""
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="mwasson"/>

# Configurer un nom de domaine personnalisé dans Azure App Service

<div class="dev-center-tutorial-selector sublanding">
  <a href="/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Domaine personnalisé</a>
  <a href="/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a>
  <a href="/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Solutions réseau</a>
  <a href="/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a>
  <a href="/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a>
  <a href="/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a>
  <a href="/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a>
  <a href="/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a>
  <a href="/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a>
</div>
<div class="dev-center-tutorial-subselector">
  <a href="/documentation/articles/web-sites-custom-domain-name/" title="Websites" class="current">Web Apps</a> | <a href="/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Web Apps using Traffic Manager">Web Apps avec Traffic Manager</a>
</div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

Lorsque vous créez une application web, Azure l'attribue à un sous-domaine de azurewebsites.net. Par exemple, si votre application web est nommée **contoso**, l'URL est **contoso.azurewebsites.net**. Azure attribue également une adresse IP virtuelle.

<!--todo:![contoso.azurewebsites.net subdomain][subdomain]-->

Pour une application web de production, vous souhaitez probablement afficher un nom de domaine personnalisé pour vos utilisateurs. Cet article explique comment configurer un domaine personnalisé avec [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). (Cet article présente des instructions générales pour tout bureau d'enregistrement de domaines. Les onglets en haut de cet article fournissent des liens vers des articles pour des bureaux d'enregistrement de domaines spécifiques.)

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :


-   [Vue d'ensemble]
-   [Types d'enregistrement DNS]
-   [Recherche de l'adresse IP virtuelle]
-   [Création des enregistrements DNS]
-   [Création d'un enregistrement " awverify " (enregistrements A uniquement)](#awverify)
-   [Activation du nom de domaine sur votre application web]


## Vue d'ensemble

Voici les étapes générales de configuration d'un nom de domaine personnalisé :

1. Réservation de votre nom de domaine. Cet article ne traite pas cette étape. Il existe un grand choix de bureaux d'enregistrement de domaines. Lorsque vous vous inscrivez, leur site vous expliquera les étapes du processus.
1. Création d'enregistrements DNS qui mappent le domaine vers votre application web Azure.
1. Ajout du nom de domaine dans le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

Au-delà de ces fondamentaux, il faut prendre en compte des cas spécifiques :

- Mappage de votre domaine racine. Le domaine racine est le domaine que vous avez réservé auprès du bureau d'enregistrement de domaines. Par exemple, **contoso.com**.
- Mappage d'un sous-domaine. Par exemple, **blogs.contoso.com**.  Vous pouvez mapper différents sous-domaines vers différentes applications web.
- Mappage d'un caractère générique. Par exemple, ***.contoso.com**. Une entrée à caractère générique s'applique à tous les sous-domaines de votre domaine.

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]


## Types d'enregistrement DNS

Le DNS (Domain Name System) utilise des enregistrements de données pour mapper les noms de domaines dans les adresses IP. Il existe plusieurs types d'enregistrements DNS. Pour les applications web, vous pouvez créer un enregistrement *A* ou *CNAME*

- Un enregistrement A **(adresse)** mappe un nom de domaine vers une adresse IP.
- Un enregistrement **CNAME (Canonical Name, nom canonique)** mappe un nom de domaine vers un autre. DNS utilise le deuxième nom pour rechercher l'adresse. Les utilisateurs voient le premier nom de domaine dans leur navigateur. Par exemple, vous pouvez mapper contoso.com vers &lt;<votresite>&gt;*.azurewebsites.net.

Si l'adresse IP change, une entrée CNAME reste valide, tandis qu'un enregistrement A doit être mis à jour. Toutefois, certains bureaux d'enregistrement de domaines n'autorisent pas les enregistrements CNAME pour le domaine racine ou les domaines avec caractère générique. Dans ce cas, utilisez un enregistrement A.

> [AZURE.NOTE] L'adresse IP peut être modifiée si vous supprimez et recréez votre application web ou la faites de nouveau passer en mode Gratuit.


## Recherche de l'adresse IP virtuelle

Ignorez cette étape si vous créez un enregistrement CNAME. Pour créer un enregistrement A, vous devez connaître l'adresse IP virtuelle de votre application web. Pour obtenir cette adresse IP :

1.	Dans votre navigateur, ouvrez le [Portail de gestion Azure](https://portal.azure.com).
2.	Cliquez sur l'option **Parcourir** sur le côté gauche de la page.
3.	Cliquez sur le panneau **Web Apps**.
4.	Cliquez sur le nom de votre application web.
5.	Dans la page **Essentials**, cliquez sur **Tous les paramètres**.
6.	Cliquez sur **Domaines personnalisés et SSL**. L'adresse IP se trouve au bas de la page (juste au-dessus de la section **Liaisons SSL**).

## Création des enregistrements DNS

Connectez-vous à votre bureau d'enregistrement de domaine et utilisez son utilitaire pour ajouter un enregistrement A ou CNAME. Même si les applications web des bureaux d'enregistrement de domaine peuvent être un peu différentes, voici quelques instructions générales.

1.	Trouvez la page de gestion des enregistrements DNS. Recherchez la mention **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**. Vous trouverez généralement un lien en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**.
2.	Dans la page de gestion, cherchez un lien vous permettant d'ajouter ou d'éditer les enregistrements DNS. Il est probablement répertorié en tant que lien de configuration de **fichier de zone**, d'**enregistrements DNS**, ou **avancé**.

La page mentionnera les enregistrements A et les enregistrements CNAME séparément, ou fournira une liste déroulante vous permettant de sélectionner le type d'enregistrement. De plus, il est possible qu'elle utilise d'autres noms pour les types d'enregistrement, comme **enregistrements d'adresses IP** au lieu d'enregistrement A, ou **enregistrements d'alias** au lieu d'enregistrements CNAME.  Le bureau d'enregistrement de domaines crée habituellement des enregistrements. Il se peut donc qu'il y ait des enregistrements pour le domaine racine ou les sous-domaines communs comme **www**.

Lorsque vous créez ou éditez un enregistrement, les champs vous permettent de mapper votre nom de domaine vers une adresse IP (enregistrements A) ou un autre domaine (enregistrements CNAME). Pour un enregistrement CNAME, vous mapperez *from* votre domaine personnalisé *to* votre sous-domaine azurewebsites.net.

Dans de nombreux utilitaires de bureaux d'enregistrement, il vous suffit de saisir la partie de sous-domaine de votre domaine, plutôt que la totalité du nom de domaine. De plus, de nombreux utilitaires utilisent " @ " pour désigner le domaine racine. Par exemple :

<table cellspacing="0" border="1">
  <tr>
    <th>Hôte</th>
    <th>Type d'enregistrement</th>
    <th>Adresse IP ou URL</th>
  </tr>
  <tr>
    <td>@</td>
    <td>A (adresse)</td>
    <td>127.0.0.1</td>
  </tr>
  <tr>
    <td>www</td>
    <td>CNAME (alias)</td>
    <td>contoso.azurewebsites.net</td>
  </tr>
</table>

Dans le cas où le nom de domaine personnalisé est " contoso.com ", cela créera les enregistrements suivant :

- **contoso.com** mappé vers 127.0.0.1.
- **www.contoso.com** mappé vers **contoso.azurewebsites.net**.


<h2 id="awverify">Création d'un enregistrement " awverify " (enregistrements A uniquement)</h2>

Si vous créez un enregistrement A, l'application web requiert également un enregistrement CNAME spécial, utilisé pour vérifier que vous possédez le domaine que vous souhaitez utiliser. Cet enregistrement CNAME doit avoir la forme suivante.

- *Si l'enregistrement A mappe le domaine racine ou un domaine avec caractère générique :* Créez un enregistrement CNAME qui mappe de **awverify.&lt;votredomaine&gt;** vers **awverify.&lt;nomdevotreapplicationweb&gt;.azurewebsites.net**.  Par exemple, si l'enregistrement A concerne **contoso.com**, créez un enregistrement CNAME pour **awverify.contoso.com**.
- *Si l'enregistrement A mappe vers un sous-domaine spécifique :* Créez un enregistrement CNAME qui mappe de **awverify.&lt;sousdomaine&gt;** vers **awverify.&lt;nomdevotreapplicationweb&gt;.azurewebsites.net**. Par exemple, si l'enregistrement A concerne **blogs.contoso.com**, créez un enregistrement CNAME pour **awverify.blogs.contoso.com**.

Les visiteurs de votre application web ne verront pas le sous-domaine awverify. Il sert uniquement à la vérification de votre domaine par Azure.

## Activation du nom de domaine sur votre application web

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Si vous souhaitez commencer à utiliser Azure App Service avant d'ouvrir un compte Azure, accédez à la page d'[essai d'App Service](http://go.microsoft.com/fwlink/?LinkId=523751) (en anglais), qui vous permet de créer immédiatement une application web de départ de courte durée dans App Service. Aucune carte de crédit n'est requise, et vous ne prenez aucun engagement.

## Modifications
* Pour plus d'informations sur les modifications entre Sites Web et App Service, consultez la page : [Azure App Service et son impact sur les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714) (en anglais)
* Pour plus d'informations sur les modifications entre l'ancien portail et le nouveau, consultez la page : [Référence pour la navigation sur la version préliminaire du portail](http://go.microsoft.com/fwlink/?LinkId=529715) (en anglais)

<!-- Anchors. -->
[Vue d'ensemble]: #overview
[Types d'enregistrement DNS]: #dns-record-types
[Recherche de l'adresse IP virtuelle]: #find-the-virtual-ip-address
[Création des enregistrements DNS]: #create-the-dns-records
[Activation du nom de domaine sur votre application web]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!--HONumber=49-->