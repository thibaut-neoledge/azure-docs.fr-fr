<properties 
	pageTitle="Configuration d'un nom de domaine eNom pour un site Web Azure utilisant Traffic Manager" 
	description="Apprenez à utiliser un nom de domaine à partir d'eNom avec Sites Web Azure" 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr, jroth"/>

#Configuration d'un nom de domaine personnalisé pour un site Web Azure utilisant Traffic Manager (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domaine personnalisé</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Solutions réseau</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">eNom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name/" title="Websites">Site Web</a> | <a href="/fr-fr/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Site Web utilisant Traffic Manager</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès d'[eNom](https://enom.com) avec Sites Web Azure.

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :

-   [Présentation des enregistrements DNS](#understanding-records)
-   [Configuration des sites Web pour le mode Standard](#bkmk_configsharedmode)
-   [Ajout d'un enregistrement DNS pour votre domaine personnalisé](#bkmk_configurecname)
-   [Activation de Traffic Manager pour votre site Web](#enabledomain)

<h2><a name="understanding-records"></a>Présentation des enregistrements DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Configuration de vos sites Web pour le mode Standard</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Ajouter un enregistrement DNS pour votre domaine personnalisé</h2>

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par eNom. Pour localiser les outils DNS pour enom.com, procédez comme suit.

1. Connectez-vous à votre compte eNom et sélectionnez **Domains** puis **My Domains**. Vos noms de domaine s'affichent.

2. Sur la page **My Domains**, dans le champ **Manage Domain**, sélectionnez **Host Records**. Les champs d'enregistrements d'hôtes s'affichent.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/e-hostrecords.png)

4. L'éditeur d'enregistrements d'hôtes vous permet de sélectionner le type d'enregistrement à l'aide du champ **Record Type**. Pour les sites Web Azure utilisant Traffic Manager, employez uniquement l'option **CNAME (Alias)**, car Traffic Manager n'est compatible qu'avec les enregistrements CNAME.

	![zone file editor](./media/web-sites-custom-domain-name/e-editrecordstm.png)

	* Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Host Name** sur le sous-domaine à utiliser. Par exemple, **www**. Vous devez définir le champ **Address** en fonction du nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site Web Azure. Par exemple, **contoso.trafficmanager.net**.

5. Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Save** pour enregistrer les modifications.

<h2><a name="enabledomain"></a>Activation de Traffic Manager pour votre site Web</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]


<!--HONumber=42-->
