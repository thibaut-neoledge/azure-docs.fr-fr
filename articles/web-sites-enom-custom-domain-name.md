<properties title="Learn how to configure an Azure website to use a domain name registered with eNom" pageTitle="Configuration d'un nom de domaine eNom pour un site web Azure" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configuration d'un nom de domaine personnalisé pour un site web Azure (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domaine personnalisé</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name/" title="Websites" class="current">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Site web utilisant Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions sur l'utilisation d'un nom de domaine personnalisé acheté auprès de [eNom](https://enom.com) avec Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par eNom. Pour localiser les outils DNS pour enom.com, procédez comme suit.

1. Connectez-vous à votre compte eNom et sélectionnez **Domains**, puis **My Domains**. Vos noms de domaine s'affichent.

2. Dans la page **My Domains**, dans le champ **Manage Domain**, sélectionnez **Host Records**. Les champs des enregistrements d'hôtes s'affichent.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/e-hostrecords.png)

4. L'éditeur d'enregistrements d'hôtes vous permet de sélectionner le type d'enregistrement à l'aide du champ **Record Type**. Pour Sites Web Azure, vous devez uniquement utiliser la sélection **CNAME (Alias)** ou **A (Address)**.

	![zone file editor](./media/web-sites-custom-domain-name/e-editrecords.png)

	> [WACOM.NOTE] Avant d'ajouter des entrées au fichier de zone, notez qu'eNom a déjà créé les enregistrements DNS pour le domaine racine (" @ ") et un caractère générique pour les sous-domaines (" * "). Si vous souhaitez rediriger le domaine racine vers votre site web ou si vous utilisez un enregistrement A comportant un caractère générique, vous devez changer ces entrées au lieu d'en créer d'autres.

	* Pendant l'ajout d'un enregistrement CNAME, vous devez définir le champ **Host Name** sur le sous-domaine à utiliser. Par exemple, **www**. Vous devez définir le champ **Address** sur le nom de domaine **.azurewebsites.net** de votre site web Azure. Par exemple, **contoso.azurwebsites.net**.

		> [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
		> 
		> * Valeur **Alias** de **www** dont la valeur indiquée dans **Other host** est définie sur **&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> OU
		> 
		> * Valeur **Alias** de **awverify.www** dont la valeur indiquée dans **Other host** est définie sur **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

	* Quand vous ajoutez un enregistrement A, vous devez définir le champ **Host Name** sur **@** (ce qui correspond au nom de domaine racine, comme **contoso.com**), * (caractère générique pour la mise en correspondance de plusieurs sous-domaines) ou le sous-domaine que vous comptez utiliser (par exemple, **www**). Vous devez définir le champ **Address** en fonction de l'adresse IP de votre site web Azure.

		> [WACOM.NOTE] Quand vous ajoutez un enregistrement A, vous devez ajouter un enregistrement CNAME dont l'hôte est **awverify** et une valeur **Points to** qui est **awverify.&lt;nomdevotresiteweb&gt;.azurewebsites.net.

5. Quand vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Save** pour enregistrer les modifications.

<h2><a name="enabledomain"></a>Activation du nom de domaine sur votre site Web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
