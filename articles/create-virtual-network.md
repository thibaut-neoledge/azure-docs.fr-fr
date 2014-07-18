Création d'un réseau virtuel dans Azure
=======================================

Ce didacticiel va vous guider lors de la création d'un réseau virtuel Azure Virtual Network de base à l'aide du portail de gestion Azure. Pour plus d'informations sur Azure Virtual Network, consultez la page [Vue d'ensemble d'Azure Virtual Network](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx).

Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. Il a pour but de vous familiariser avec la procédure requise pour créer un réseau virtuel. Si vous recherchez des scénarios de conception et des informations avancées sur Virtual Network, consultez la page [Vue d'ensemble d'Azure Virtual Network](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx).

À la fin de ce didacticiel, vous disposerez d'un réseau virtuel vers lequel vous pouvez déployer des services et des machines virtuelles Azure.

**Remarque**

Ce didacticiel ne présente pas la création d'une configuration entre différents locaux. Pour accéder à un didacticiel présentant les étapes de création d'un réseau virtuel avec une connexion de site à site entre différents locaux (par exemple, une connexion à Active Directory ou SharePoint au sein de votre société), consultez la rubrique [Création d'un réseau virtuel pour la connexion entre différents locaux](/en-us/manage/services/networking/cross-premises-connectivity/).

Pour plus d'informations sur les paramètres et procédures de configuration de Virtual Network, consultez la page [Tâches de configuration d'Azure Virtual Network](http://go.microsoft.com/fwlink/?LinkId=296652).

Pour obtenir des instructions sur le déploiement des services de domaine Active Directory (AD DS) dans Azure Virtual Machines, consultez la page [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Windows Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx).

Objectifs
---------

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   configurer un réseau virtuel Azure de base auquel vous pouvez ajouter Azure Cloud Services et des machines virtuelles.

Configuration requise
---------------------

-   Vous devez avoir un compte Windows Live avec au moins un abonnement actif en cours de validité.

Création d'un réseau virtuel
----------------------------

**Pour créer un réseau virtuel sur le cloud uniquement :**

1.  Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com/).

2.  Dans le coin inférieur gauche de l'écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Réseaux**, puis sur **Virtual Network**. Cliquez sur **Custom Create** pour démarrer l'Assistant Configuration.

    ![](./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png)

3.  Sur la page **Détails du réseau virtuel**, entrez les informations suivantes, puis cliquez sur la flèche Suivant située dans le coin inférieur droit. Pour plus d'informations sur les paramètres de la page des détails, consultez la section **Page Détails du réseau virtuel** de la page [À propos de la configuration d'un réseau virtuel dans le portail de gestion](http://go.microsoft.com/fwlink/?LinkID=248092).

-   **Nom -** Nommez votre réseau virtuel. Tapez *YourVirtualNetwork*.

-   **Groupe d'affinités -** Dans la liste déroulante, sélectionnez **Créer un nouveau groupe d'affinités**. Les groupes d'affinités permettent de regrouper physiquement les services Azure dans un même centre de données pour améliorer les performances. Vous ne pouvez attribuer qu'un seul réseau virtuel par groupe d'affinités.

-   **Région -** Dans la liste déroulante, sélectionnez la région de votre choix. Votre réseau virtuel sera créé dans un centre de données situé dans la région indiquée.

-   **Nom du groupe d'affinités -** Nommez le nouveau groupe d'affinités. Tapez *YourAffinityGroup*.

    ![](./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png)

1.  Sur la page **Serveurs DNS et connectivité VPN**, entrez les informations suivantes, puis cliquez sur la flèche Suivant située dans le coin inférieur droit. Pour plus d'informations sur la configuration de cette page, consultez la section **Page Serveurs DNS et connectivité VPN** de la page [À propos de la configuration d'un réseau virtuel à l'aide du portail de gestion](http://go.microsoft.com/fwlink/?LinkID=248092).

    -   **DNS Servers-Optional -** Entrez le nom de serveur DNS et l'adresse IP que vous souhaitez utiliser. Ce paramètre ne crée pas de serveur DNS ; il fait référence à un serveur DNS existant.

        **Remarque**

        Pour utiliser un service DNS public, vous pouvez entrer ces informations à l'écran. Sinon, la résolution de noms sera définie par défaut sur le service Azure. Pour plus d'informations, consultez la page [Vue d'ensemble de la résolution de noms Azure](http://go.microsoft.com/fwlink/?linkid=248097)

    -   **N'activez pas la case de la connectivité de point à site ou de site à site**. Le réseau virtuel que nous allons créer dans le cadre de ce didacticiel n'est pas conçu pour la connectivité entre différents locaux.

    ![](./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png)

2.  Sur la page **Virtual Network Address Spaces**, entrez les informations ci-dessous, puis cliquez sur la coche située dans le coin inférieur droit pour configurer votre réseau. L'espace d'adressage doit avoir une plage d'adresses privée, indiquée selon la notation CIDR 10.0.0.0/8, 172.16.0.0/12 ou 192.168.0.0/16 (comme indiqué par RFC 1918). Pour plus d'informations sur les paramètres de cette page, consultez la section **Page Espaces d'adresses du réseau virtuel** de la page [À propos de la configuration d'un réseau virtuel à l'aide du portail de gestion](http://go.microsoft.com/fwlink/?LinkID=248092).

    -   **Espace d'adressage :** cliquez sur CIDR dans le coin inférieur droit, puis entrez les informations suivantes :

        -   **Adresse IP de début :** 10.4.0.0

        -   **CIDR :** /16

    -   **Ajouter un sous-réseau :** entrez les informations suivantes :

        -   **Renommez Subnet-1** en *FrontEndSubnet* avec l'IP de démarrage *10.4.2.0/24*, puis cliquez sur **ajouter un sous-réseau**.

        -   **Créez un sous-réseau** appelé *BackEndSubnet* avec l'adresse IP de début *10.4.3.0/24*.

        -   Vérifiez que vous disposez à présent de deux sous-réseaux, puis cliquez sur la coche située dans le coin inférieur droit pour créer le réseau virtuel.

    ![](./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png)

3.  La création de votre réseau virtuel commence une fois que vous avez cliqué sur cette coche. Après la création de votre réseau virtuel, le statut **Créé** apparaît sous **Statut** sur la page des réseaux dans le portail de gestion.

    ![](./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png)

4.  Vous pouvez ensuite consulter les didacticiels suivants :

    -   [Ajout d'une machine virtuelle à un réseau virtuel](/en-us/manage/services/networking/add-a-vm-to-a-virtual-network/) - Ce didacticiel de base vous aide à installer une machine virtuelle dans le réseau virtuel.

    -   Pour plus d'informations sur les machines virtuelles et les options d'installation, consultez les rubriques [Création d'une machine virtuelle personnalisée](/en-us/manage/windows/how-to-guides/custom-create-a-vm/) et [Azure Virtual Machines](/en-us/manage/windows/).

    -   [Installation d'une nouvelle forêt Active Directory dans Azure](/en-us/manage/services/networking/active-directory-forest/) - Ce didacticiel vous aide à installer une nouvelle forêt sans connectivité avec un autre réseau. Il décrit les étapes nécessaires à la création d'une machine virtuelle pour une nouvelle installation de forêt. Si vous envisagez de suivre ce didacticiel, ne créez pas de machines virtuelles à l'aide du portail de gestion.

Voir aussi
----------

-   [Vue d'ensemble d'Azure Virtual Network](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)

-   [Forum aux questions sur Azure Virtual Network](http://go.microsoft.com/fwlink/?LinkId=296650)

-   [Tâches de configuration Azure Virtual Network](http://go.microsoft.com/fwlink/?LinkId=296652)

-   [Configuration d'un réseau virtuel à l'aide de fichiers de configuration de réseau](http://msdn.microsoft.com/en-us/library/windowsazure/jj156097.aspx)

-   [Présentation de la résolution de noms Azure](http://go.microsoft.com/fwlink/?LinkId=248097)


