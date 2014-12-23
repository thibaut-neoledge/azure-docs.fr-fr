<properties title="Learn how to configure an Azure website to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configuration d'un nom de domaine DomainDiscover pour un site web Azure" metaKeywords="Azure, Azure Web Sites, DomainDiscover, TierraNet" description="Learn how to configure an Azure website to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#Configuration d'un nom de domaine personnalisé pour un site web Azure (DomainDiscover / TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domaine personnalisé</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Websites" class="current">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Site web utilisant Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions sur l'utilisation d'un nom de domaine personnalisé acheté auprès de [DomainDiscover.com](https://domaindiscover.com) avec Sites Web Azure. DomainDiscover.com fait partie de TierraNet.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :

-   [Présentation des enregistrements DNS](#understanding-records)
-   [Configurer vos sites web pour le mode De base, Partagé ou Standard](#bkmk_configsharedmode)
-   [Ajouter un enregistrement DNS pour votre domaine personnalisé](#bkmk_configurecname)
-   [Activer le domaine sur votre site Web](#enabledomain)

<h2><a name="understanding-records"></a>Présentation des enregistrements DNS</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Configuration de vos sites web pour le mode De base, Partagé ou Standard</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Ajouter un enregistrement DNS pour votre domaine personnalisé</h2>

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par DomainDiscover. Pour localiser et utiliser les outils DNS pour DomainDiscover.com, procédez comme suit.

1. Connectez-vous à votre compte sur DomainDiscover.com (TierraNet) en sélectionnant **Control Panel** dans le menu **Login**.

    ![DomainDiscover Login Menu](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2. Dans la page **Domain Services**, sélectionnez le domaine que vous souhaitez utiliser pour votre site web Azure.

    ![Domain management page](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3. Dans les paramètres de domaine, cliquez sur le bouton **Edit** correspondant à **DNS Service**.

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4. Dans la fenêtre **Manage DNS**, sélectionnez le type d'enregistrement DNS à ajouter à la liste **Add Records**. Cliquez ensuite sur le bouton **Add**.

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5. Sur la page suivante, entrez les valeurs d'enregistrement DNS. Cliquez ensuite sur le bouton **Add**.

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png)

    * Lorsque vous ajoutez un enregistrement CNAME, vous devez d'abord sélectionner **CNAME (Alias)** sur la page **Manage DNS**. Définissez ensuite le champ **Host** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Vous devez définir le champ **Alias Hostname** en fonction du nom de domaine **.azurewebsites.net** de votre site web Azure. Par exemple, **contoso.azurwebsites.net**. Ensuite, indiquez une valeur de durée de vie " time-to-live " (TTL), par exemple 1 800 secondes.

    * Lorsque vous ajoutez un enregistrement A, vous devez d'abord sélectionner **A** sur la page **Manage DNS**. Ensuite, définissez le champ **Host** sur **@** (ce qui correspond au nom de domaine racine, comme **contoso.com**,) ou sur le sous-domaine que vous comptez utiliser (par exemple, **www**). Vous devez définir le champ **IP Address** en fonction de l'adresse IP de votre site web Azure. Ensuite, indiquez une valeur de durée de vie " time-to-live " (TTL), par exemple 1 800 secondes.

		> [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
		> 
		> * Une valeur **Host** correspondant à **www** avec une valeur **Alias Hostname** correspondant à **&lt;nomdevotresiteweb&gt;.azurewebsites.net**.
		> 
		> OU
		> 
		> * Une valeur **Host** correspondant à **awverify.www** avec une valeur **Alias Hostname** correspondant à **awverify.&lt;nomdevotresiteweb&gt;.azurewebsites.net**.
		> 
		> Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.


5. Si vous avez ajouté un enregistrement A, il se peut qu'un avertissement s'affiche, indiquant que l'enregistrement A existant pour votre domaine n'est pas inactif. Ce dernier utilise le dernier enregistrement modifié et DomainDirect dispose déjà d'un enregistrement A par défaut pour le nom de domaine racine. Vous pouvez soit utiliser l'enregistrement précédent, soit supprimer l'enregistrement A par défaut en sélectionnant le bouton **DELETE**.

<h2><a name="enabledomain"></a>Activation du nom de domaine sur votre site Web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
