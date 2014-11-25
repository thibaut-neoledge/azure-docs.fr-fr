<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Websites, Moniker, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# Configuration d'un nom de domaine personnalisé pour un site web Azure à l'aide de Traffic Manager (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Domaine personnalis&eacute;">Domaine personnalis&eacute;</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name/" title="Sites web">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Site web &agrave; l'aide de Traffic Manager" class="current">Site web &agrave; l'aide de Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [Moniker][1] avec le service Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Moniker. Pour localiser les outils DNS pour Moniker.com, procédez comme suit.

1.  Connectez-vous à votre compte Moniker.com, puis sélectionnez **My Domains** et cliquez sur **Manage Templates**.

    ![Page My Domains pour Moniker][Page My Domains pour Moniker]

2.  Sur la page **Zone Template Management**, sélectionnez **Create New Template**.

    ![Page Zone Template Management de Moniker][Page Zone Template Management de Moniker]

3.  Indiquez le nom de modèle sous **Template Name**.

4.  Créez ensuite un enregistrement DNS en commençant par sélectionner un type d'enregistrement, dans **Record Type**. Remplissez ensuite les champs **Hostname** (non d'hôte) et **Address** (adresse).

    ![Page Create Zone Template de Moniker][Page Create Zone Template de Moniker]

    -   Lors de l'ajout d'un enregistrement CNAME, définissez le champ **Hostname** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Définissez le champ **Address** sur le nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site web Azure. Par exemple, **contoso.trafficmanager.net**.

        > [WACOM.NOTE] Vous devez uniquement utiliser les enregistrements CNAME lorsque vous associez votre nom de domaine personnalisé à un site web équilibré en charge à l'aide de Traffic Manager.

5.  Cliquez sur le bouton **Add** pour ajouter l'entrée.

6.  Une fois toutes les entrées ajoutées, cliquez sur le bouton **Save**.

7.  Sélectionnez **Domain Manager** pour revenir à votre liste de domaines.

8.  Cochez la case correspondant à votre domaine cible, puis cliquez à nouveau sur **Manage Templates**.

9.  Sélectionnez le nouveau modèle que vous avez créé au cours des étapes précédentes. Cliquez ensuite sur le lien **place selected domains (1) into this Template**.

    ![Page Create Zone Template de Moniker][2]

## <a name="enabledomain"></a>Activer Traffic Manager pour votre site web

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

  [Moniker]: /fr-fr/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [1]: https://moniker.com
  [Présentation des enregistrements DNS]: #understanding-records
  [Configurer vos sites web pour le mode Standard]: #bkmk_configsharedmode
  [Ajouter un enregistrement DNS pour votre domaine personnalisé]: #bkmk_configurecname
  [Activer Traffic Manager pour votre site web]: #enabledomain
  [Page My Domains pour Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png
  [Page Zone Template Management de Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png
  [Page Create Zone Template de Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png
  [2]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png
