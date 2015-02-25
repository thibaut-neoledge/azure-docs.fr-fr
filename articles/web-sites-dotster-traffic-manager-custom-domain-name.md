<properties 
	pageTitle="Configuration d'un nom de domaine Dotster pour un site Web Azure utilisant Traffic Manager" 
	description="Découvrez comment configurer un site Web Azure qui se sert de Traffic Manager pour utiliser un nom de domaine enregistré avec Dotster" 
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
	ms.author="larryfr,jroth"/>

#Configuration d'un nom de domaine personnalisé pour un site Web Windows Azure utilisant Traffic Manager (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domaine personnalisé</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Solutions réseau</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">eNom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name/" title="Websites">Site Web</a> | <a href="/fr-fr/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Site Web utilisant Traffic Manager</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [Dotster.com](https://dotster.com) avec Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Dotster. Pour trouver les outils DNS pour Dotster.com, procédez comme suit.

1. Connectez-vous à votre compte avec Dotster.com. Dans le menu **Domain**, sélectionnez **DomainCentral**.

    ![Domain Central Dotster Menu](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png)

2. Sélectionnez votre domaine pour afficher une liste de paramètres. Sélectionnez ensuite le lien **Nameservers**.

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png)

3. Sélectionnez l'option **Use different name servers**. Afin de bénéficier des services DNS sur Dotster, vous devez spécifier les serveurs de noms suivants : ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com, et ns4.nameresolve.com.

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png)

    > [AZURE.NOTE] La modification des serveurs de noms peut prendre entre 24 et 48 h. Elle est indispensable au bon déroulement de la suite de cet article.

4. Dans DomainCentral, sélectionnez votre domaine, puis sélectionnez **DNS**. Dans la liste **Modify**, sélectionnez le type d'enregistrement DNS à ajouter (**CNAME Alias** ou **A Record**). 

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png)

5. Ensuite, spécifiez les champs **Host** et **Points To** pour l'enregistrement. Lorsque vous avez terminé, cliquez sur le bouton **Add**.

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME_TM.png)
 
    * Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Host**sur le sous-domaine à utiliser. Par exemple, **www**. Définissez le champ **Points To** en fonction du nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site Web Azure. Par exemple : **contoso.trafficmanager.net**.

	    > [AZURE.NOTE] Vous devez uniquement utiliser les enregistrements CNAME quand vous associez votre nom de domaine personnalisé à un site Web dont la charge est équilibrée à l'aide de Traffic Manager.

<h2><a name="enabledomain"></a>Activation de Traffic Manager pour votre site Web</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]


<!--HONumber=42-->
