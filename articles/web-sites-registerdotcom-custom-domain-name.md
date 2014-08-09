<properties title="Learn how to configure an Azure web site to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Azure (Register.com)
====================================================================================

[Domaine personnalisé](/fr-fr/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/fr-fr/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/fr-fr/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/fr-fr/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/fr-fr/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/fr-fr/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/fr-fr/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/fr-fr/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/fr-fr/documentation/articles/web-sites-registerdotcom-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/fr-fr/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [Register.com](https://register.com) avec le service Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Register.com. Pour localiser et utiliser les outils DNS, procédez comme suit.

1.  Connectez-vous à votre compte sur register.com et sélectionnez **Your Account** dans le coin supérieur droit pour afficher vos domaines, puis sélectionnez votre nom de domaine personnalisé.

    ![Page My Account](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

2.  Faites défiler la page pour accéder à **Advanced Technical Settings**. Les liens de cette section vous permettent de gérer les enregistrements de votre domaine.

    -   Pour les enregistrements A, utilisez le lien **Edit IP Address Records**.
    -   Pour les enregistrements CNAME, utilisez le lien **Edit Domain Aliases Records**.

    ![Paramètres techniques avancés](./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png)

3.  Lorsque vous cliquez sur le bouton **Edit**, vous accédez à un formulaire permettant de modifier les enregistrements existants ou d'en ajouter de nouveaux. Le formulaire est identique pour les enregistrements CNAME et A.

    -   Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **.mydomainname.com** sur le sous-domaine à utiliser. Par exemple, **www**. Vous devez définir la valeur **points to** sur le nom de domaine **.azurewebsites.net** de votre site Web Azure. Par exemple, **contoso.azurwebsites.net**. Laissez le champ **Refers to Host Name** sur **Select**, car ce champ n'est pas obligatoire lors de la création d'un enregistrement CNAME à utiliser avec le service Sites Web Azure.

        ![Formulaire cname](./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png)

        > [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
        >
        > -   Une valeur **Alias** de **www** dont la valeur **Other host** est définie sur **&lt;nomdevotresiteweb\>.azurewebsites.net**
        >
        > OU
        >
        > -   Une valeur **Alias** de **awverify.www** dont la valeur **Other host** est définie sur **awverify.&lt;nomdevotresiteweb\>.azurewebsites.net**
        >
        > Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

    -   Lors de l'ajout d'un enregistrement A, vous devez définir le champ **.mydomainname.com** sur le sous-domaine que vous souhaitez utiliser (par exemple, **www**). Laissez ce champ vierge pour définir le domaine racine ou utilisez **\*\* pour créer un mappage de caractère générique. Vous devez définir le champ \*\*points to** sur l'adresse IP de votre site Web Azure.

        ![Formulaire d'enregistrement A](./media/web-sites-custom-domain-name/rdotcom-editarecord.png)

4.  Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Continue** pour vérifier les modifications. Sélectionnez à nouveau **Continue** pour enregistrer les modifications.

Activation du nom de domaine sur votre site Web
-----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

