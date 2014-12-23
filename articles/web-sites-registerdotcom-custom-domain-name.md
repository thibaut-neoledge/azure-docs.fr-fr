<properties title="Learn how to configure an Azure website to use a domain name registered with Register.com" pageTitle="Configuration d'un nom de domaine Register.com pour un site web Azure" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configuration d'un nom de domaine personnalisé pour un site web Azure (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domaine personnalisé</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Websites" class="current">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Site web utilisant Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions sur l'utilisation d'un nom de domaine personnalisé acheté auprès de [Register.com](https://www.register.com) avec le service Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée dans la table DNS pour votre domaine personnalisé à l'aide des outils fournis par Register.com. Procédez comme suit pour localiser et utiliser les outils DNS.

1. Connectez-vous à votre compte sur register.com et sélectionnez **Your Account** dans le coin supérieur droit pour afficher vos domaines, puis sélectionnez votre nom de domaine personnalisé.

	![the my account page](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

3. Faites défiler la page pour accéder à **Advanced Technical Settings**. Les liens de cette section vous permettent de gérer les enregistrements de votre domaine.

	* Pour les enregistrements A, utilisez le lien **Edit IP Address Records**.
	* Pour les enregistrements CNAME, utilisez le lien **Edit Domain Aliases Records**.

	![Advanced technical settings](./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png)

5. Lorsque vous cliquez sur le bouton **Edit**, vous accédez à un formulaire permettant de modifier les enregistrements existants ou d'en ajouter de nouveaux. Le formulaire est identique pour les enregistrements CNAME et A.

	* Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **.mydomainname.com** sur le sous-domaine à utiliser. Par exemple, **www**. Vous devez définir la valeur **points to** sur le nom de domaine **.azurewebsites.net** de votre site web Azure. Par exemple, **contoso.azurwebsites.net**. Laissez le champ **Refers to Host Name** sur **Select**, car ce champ n'est pas obligatoire lors de la création d'un enregistrement CNAME à utiliser avec le service Sites Web Azure.
	
		![cname form](./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png)

		> [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
		> 
		> * Valeur **Alias** de **www** dont la valeur indiquée dans **Other host** est définie sur **&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> OU
		> 
		> * Valeur **Alias** de **awverify.www** dont la valeur indiquée dans **Other host** est définie sur **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

	* Lors de l'ajout d'un enregistrement A, vous devez définir le champ **.mydomainname.com** sur le sous-domaine que vous souhaitez utiliser (par exemple, **www**.) Laissez ce champ vierge pour définir le domaine racine ou utilisez **\** pour créer un mappage de caractère générique. Vous devez définir le champ **points to** sur l'adresse IP de votre site web Azure.

		![a record form](./media/web-sites-custom-domain-name/rdotcom-editarecord.png)

5. Une fois que vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Continue** pour vérifier les modifications. Sélectionnez à nouveau **Continue** pour enregistrer les modifications.

<h2><a name="enabledomain"></a>Activation du nom de domaine sur votre site Web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
