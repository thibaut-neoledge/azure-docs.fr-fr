<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a custom domain name" pageTitle="Configuration d'un nom de domaine personnalisé pour un site web Azure utilisant Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configuration d'un nom de domaine personnalisé pour un site web Azure à l'aide de Traffic Manager

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Domaine personnalisé</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name/" title="Websites">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Site web utilisant Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions génériques sur l'utilisation d'un nom de domaine personnalisé avec le service Sites Web Azure en utilisant Traffic Manager pour l'équilibrage de charge. Veuillez consulter les onglets en haut de cet article pour vérifier si votre bureau d'enregistrement de domaines est bien répertorié. Le cas échéant, veuillez sélectionner cet onglet pour consulter la procédure relative à ce bureau d'enregistrement de domaines.

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

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par le bureau d'enregistrement de domaines auprès duquel vous avez acheté votre nom de domaine. Pour trouver les outils DNS, procédez comme suit.

1. Connectez-vous à votre compte du bureau d'enregistrement de domaines, puis recherchez une page pour la gestion des enregistrements DNS. Recherchez la mention **Domain Name**, **DNS** ou **Name Server Management**. Vous trouverez généralement un lien vers cette page en affichant vos informations de compte, puis en recherchant un lien tel que **Mes domaines**.

4. Lorsque vous avez trouvé la page de gestion de votre nom de domaine, recherchez un lien vous permettant de modifier les enregistrements DNS. Il est probablement répertorié en tant que lien de configuration d'un **fichier de zone**, d'**enregistrements DNS** ou sous forme de lien de configuration **avancé**.

	* Il est possible que plusieurs enregistrements soient déjà créés pour cette page, par exemple, une entrée associant " **@** " ou " \* " à une page de " parc de domaine ". Cette page peut également contenir des enregistrements pour des sous-domaines courants, tels que **www**.
	* La page mentionnera les **enregistrements CNAME** ou fournira une liste déroulante permettant de sélectionner un type d'enregistrement. Elle peut également mentionner d'autres enregistrements, tels que des **enregistrements A** et des **enregistrements MX**. Dans certains cas, les enregistrements CNAME sont appelés différemment, par exemple **Enregistrement d'alias**.
	* Cette page contient également des champs qui vous permettent de **mapper** à partir d'un **nom d'hôte** ou d'un **nom de domaine** vers un autre nom de domaine.

5. Bien que les spécificités de chaque bureau d'enregistrement varient, vous mappez généralement *à partir de* votre nom de domaine personnalisé (par exemple, **contoso.com**,) *vers* le nom de domaine Traffic Manager (**contoso.trafficmanager.net**) qui est utilisé pour votre site Web Azure.

6. Lorsque vous avez fini d'ajouter ou de modifier des enregistrements DNS auprès de votre bureau d'enregistrement, enregistrez les modifications.

<h2><a name="enabledomain"></a>Activation de Traffic Manager pour votre site web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]
