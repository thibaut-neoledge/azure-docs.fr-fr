<properties title="Learn how to configure an Azure website to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="Learn how to configure an Azure website to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Configuration d'un nom de domaine personnalisé pour un site web Azure (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Domaine personnalis&eacute;">Domaine personnalis&eacute;</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name/" title="Sites web" class="current">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Site web &agrave; l'aide de Traffic Manager">Site web &agrave; l'aide de Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][]]

[WACOM.INCLUDE [intro][]]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [Moniker.com][] avec le service Sites Web Azure.

[WACOM.INCLUDE [introfooter][]]

Dans cet article :

-   [Présentation des enregistrements DNS][]
-   [Configurer vos sites web pour le mode De base, Partagé ou Standard][]
-   [Ajouter un enregistrement DNS pour votre domaine personnalisé][]
-   [Activer le domaine sur votre site Web][]

## <a name="understanding-records"></a>Présentation des enregistrements DNS

[WACOM.INCLUDE [understandingdns][]]

## <a name="bkmk_configsharedmode"></a>Configurer vos sites web pour le mode De base, Partagé ou Standard

[WACOM.INCLUDE [modes][]]

<a name="bkmk_configurecname"></a>

## Ajouter un enregistrement DNS pour votre domaine personnalisé

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Moniker. Pour localiser les outils DNS pour Moniker.com, procédez comme suit.

1.  Connectez-vous à votre compte Moniker.com, puis sélectionnez **My Domains** et cliquez sur **Manage Templates**.

    ![Page My Domains pour Moniker][]

2.  Sur la page **Zone Template Management**, sélectionnez **Create New Template**.

    ![Page Zone Template Management de Moniker][]

3.  Indiquez le nom de modèle sous **Template Name**.

4.  Créez ensuite un enregistrement DNS en commençant par sélectionner un type d'enregistrement, dans **Record Type**. Remplissez ensuite les champs **Hostname** (non d'hôte) et **Address** (adresse).

    ![Page Create Zone Template de Moniker][]

    -   Lors de l'ajout d'un enregistrement CNAME, définissez le champ **Hostname** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Vous devez définir le champ **Address** sur le nom de domaine **.azurewebsites.net** de votre site web Azure. Par exemple, **contoso.azurwebsites.net**.

    -   Lorsque vous ajoutez un enregistrement A, vous devez définir le champ **Hostname** sur **@** (ce qui correspond au nom de domaine racine, comme **contoso.com**,) ou sur le sous-domaine que vous comptez utiliser (par exemple, **www**). Vous devez définir le champ **Address** sur l'adresse IP de votre site web Azure.

        > [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
        >
        > -   Une valeur **Hostname** de **www** avec une valeur **Address** correspondant à **\<nomdusite\>.azurewebsites.net**.
        >
        > OU
        >
        > -   Une valeur **Hostname** de **awverify.www** avec une valeur **Address** correspondant à **awverify.\<nomdusite\>.azurewebsites.net**.
        >
        > Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

5.  Cliquez sur le bouton **Add** pour ajouter l'entrée.

6.  Une fois toutes les entrées ajoutées, cliquez sur le bouton **Save**.

7.  Sélectionnez **Domain Manager** pour revenir à votre liste de domaines.

8.  Cochez la case correspondant à votre domaine cible, puis cliquez à nouveau sur **Manage Templates**.

9.  Sélectionnez le nouveau modèle que vous avez créé au cours des étapes précédentes. Cliquez ensuite sur le lien **place selected domains (1) into this Template**.

    ![Page Create Zone Template de Moniker][1]

## <a name="enabledomain"></a>Activer le nom de domaine de votre site web

[WACOM.INCLUDE [modes][2]]

  [Domaine personnalisé]: /fr-fr/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé"
  [GoDaddy]: /fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /fr-fr/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /fr-fr/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /fr-fr/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /fr-fr/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Site web]: /fr-fr/documentation/articles/web-sites-moniker-custom-domain-name/ "Sites web"
  [Site web à l'aide de Traffic Manager]: /fr-fr/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/ "Site web à l'aide de Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [Moniker.com]: https://moniker.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Présentation des enregistrements DNS]: #understanding-records
  [Configurer vos sites web pour le mode De base, Partagé ou Standard]: #bkmk_configsharedmode
  [Ajouter un enregistrement DNS pour votre domaine personnalisé]: #bkmk_configurecname
  [Activer le domaine sur votre site Web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [Page My Domains pour Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png
  [Page Zone Template Management de Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png
  [Page Create Zone Template de Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png
  [1]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png
  [2]: ../includes/custom-dns-web-site-enable-on-web-site.md
