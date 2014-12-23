<properties title="Learn how to configure an Azure website to use a domain name registered with Moniker" pageTitle="Configuration d'un nom de domaine Moniker pour un site web Azure" metaKeywords="Azure, Azure Web Sites, domain name" description="Learn how to configure an Azure website to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#Configuration d'un nom de domaine personnalisé pour un site web Azure (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domaine personnalisé</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name/" title="Websites" class="current">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Site web utilisant Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions sur l'utilisation d'un nom de domaine personnalisé acheté auprès de [Moniker.com](https://moniker.com) avec Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Moniker. Pour trouver les outils DNS pour Moniker.com, procédez comme suit.

1. Connectez-vous à votre compte Moniker.com, puis sélectionnez **My Domains** et cliquez sur **Manage templates**.

    ![My Domains page for Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2. Dans la page **Zone Template Management**, sélectionnez **Create New Template**.

    ![Moniker Zone Template Management](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3. Indiquez le nom de modèle sous **Template Name**. 

5. Créez ensuite un enregistrement DNS en commençant par sélectionner un type d'enregistrement, dans **Record Type**. Remplissez ensuite les champs **Hostname** (non d'hôte) et **Address** (adresse). 

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png)

    * Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Hostname** sur le sous-domaine à utiliser. Par exemple, **www**. Vous devez définir le champ **Address** sur le nom de domaine **.azurewebsites.net** de votre site web Azure. Par exemple, **contoso.azurwebsites.net**.

    * Lors de l'ajout d'un enregistrement A, vous devez affecter au champ **Hostname** la valeur **@** (ceci correspond au nom de domaine racine, par exemple **contoso.com** ou le sous-domaine à utiliser (par exemple, **www**.) Vous devez définir le champ **Address** en fonction de l'adresse IP de votre site web Azure.

		> [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
		> 
		> * Valeur **Hostname** de **www** dont la valeur indiquée dans **Address** est **&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> OU
		> 
		> * Valeur **Hostname** de **awverify.www** dont la valeur indiquée dans **Address** est **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

7. Cliquez sur le bouton **Add** pour ajouter l'entrée.

8. Une fois toutes les entrées ajoutées, cliquez sur le bouton **Save**.

5. Sélectionnez **Domain Manager** pour revenir à votre liste de domaines.

6. Activez la case à cocher de votre domaine cible, puis cliquez à nouveau sur **Manage Templates**.

7. Sélectionnez le nouveau modèle que vous avez créé au cours des étapes précédentes. Cliquez ensuite sur le lien **place selected domains (1) into this Template**.

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

<h2><a name="enabledomain"></a>Activation du nom de domaine sur votre site Web</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
