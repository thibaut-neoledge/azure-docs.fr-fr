<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Dotster, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# Configuration d'un nom de domaine personnalisé pour un site web Windows Azure utilisant Traffic Manager (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Domaine personnalis&eacute;">Domaine personnalis&eacute;</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name/" title="Sites web">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Site web utilisant Traffic Manager" class="current">Site web utilisant Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [Dotster.com][Dotster.com] avec Sites Web Azure.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :

-   [Présentation des enregistrements DNS][Présentation des enregistrements DNS]
-   [Configurer vos sites web pour le mode Standard][Configurer vos sites web pour le mode Standard]
-   [Ajouter un enregistrement DNS pour votre domaine personnalisé][Ajouter un enregistrement DNS pour votre domaine personnalisé]
-   [Activer Traffic Manager pour votre site web][Activer Traffic Manager pour votre site web]

## <a name="understanding-records"></a>Présentation des enregistrements DNS

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

## <a name="bkmk_configsharedmode"></a>Configurer vos sites web pour le mode Standard

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## Ajouter un enregistrement DNS pour votre domaine personnalisé

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Dotster. Pour trouver les outils DNS pour Dotster.com, procédez comme suit.

1.  Connectez-vous à votre compte Dotster.com et sélectionnez **Domain** puis **Domain Central**.

    ![Menu Domain Central de Dotster][Menu Domain Central de Dotster]

2.  Sélectionnez votre domaine pour afficher une liste de paramètres. Sélectionnez ensuite le lien **Nameservers**.

    ![Options de configuration de domaine Dotster][Options de configuration de domaine Dotster]

3.  Sélectionnez l'option **Use different name servers**. Afin de bénéficier des services DNS sur Dotster, vous devez spécifier les serveurs de noms suivants : ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com et ns4.nameresolve.com.

    ![Options de configuration de domaine Dotster][1]

    > [WACOM.NOTE] La modification des serveurs de noms peut prendre entre 24 et 48 h. Elle est indispensable au bon déroulement de la suite de cet article.

4.  Dans DomainCentral, sélectionnez votre domaine, puis sélectionnez **DNS**. Dans la liste **Modify**, sélectionnez le type d'enregistrement DNS à ajouter (**CNAME Alias** ou **A Record**).

    ![Options de configuration de domaine Dotster][2]

5.  Ensuite, spécifiez les champs **Host** et **Points To** pour l'enregistrement. Lorsque vous avez terminé, cliquez sur le bouton **Add**.

    ![Options de configuration de domaine Dotster][3]

    -   Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Host** sur le sous-domaine à utiliser. Par exemple, **www**. Définissez le champ **Points To** en fonction du nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site web Azure. Par exemple, **contoso.trafficmanager.net**.

        > [WACOM.NOTE] Vous devez uniquement utiliser les enregistrements CNAME quand vous associez votre nom de domaine personnalisé à un site web dont la charge est équilibrée à l'aide de Traffic Manager.

## <a name="enabledomain"></a>Activer Traffic Manager pour votre site web

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

  [Dotster.com]: https://dotster.com
  [Présentation des enregistrements DNS]: #understanding-records
  [Configurer vos sites web pour le mode Standard]: #bkmk_configsharedmode
  [Ajouter un enregistrement DNS pour votre domaine personnalisé]: #bkmk_configurecname
  [Activer Traffic Manager pour votre site web]: #enabledomain
  [Menu Domain Central de Dotster]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png
  [Options de configuration de domaine Dotster]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png
  [1]: .\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png
  [2]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png
  [3]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME_TM.png
