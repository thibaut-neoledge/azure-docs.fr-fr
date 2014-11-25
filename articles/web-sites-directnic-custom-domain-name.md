<properties title="Learn how to configure an Azure website to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, Directnic" description="Learn how to configure an Azure website to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# Configuration d'un nom de domaine personnalisé pour un site web Azure (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Domaine personnalis&eacute;">Domaine personnalis&eacute;</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name/" title="Sites web" class="current">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Site web utilisant Traffic Manager">Site web utilisant Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [DirectNic.com][DirectNic.com] avec Sites Web Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :

-   [Présentation des enregistrements DNS][Présentation des enregistrements DNS]
-   [Configurer vos sites web pour le mode De base, Partagé ou Standard][Configurer vos sites web pour le mode De base, Partagé ou Standard]
-   [Ajouter un enregistrement DNS pour votre domaine personnalisé][Ajouter un enregistrement DNS pour votre domaine personnalisé]
-   [Activer le domaine sur votre site web][Activer le domaine sur votre site web]

## <a name="understanding-records"></a>Présentation des enregistrements DNS

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

## <a name="bkmk_configsharedmode"></a>Configurer vos sites web pour le mode De base, Partagé ou Standard

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a>

## Ajouter un enregistrement DNS pour votre domaine personnalisé

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Directnic. Pour localiser les outils DNS pour Directnic.com, procédez comme suit.

1.  Connectez-vous à votre compte Directnic.com et sélectionnez **My Services** puis **Domains**.

    ![Menu des services Directnic][Menu des services Directnic]

2.  Cliquez sur le nom de domaine que vous souhaitez utiliser pour votre site web Azure.

3.  Dans la page de gestion de votre domaine, cliquez sur le bouton **Manage** correspondant à **DNS** dans le volet **Services**.

    ![Volet Services][Volet Services]

4.  Ajoutez des enregistrements DNS en remplissant les champs **Type**, **Name** et **Data**. Lorsque vous avez terminé, cliquez sur le bouton **Add Record**.

    ![][0]

    -   Lorsque vous ajoutez un enregistrement CNAME, vous devez définir le champ **Name** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Vous devez définir le champ **Data** en fonction du nom de domaine **.azurewebsites.net** de votre site web Azure. Par exemple, **contoso.azurwebsites.net**.

    -   Quand vous ajoutez un enregistrement A, vous devez affecter au champ **Name** la valeur **@** (ceci correspond au nom de domaine racine, par exemple **contoso.com**) ou le sous-domaine à utiliser (par exemple **www**). Vous devez définir le champ **Data** en fonction de l'adresse IP de votre site web Azure.

        > [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
        >
        > -   Une valeur **Name** de **www** dont la valeur **Data** est définie sur **\<nomdevotresiteweb\>.azurewebsites.net**
        >
        > OU
        >
        > -   Une valeur **Name** de **awverify.www** dont la valeur **Data** est définie sur **awverify.\<nomdevotresiteweb\>.azurewebsites.net**
        >
        > Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

## <a name="enabledomain"></a>Activer le nom de domaine de votre site web

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

  [DirectNic.com]: https://directnic.com
  [Présentation des enregistrements DNS]: #understanding-records
  [Configurer vos sites web pour le mode De base, Partagé ou Standard]: #bkmk_configsharedmode
  [Ajouter un enregistrement DNS pour votre domaine personnalisé]: #bkmk_configurecname
  [Activer le domaine sur votre site web]: #enabledomain
  [Menu des services Directnic]: .\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png
  [Volet Services]: .\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png
  [0]: .\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png
