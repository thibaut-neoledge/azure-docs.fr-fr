<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Configure Traffic Manager Settings" authors="" solutions="" manager="" editor="" />

Configuration des paramètres Traffic Manager
============================================

Azure Traffic Manager permet de contrôler la répartition du trafic utilisateur sur les services hébergés Azure.

Traffic Manager utilise un moteur de stratégie intelligent pour les requêtes DNS exécutées sur le nom de domaine d'entreprise principal. Vous pouvez ainsi mettre à jour les enregistrements de ressource DNS de votre entreprise afin de les faire pointer vers des domaines Traffic Manager. Les stratégies Traffic Manager associées à ces domaines résolvent les requêtes DNS exécutées sur le nom de domaine d'entreprise principal en adresses IP de services hébergés Azure spécifiques inclus dans les stratégies Traffic Manager. Pour plus d'informations, consultez la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh744833.aspx).

Sommaire
--------

-   [Redirection d'un domaine Internet d'entreprise vers un domaine Traffic Manager](#howto_point_company)
-   [Test d'une stratégie](#howto_test)
-   [Désactivation temporaire des stratégies et services hébergés](#howto_temp_disable)
-   [Modification d'une stratégie](#howto_edit_policy)
-   [Équilibrage de charge du trafic sur un ensemble de services hébergés](#howto_load_balance)
-   [Création d'une stratégie de basculement](#howto_create_failover)
-   [Redirection du trafic entrant vers des services hébergés en fonction des performances réseau](#howto_direct)

Redirection d'un domaine Internet d'entreprise vers un domaine Traffic Manager
------------------------------------------------------------------------------

Pour faire pointer votre nom de domaine d'entreprise vers un domaine Traffic Manager, modifiez l'enregistrement de ressource DNS sur votre serveur DNS à l'aide d'un enregistrement CNAME.

Par exemple, pour faire pointer le domaine d'entreprise principal **www.contoso.com** vers un domaine Traffic Manager nommé **contoso.trafficmanager.net**, mettez à jour l'enregistrement de ressource DNS comme suit : `www.contoso.com IN CNAME contoso.trafficmanager.net`

L'intégralité du trafic entrant pour *www.contoso.com* sera alors redirigée vers *contoso.trafficmanager.net*. Veillez à utiliser un domaine pour lequel vous souhaitez rediriger la totalité du trafic vers Traffic Manager.

Test d'une stratégie
--------------------

La meilleure façon de tester une stratégie est de configurer des clients puis de mettre hors fonction un à un les services inclus dans la stratégie. Voici quelques conseils relatifs au test d'une stratégie Traffic Manager :

-   **Définissez une durée de vie DNS très courte**, afin que les modifications se répercutent rapidement, en 30 secondes, par exemple.

-   **Veillez à connaître l'adresse IP de tous les services hébergés Azure** de la stratégie testée. Ces informations sont disponibles via le portail de gestion Azure. Cliquez sur le déploiement de production du service puis, dans le volet Propriétés à droite, recherchez l'adresse IP virtuelle (la dernière entrée).

    ![Emplacement de l'adresse IP d'un service hébergé](./media/traffic-manager-configure-settings/hosted_service_IP_location.png)

    **Illustration 1** - Emplacement de l'adresse IP d'un service hébergé

-   **Utilisez des outils permettant de résoudre un nom DNS en adresse IP** et d'afficher cette adresse. Vous devez vérifier que votre nom de domaine d'entreprise est résolu en adresses IP des services hébergés de la stratégie. Ils doivent être résolus de manière cohérente avec la méthode d'équilibrage de charge utilisée pour la stratégie Traffic Manager. Si vous disposez d'un système Windows, vous pouvez utiliser le programme nslookup à partir d'une fenêtre de commande (voir ci-après). D'autres outils publics permettant d'obtenir les adresses IP sont également disponibles sur Internet.

-   **Contrôlez la stratégie Traffic Manager** à l'aide de *nslookup*.

> **Pour contrôler une stratégie Traffic Manager à l'aide de nslookup :**

> 1.  Sélectionnez **Démarrer - Exécuter**, puis tapez **CMD** pour ouvrir une fenêtre de commande.

> 1.  Tapez `ipconfig /flushdns` pour vider le cache de résolution DNS.

> 1.  Tapez la commande `nslookup <your traffic manager domain>`. Par exemple, la commande suivante permet de contrôler un domaine ayant le préfixe *myapp.contoso* : `nslookup myapp.contoso.trafficmanager.net`.

> > On obtient alors le résultat suivant :

> > -   le nom DNS et l'adresse IP du serveur DNS utilisé pour résoudre le domaine Traffic Manager ;

> > -   le nom de domaine Traffic Manager saisi dans la ligne de commande après « nslookup » et l'adresse IP correspondant au domaine Traffic Manager. La deuxième adresse IP est celle à vérifier. Elle doit correspondre à l'adresse IP virtuelle de l'un des services hébergés de la stratégie Traffic Manager testée.

> > ![Exemple de commande nslookup](./media/traffic-manager-configure-settings/nslookup_command_example.png)

> > **Illustration 2** - Exemple de commande nslookup

-   **Utilisez l'une des méthodes de test supplémentaires répertoriées ci-après.** Sélectionnez la méthode appropriée pour le type d'équilibrage de charge testé.

### Stratégies de performance

Vous devez disposer de clients répartis dans différentes parties du monde pour pouvoir tester efficacement votre domaine. Vous pouvez en créer dans Azure, afin qu'ils tentent d'appeler vos services via votre nom de domaine d'entreprise. Si votre entreprise est implantée à l'international, vous pouvez également vous connecter à distance à des clients dans d'autres régions et procéder au test depuis ces clients.

Vous trouverez différents services dig et nslookup gratuits sur Internet. Certains d'entre eux permettent de contrôler la résolution de noms DNS à partir de différents emplacements. Faites une recherche sur nslookup pour obtenir des exemples. Vous pouvez également utiliser une solution tierce, telle que Gomez ou Keynote, pour vérifier que vos stratégies répartissent le trafic comme prévu.

### Stratégies de basculement

1.  Laissez tous les services en fonction.

2.  Utilisez un client unique.

3.  Exécutez une requête de résolution DNS pour votre domaine d'entreprise à l'aide d'un utilitaire ping ou similaire.

4.  Vérifiez que l'adresse IP obtenue correspond au premier service hébergé.

5.  Mettez le service principal hors fonction ou supprimez le fichier de surveillance afin que Traffic Manager pense qu'il l'est.

6.  Patientez au moins 2 minutes.

7.  Exécutez une requête de résolution DNS.

8.  Vérifiez que l'adresse IP obtenue correspond au deuxième service hébergé, conformément à l'ordre des services indiqué dans la boîte de dialogue Edit Traffic Manager Policy.

9.  Répétez le processus, en mettant le deuxième service hors fonction, puis le troisième, etc. Assurez-vous chaque fois que la résolution DNS renvoie l'adresse IP du service suivant dans la liste. Une fois tous les services hors fonction, vous devez obtenir à nouveau l'adresse IP du premier service hébergé.

### Stratégies de tourniquet

1.  Laissez tous les services en fonction.

2.  Utilisez un client unique.

3.  Exécutez une requête de résolution DNS pour le domaine supérieur.

4.  Vérifiez que l'adresse IP obtenue figure bien dans votre liste.

5.  Répétez le processus, en laissant le DNS expirer, afin de recevoir une nouvelle adresse IP. Vous devriez alors obtenir les adresses IP de chacun de vos services hébergés. Ensuite, le processus se répète.

Désactivation temporaire des stratégies et services hébergés
------------------------------------------------------------

Vous pouvez désactiver des stratégies Traffic Manager précédemment créées afin que le trafic ne soit plus redirigé. Lorsqu'une stratégie Traffic Manager est désactivée, les informations connexes restent intactes et peuvent être modifiées via l'interface de Traffic Manager. Vous pouvez ensuite facilement réactiver la stratégie afin que le trafic reprenne son cours.

Vous pouvez également désactiver les services hébergés des stratégies Traffic Manager. Un service hébergé désactivé reste inclus dans la stratégie, mais celle-ci agit comme si le service n'existait pas. Cela s'avère particulièrement utile pour supprimer temporairement un service hébergé en mode de maintenance ou redéployé et, par conséquent, instable. Une fois le service hébergé à nouveau en fonction, il peut être réactivé.

**Remarque**

 La désactivation d'un service hébergé n'a aucun lien avec son état de déploiement dans Azure. Un service sain reste en fonction et peut recevoir du trafic même lorsqu'il est désactivé dans Traffic Manager. De même, le fait de désactiver un service hébergé dans une stratégie n'affecte pas son statut dans une autre stratégie.

### Pour désactiver une stratégie :

1.  Sélectionnez une stratégie active dans l'arborescence de l'interface Traffic Manager.

2.  Cliquez sur **Désactiver la stratégie** dans la barre d'outils supérieure. Notez que ce bouton est grisé si la stratégie sélectionnée est déjà inactive.

### Pour activer une stratégie :

1.  Sélectionnez une stratégie inactive dans l'arborescence de l'interface Traffic Manager.

2.  Cliquez sur **Activer la stratégie** dans la barre d'outils supérieure. Notez que ce bouton est grisé si la stratégie sélectionnée est déjà inactive.

### Pour désactiver un service hébergé :

1.  Sélectionnez un service hébergé actif dans une stratégie dans l'interface Traffic Manager. Vous devez développer une stratégie pour pouvoir accéder aux services hébergés qu'elle contient.

2.  Cliquez sur **Disable Service Policy** dans la barre d'outils supérieure. Notez que ce bouton est grisé si le service hébergé sélectionné est déjà inactif.

3.  Le trafic vers le service hébergé est alors interrompu, en fonction de la durée de vie DNS définie pour le domaine Traffic Manager appartenant à la stratégie. Pour plus d'informations, consultez la section « À propos de la surveillance Traffic Manager » de la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring).

### Pour activer un service hébergé :

1.  Sélectionnez un service hébergé inactif dans une stratégie dans l'interface Traffic Manager. Vous devez développer une stratégie pour pouvoir accéder aux services hébergés qu'elle contient.

2.  Cliquez sur **Enable Service Policy** dans la barre d'outils supérieure. Notez que ce bouton est grisé si le service hébergé sélectionné est déjà actif.

3.  Le trafic vers le service hébergé reprend alors, comme défini par la stratégie.

Modification d'une stratégie
----------------------------

Si vous avez besoin de mettre temporairement hors fonction une stratégie ou des services hébergés spécifiques d'une stratégie, vous pouvez les désactiver temporairement sans modifier la stratégie.

Pour modifier le type d'une stratégie :

1.  **Connectez-vous à la section Traffic Manager du portail de gestion**, à l'adresse <http://manage.windowsazure.com>. Cliquez sur **Virtual Network** dans l'angle inférieur gauche des pages du portail, puis sélectionnez **Traffic Manager** dans le volet de gauche.

2.  **Sélectionnez la stratégie** à modifier dans l'écran Traffic Manager du portail de gestion.

3.  **Cliquez sur Configurer** dans la barre de menus supérieure.

4.  **Modifiez la stratégie comme nécessaire.** Notez que si vous modifiez la méthode d'équilibrage de charge, selon l'option sélectionnée, l'ordre des services hébergés dans la liste **Selected hosted services** peut avoir de l'importance.

5.  Cliquez sur **Enregistrer**.

Équilibrage de charge du trafic sur un ensemble de services hébergés
--------------------------------------------------------------------

Il est courant d'utiliser un ensemble de services hébergés identiques et de répartir le trafic en tourniquet. Cet article détaille la procédure à suivre pour configurer un domaine et une stratégie Traffic Manager pour ce type d'équilibrage de charge.

Pour plus d'informations sur les différentes méthodes d'équilibrage de charge assumées par Traffic Manager, consultez la section « À propos des méthodes d'équilibrage de charge de Traffic Manager » de la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh744833.aspx).

1.  **Déployez les services hébergés** dans votre environnement de production. Pour plus d'informations sur le développement et le déploiement des services hébergés, consultez la rubrique [Services hébergés Azure](http://msdn.microsoft.com/library/gg432967.aspx).

2.  **Connectez-vous à la section Traffic Manager du portail de gestion**, à l'adresse <http://manage.windowsazure.com>. Cliquez sur **Virtual Network** dans l'angle inférieur gauche des pages du portail, puis sélectionnez **Traffic Manager** dans le volet de gauche.

3.  **Sélectionnez Stratégies, puis cliquez sur Créer.** Sélectionnez le dossier **Stratégies** dans l'arborescence de gauche afin d'activer l'option **Créer** dans la barre d'outils supérieure, puis cliquez sur **Créer**. La boîte de dialogue **Create Traffic Manager policy** s'affiche alors.

    ![Bouton Créer pour les stratégies](./media/traffic-manager-configure-settings/Create_button_for_policies.png)

    **Illustration 1** - Bouton Créer pour les stratégies

4.  **Choisissez un abonnement.** Les stratégies et domaines sont associés à un abonnement unique.

5.  **Sélectionnez la méthode d'équilibrage de charge Tourniquet.** Pour plus d'informations sur les différentes méthodes d'équilibrage de charge assumées par Traffic Manager, consultez la section « À propos des méthodes d'équilibrage de charge de Traffic Manager » de la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh744833.aspx).

6.  **Sélectionnez les services hébergés afin de les ajouter à la stratégie.** Vous pouvez effectuer une recherche à l'aide du champ de filtrage ou laisser ce champ vide pour afficher tous les services hébergés en production pour l'abonnement sélectionné à l'étape 4. Utilisez ensuite les boutons fléchés pour les ajouter à la stratégie. L'ordre des éléments spécifiés dans le champ **Selected DNS names** n'a aucune incidence dans le cadre de cette méthode d'équilibrage de charge.

7.  **Configurez la surveillance.** La surveillance permet de s'assurer qu'aucun trafic n'est dirigé vers les services hébergés désactivés. Vous devez disposer d'un chemin d'accès et d'un nom de fichier spécifiques configurés. Pour plus d'informations, consultez la section « À propos de la surveillance Traffic Manager » de la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring).

8.  **Attribuez un nom au domaine Traffic Manager.** Indiquez un nom de domaine unique. Notez que seul le préfixe du domaine peut être spécifié. Laissez la durée de vie DNS à la valeur par défaut. Pour plus d'informations sur l'incidence de ce paramètre, consultez les meilleures pratiques recommandées relatives aux stratégies et aux services hébergés avec Azure Traffic Manager, dans la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring).

    La boîte de dialogue **Create Traffic Manager policy** doit être similaire à l'illustration ci-après.

    ![Boîte de dialogue de la méthode d'équilibrage de charge Tourniquet](./media/traffic-manager-configure-settings/Dialog_box_for_Round_Robin_load_balancing_method.png)

    **Illustration 2** - Boîte de dialogue de la méthode d'équilibrage de charge Tourniquet

9.  **Testez le domaine et la stratégie Traffic Manager.** Pour plus d'informations, consultez la rubrique [Test d'une stratégie Azure Traffic Manager](#howto_test).

10. **Faites pointer le serveur DNS vers le domaine Traffic Manager.** Une fois le domaine Traffic Manager configuré et opérationnel, modifiez l'enregistrement DNS sur le serveur DNS faisant autorité, afin de faire pointer votre domaine d'entreprise vers le domaine Traffic Manager. Par exemple, la commande suivante redirige l'intégralité du trafic entrant pour **www.contoso.com** vers le domaine Traffic Manager **contoso.trafficmanager.net** : `www.contoso.com IN CNAME contoso.trafficmanager.net`. Pour plus d'informations, consultez la rubrique [Redirection d'un domaine Internet d'entreprise vers un domaine Azure Traffic Manager](#howto_point_company).

Création d'une stratégie de basculement
---------------------------------------

Bien souvent, une organisation souhaite assurer une certaine fiabilité pour ses services. Pour ce faire, elle fournit des services de sauvegarde au cas où le service principal serait hors fonction. Dans le cadre du basculement des services, il est courant d'utiliser un ensemble de services hébergés identiques et de diriger le trafic vers un service principal, avec une liste répertoriant une ou plusieurs sauvegardes. Cet article détaille la procédure à suivre pour configurer une stratégie Traffic Manager pour ce type de sauvegarde de basculement.

Pour plus d'informations sur les différentes méthodes d'équilibrage de charge assumées par Traffic Manager, consultez la section « À propos des méthodes d'équilibrage de charge de Traffic Manager » de la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh744833.aspx).

1.  **Déployez les services hébergés** dans votre environnement de production. Pour plus d'informations sur le développement et le déploiement des services hébergés, consultez la rubrique [Services hébergés Azure](http://msdn.microsoft.com/library/gg432967.aspx).

2.  **Connectez-vous à la section Traffic Manager du portail de gestion**, à l'adresse <http://manage.windowsazure.com>. Cliquez sur **Virtual Network** dans l'angle inférieur gauche des pages du portail, puis sélectionnez **Traffic Manager** dans le volet de gauche.

3.  **Sélectionnez Stratégies, puis cliquez sur Créer.** Sélectionnez le dossier **Stratégies** dans l'arborescence de gauche afin d'activer l'option **Créer** dans la barre d'outils supérieure, puis cliquez sur **Créer**. La boîte de dialogue **Create Traffic Manager policy** s'affiche alors.

    ![Bouton Créer pour les stratégies](./media/traffic-manager-configure-settings/Create_button_for_policies.png)

    **Illustration 1** - Bouton Créer pour les stratégies

4.  **Choisissez un abonnement.** Les stratégies et domaines sont associés à un abonnement unique.

5.  **Sélectionnez la méthode d'équilibrage de charge Basculement.** Pour plus d'informations sur les différentes méthodes d'équilibrage de charge assumées par Traffic Manager, consultez la section « À propos des méthodes d'équilibrage de charge de Traffic Manager » de la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh744833.aspx).

6.  **Sélectionnez les services hébergés afin de les ajouter à la stratégie.** Vous pouvez effectuer une recherche à l'aide du champ de filtrage ou laisser ce champ vide pour afficher tous les services hébergés en production pour l'abonnement sélectionné à l'étape 4. Utilisez ensuite les boutons fléchés pour les ajouter à la stratégie. Dans le cadre de la méthode d'équilibrage de charge **Basculement**, l'ordre de classement des services sélectionnés importe. Le service hébergé principal doit se trouver en haut de la liste. Utilisez les flèches vers le haut et vers le bas pour modifier l'ordre si besoin.

7.  La surveillance permet de s'assurer qu'aucun trafic n'est dirigé vers les services hébergés désactivés. Utiliser une stratégie de basculement sans configurer la surveillance est inutile, car une partie du trafic sera envoyée au service hébergé principal, même s'il est affiché comme étant désactivé. Pour surveiller les services hébergés, vous devez indiquer un chemin et un nom de fichier spécifiques. Pour plus d'informations, consultez la section « À propos de la surveillance Traffic Manager » de la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring).

8.  **Attribuez un nom au domaine Traffic Manager.** Indiquez un nom de domaine unique. Notez que seul le préfixe du domaine peut être spécifié. Laissez la **durée de vie DNS** à la valeur par défaut. Pour plus d'informations sur l'incidence de ce paramètre, consultez les meilleures pratiques recommandées relatives aux stratégies et aux services hébergés avec Azure Traffic Manager, dans la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring).

    La boîte de dialogue **Create Traffic Manager policy** doit être similaire à l'illustration ci-après.

    ![Boîte de dialogue de la méthode d'équilibrage de charge Basculement](./media/traffic-manager-configure-settings/Dialog_box_for_Failover_load_balancing_method.png)

    **Illustration 2** - Boîte de dialogue de la méthode d'équilibrage de charge Basculement

9.  **Testez le domaine et la stratégie Traffic Manager.** Pour plus d'informations, consultez la rubrique [Test d'une stratégie Azure Traffic Manager](#howto_test).

10. **Faites pointer le serveur DNS vers le domaine Traffic Manager.** Une fois le domaine Traffic Manager configuré et opérationnel, modifiez l'enregistrement DNS sur le serveur DNS faisant autorité, afin de faire pointer votre domaine d'entreprise vers le domaine Traffic Manager. Pour plus d'informations, consultez la rubrique [Redirection d'un domaine Internet d'entreprise vers un domaine Traffic Manager](#howto_point_company). Par exemple, la commande suivante redirige l'intégralité du trafic entrant pour **www.contoso.com** vers le domaine Traffic Manager **contoso.trafficmanager.net** : `www.contoso.com IN CNAME contoso.trafficmanager.net`.

Redirection du trafic entrant vers des services hébergés en fonction des performances réseau
--------------------------------------------------------------------------------------------

Pour équilibrer la charge de services hébergés situés dans différents centres de données à l'international, vous pouvez rediriger le trafic entrant vers le service hébergé le plus proche. Bien que l'expression « le plus proche » puisse faire référence à une distance géographique, elle peut également correspondre à l'emplacement présentant la plus faible latence pour répondre aux requêtes. La méthode d'équilibrage de charge Performance permet de répartir la charge en fonction des emplacements et de la latence, mais ne tient pas compte des modifications apportées en temps réel à la charge ou à la configuration réseau. Pour plus d'informations sur les différentes méthodes d'équilibrage de charge assumées par Traffic Manager, consultez la section « À propos des méthodes d'équilibrage de charge de Traffic Manager » de la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh744833.aspx).

Voici la procédure à suivre :

1.  **Déployez les services hébergés** dans votre environnement de production. Pour plus d'informations, consultez la page [Création d'un service hébergé pour Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg432967.aspx), ainsi que les meilleures pratiques recommandées relatives aux stratégies et aux services hébergés, dans la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring).

2.  **Connectez-vous à la section Traffic Manager du portail de gestion**, à l'adresse <http://manage.windowsazure.com>. Cliquez sur **Virtual Network** dans l'angle inférieur gauche des pages du portail, puis sélectionnez **Traffic Manager** dans le volet de gauche.

3.  **Sélectionnez Stratégies, puis cliquez sur Créer.** Sélectionnez le dossier **Stratégies** dans l'arborescence de gauche afin d'activer l'option **Créer** dans la barre d'outils supérieure, puis cliquez sur **Créer**. La boîte de dialogue **Create Traffic Manager policy** s'affiche alors.

    ![Bouton Créer pour les stratégies](./media/traffic-manager-configure-settings/Create_button_for_policies.png)

    **Illustration 1** - Bouton Créer pour les stratégies

4.  **Choisissez un abonnement.** Les stratégies et domaines sont associés à un abonnement unique.

5.  **Sélectionnez la méthode d'équilibrage de charge Performance.** Pour plus d'informations sur les différentes méthodes d'équilibrage de charge assumées par Traffic Manager, consultez la section « À propos des méthodes d'équilibrage de charge de Traffic Manager » de la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh744833.aspx).

6.  **Sélectionnez les services hébergés afin de les ajouter à la stratégie.** Vous pouvez effectuer une recherche à l'aide du champ de filtrage ou laisser ce champ vide pour afficher tous les services hébergés en production pour l'abonnement sélectionné à l'étape 4. Utilisez ensuite les boutons fléchés pour les ajouter à la stratégie. L'ordre des éléments spécifiés dans le champ **Selected DNS names** n'a aucune incidence dans le cadre de cette méthode d'équilibrage de charge.

7.  **Configurez la surveillance.** La surveillance permet de s'assurer qu'aucun trafic n'est dirigé vers les services hébergés désactivés. Vous devez disposer d'un chemin d'accès et d'un nom de fichier spécifiques configurés. Pour plus d'informations, consultez la section « À propos de la surveillance Traffic Manager » de la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring).

8.  **Attribuez un nom au domaine Traffic Manager.** Indiquez un nom de domaine unique. Notez que seul le préfixe du domaine peut être spécifié. Laissez la **durée de vie DNS** à la valeur par défaut. Pour plus d'informations sur l'incidence de ce paramètre, consultez les meilleures pratiques recommandées relatives aux stratégies et aux services hébergés avec Azure Traffic Manager, dans la rubrique [Présentation d'Azure Traffic Manager](http://msdn.microsoft.com/fr-fr/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring).

    La boîte de dialogue **Create Traffic Manager policy** doit être similaire à l'illustration ci-après.

    ![Boîte de dialogue de la méthode d'équilibrage de charge Performance](./media/traffic-manager-configure-settings/Dialog_box_for_Performance_load_balancing_method.png)

    **Illustration 2** - Boîte de dialogue de la méthode d'équilibrage de charge Performance

9.  **Testez le domaine et la stratégie Traffic Manager.** Pour plus d'informations sur le test, consultez la rubrique [Test d'une stratégie Azure Traffic Manager](#howto_test).

10. **Faites pointer le serveur DNS vers le domaine Traffic Manager.** Une fois le domaine Traffic Manager configuré et opérationnel, modifiez l'enregistrement DNS sur le serveur DNS faisant autorité, afin de faire pointer votre domaine d'entreprise vers le domaine Traffic Manager. Par exemple, la commande suivante redirige l'intégralité du trafic entrant pour **www.contoso.com** vers le domaine Traffic Manager **contoso.trafficmanager.net** : `www.contoso.com IN CNAME contoso.trafficmanager.net`. Pour plus d'informations, consultez la rubrique [Redirection d'un domaine Internet d'entreprise vers un domaine Azure Traffic Manager](#howto_point_company).


