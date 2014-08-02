<properties title="Learn how to configure an Azure web site to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, DomainDiscover, TierraNet" description="Learn how to configure an Azure web site to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Azure (DomainDiscover / TierraNet)
==================================================================================================

[Domaine personnalisé](/en-us/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/en-us/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [DomainDiscover.com](https://domaindiscover.com) avec le service Sites Web Azure. DomainDiscover.com fait partie de TierraNet.

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

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par DomainDiscover. Pour localiser et utiliser les outils DNS pour DomainDiscover.com, procédez comme suit.

1.  Connectez-vous à votre compte sur DomainDiscover.com (TierraNet) en sélectionnant **Control Panel** dans le menu **Login**.

    ![Menu Login de DomainDiscover](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2.  Sur la page **Domain Services**, sélectionnez le domaine que vous souhaitez utiliser pour votre site Web Azure.

    ![Page de gestion de domaine](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3.  Dans les paramètres de domaine, cliquez sur le bouton **Edit** correspondant à **DNS Service**.

    ![Bouton de modification des DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4.  Dans la fenêtre **Manage DNS**, sélectionnez le type d'enregistrement DNS à ajouter à la liste **Add Records**. Cliquez ensuite sur le bouton **Add**.

    ![Bouton de modification des DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5.  Sur la page suivante, entrez les valeurs d'enregistrement DNS. Cliquez ensuite sur le bouton **Add**.

    ![Bouton de modification des DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png)

    -   Lorsque vous ajoutez un enregistrement CNAME, vous devez d'abord sélectionner **CNAME (Alias)** sur la page **Manage DNS**. Définissez ensuite le champ **Host** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Vous devez définir le champ **Alias Hostname** sur le nom de domaine **.azurewebsites.net** de votre site Web Azure. Par exemple, **contoso.azurwebsites.net**. Ensuite, indiquez une valeur de durée de vie « time-to-live » (TTL), par exemple 1 800 secondes.

    -   Lorsque vous ajoutez un enregistrement A, vous devez d'abord sélectionner **A** sur la page **Manage DNS**. Ensuite, définissez le champ **Host** sur **@** (ce qui correspond au nom de domaine racine, comme **contoso.com**,) ou sur le sous-domaine que vous comptez utiliser (par exemple, **www**). Vous devez définir le champ **IP Address** sur l'adresse IP de votre site Web Azure. Ensuite, indiquez une valeur de durée de vie « time-to-live » (TTL), par exemple 1 800 secondes.

        > [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
        >
        > -   A Une valeur **Host** de **www** dont la valeur **Alias Hostname** est définie sur **&lt;nomdevotresiteweb\>.azurewebsites.net**
        >
        > OU
        >
        > -   Une valeur **Host** de **awverify.www** dont la valeur **Alias Hostname** est définie sur **awverify.&lt;nomdevotresiteweb\>.azurewebsites.net**
        >
        > Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

6.  Si vous avez ajouté un enregistrement A, il se peut qu'un avertissement s'affiche, indiquant que l'enregistrement A existant pour votre domaine n'est pas inactif. Ce dernier utilise le dernier enregistrement modifié et DomainDirect dispose déjà d'un enregistrement A par défaut pour le nom de domaine racine. Vous pouvez soit utiliser l'enregistrement précédent, soit supprimer l'enregistrement A par défaut en sélectionnant le bouton **DELETE**.

Activation du nom de domaine sur votre site Web
-----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

