<properties title="Learn how to configure an Azure website to use a custom domain name" pageTitle="Configuration d'un nom de domaine personnalisé pour un site web Azure" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth, mwasson" />

#Configuration d'un nom de domaine personnalisé pour un site Web Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Domaine personnalisé</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name/" title="Websites" class="current">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Site web utilisant Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

Lorsque vous créez un site Web, Azure l'attribue à un sous-domaine d'azurewebsites.net. Par exemple, si le nom de votre site Web est **contoso**, l'URL correspondante est **contoso.azurewebsites.net**. Azure attribue également une adresse IP virtuelle.

![contoso.azurewebsites.net subdomain][subdomain]

Pour un site web de production, vous souhaitez probablement afficher un nom de domaine personnalisé pour vos utilisateurs. Cet article explique comment configurer un domaine personnalisé avec Sites Web Azure. (Cet article présente des instructions générales pour tout bureau d'enregistrement de domaines. Les onglets en haut de cet article fournissent des liens vers des articles pour des bureaux d'enregistrement de domaines spécifiques.)

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :


-   [Vue d'ensemble]
-   [Types d'enregistrement DNS]
-   [Recherche de l'adresse IP virtuelle]
-   [Création des enregistrements DNS]
-   [Création d'un enregistrement " awverify " (enregistrements A uniquement)](#awverify)
-   [Activation du nom de domaine sur votre site Web]


## Vue d'ensemble

Voici les étapes générales de configuration d'un nom de domaine personnalisé :

1. Réservation de votre nom de domaine Cet article ne traite pas cette étape. Il existe un grand choix de bureaux d'enregistrement de domaines. Lors de votre inscription, leur site vous expliquera les étapes du processus.
1. Création d'enregistrements DNS qui mappent le domaine vers votre site web Azure 
1. Ajout du nom de domaine dans le portail de gestion Azure 

Au-delà de ces fondamentaux, il faut prendre en compte des cas spécifiques :

- Mappage de votre domaine racine Le domaine racine est le domaine que vous avez réservé auprès du bureau d'enregistrement de domaines. Par exemple, **contoso.com**.
- Mappage d'un sous-domaine Par exemple, **blogs.contoso.com**.  Vous pouvez mapper différents sous-domaines vers divers sites web.
- Mappage d'un caractère générique Par exemple, ***.contoso.com**. Un entrée à caractère générique s'applique à tous les sous-domaines de votre domaine. 
 
[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]


## Types d'enregistrement DNS

DNS (Domain Name System) utilise des enregistrements de données pour mapper les noms de domaines vers des adresses IP. Il existe plusieurs types d'enregistrements DNS. Pour les sites web, vous pouvez créer un enregistrement *A* ou *CNAME*.

- Un enregistrement A **(Adresse)** mappe un nom de domaine vers une adresse IP. 
- Un enregistrement **CNAME (Canonical Name, nom canonique)** mappe un nom de domaine vers un autre. DNS utilise le deuxième nom pour rechercher l'adresse. Les utilisateurs voient le premier nom de domaine dans leur navigateur. Par exemple, vous pouvez mapper contoso.com vers *&lt;votresite&gt;*.azurewebsites.net. 

Si l'adresse IP change, une entrée CNAME reste valide, tandis qu'un enregistrement A doit être mis à jour. Toutefois, certains bureaux d'enregistrement de domaines n'autorisent pas les enregistrements CNAME pour le domaine racine ou les domaines avec caractère générique. Dans ce cas, utilisez un enregistrement A. 

> [WACOM.NOTE] L'adresse IP peut être modifiée si vous supprimez, recréez votre site web ou le faites de nouveau passer en mode Gratuit.


## Recherche de l'adresse IP virtuelle 

Ignorez cette étape si vous créez un enregistrement CNAME. Pour créer un enregistrement A, vous devez connaître l'adresse IP de votre site Web. Pour obtenir cette adresse IP :

1.	Dans votre navigateur, ouvrez le [portail de gestion Azure](https://manage.windowsazure.com).
2.	Sous l'onglet **Sites Web**, cliquez sur le nom de votre site et sélectionnez **Tableau de bord**.
3.	Sélectionnez **Gérer les domaines** en bas de la page. (Si cette option est désactivée, assurez-vous d'utiliser le mode Partagé, De base ou Standard. Pour plus d'informations, consultez la page [Mise à l'échelle de sites Web](http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-scale/).) 

	![](media/web-sites-custom-domain-name/dncmntask-cname-6.png)

4.	L'adresse IP apparaît vers le bas de la boîte de dialogue.

	![](media/web-sites-custom-domain-name/ipaddress.png)

## Création des enregistrements DNS

Connectez-vous à votre bureau d'enregistrement de domaine et utilisez son utilitaire pour ajouter un enregistrement A ou CNAME. Même si les sites Web des bureaux d'enregistrement de domaine peuvent être un peu différents, voici quelques instructions générales. 

1.	Trouvez la page de gestion des enregistrements DNS. Recherchez la mention **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**. Vous trouverez généralement un lien en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**.
2.	Dans la page de gestion, cherchez un lien vous permettant d'ajouter ou d'éditer les enregistrements DNS. Il est probablement répertorié en tant que lien de configuration d'un **fichier de zone**, d'**enregistrements DNS** ou sous forme de lien de configuration **avancé**.

La page mentionnera les enregistrements A et les enregistrements CNAME séparément, ou fournira une liste déroulante vous permettant de sélectionner le type d'enregistrement. De plus, il est possible qu'elle utilise d'autres noms pour les types d'enregistrement, comme **enregistrements d'adresses IP** au lieu d'enregistrement A, ou **enregistrements d'alias** au lieu d'enregistrements CNAME.  Le bureau d'enregistrement de domaines crée habituellement des enregistrements. Il se peut donc qu'il y ait des enregistrements pour le domaine racine ou les sous-domaines communs comme **www**.

Lorsque vous créez ou éditez un enregistrement, les champs vous permettent de mapper votre nom de domaine vers une adresse IP (enregistrements A) ou un autre domaine (enregistrements CNAME). Pour un enregistrement CNAME, vous mapperez *à partir de* votre domaine personnalisé *vers* votre sous-domaine azurewebsites.net. 

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

Si vous créez un enregistrement A, Sites Web Azure requiert un enregistrement CNAME spécial, utilisé pour vérifier que vous possédez le domaine que vous souhaitez utiliser. Cet enregistrement CNAME doit avoir la forme suivante. 

- *Si l'enregistrement A mappe le domaine racine ou un domaine avec caractère générique :* créez un enregistrement CNAME qui mappe depuis **awverify.&lt;votredomaine&gt;** vers **awverify.&lt;votrenomsiteweb&gt;.azurewebsites.net**.  Par exemple, si l'enregistrement A concerne **contoso.com**, créez un enregistrement CNAME pour **awverify.contoso.com**.
- *Si l'enregistrement A mappe un sous-domaine spécifique :* créez un enregistrement CNAME qui mappe depuis **awverify.&lt;sousdomaine&gt;** vers **awverify.&lt;votrenomsiteweb&gt;.azurewebsites.net**. Par exemple, si l'enregistrement A concerne **blogs.contoso.com**, créez un enregistrement CNAME pour **awverify.blogs.contoso.com**.

Les visiteurs de votre site ne verront pas le sous-domaine awverify. Il sert uniquement à la vérification de votre domaine par Azure.

## Activation du nom de domaine sur votre site Web

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


<!-- Anchors. -->
[Vue d'ensemble]: #overview
[Types d'enregistrement DNS]: #dns-record-types
[Recherche de l'adresse IP virtuelle]: #find-the-virtual-ip-address
[Création des enregistrements DNS]: #create-the-dns-records
[Activation du nom de domaine sur votre site Web]: #enable-the-domain-name-on-your-website

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png
