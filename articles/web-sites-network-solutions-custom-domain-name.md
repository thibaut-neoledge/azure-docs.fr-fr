<properties title="Learn how to configure an Azure web site to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Configuration d'un nom de domaine personnalisé pour un site Web Azure (Network Solutions)
=========================================================================================

[Domaine personnalisé](/en-us/documentation/articles/web-sites-custom-domain-name "Domaine personnalisé")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Site Web](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name/ "Sites Web") | [Site Web utilisant Traffic Manager](/en-us/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/ "Site Web utilisant Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article contient des instructions permettant d'utiliser un nom de domaine personnalisé acheté auprès de [Network Solutions](https://networksolutions.com) avec le service Sites Web Azure.

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

Pour associer votre domaine personnalisé à un site Web Azure, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par Network Solutions. Pour localiser et utiliser les outils DNS pour networksolutions.com, procédez comme suit.

1.  Connectez-vous à votre compte sur networksolutions.com, puis sélectionnez **My Account** dans le coin supérieur droit.

2.  Sous l'onglet **My Products and Services**, sélectionnez **Edit DNS**.

    ![page Edit DNS](./media/web-sites-custom-domain-name/ns-editdns.png)

3.  Dans la section \*\*Manage \*\* de la page \*\*Domain Names\*\*, sélectionnez \*\*Edit Advanced DNS Records\*\*.

    ![Pages Domain Names avec Edit Advanced DNS Records mis en surbrillance](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4.  La page **Update Advanced DNS** contient une section pour chaque type d'enregistrement, avec un bouton **Edit** sous chaque section.

    -   Pour les enregistrements A, utilisez la section **IP Address (A Records)**.
    -   Pour les enregistrements CNAME, utilisez la section **Host Alias (CNAME Records)**.

    ![page Updated Advanced DNS](./media/web-sites-custom-domain-name/ns-updateadvanced.png)

5.  Lorsque vous cliquez sur le bouton **Edit**, vous accédez à un formulaire permettant de modifier les enregistrements existants ou d'en ajouter de nouveaux.

    > [WACOM.NOTE] Avant d'ajouter de nouvelles entrées, notez que Network Solutions a déjà créé quelques enregistrements DNS par défaut, pour le domaine racine(« @ ») et un enregistrement générique (« \* ») pour les sous-domaines, par exemple. Si l'enregistrement que vous souhaitez utiliser existe déjà, modifiez-le plutôt que d'en créer un nouveau.

    -   Lors de l'ajout d'un enregistrement CNAME, vous devez définir le champ **Alias** sur le sous-domaine que vous souhaitez utiliser. Par exemple, **www**. Vous devez sélectionner le champ circulaire situé en regard du champ **Other host** et définir **Other host** sur le nom de domaine **.trafficmanager.net** de votre site Web Azure. Par exemple, **contoso.azurwebsites.net**. Laissez le champ **Refers to Host Name** sur **Select**, car ce champ n'est pas obligatoire lors de la création d'un enregistrement CNAME à utiliser avec le service Sites Web Azure.

        ![Formulaire cname](./media/web-sites-custom-domain-name/ns-cname.png)

        > [WACOM.NOTE] Si vous utilisez un enregistrement A, vous devez également ajouter un enregistrement CNAME avec l'une des configurations suivantes :
        >
        > -   Une valeur **Alias** de **www** dont la valeur **Other host** est définie sur **&lt;nomdevotresiteweb\>.azurewebsites.net**
        >
        > OU
        >
        > -   Une valeur **Alias** de **awverify.www** dont la valeur **Other host** est définie sur **awverify.&lt;nomdevotresiteweb\>.azurewebsites.net**
        >
        > Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

    -   Lors de l'ajout d'un enregistrement A, vous devez définir le champ **Host** sur **@** (ce qui correspond au nom de domaine racine, comme **contoso.com**), sur \* (un caractère générique pour la correspondance de plusieurs sous-domaines) ou sur le sous-domaine que vous souhaitez utiliser (par exemple, **www**). Vous devez définir le champ **Numeric IP** sur l'adresse IP de votre site Web Azure.

        ![Formulaire d'enregistrement A](./media/web-sites-custom-domain-name/ns-arecord.png)

6.  Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Continue** pour vérifier les modifications. Pour enregistrer les modifications, sélectionnez **Save changes only**.

Activation du nom de domaine sur votre site Web
-----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

