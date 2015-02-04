<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Network Solutions" pageTitle="Configuration d'un nom de domaine Network Solutions pour un site web Azure utilisant Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# Configuration d'un nom de domaine personnalisé pour un site web Azure à l'aide de Traffic Manager (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domaine personnalisé</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Websites">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Site web utilisant Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions sur l'utilisation d'un nom de domaine personnalisé acheté auprès de [Network Solutions](https://www.networksolutions.com) avec Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Network Solutions. Pour trouver les outils DNS, procédez comme suit.

1. Connectez-vous à votre compte networksolutions.com, puis sélectionnez **My Account** dans le coin supérieur droit.

3. Sous l'onglet **My Products and Services**, sélectionnez **Edit DNS**.

	![edit dns page](./media/web-sites-custom-domain-name/ns-editdns.png)

2. Dans la section **Manage <yourdomainname>** de la page **Domain Names**, sélectionnez **Edit Advanced DNS Records**.

	![domain names page with edit advanced dns records highlighted](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4. La page **Update Advanced DNS** contient une section pour chaque type d'enregistrement, avec un bouton **Edit** sous chaque section. Pour les enregistrements CNAME, utilisez la section **Host Alias (CNAME Records)**.

	![update advanced dns page](./media/web-sites-custom-domain-name/ns-updatecnameadvanced.png)

5. Lorsque vous cliquez sur le bouton **Edit**, vous accédez à un formulaire permettant de modifier les enregistrements existants ou d'en ajouter de nouveaux. 

    Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Alias** sur le sous-domaine à utiliser. Par exemple, **www**. Vous devez sélectionner le champ circulaire situé en regard du champ **Other host** et définir **Other host** sur le nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site web Azure. Par exemple, **contoso.trafficmanager.net**. Laissez le champ **Refers to Host Name** sur **Select**, car ce champ n'est pas obligatoire lors de la création d'un enregistrement CNAME à utiliser avec le service Sites Web Azure.
	
	![cname form](./media/web-sites-custom-domain-name/ns-cnametm.png)

5. Une fois que vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Continue** pour vérifier les modifications. Pour enregistrer les modifications, sélectionnez **Save changes only**.

<h2><a name="enabledomain"></a>Activation de Traffic Manager pour votre site web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]
