<properties title="Learn how to configure an Azure website to use a domain name registered with GoDaddy" pageTitle="Configuration d'un nom de domaine GoDaddy pour un site web Azure" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configuration d'un nom de domaine personnalisé pour un site web Azure (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domaine personnalisé</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites" class="current">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Site web utilisant Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions sur l'utilisation d'un nom de domaine personnalisé acheté auprès de [Go Daddy](https://godaddy.com) avec Sites Web Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :

-   [Présentation des enregistrements DNS](#understanding-records)
-   [Ajouter un enregistrement DNS pour votre domaine personnalisé](#bkmk_configurecname)
-   [Activer le domaine sur votre site Web](#enabledomain)

<h2><a name="understanding-records"></a>Présentation des enregistrements DNS</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]


<h2><a name="bkmk_configurecname"></a>Ajouter un enregistrement DNS pour votre domaine personnalisé</h2>

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par GoDaddy. Pour trouver les outils DNS pour GoDaddy.com, procédez comme suit.

1. Connectez-vous à votre compte GoDaddy.com et sélectionnez **My Account**, puis **Manage my domains**. Enfin, sélectionnez le menu déroulant du nom de domaine à utiliser avec votre site web Azure, puis sélectionnez **Manage DNS**.

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. Dans la page **Domain details**, faites défiler jusqu'à l'onglet **DNS Zone File**. Il s'agit de la section qui vous permet d'ajouter et de modifier des enregistrements DNS pour votre nom de domaine. 

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

	Sélectionnez **Add Record** pour ajouter un enregistrement existant.

	Pour **modifier** un enregistrement existant, sélectionnez l'icône représentant un stylo et du papier à côté de l'enregistrement souhaité.

	> [WACOM.NOTE] Avant d'ajouter de nouveaux enregistrements, notez que GoDaddy a déjà créé des enregistrements DNS pour les sous-domaines populaires (sous **Host** dans l'éditeur), par exemple **email**, **files**, **mail**, etc. Si le nom que vous souhaitez utiliser existe déjà, modifiez l'enregistrement existant plutôt que d'en créer un nouveau.

4. Quand vous ajoutez un enregistrement, vous devez d'abord sélectionner son type.

	![select record type](./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png)

	Vous devez ensuite indiquer l'**hôte (Host)** (domaine ou sous-domaine personnalisé) et sa cible dans **Points to**.

	![add zone record](./media/web-sites-custom-domain-name/godaddy-addzonerecord.png)

	* Durant l'ajout d'un **enregistrement (hôte A) (A (host) record)** , vous devez affecter au champ **Host** la valeur **@** (ceci correspond au nom de domaine racine, par exemple **contoso.com**), la valeur * (caractère générique pour la correspondance avec plusieurs sous-domaines) ou le sous-domaine à utiliser (par exemple, **www**.) Vous devez définir le champ **Points to** sur l'adresse IP de votre site web Azure.
	
		> [WACOM.NOTE] Quand vous utilisez des enregistrements A (hôte), vous devez également ajouter un enregistrement CNAME avec la configuration suivante :
		> 
		> * Valeur **Host** **awverify** dont la valeur indiquée dans **Points to** est **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

	* Quand vous ajoutez un **enregistrement (alias) CNAME (CNAME (alias) record)** , vous devez définir le champ **Host** en fonction du sous-domaine à utiliser. Par exemple, **www**. Vous devez définir le champ **Points to** en fonction du nom de domaine **.azurewebsites.net** de votre site web Azure. Par exemple, **contoso.azurwebsites.net**.


5. Une fois que vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Finish** pour enregistrer les modifications.

<h2><a name="enabledomain"></a>Activation du nom de domaine sur votre site Web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

