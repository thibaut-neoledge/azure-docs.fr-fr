<properties
	pageTitle="Configurer un nom de domaine personnalisé dans Azure App Service"
	description="Apprenez à utiliser un nom de domaine personnalisé avec une application web dans Azure App Service."
	services="app-service\web"
	documentationCenter=""
	authors="MikeWasson"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="mwasson"/>

# Configurer un nom de domaine personnalisé dans Azure App Service

> [AZURE.SELECTOR]
- [Buy Domain for Web Apps](custom-dns-web-site-buydomains-web-app.md)
- [Web Apps with External Domains](web-sites-custom-domain-name.md)
- [Web Apps with Traffic Manager](web-sites-traffic-manager-custom-domain-name.md)
- [GoDaddy](web-sites-godaddy-custom-domain-name.md)

Lorsque vous créez une application web, Azure l'attribue à un sous-domaine de azurewebsites.net. Par exemple, si votre application web se nomme **contoso**, l'URL est **contoso.azurewebsites.net**. Azure attribue également une adresse IP virtuelle.

Pour une application web de production, vous souhaitez probablement afficher un nom de domaine personnalisé pour vos utilisateurs. Cet article explique comment réserver ou configurer un domaine personnalisé avec [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## Vue d'ensemble

Si vous avez déjà un nom de domaine, ou que vous souhaitez réserver un domaine à partir d'autres bureaux d’enregistrement de noms de domaine, voici les étapes générales pour donner un nom de domaine personnalisé à une application web :

1. Réservation de votre nom de domaine Cet article ne traite pas cette étape. Il existe un grand choix de bureaux d'enregistrement de domaines. Lorsque vous vous inscrivez, leur site vous expliquera les étapes du processus.
1. Création d’enregistrements DNS qui mappent le domaine vers votre application web Azure.
1. Ajout du nom de domaine sur le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

Au-delà de ces fondamentaux, il faut prendre en compte des cas spécifiques :

- Mappage de votre domaine racine. Le domaine racine est le domaine que vous avez réservé auprès du bureau d'enregistrement de domaines. Par exemple, **contoso.com**.
- Mappage d'un sous-domaine. Par exemple, **blogs.contoso.com**. Vous pouvez mapper différents sous-domaines vers différentes applications web.
- Mappage d’un caractère générique. Par exemple, ***.contoso.com**. Une entrée à caractère générique s’applique à tous les sous-domaines de votre domaine.

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes.md)]


## Types d'enregistrement DNS

Le DNS (Domain Name System) utilise des enregistrements de données pour mapper les noms de domaines dans les adresses IP. Il existe plusieurs types d'enregistrements DNS. Pour les applications Web, vous pouvez créer un enregistrement *A* ou *CNAME*.

- Un enregistrement A **(Addresse)** mappe un nom de domaine vers une adresse IP.
- Un enregistrement **CNAME (Canonical Name, nom canonique)** mappe un nom de domaine vers un autre. DNS utilise le deuxième nom pour rechercher l'adresse. Les utilisateurs voient le premier nom de domaine dans leur navigateur. Par exemple, vous pouvez mapper contoso.com vers *&lt;yourwebapp&gt;*.azurewebsites.net.

Si l'adresse IP change, une entrée CNAME reste valide, tandis qu'un enregistrement A doit être mis à jour. Toutefois, certains bureaux d'enregistrement de domaines n'autorisent pas les enregistrements CNAME pour le domaine racine ou les domaines avec caractère générique. Dans ce cas, utilisez un enregistrement A.

> [AZURE.NOTE]L’adresse IP peut être modifiée si vous supprimez et recréez votre application web ou la faites de nouveau passer en mode Gratuit.


## Recherche de l'adresse IP virtuelle

Ignorez cette étape si vous créez un enregistrement CNAME. Pour créer un enregistrement A, vous devez connaître l’adresse IP virtuelle de votre application web. Pour obtenir cette adresse IP :

1.	Dans votre navigateur, ouvrez le [portail Azure](https://portal.azure.com).
2.	Cliquez sur l’option **Parcourir** sur le côté gauche de la page.
3.	Cliquez sur le panneau **Web Apps**.
4.	Cliquez sur le nom de votre application web.
5.	Sur la page **Essentials**, cliquez sur **Tous les paramètres**.
6.	Cliquez sur **Domaines personnalisés et SSL**. 
7.	Dans le panneau **Domaines personnalisés et SSL**, cliquez sur **Apporter des domaines externes**. L'adresse IP se trouve en bas de cette partie.

## Création des enregistrements DNS

Connectez-vous à votre bureau d'enregistrement de domaine et utilisez son utilitaire pour ajouter un enregistrement A ou CNAME. Même si les applications web des bureaux d’enregistrement de domaine peuvent être un peu différentes, voici quelques instructions générales.

1.	Trouvez la page de gestion des enregistrements DNS. Recherchez la mention **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**. Vous trouverez généralement un lien en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**.
2.	Dans la page de gestion, cherchez un lien vous permettant d'ajouter ou d'éditer les enregistrements DNS. Il est probablement répertorié en tant que lien de configuration de **fichier de zone**, d'**enregistrements DNS**, ou **avancé**.

La page mentionnera les enregistrements A et les enregistrements CNAME séparément, ou fournira une liste déroulante vous permettant de sélectionner le type d’enregistrement. De plus, il est possible qu'elle utilise d'autres noms pour les types d'enregistrement, comme **enregistrements d’adresses IP** au lieu d’enregistrement A, ou **enregistrements d’alias** au lieu d’enregistrements CNAME. Le bureau d'enregistrement de domaines crée habituellement des enregistrements. Il se peut donc qu'il y ait des enregistrements pour le domaine racine ou les sous-domaines communs comme **www**.

Lorsque vous créez ou éditez un enregistrement, les champs vous permettent de mapper votre nom de domaine vers une adresse IP (enregistrements A) ou un autre domane (enregistrements CNAME). Pour un enregistrement CNAME, vous mapperez *de* votre domaine personnalisé *vers* votre sous-domaine azurewebsites.net.

Dans de nombreux utilitaires de bureaux d'enregistrement, il vous suffit de saisir la partie de sous-domaine de votre domaine, plutôt que la totalité du nom de domaine. De plus, de nombreux utilitaires utilisent « @ » pour désigner le domaine racine. Par exemple :

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

Dans le cas où le nom de domaine personnalisé est « contoso.com », cela créera les enregistrements suivant :

- **contoso.com** mappé vers 127.0.0.1.
- **www.contoso.com** mappé vers **contoso.azurewebsites.net**.

>[AZURE.NOTE]Vous pouvez utiliser Azure DNS pour héberger les enregistrements de domaine nécessaires pour votre application web. Pour configurer votre domaine personnalisé et créer vos enregistrements, dans le système Azure DNS, consultez [Création d’enregistrements DNS personnalisés pour une application web](../dns-web-sites-custom-domain).

<a name="awverify" />
## Création d’un enregistrement awverify (enregistrements A uniquement)

Si vous créez un enregistrement A, l’application web requiert également un enregistrement CNAME spécial, utilisé pour vérifier que vous possédez le domaine que vous souhaitez utiliser. Cet enregistrement CNAME doit avoir la forme suivante.

- *Si l’enregistrement A mappe le domaine racine ou un domaine avec caractère générique :* Créez un enregistrement CNAME qui mappe depuis **awverify.&lt;yourdomain&gt;** vers **awverify.&lt;yourwebappname&gt;.azurewebsites.net**. Par exemple, si l'enregistrement A concerne **contoso.com**, créez un enregistrement CNAME pour **awverify.contoso.com**.
- *Si l’enregistrement A mappe un sous-domaine spécifique :* Créez un enregistrement CNAME qui mappe depuis **awverify.&lt;subdomain&gt;** vers **awverify.&lt;yourwebappname&gt;.azurewebsites.net**. Par exemple, si l'enregistrement A concerne **blogs.contoso.com**, créez un enregistrement CNAME pour **awverify.blogs.contoso.com**.

Les visiteurs de votre application web ne verront pas le sous-domaine awverify. Il sert uniquement à la vérification de votre domaine par Azure.

## Activer le nom de domaine sur votre application web

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.


## Étapes suivantes

Pour plus d'informations, consultez [Prise en main d’Azure DNS](../dns/dns-getstarted-create-dnszone.md) et [Domaine délégué vers Azure DNS](../dns/dns-domain-delegation.md)

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

<!-- Anchors. -->
[Overview]: #overview
[DNS record types]: #dns-record-types
[Find the virtual IP address]: #find-the-virtual-ip-address
[Create the DNS records]: #create-the-dns-records
[Enable the domain name on your web app]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png
 

<!---HONumber=Sept15_HO4-->