<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure web site that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Azure à l'aide de Traffic Manager (Network Solutions)
=====================================================================================================================

[Domaine personnalisé](/en-us/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/en-us/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [Network Solutions](https://networksolutions.com) avec le service Sites Web Azure.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :

-   [Présentation des enregistrements DNS](#understanding-records)
-   [Configuration des sites Web pour le mode Standard](#bkmk_configsharedmode)
-   [Ajout d'un enregistrement DNS pour votre domaine personnalisé](#bkmk_configurecname)
-   [Activation de Traffic Manager pour votre site Web](#enabledomain)

Présentation des enregistrements DNS
------------------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

Configuration des sites Web pour le mode Standard
-------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

## Ajout d'un enregistrement DNS pour votre domaine personnalisé 

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Network Solutions. Pour localiser et utiliser les outils DNS, procédez comme suit.

1.  Connectez-vous à votre compte sur networksolutions.com, puis sélectionnez **My Account** dans le coin supérieur droit.

2.  Sous l'onglet **My Products and Services**, sélectionnez **Edit DNS**.

    ![page Edit DNS](./media/web-sites-custom-domain-name/ns-editdns.png)

3.  Dans la section \*\*Manage \*\* de la page \*\*Domain Names\*\*, sélectionnez \*\*Edit Advanced DNS Records\*\*.

    ![Pages Domain Names avec Edit Advanced DNS Records mis en surbrillance](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4.  La page **Update Advanced DNS** contient une section pour chaque type d'enregistrement, avec un bouton **Edit** sous chaque section. Pour les enregistrements CNAME, utilisez la section **Host Alias (CNAME Records)**.

    ![page Updated Advanced DNS](./media/web-sites-custom-domain-name/ns-updatecnameadvanced.png)

5.  Lorsque vous cliquez sur le bouton **Edit**, vous accédez à un formulaire permettant de modifier les enregistrements existants ou d'en ajouter de nouveaux.

    Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Alias** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Vous devez sélectionner le champ circulaire situé en regard du champ **Other host** et définir **Other host** sur le nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site Web Azure. Par exemple, **contoso.trafficmanager.net**. Laissez le champ **Refers to Host Name** sur **Select**, car ce champ n'est pas obligatoire lors de la création d'un enregistrement CNAME à utiliser avec le service Sites Web Azure.

    ![Formulaire cname](./media/web-sites-custom-domain-name/ns-cnametm.png)

6.  Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Continue** pour vérifier les modifications. Pour enregistrer les modifications, sélectionnez **Save changes only**.

Activation du site Web Traffic Manager
--------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

