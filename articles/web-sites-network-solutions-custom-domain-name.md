<properties title="Learn how to configure an Azure website to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Configuration d'un nom de domaine personnalisé pour un site web Azure (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Domaine personnalis&eacute;">Domaine personnalis&eacute;</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Sites web" class="current">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Site web &agrave; l'aide de Traffic Manager">Site web &agrave; l'aide de Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][]]

[WACOM.INCLUDE [intro][]]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [Network Solutions][1] avec le service Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Network Solutions. Pour localiser et utiliser les outils DNS pour networksolutions.com, procédez comme suit.

1.  Connectez-vous à votre compte sur networksolutions.com, puis sélectionnez **My Account** dans le coin supérieur droit.

2.  Sous l'onglet **My Products and Services**, sélectionnez **Edit DNS**.

    ![page Edit DNS][]

3.  Dans la section **Manage <yourdomainname>** de la page **Domain Names**, sélectionnez **Edit Advanced DNS Records**.

    ![Pages Domain Names avec Edit Advanced DNS Records mis en surbrillance][]

4.  La page **Update Advanced DNS** contient une section pour chaque type d'enregistrement, avec un bouton **Edit** sous chaque section.

    -   Pour les enregistrements A, utilisez la section **IP Address (A Records)**.
    -   Pour les enregistrements CNAME, utilisez la section **Host Alias (CNAME Records)**.

    ![page Updated Advanced DNS][]

5.  Lorsque vous cliquez sur le bouton **Edit**, vous accédez à un formulaire permettant de modifier les enregistrements existants ou d'en ajouter de nouveaux.

    > [WACOM.NOTE] Avant d'ajouter de nouvelles entrées, notez que Network Solutions a déjà créé quelques enregistrements DNS par défaut, pour le domaine racine(« @ ») et un enregistrement générique (« \* ») pour les sous-domaines, par exemple. Si l'enregistrement que vous souhaitez utiliser existe déjà, modifiez-le plutôt que d'en créer un nouveau.

    -   Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Alias** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Vous devez sélectionner le champ circulaire situé en regard du champ **Other host** et définir **Other host** sur le nom de domaine **.trafficmanager.net** de votre site web Azure. Par exemple, **contoso.azurwebsites.net**. Laissez le champ **Refers to Host Name** sur **Select**, car ce champ n'est pas obligatoire lors de la création d'un enregistrement CNAME à utiliser avec le service Sites Web Azure.

        ![Formulaire cname][]

        > [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
        >
        > -   Une valeur **Alias** de **www** dont la valeur **Other host** est définie sur **\<nomdevotresiteweb\>.azurewebsites.net**
        >
        > OU
        >
        > -   Une valeur **Alias** de **awverify.www** dont la valeur **Other host** est définie sur **awverify.\<nomdevotresiteweb\>.azurewebsites.net**
        >
        > Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

    -   Lors de l'ajout d'un enregistrement A, vous devez définir le champ **Host** sur **@** (ce qui correspond au nom de domaine racine, comme **contoso.com**), sur \* (un caractère générique pour la correspondance de plusieurs sous-domaines) ou sur le sous-domaine que vous souhaitez utiliser (par exemple, **www**.) Vous devez définir le champ **Numeric IP** sur l'adresse IP de votre site web Azure.

        ![Formulaire d'enregistrement A][]

6.  Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Continue** pour vérifier les modifications. Pour enregistrer les modifications, sélectionnez **Save changes only**.

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
  [Site web]: /fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name/ "Sites web"
  [Site web à l'aide de Traffic Manager]: /fr-fr/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/ "Site web à l'aide de Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [1]: https://www.networksolutions.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Présentation des enregistrements DNS]: #understanding-records
  [Configurer vos sites web pour le mode De base, Partagé ou Standard]: #bkmk_configsharedmode
  [Ajouter un enregistrement DNS pour votre domaine personnalisé]: #bkmk_configurecname
  [Activer le domaine sur votre site Web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [page Edit DNS]: ./media/web-sites-custom-domain-name/ns-editdns.png
  [Pages Domain Names avec Edit Advanced DNS Records mis en surbrillance]: ./media/web-sites-custom-domain-name/ns-editadvanced.png
  [page Updated Advanced DNS]: ./media/web-sites-custom-domain-name/ns-updateadvanced.png
  [Formulaire cname]: ./media/web-sites-custom-domain-name/ns-cname.png
  [Formulaire d'enregistrement A]: ./media/web-sites-custom-domain-name/ns-arecord.png
  [2]: ../includes/custom-dns-web-site-enable-on-web-site.md
