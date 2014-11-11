<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure website that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# Configuration d'un nom de domaine personnalisé pour un site web Azure utilisant Traffic Manager (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Domaine personnalis&eacute;">Domaine personnalis&eacute;</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name/" title="Sites web">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Site web utilisant Traffic Manager" class="current">Site web utilisant Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [eNom][eNom] avec Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par eNom. Pour localiser les outils DNS pour enom.com, procédez comme suit.

1.  Connectez-vous à votre compte eNom et sélectionnez **Domains** puis **My Domain**. Vos noms de domaine s'affichent.

2.  Sur la page **My Domains**, dans le champ **Manage Domain**, sélectionnez **Host Records**. Les champs d'enregistrements d'hôtes s'affichent.

    ![Onglet DNS Zone File][Onglet DNS Zone File]

3.  L'éditeur d'enregistrements d'hôtes vous permet de sélectionner le type d'enregistrement à l'aide du champ **Record Type**. Pour les sites web Azure utilisant Traffic Manager, employez uniquement l'option **CNAME (Alias)**, car Traffic Manager n'est compatible qu'avec les enregistrements CNAME.

    ![éditeur de fichier de zone][éditeur de fichier de zone]

    -   Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Host Name** sur le sous-domaine à utiliser. Par exemple, **www**. Vous devez définir le champ **Address** en fonction du nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site web Azure. Par exemple, **contoso.trafficmanager.net**.

4.  Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Save** pour enregistrer les modifications.

## <a name="enabledomain"></a>Activer Traffic Manager pour votre site web

[WACOM.INCLUDE [modes][1]]

  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [eNom]: https://enom.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Présentation des enregistrements DNS]: #understanding-records
  [Configurer vos sites web pour le mode Standard]: #bkmk_configsharedmode
  [Ajouter un enregistrement DNS pour votre domaine personnalisé]: #bkmk_configurecname
  [Activer Traffic Manager pour votre site web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Onglet DNS Zone File]: ./media/web-sites-custom-domain-name/e-hostrecords.png
  [éditeur de fichier de zone]: ./media/web-sites-custom-domain-name/e-editrecordstm.png
  [1]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
