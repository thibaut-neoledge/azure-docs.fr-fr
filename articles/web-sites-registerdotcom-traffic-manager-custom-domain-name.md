<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure website that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Configuration d'un nom de domaine personnalisé pour un site web Azure à l'aide de Traffic Manager (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/web-sites-custom-domain-name" title="Domaine personnalis&eacute;">Domaine personnalis&eacute;</a><a href="/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/fr-fr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Sites web">Site web</a> | <a href="/fr-fr/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Site web &agrave; l'aide de Traffic Manager" class="current">Site web &agrave; l'aide de Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][]]

[WACOM.INCLUDE [intro][]]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [Register.com][1] avec le service Sites Web Azure.

[WACOM.INCLUDE [tmwebsitefooter][]]

[WACOM.INCLUDE [introfooter][]]

Dans cet article :

-   [Présentation des enregistrements DNS][]
-   [Configurer vos sites web pour le mode Standard][]
-   [Ajouter un enregistrement DNS pour votre domaine personnalisé][]
-   [Activer Traffic Manager pour votre site web][]

## <a name="understanding-records"></a>Présentation des enregistrements DNS

[WACOM.INCLUDE [understandingdns][]]

## <a name="bkmk_configsharedmode"></a>Configurer vos sites web pour le mode Standard

[WACOM.INCLUDE [modes][]]

<a name="bkmk_configurecname"></a>

## Ajouter un enregistrement DNS pour votre domaine personnalisé

</p>
Pour associer votre domaine personnalisé à un site web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Register.com. Pour localiser et utiliser les outils DNS, procédez comme suit.

1.  Connectez-vous à votre compte sur register.com et sélectionnez **Your Account** dans le coin supérieur droit pour afficher vos domaines, puis sélectionnez votre nom de domaine personnalisé.

    ![Page My Account][]

2.  Faites défiler la page pour accéder à **Advanced Technical Settings**. Les liens de cette section vous permettent de gérer les enregistrements de votre domaine. Pour les enregistrements CNAME, utilisez le lien **Edit Domain Aliases Records**.

    ![Paramètres techniques avancés][]

3.  Lorsque vous cliquez sur le bouton **Edit**, vous accédez à un formulaire permettant de modifier les enregistrements existants ou d'en ajouter de nouveaux. Le formulaire est identique pour les enregistrements CNAME et A.

    -   Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **.mydomainname.com** sur le sous-domaine à utiliser. Par exemple, **www**. Définissez la valeur **points to** sur le nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site web Azure. Par exemple, **contoso.trafficmanager.net**. Laissez le champ **Refers to Host Name** sur **Select**, car ce champ n'est pas obligatoire lors de la création d'un enregistrement CNAME à utiliser avec le service Sites Web Azure.

        ![Formulaire cname][]

4.  Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Continue** pour vérifier les modifications. Sélectionnez à nouveau **Continue** pour enregistrer les modifications.

## <a name="enabledomain"></a>Activer Traffic Manager pour votre site web

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
  [Site web]: /fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name/ "Sites web"
  [Site web à l'aide de Traffic Manager]: /fr-fr/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/ "Site web à l'aide de Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [1]: https://www.register.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Présentation des enregistrements DNS]: #understanding-records
  [Configurer vos sites web pour le mode Standard]: #bkmk_configsharedmode
  [Ajouter un enregistrement DNS pour votre domaine personnalisé]: #bkmk_configurecname
  [Activer Traffic Manager pour votre site web]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Page My Account]: ./media/web-sites-custom-domain-name/rdotcom-myaccount.png
  [Paramètres techniques avancés]: ./media/web-sites-custom-domain-name/rdotcom-advancedsettingstm.png
  [Formulaire cname]: ./media/web-sites-custom-domain-name/rdotcom-editcnamerecordtm.png
  [2]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
