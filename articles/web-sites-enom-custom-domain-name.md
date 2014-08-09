<properties title="Learn how to configure an Azure web site to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure web site" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Azure (eNom)
============================================================================

[Domaine personnalisé](/fr-fr/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/fr-fr/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/fr-fr/documentation/articles/web-sites-enom-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/fr-fr/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès d'[eNom](https://enom.com) avec le service Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par eNom. Pour localiser les outils DNS pour enom.com, procédez comme suit.

1.  Connectez-vous à votre compte eNom et sélectionnez **Domains** puis **My Domain**. Vos noms de domaine s'affichent.

2.  Sur la page **My Domains**, dans le champ **Manage Domain**, sélectionnez **Host Records**. Les champs d'enregistrements d'hôtes s'affichent.

    ![Onglet DNS Zone File](./media/web-sites-custom-domain-name/e-hostrecords.png)

3.  L'éditeur d'enregistrements d'hôtes vous permet de sélectionner le type d'enregistrement à l'aide du champ **Record Type**. Pour le service Sites Web Azure, vous devez uniquement utiliser la sélection **CNAME (Alias)** ou **A (Address)**.

    ![éditeur de fichier de zone](./media/web-sites-custom-domain-name/e-editrecords.png)

    > [WACOM.NOTE] Avant d'ajouter des entrées au fichier de zone, notez qu'eNom a déjà créé les enregistrements DNS pour le domaine racine (« @ ») et un caractère générique pour les sous-domaines (« \* »). Si vous souhaitez rediriger le domaine racine sur votre site Web ou si vous utilisez un enregistrement A comportant un caractère générique, vous devez modifier ces entrées au lieu d'en créer de nouvelles.

    -   Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Host Name** sur le sous-domaine à utiliser. Par exemple, **www**. Vous devez définir le champ **Address** sur le nom de domaine **.azurewebsites.net** de votre site Web Azure. Par exemple, **contoso.azurwebsites.net**.

        > [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
        >
        > -   Une valeur **Alias** de **www** dont la valeur **Other host** est définie sur **&lt;nomdevotresiteweb\>.azurewebsites.net**
        >
        > OU
        >
        > -   Une valeur **Alias** de **awverify.www** dont la valeur **Other host** est définie sur **awverify.&lt;nomdevotresiteweb\>.azurewebsites.net**
        >
        > Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

    -   Lorsque vous ajoutez un enregistrement A, vous devez définir le champ **Host Name** sur **@** (ce qui correspond au nom de domaine racine, comme **contoso.com**), \* (un caractère générique pour la correspondance de plusieurs sous-domaines) ou sur le sous-domaine que vous comptez utiliser (par exemple, **www**). Vous devez définir le champ **Address** sur l'adresse IP de votre site Web Azure.

        > [WACOM.NOTE] Lorsque vous ajoutez un enregistrement A, vous devez ajouter un enregistrement CNAME dont l'hôte est **awverify** et une valeur **Points to** qui est \*\*awverify.&lt;nomdevotresiteweb\>.azurewebsites.net.

4.  Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Save** pour enregistrer les modifications.

Activation du nom de domaine sur votre site Web
-----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

