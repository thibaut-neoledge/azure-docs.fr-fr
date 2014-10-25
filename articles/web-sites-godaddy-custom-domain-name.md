<properties title="Learn how to configure an Azure website to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Configuration d'un nom de domaine personnalisé pour un site web Azure (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Domaine personnalis&eacute;">Domaine personnalis&eacute;</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Sites web" class="current">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Site web utilisant Traffic Manager">Site web utilisant Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][]]

[WACOM.INCLUDE [intro][]]

Cet article contient des instructions sur l'utilisation d'un nom de domaine personnalisé acheté auprès de [Go Daddy][] avec Sites Web Azure.

[WACOM.INCLUDE [introfooter][]]

Dans cet article :

-   [Présentation des enregistrements DNS][]
-   [Ajouter un enregistrement DNS pour votre domaine personnalisé][]
-   [Activer le domaine sur votre site Web][]

## <a name="understanding-records"></a>Présentation des enregistrements DNS

[WACOM.INCLUDE [understandingdns][]]

## <a name="bkmk_configurecname"></a>Ajouter un enregistrement DNS pour votre domaine personnalisé

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par GoDaddy. Pour trouver les outils DNS pour GoDaddy.com, procédez comme suit.

1.  Connectez-vous à votre compte GoDaddy.com et sélectionnez **My Account**, puis **Manage my domains**. Enfin, sélectionnez le menu déroulant du nom de domaine à utiliser avec votre site web Azure, puis sélectionnez **Manage DNS**.

    ![page des domaines personnalisés de GoDaddy][]

2.  Dans la page **Domain details**, faites défiler jusqu'à l'onglet **DNS Zone File**. Il s'agit de la section qui vous permet d'ajouter et de modifier des enregistrements DNS pour votre nom de domaine.

    ![Onglet DNS Zone File][]

    Sélectionnez **Add Record** pour ajouter un enregistrement existant.

    Pour **modifier** un enregistrement existant, sélectionnez l'icône représentant un stylo et du papier à côté de l'enregistrement souhaité.

    > [WACOM.NOTE] Avant d'ajouter de nouveaux enregistrements, notez que GoDaddy a déjà créé des enregistrements DNS pour les sous-domaines populaires (sous **Host** dans l'éditeur), par exemple **email**, **files**, **mail**, etc. Si le nom que vous souhaitez utiliser existe déjà, modifiez l'enregistrement existant plutôt que d'en créer un nouveau.

3.  Quand vous ajoutez un enregistrement, vous devez d'abord sélectionner son type.

    ![sélectionner le type d'enregistrement][]

    Vous devez ensuite indiquer l'**Host** (domaine ou sous-domaine personnalisé) et sa cible dans **Points to**.

    ![ajouter un enregistrement de zone][]

    -   Durant l'ajout d'un **A (host) record**, vous devez affecter au champ **Host** la valeur **@** (ceci correspond au nom de domaine racine, par exemple **contoso.com**), la valeur \* (caractère générique pour la correspondance avec plusieurs sous-domaines) ou le sous-domaine à utiliser (par exemple **www**). Vous devez définir le champ **Points to** en fonction de l'adresse IP de votre site web Azure.

        > [WACOM.NOTE] Quand vous utilisez des enregistrements A (hôte), vous devez également ajouter un enregistrement CNAME avec la configuration suivante :
        >
        > -   Valeur **Host** **awverify** dont la valeur indiquée dans **Points to** est **awverify.\<nomdevotresiteweb\>.azurewebsites.net**.
        >
        > Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

    -   Quand vous ajoutez un **CNAME (alias) record**, vous devez définir le champ **Host** en fonction du sous-domaine à utiliser. Par exemple, **www**. Vous devez définir le champ **Points to** en fonction du nom de domaine **.azurewebsites.net** de votre site web Azure. Par exemple, **contoso.azurwebsites.net**.

4.  Une fois que vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Finish** pour enregistrer les changements.

## <a name="enabledomain"></a>Activer le nom de domaine de votre site web

[WACOM.INCLUDE [modes][]]

  [Domaine personnalisé]: /fr-fr/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé"
  [GoDaddy]: /fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /fr-fr/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /fr-fr/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /fr-fr/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /fr-fr/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Site web]: /fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name/ "Sites web"
  [Site web utilisant Traffic Manager]: /fr-fr/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/ "Site web utilisant Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [Go Daddy]: https://godaddy.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Présentation des enregistrements DNS]: #understanding-records
  [Ajouter un enregistrement DNS pour votre domaine personnalisé]: #bkmk_configurecname
  [Activer le domaine sur votre site Web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [page des domaines personnalisés de GoDaddy]: ./media/web-sites-custom-domain-name/godaddy-customdomain.png
  [Onglet DNS Zone File]: ./media/web-sites-custom-domain-name/godaddy-zonetab.png
  [sélectionner le type d'enregistrement]: ./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png
  [ajouter un enregistrement de zone]: ./media/web-sites-custom-domain-name/godaddy-addzonerecord.png
  [modes]: ../includes/custom-dns-web-site-enable-on-web-site.md
