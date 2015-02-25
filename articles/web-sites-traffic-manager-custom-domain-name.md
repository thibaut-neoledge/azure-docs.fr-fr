<properties 
	pageTitle="Configuration d'un nom de domaine personnalisé pour un site Web Azure utilisant Traffic Manager" 
	description="" 
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

#Configuration d'un nom de domaine personnalisé pour un site Web Azure à l'aide de Traffic Manager

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Domaine personnalisé</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">eNom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name/" title="Websites">Site Web</a> | <a href="/fr-fr/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Site Web utilisant Traffic Manager</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions génériques sur l'utilisation d'un nom de domaine personnalisé avec le service Sites Web Azure en utilisant Traffic Manager pour l'équilibrage de charge. Veuillez consulter les onglets en haut de cet article pour vérifier si votre bureau d'enregistrement de domaines est bien répertorié. Le cas échéant, veuillez sélectionner cet onglet pour consulter la procédure relative à ce bureau d'enregistrement de domaines.

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

<a name="bkmk_configurecname"></a><h2>Ajout d'un enregistrement DNS pour votre domaine personnalisé</h2>

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par le bureau d'enregistrement de domaines auprès duquel vous avez acheté votre nom de domaine. Pour localiser et utiliser les outils DNS, procédez comme suit.

1. Connectez-vous à votre compte du bureau d'enregistrement de domaines, puis recherchez une page pour la gestion des enregistrements DNS. Recherchez les liens ou zones du site intitulés **Nom de domaine**, **DNS** ou **Gestion de serveur de noms**. Vous trouverez généralement un lien vers cette page en affichant vos informations de compte, puis en recherchant un lien tel que **Mes domaines**.

4. Lorsque vous avez trouvé la page de gestion de votre nom de domaine, recherchez un lien vous permettant de modifier les enregistrements DNS. Il est probablement répertorié en tant que lien de configuration d'un **fichier de zone**, d'**enregistrements DNS**, ou sous forme de lien de configuration **avancé**.

	* Il est possible que plusieurs enregistrements soient déjà créés pour cette page, par exemple une entrée associant " '**@**' " ou " '\*' " à une page de  'domain parking'. Cette page peut également contenir des enregistrements pour des sous-domaines courants, tels que **www**.
	* La page mentionnera les **enregistrements CNAME** ou fournira une liste déroulante permettant de sélectionner un type d'enregistrement. Elle peut également mentionner d'autres enregistrements, tels que des **enregistrements A** et des **enregistrements MX**. Dans certains cas, les enregistrements CNAME sont appelés différemment, par exemple **Enregistrement d'alias**.
	* Cette page comprend également des champs permettant de **mapper** à partir d'un **nom d'hôte** ou d'un **nom de domaine** vers un autre nom de domaine.

5. Même si chaque bureau d'enregistrement a ses particularités, vous pouvez généralement mapper *from* de votre nom de domaine personnalisé (comme **contoso.com**) *to* le nom de domaine Traffic Manager (**contoso.trafficmanager.net**) utilisé pour votre site Web Azure.

6. Lorsque vous avez fini d'ajouter ou de modifier des enregistrements DNS auprès de votre bureau d'enregistrement, enregistrez les modifications.

<h2><a name="enabledomain"></a>Activation de Traffic Manager pour votre site Web</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]


<!--HONumber=42-->
