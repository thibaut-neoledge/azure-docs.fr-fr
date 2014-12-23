<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Directnic" pageTitle="Configuration d'un nom de domaine Directnic pour un site web Azure utilisant Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Directnic, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#Configuration d'un nom de domaine personnalisé pour un site web Windows Azure utilisant Traffic Manager (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domaine personnalisé</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name/" title="Websites">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Site web utilisant Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions sur l'utilisation d'un nom de domaine personnalisé acheté auprès de [DirectNic.com](https://directnic.com) avec Sites Web Azure.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :

-   [Présentation des enregistrements DNS](#understanding-records)
-   [Configurer vos sites web pour le mode Standard](#bkmk_configsharedmode)
-   [Ajouter un enregistrement DNS pour votre domaine personnalisé](#bkmk_configurecname)
-   [Activer Traffic Manager pour votre site web](#enabledomain)

<h2><a name="understanding-records"></a>Présentation des enregistrements DNS</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Configuration de vos sites web pour le mode Standard</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Ajouter un enregistrement DNS pour votre domaine personnalisé</h2>

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Directnic. Pour trouver les outils DNS pour Directnic.com, procédez comme suit.

1. Connectez-vous à votre compte Directnic.com et sélectionnez **My Services** puis **Domains**. 

    ![Directnic Services Menu](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2. Cliquez sur le nom de domaine que vous souhaitez utiliser pour votre site web Azure.

2. Dans la page de gestion de votre domaine, cliquez sur le bouton **Manage** correspondant à **DNS** dans le volet **Services**.

    ![Services Panel](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4. Ajoutez des enregistrements DNS en remplissant les champs **Type**, **Name** et **Data**. Lorsque vous avez terminé, cliquez sur le bouton **Add Record**.

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS_TM.png)

    * Lorsque vous ajoutez un enregistrement CNAME, vous devez définir le champ **Name** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Définissez le champ **Data** en fonction du nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site web Azure. Par exemple, **contoso.trafficmanager.net**.

	    > [WACOM.NOTE] Vous devez uniquement utiliser les enregistrements CNAME quand vous associez votre nom de domaine personnalisé à un site web dont la charge est équilibrée à l'aide de Traffic Manager.

<h2><a name="enabledomain"></a>Activation de Traffic Manager pour votre site web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]
