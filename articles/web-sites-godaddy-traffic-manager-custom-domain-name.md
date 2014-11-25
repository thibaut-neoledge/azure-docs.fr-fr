<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure website that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# Configuration d'un nom de domaine personnalisé pour un site web Azure utilisant Traffic Manager (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Domaine personnalis&eacute;">Domaine personnalis&eacute;</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Sites web">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Site web utilisant Traffic Manager" class="current">Site web utilisant Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions sur l'utilisation d'un nom de domaine personnalisé acheté auprès de [Go Daddy][Go Daddy] avec Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par GoDaddy. Pour trouver les outils DNS pour GoDaddy.com, procédez comme suit.

1.  Connectez-vous à votre compte GoDaddy.com et sélectionnez **My Account** puis **Manage your domains**. Enfin, sélectionnez le nom de domaine que vous souhaitez utiliser avec votre site web Azure.

    ![page des domaines personnalisés de GoDaddy][page des domaines personnalisés de GoDaddy]

2.  Sur la page **Domain details**, sélectionnez l'onglet **DNS Zone File**. Il s'agit de la section qui vous permet d'ajouter et de modifier des enregistrements DNS pour votre nom de domaine. Cliquez sur le bouton **Edit** pour afficher l'éditeur **Zone File Editor**.

    ![Onglet DNS Zone File][Onglet DNS Zone File]

3.  L'éditeur **Zone File Editor** se compose de différentes sections, une pour chaque type d'enregistrement, la première étant la section pour les enregistrements A **A (Host)**, suivie de celle des enregistrements CNAME (appelée **CNAME (Alias)**). Pour ajouter une entrée, cliquez sur le bouton **Quick Add** sous la section correspondante. Pour modifier une entrée existante, sélectionnez-la et modifiez-en les informations.

    ![éditeur de fichier de zone][éditeur de fichier de zone]

    > [WACOM.NOTE] Avant d'ajouter des entrées au fichier de zone, notez que GoDaddy a déjà créé des enregistrements DNS pour les sous-domaines populaires (sous **Host** dans l'éditeur), tels que **email**, **files**, **mail**, etc. Si le nom que vous souhaitez utiliser existe déjà, modifiez l'enregistrement existant plutôt que d'en créer un nouveau.

    Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **host** sur le sous-domaine à utiliser. Par exemple, **www**. Vous devez définir le champ **Points to** en fonction du nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site web Azure. Par exemple, **contoso.trafficmanager.net**.

    > [WACOM.NOTE] Vous devez uniquement utiliser les enregistrements CNAME quand vous associez votre nom de domaine personnalisé à un site web dont la charge est équilibrée à l'aide de Traffic Manager.

4.  Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Save Zone File** pour enregistrer les modifications.

## <a name="enabledomain"></a>Activer Traffic Manager pour votre site web

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

  [Go Daddy]: https://godaddy.com
  [Présentation des enregistrements DNS]: #understanding-records
  [Configurer vos sites web pour le mode Standard]: #bkmk_configsharedmode
  [Ajouter un enregistrement DNS pour votre domaine personnalisé]: #bkmk_configurecname
  [Activer Traffic Manager pour votre site web]: #enabledomain
  [page des domaines personnalisés de GoDaddy]: ./media/web-sites-custom-domain-name/godaddy-customdomain.png
  [Onglet DNS Zone File]: ./media/web-sites-custom-domain-name/godaddy-zonetab.png
  [éditeur de fichier de zone]: ./media/web-sites-custom-domain-name/godaddy-quickaddcname.png
