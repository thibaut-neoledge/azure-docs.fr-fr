<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure web site that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Azure à l'aide de Traffic Manager (eNom)
========================================================================================================

[Domaine personnalisé](/fr-fr/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/fr-fr/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/fr-fr/documentation/articles/web-sites-enom-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/fr-fr/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès d'[eNom](https://enom.com) avec le service Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par eNom. Pour localiser les outils DNS pour enom.com, procédez comme suit.

1.  Connectez-vous à votre compte eNom et sélectionnez **Domains** puis **My Domain**. Vos noms de domaine s'affichent.

2.  Sur la page **My Domains**, dans le champ **Manage Domain**, sélectionnez **Host Records**. Les champs d'enregistrements d'hôtes s'affichent.

    ![Onglet DNS Zone File](./media/web-sites-custom-domain-name/e-hostrecords.png)

3.  L'éditeur d'enregistrements d'hôtes vous permet de sélectionner le type d'enregistrement à l'aide du champ **Record Type**. Pour les sites Web Azure utilisant Traffic Manager, utilisez uniquement l'option **CNAME (Alias)** car Traffic Manager n'est compatible qu'avec les enregistrements CNAME.

    ![éditeur de fichier de zone](./media/web-sites-custom-domain-name/e-editrecordstm.png)

    -   Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Host Name** sur le sous-domaine à utiliser. Par exemple, **www**. Vous devez définir le champ **Address** sur le nom de domaine **.trafficmanager.net** du profil Traffic Manager utilisé avec votre site Web Azure. Par exemple, **contoso.trafficmanager.net**.

4.  Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Save** pour enregistrer les modifications.

Activation du site Web Traffic Manager
--------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

