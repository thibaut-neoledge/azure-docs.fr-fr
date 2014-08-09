<properties title="Learn how to configure an Azure web site to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, Directnic" description="Learn how to configure an Azure web site to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Azure (Directnic)
=================================================================================

[Domaine personnalisé](/fr-fr/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/fr-fr/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/fr-fr/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [DirectNic.com](https://directnic.com) avec le service Sites Web Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :

-   [Présentation des enregistrements DNS](#understanding-records)
-   [Configuration de vos sites Web pour le mode Basique, Partagé ou Standard](#bkmk_configsharedmode)
-   [Ajout d'un enregistrement DNS pour votre domaine personnalisé](#bkmk_configurecname)
-   [Activation du domaine sur votre site Web](#enabledomain)

Présentation des enregistrements DNS
------------------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

Configuration de vos sites Web pour le mode Basique, Partagé ou Standard
------------------------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

## Ajout d'un enregistrement DNS pour votre domaine personnalisé 

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Directnic. Pour localiser les outils DNS pour Directnic.com, procédez comme suit.

1.  Connectez-vous à votre compte Directnic.com et sélectionnez **My Services** puis **Domains**.

    ![Menu des services Directnic](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2.  Cliquez sur le nom de domaine que vous souhaitez utiliser pour votre site Web Azure.

3.  Dans la page de gestion de votre domaine, cliquez sur le bouton **Manage** correspondant à **DNS** dans le volet **Services**.

    ![Volet Services](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4.  Ajoutez des enregistrements DNS en remplissant les champs **Type**, **Name** et **Data**. Lorsque vous avez terminé, cliquez sur le bouton **Add Record**.

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png)

    -   Lorsque vous ajoutez un enregistrement CNAME, vous devez définir le champ **Name** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Vous devez définir le champ **Data** sur le nom de domaine **.azurewebsites.net** de votre site Web Azure. Par exemple, **contoso.azurwebsites.net**.

    -   Lors de l'ajout d'un enregistrement A, vous devez définir le champ **Name** sur **@** (ce qui correspond au nom de domaine racine, comme **contoso.com**) ou sur le sous-domaine que vous souhaitez utiliser (par exemple, **www**). Vous devez définir le champ **Data** en fonction de l'adresse IP de votre site Web Azure.

        > [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
        >
        > -   Une valeur **Name** de **www** dont la valeur **Data** est définie sur **&lt;nomdevotresiteweb\>.azurewebsites.net**
        >
        > OU
        >
        > -   Une valeur **Name** de **awverify.www** dont la valeur **Data** est définie sur **awverify.&lt;nomdevotresiteweb\>.azurewebsites.net**
        >
        > Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

Activation du nom de domaine sur votre site Web
-----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

