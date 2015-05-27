<properties 
	pageTitle="Configuration d'un nom de domaine Network Solutions pour un site web Azure" 
	description="Apprendre à utiliser un nom de domaine Network Solutions avec Sites Web Azure" 
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

#Configuration d'un nom de domaine personnalisé pour un site web Azure (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/web-sites-custom-domain-name" title="Domaine personnalisé">Domaine personnalisé</a><a href="/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Sites web" class="current">Site web</a> | <a href="/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Site web à l’aide de Traffic Manager">Site web à l’aide de Traffic Manager</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [Network Solutions](https://www.networksolutions.com) avec le service Sites Web Azure.

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :

-   [Présentation des enregistrements DNS](#understanding-records)
-   [Configurer vos sites web pour le mode De base, Partagé ou Standard](#bkmk_configsharedmode)
-   [Ajout d'un enregistrement DNS pour votre domaine personnalisé](#bkmk_configurecname)
-   [Activer le domaine sur votre site Web](#enabledomain)

<h2><a name="understanding-records"></a>Présentation des enregistrements DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Configurer vos sites web pour le mode De base, Partagé ou Standard</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Ajouter un enregistrement DNS pour votre domaine personnalisé</h2>

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Network Solutions. Pour localiser et utiliser les outils DNS pour networksolutions.com, procédez comme suit.

1. Connectez-vous à votre compte sur networksolutions.com, puis sélectionnez **My Account** dans le coin supérieur droit.

3. Sous l'onglet **My Products and Services**, sélectionnez **Edit DNS**.

	![page Edit DNS](./media/web-sites-custom-domain-name/ns-editdns.png)

2. Dans la section **Manage<yourdomainname>** de la page **Domain Names**, sélectionnez **Edit Advanced DNS Records**.

	![Pages Domain Names avec Edit Advanced DNS Records mis en surbrillance](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4. La page **Update Advanced DNS** contient une section pour chaque type d'enregistrement, avec un bouton **Edit** sous chaque section.
	
	* Pour les enregistrements A, utilisez la section **IP Address (A Records)**.
	* Pour les enregistrements CNAME, utilisez la section **Host Alias (CNAME Records)**.

	![page Updated Advanced DNS](./media/web-sites-custom-domain-name/ns-updateadvanced.png)

5. Lorsque vous cliquez sur le bouton **Edit**, vous accédez à un formulaire permettant de modifier les enregistrements existants ou d'en ajouter de nouveaux.

	> [AZURE.NOTE]Avant d'ajouter de nouvelles entrées, notez que Network Solutions a déjà créé quelques enregistrements DNS par défaut, pour le domaine racine (« @ ») et un enregistrement générique (« * ») pour les sous-domaines, par exemple. Si l'enregistrement que vous souhaitez utiliser existe déjà, modifiez-le plutôt que d'en créer un nouveau.

	* Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Alias** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Vous devez sélectionner le champ circulaire situé en regard du champ **Other host** et définir **Other host** sur le nom de domaine **.trafficmanager.net** de votre site web Azure. Par exemple, **contoso.azurwebsites.net**. Laissez le champ **Refers to Host Name** sur **Select**, car ce champ n'est pas obligatoire lors de la création d'un enregistrement CNAME à utiliser avec le service Sites Web Azure.
	
		![Formulaire cname](./media/web-sites-custom-domain-name/ns-cname.png)

		> [AZURE.NOTE]Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
		> 
		> * Valeur **Alias** de **www** avec une valeur **Other host** de **&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> OU
		> 
		> * Valeur **Alias** de **awverify.www** avec une valeur **Other host** de **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

	* Lors de l'ajout d'un enregistrement A, vous devez définir le champ **Host** sur **@** (ce qui correspond au nom de domaine racine, comme **contoso.com**), sur * (un caractère générique pour la correspondance de plusieurs sous-domaines) ou sur le sous-domaine que vous souhaitez utiliser (par exemple, **www**). Vous devez définir le champ **Numeric IP** sur l'adresse IP de votre site web Azure.

		![Formulaire d'enregistrement A](./media/web-sites-custom-domain-name/ns-arecord.png)

5. Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Continue** pour vérifier les modifications. Pour enregistrer les modifications, sélectionnez **Save changes only**.

<h2><a name="enabledomain"></a>Activer le nom de domaine de votre site web</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

<!--HONumber=54-->