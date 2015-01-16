<properties urlDisplayName="Install SQL Server" pageTitle="Approvisionnement d'une machine virtuelle SQL Server dans Azure " metaKeywords="Azure tutorial creating SQL Server, SQL Server vm, configuring SQL Server" description="Didacticiel qui explique la création et la configuration d'une machine virtuelle SQL Server dans Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Provisioning a SQL Server Virtual Machine on Azure" authors="selcint" solutions="" manager="jhubbard" editor="tyson" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/30/2014" ms.author="" />





# Configuration d'une machine virtuelle SQL Server sur Azure #

La galerie de machines virtuelles Azure inclut différentes images contenant Microsoft SQL Server. Vous pouvez sélectionner l'une des images de machine virtuelle de cette galerie et la configurer en quelques clics pour votre environnement Azure.

Ce didacticiel présente les procédures suivantes :

* [Connexion du portail de gestion Azure et configuration d'une machine virtuelle à partir de la galerie](#Provision)
* [Ouverture de la machine virtuelle à l'aide du Bureau à distance et achèvement de l'installation](#RemoteDesktop)
* [Procédure de configuration complète pour la connexion à la machine virtuelle en utilisant SSMS (SQL Server Management Studio) sur un autre ordinateur](#SSMS)
* [Étapes suivantes](#Optional)

##<a id="Provision">Connexion du portail de gestion Azure et configuration d'une machine virtuelle à partir de la galerie</a>

1. Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com) à l'aide de votre compte. Si vous n'avez pas de compte Azure, visitez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/fr-fr/pricing/free-trial/).

2. Dans le portail de gestion Azure, en bas à gauche de la page web, cliquez sur **+NOUVEAU**, sur **CALCUL**, sur **MACHINE VIRTUELLE**, puis sur **À PARTIR DE LA GALERIE**.

3. Dans la page **Créer une machine virtuelle**, sélectionnez une machine virtuelle contenant SQL Server, puis cliquez sur la flèche Suivant en bas à droite de la page. Pour obtenir les informations les plus récentes concernant les images SQL Server prises en charge sur Azure, consultez la rubrique [Prise en main de SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294720) de la documentation sur [SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294719) . 

    >[WACOM.NOTE] Si vous avez créé une machine virtuelle à l'aide de l'image de plateforme de la version d'évaluation de SQL Server, vous ne pouvez pas la mettre à jour vers une image de la version payante à la minute dans la galerie. Vous pouvez choisir l'une des deux options suivantes :
    
    > - Vous pouvez créer une machine virtuelle à l'aide de la version de SQL Server payante à la minute à partir de la galerie et migrer vos fichiers de base de données vers cette nouvelle machine virtuelle en suivant la procédure de la page [Migration des fichiers de base de données SQL Server et schéma entre les machines virtuelles dans Azure en utilisant des disques de données](http://go.microsoft.com/fwlink/p/?LinkId=294738). **Ou**,

    > - Vous pouvez mettre à jour une instance existante de la version d'évaluation de SQL Server vers une autre édition de SQL Server dans le cadre du contrat de [licence Mobility via Software Assurance sur Azure](http://www.windowsazure.com/fr-fr/pricing/license-mobility/) en suivant la procédure de la section [Mise à jour vers une autre édition de SQL Server 2014](http://go.microsoft.com/fwlink/?LinkId=396915). Pour plus d'informations sur l'achat d'une copie sous licence de SQL Server, consultez [Achat de SQL Server](http://www.microsoft.com/fr-fr/sqlserver/get-sql-server/how-to-buy.aspx).
   

4. Dans la première page de **Configuration de la machine virtuelle**, entrez les informations suivantes :
	- Entrez un **NOM DE MACHINE VIRTUELLE**.
	- Dans la zone **NOUVEAU NOM D'UTILISATEUR**, tapez un nom d'utilisateur unique pour le compte administrateur local de la machine virtuelle.
	- Dans la zone **NOUVEAU MOT DE PASSE**, tapez un mot de passe fort. Pour plus d'informations, consultez [Mots de passe forts](http://msdn.microsoft.com/library/ms161962.aspx).
	- Dans la zone **CONFIRMER LE MOT DE PASSE**, retapez le mot de passe.
	- Sélectionnez la **TAILLE** appropriée dans la liste déroulante. 

	>[WACOM.NOTE] La taille de la machine virtuelle est indiquée durant la configuration :
 	> A2 est la plus petite taille recommandée pour les charges de travail de production. 
    > La taille minimale recommandée pour une machine virtuelle utilisant SQL Server Édition Entreprise est A3.
    > Sélectionnez A3 ou plus lorsque vous utilisez SQL Server Enterprise Edition.
   	> Sélectionnez A4 lorsque vous utilisez des images SQL Server 2012 ou 2014 Enterprise optimisées pour les charges de travail transactionnelles.  
   	> Sélectionnez A7 lorsque vous utilisez des images SQL Server 2012 ou 2014 Enterprise optimisées pour les charges de travail pour l'entreposage de données. 
   	> La taille sélectionnée limite le nombre de disques de données que vous pouvez configurer. Pour obtenir les informations les plus récentes sur les tailles de machine virtuelle disponibles et le nombre de disques de données que vous pouvez attacher à une machine virtuelle, consultez [Tailles de machines virtuelles pour Azure](http://msdn.microsoft.com/fr-fr/library/azure/dn197896.aspx).

	Cliquez sur la flèche Suivant située en bas à droite pour continuer.

	![VM Configuration](./media/virtual-machines-provision-sql-server/4VM-Config.png)


5. Dans la deuxième page de **Configuration de la machine virtuelle**, configurez les ressources pour la mise en réseau, le stockage et la disponibilité :
	- Dans la zone **Service cloud**, choisissez **Créer un nouveau service de cloud computing**.
	- Dans la zone **Nom du cloud Service DNS**, entrez la première partie d'un nom DNS de votre choix pour terminer un nom au format **NOM_TEST.cloudapp.net** 
	- Dans la zone **RÉGION/GROUPE D'AFFINITÉ/RÉSEAU VIRTUEL**, sélectionnez une région d'hébergement pour cette image virtuelle.
	- Dans **Compte de stockage**, sélectionnez un compte de stockage existant ou un compte automatiquement généré.
	- Dans la zone **GROUPE À HAUTE DISPONIBILITÉ**, sélectionnez **(aucun)**.
	- Lisez et acceptez les conditions juridiques.
	

6. Cliquez sur la flèche Suivant pour continuer.


7. Pour continuer, cliquez sur la coche dans le coin en inférieur droit.

8. Patientez pendant qu'Azure prépare votre machine virtuelle. L'état de la machine virtuelle doit normalement passer par les phases suivantes :

	- Démarrage (configuration)
	- Arrêté
	- Démarrage (configuration)
	- Exécution (configuration)
	- Exécution
	

##<a id="RemoteDesktop">Ouverture de la machine virtuelle à l'aide du Bureau à distance et achèvement de l'installation</a>

1. Une fois la configuration terminée, cliquez sur le nom de votre machine virtuelle pour accéder à la page DASHBOARD. En bas de la page, cliquez sur **Connexion**.

	
2. Choisissez d'ouvrir le fichier .rpd à l'aide du programme Bureau à distance Windows (" %windir%\system32\mstsc.exe ").

	
3. Dans la boîte de dialogue **Sécurité de Windows**, entrez le mot de passe du compte administrateur local spécifié précédemment. (Il se peut que vous soyez invité à vérifier les informations d'identification de la machine virtuelle.)

4. Lors de la première connexion à cette machine virtuelle, il est possible que plusieurs processus doivent s'effectuer, par exemple, la configuration de votre bureau, les mises à jour de Windows et l'achèvement des tâches de configuration initiales de Windows (sysprep). Une fois Windows sysprep terminé, les tâches de configuration de SQL Server sont terminées. Ces tâches peuvent provoquer une brève latence lorsqu'elles se terminent. " SELECT @@SERVERNAME " peut ne pas renvoyer de nom correct avant la fin de la configuration de SQL Server.

Une fois que vous êtes connecté à la machine virtuelle avec le Bureau à distance Windows, celle-ci fonctionne comme un autre ordinateur. Connectez-vous normalement à l'instance par défaut de SQL Server avec SQL Server Management Studio (exécuté sur la machine virtuelle). 

##<a id="SSMS">Procédure de configuration complète pour la connexion à la machine virtuelle en utilisant SSMS (SQL Server Management Studio) sur un autre ordinateur</a>

Avant de pouvoir vous connecter à l'instance de SQL Server à partir d'Internet, vous devez mener à bien les tâches suivantes, comme indiqué dans les sections suivantes :

- [Création d'un point de terminaison TCP pour la machine virtuelle](#Endpoint)
- [Ouverture de ports TCP dans le pare-feu Windows](#FW)
- [Configuration de SQL Server pour écouter le protocole TCP](#TCP)
- [Configuration de SQL Server pour l'authentification en mode mixte](#Mixed)
- [Création de connexions d'authentification SQL Server](#Logins)
- [Détermination du nom DNS de la machine virtuelle](#DNS)
- [Connexion au moteur de base de données à partir d'un autre ordinateur](#cde)
- [Connexion au moteur de base de données à partir de votre application](#cdea)

Le chemin de connexion est résumé dans le schéma suivant :

![Connecting to a SQL Server virtual machine][Image8b]

###<a id="Endpoint">Création d'un point de terminaison TCP pour la machine virtuelle</a>

La machine virtuelle doit avoir un point de terminaison pour écouter les communications TCP entrantes. Dans cette étape de configuration Azure, le trafic du port TCP entrant est dirigé vers un port TCP accessible à la machine virtuelle.

1. Dans le portail de gestion Azure, cliquez sur **MACHINES VIRTUELLES**.

	
2. Cliquez sur la machine virtuelle que vous venez de créer. Les informations relatives à la machine virtuelle sont affichées.
	

3. En haut de la page, sélectionnez la page **POINTS DE TERMINAISON**, puis en bas de la page, cliquez sur **AJOUTER**.
	

4. Dans la page **Ajouter un point de terminaison à une machine virtuelle**, cliquez sur **Ajouter un point de terminaison autonome**, puis sur la flèche Suivant pour continuer.

	
5. Dans la page **Spécifier les détails du point de terminaison**, entrez les informations suivantes.

	- Dans la zone **NOM**, entrez un nom pour le point de terminaison.
	- Dans la zone **PROTOCOLE**, sélectionnez **TCP**. Vous pouvez taper **57500** dans la zone **PORT PUBLIC**. De la même façon, vous pouvez taper le port d'écoute par défaut de SQL Server **1433** dans la zone **Port privé**. Notez que plusieurs organisations sélectionnent différents numéros de ports pour éviter les attaques de sécurité. 


6. Cliquez sur la coche pour continuer. Le point de terminaison est créé.
	

###<a id="FW">Ouverture de ports TCP dans le pare-feu Windows pour l'instance par défaut du moteur de base de données</a>

1. Connectez-vous à la machine virtuelle via le Bureau à distance Windows. Une fois connecté, dans le menu Démarrer, cliquez sur **Exécuter**, tapez **WF.msc**, puis cliquez sur**OK**.

	![Start the Firewall Program][Image12]
2. Dans **Pare-feu Windows avec fonctions avancées de sécurité**, dans le volet gauche, cliquez avec le bouton droit sur **Règles de trafic entrant**, puis cliquez sur **Nouvelle règle** dans le volet d'action.

	![New Rule][Image13]

3. Dans la boîte de dialogue **Type de règle**, sélectionnez **Port**, puis cliquez sur **Suivant**.

4. Dans la boîte de dialogue **Protocole et ports**, sélectionnez **TCP**. Sélectionnez **Ports locaux spécifiques**, puis entrez le numéro de port de l'instance du moteur de base de données (**1433** pour l'instance par défaut, ou le nombre que vous avez choisi pour le port privé à l'étape du point de terminaison). 

	![TCP Port 1433][Image14]

5. Cliquez sur **Suivant**.

6. Dans la boîte de dialogue **Action**, sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.

	**Remarque sur la sécurité :** la sélection de l'option **Autoriser la connexion si elle est sécurisée** peut renforcer la sécurité. Sélectionnez cette option si vous voulez configurer des options de sécurité supplémentaires dans votre environnement.

	![Allow Connections][Image15]

7. Dans la boîte de dialogue **Profil**, sélectionnez **Public**, puis cliquez sur **Suivant**. 

    **Remarque sur la sécurité :**  la sélection de **Public** autorise l'accès via Internet. Lorsque cela est possible, sélectionnez un profil plus restrictif.

	![Public Profile][Image16]

8. Dans la boîte de dialogue **Nom**, tapez un nom et une description pour cette règle, puis cliquez sur **Terminer**.

	![Rule Name][Image17]

Le cas échéant, ouvrez des ports supplémentaires pour les autres composants. Pour plus d'informations, consultez [Configurer le Pare-feu Windows pour autoriser l'accès à SQL Server](http://msdn.microsoft.com/fr-fr/library/cc646023.aspx).


###<a id="TCP">Configuration de SQL Server pour écouter le protocole TCP</a>

1. Quand vous êtes connecté à la machine virtuelle à l'aide du Bureau à distance, dans le menu Démarrer, cliquez sur **Tous les programmes**, **Microsoft SQL Server** *version*, sur **Outils de configuration**, puis sur **Gestionnaire de configuration SQL Server**.
	
	![Open SSCM][Image9]

2. Dans le Gestionnaire de configuration SQL Server, dans le volet de la console, développez **Configuration du réseau SQL Server**.

3. Dans le volet de la console, cliquez sur **Protocoles pour _nom de l'instance_**. (L'instance par défaut est **Protocoles pour MSSQLSERVER**.)

4. Dans le volet d'informations, cliquez avec le bouton droit sur TCP qui doit être activé pour les images de la galerie par défaut. Pour vos images personnalisées, cliquez sur **Activer** (si l'état est Désactivé).

	![Enable TCP][Image10]

5. Dans le volet de la console, cliquez sur **Services SQL Server**. (le redémarrage du moteur de base de données peut être reporté jusqu'à la fin de l'étape suivante).

6. Dans le volet d'informations, cliquez avec le bouton droit sur **SQL Server (_nom de l'instance_)** (l'instance par défaut est **SQL Server (MSSQLSERVER)**), puis cliquez sur **Redémarrer**, pour arrêter et redémarrer l'instance de SQL Server. 

	![Restart Database Engine][Image11]

7. Fermez le Gestionnaire de configuration de SQL Server.

Pour plus d'informations sur l'activation des protocoles pour le moteur de base de données SQL Server, consultez [Activer ou désactiver un protocole réseau de serveur](http://msdn.microsoft.com/fr-fr/library/ms191294.aspx).

###<a id="Mixed">Configuration de SQL Server pour l'authentification en mode mixte</a>

Le moteur de base de données de SQL Server ne peut pas utiliser l'authentification Windows sans un environnement de domaine. Pour vous connecter au moteur de base de données à partir d'un autre ordinateur, configurez SQL Server pour l'authentification en mode mixte qui permet l'authentification SQL Server et l'authentification Windows (il n'est pas nécessaire de configurer l'authentification en mode mixte si vous avez configuré un réseau virtuel Azure). Pour plus d'informations, consultez [Considérations relatives à la connectivité de SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/?LinkId=294723) de la documentation sur [SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/?LinkId=294719) .

1. Quand vous êtes connecté à la machine virtuelle à l'aide du Bureau à distance, dans le menu Démarrer, cliquez sur **Tous les programmes**, sur **Microsoft SQL Server _version_**, puis sur **SQL Server Management Studio**. 

	![Start SSMS][Image18]

	Lorsque vous ouvrez Management Studio pour la première fois, il doit créer l'environnement Management Studio pour les utilisateurs. Cette opération peut prendre du temps.

2. À l'ouverture, Management Studio affiche la boîte de dialogue **Se connecter au serveur**. Dans la zone **Nom du serveur**, tapez le nom de la machine virtuelle à connecter au moteur de base de données avec l'explorateur d'objets. (Au lieu d'entrer le nom de la machine virtuelle, vous pouvez aussi utiliser **(local)** ou un simple point pour le **Nom du serveur**. Sélectionnez **Authentification Windows**, et laissez **_nom_de_votre_machine_virtuelle_\votre_administrateur_local** dans la zone **Nom d'utilisateur**. Cliquez sur **Connexion**.

	![Connect to Server][Image19]

3. Dans l'explorateur d'objets de SQL Server Management Studio, cliquez avec le bouton droit sur l'instance de SQL Server (le nom de la machine virtuelle), puis cliquez sur **Propriétés**.

	![Server Properties][Image20]

4. Dans la page **Sécurité**, sous **Authentification du serveur**, sélectionnez **Mode d'authentification SQL Server et Windows**, puis cliquez sur **OK**.

	![Select Authentication Mode][Image21]

5. Dans la boîte de dialogue SQL Server Management Studio, cliquez sur **OK** pour confirmer l'obligation de redémarrer SQL Server.

6. Dans l'explorateur d'objets, cliquez avec le bouton droit sur votre serveur, puis cliquez sur **Redémarrer**. (si SQL Server Agent est exécuté, il doit également être redémarré).

	![Restart][Image22]

7. Dans la boîte de dialogue SQL Server Management Studio, cliquez sur **Oui** pour confirmer que vous voulez redémarrer SQL Server.

###<a id="Logins">Création de connexions d'authentification SQL Server</a>

Pour vous connecter au moteur de base de données à partir d'un autre ordinateur, vous devez créer au moins une connexion d'authentification SQL Server.

1. Dans SQL Server Management Studio Object Explorer, développez le dossier de l'instance de serveur dans laquelle vous voulez créer la connexion.

2. Cliquez avec le bouton droit sur le dossier **Sécurité**, pointez sur **Nouveau** et sélectionnez **Connexion...**.

	![New Login][Image23]

3. Dans la boîte de dialogue **Nouvelle connexion**, dans la page **Général**, entrez le nom du nouvel utilisateur dans la zone **Nom de connexion**.

4. Sélectionnez **Authentification SQL Server**.

5. Dans la zone **Mot de passe**, entrez un mot de passe pour le nouvel utilisateur. Entrez de nouveau ce mot de passe dans la zone **Confirmer le mot de passe**.

6. Pour appliquer les options de stratégie de mot de passe relatives à la complexité et au contrôle obligatoire, sélectionnez **Conserver la stratégie de mot de passe** (recommandé). Il s'agit d'une option par défaut lorsque l'authentification SQL Server est sélectionnée.

7. Pour appliquer les options de stratégie de mot de passe relatives à l'expiration, sélectionnez **Conserver l'expiration du mot de passe** (recommandé). La stratégie de conservation de mot de passe doit être sélectionnée pour activer cette case à cocher. Il s'agit d'une option par défaut lorsque l'authentification SQL Server est sélectionnée.

8. Pour obliger l'utilisateur à créer un mot de passe après la première connexion, sélectionnez **L'utilisateur doit changer de mot de passe à la prochaine connexion** (recommandé si cette connexion est destinée à une autre personne. Si la connexion est destinée à votre propre usage, ne sélectionnez pas cette option). L'option Conserver l'expiration du mot de passe doit être sélectionnée pour activer cette case à cocher. Il s'agit d'une option par défaut lorsque l'authentification SQL Server est sélectionnée. 

9. Dans la liste **Base de données par défaut**, sélectionnez une base de données par défaut pour la connexion. **master** est l'option par défaut. Si vous n'avez pas encore créé de base de données utilisateur, gardez la valeur par défaut **master**.

10. Dans la liste **Langue par défaut**, gardez la valeur **par défaut**.
    
	![Login Properties][Image24]

11. S'il s'agit de votre première connexion, vous pouvez désigner cette connexion en tant qu'administrateur SQL Server. Dans ce cas, dans la page **Rôles du serveur**, cochez la case **administrateur système**. 

	**Remarque sur la sécurité :** les membres du rôle serveur administrateur système fixe contrôlent complètement le moteur de base de données. Vous devez limiter soigneusement l'appartenance à ce rôle.

	![sysadmin][Image25]

12. Cliquez sur OK.

Pour plus d'informations sur les connexions SQL Server, consultez [Créer un compte de connexion](http://msdn.microsoft.com/fr-fr/library/aa337562.aspx).



###<a id="DNS">Détermination du nom DNS de la machine virtuelle</a>

Pour vous connecter au moteur de base de données SQL Server à partir d'un autre ordinateur, vous devez connaître le nom DNS de la machine virtuelle. Il s'agit du nom utilisé par Internet pour identifier une machine virtuelle. Vous pouvez utiliser l'adresse IP, mais celle-ci peut être modifiée lorsqu'Azure déplace des ressources pour des raisons de redondance ou de maintenance. Le nom DNS reste stable, car il peut être redirigé vers une nouvelle adresse IP.  

1. Dans le portail de gestion Azure (ou à l'étape précédente), sélectionnez **MACHINES VIRTUELLES**. 

2. Dans la page **INSTANCES DE MACHINE VIRTUELLE**, dans la colonne **NOM DNS**, recherchez et copiez le nom DNS de la machine virtuelle qui apparaît, précédé de **http://**. (si l'interface utilisateur n'affiche pas l'intégralité du nom, cliquez dessus avec le bouton droit, puis sélectionnez Copier).
	

### <a id="cde">Connexion au moteur de base de données à partir d'un autre ordinateur</a>
 
1. Sur un ordinateur connecté à Internet, ouvrez SQL Server Management Studio.

2. Dans la boîte de dialogue **Se connecter au serveur** ou **Se connecter au moteur de base de données**, dans la zone **Nom du serveur**, entrez le nom DNS de la machine virtuelle (déterminé pendant la tâche précédente) et le numéro d'un port de point de terminaison public au format *NomDNS,numéro_port*, par exemple **tutorialtestVM.cloudapp.net,57500**.

3. Dans la zone **Authentification**, sélectionnez **Authentification SQL Server**.

4. Dans la zone **Connexion**, tapez le nom d'une connexion créée précédemment.

5. Dans la zone **Mot de passe**, tapez le mot de passe de la connexion créée précédemment.

6. Cliquez sur **Connexion**.

	![Connect using SSMS][Image33]

### <a id="cdea"> Connexion au moteur de base de données à partir de votre application</a>

Si vous pouvez vous connecter à une instance de SQL Server exécutée sur une machine virtuelle Azure en utilisant Management Studio, vous devriez pouvoir vous connecter en utilisant une chaîne de connexion semblable à la suivante :

	connectionString="Server=<DNS_Name>;Integrated Security=false;User ID=<login_name>;Password=<your_password>;"providerName="System.Data.SqlClient"

Pour plus d'informations, consultez [Résolution des problèmes de connexion au moteur de base de données SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

##<a id="Optional">Étapes suivantes</a>
Vous avez vu comment créer et configurer une instance de SQL Server sur une machine virtuelle Azure en utilisant une image de plateforme. Quand vous utilisez SQL Server dans Azure Virtual Machines, nous vous recommandons de suivre les instructions de la documentation [SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294719) de la bibliothèque. Cet ensemble de documentation inclut une série d'articles et de didacticiels fournissant des informations d'utilisation détaillées. Cette série inclut les sections suivantes :

[SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294719)

[Prise en main de SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294720)

[Se préparer à migrer vers SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294721)

- Procédure de migration des fichiers et du schéma de base de données SQL Server entre des machines virtuelles dans Azure en utilisant les disques de données

[Déploiement de SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294722)

- Procédure de copie des fichiers de données et d'installation de SQL Server dans un disque de données local vers Azure en utilisant CSUpload
- Procédure de création d'une machine virtuelle de base locale en utilisant Hyper-V
- Procédure de création d'une machine virtuelle SQL Server dans Azure à l'aide du disque SQL Server local existant
- Procédure de création d'une machine virtuelle SQL Server dans Azure à l'aide d'une machine virtuelle SQL Server locale existante 
- Procédure : utiliser PowerShell pour configurer une machine virtuelle SQL Server dans Azure 
- Utilisation d'un disque de données attaché pour stocker des fichiers de base de données

[Considérations sur la connectivité de SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294723)

- Didacticiel : se connecter à SQL Server dans le même service de cloud computing 
- Didacticiel : se connecter à SQL Server dans un service de cloud computing différent 
- Didacticiel : connecter une application ASP.NET à SQL Server dans Windows Azure via Virtual Network 

[Considérations sur les performances de SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/?LinkId=294724)

[Considérations sur la sécurité de SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294725)

[Résolution de problèmes et surveillance de SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294726)

[Haute disponibilité et récupération d'urgence de SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294727)

- Didacticiel : groupes de disponibilité AlwaysOn dans Azure (GUI)
- Didacticiel : groupes de disponibilité AlwaysOn dans Azure (PowerShell)
- Didacticiel : configuration d'écouteur pour groupes de disponibilité AlwaysOn
- Didacticiel : assistant Add Azure Replica
- Didacticiel : mise en miroir de bases de données pour la récupération d'urgence dans Azure
- Didacticiel : mise en miroir de bases de données pour la récupération d'urgence dans Hybrid IT 
- Didacticiel : mise en miroir de bases de données pour la haute disponibilité dans Azure
- Didacticiel : copie des journaux de transaction pour la récupération d'urgence dans Hybrid IT 
- dépannage d'écouteur de groupes de disponibilité dans Azure

[Sauvegarde et restauration de SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294728)

[Business Intelligence SQL Server dans Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=294729)

- Utiliser PowerShell pour créer une machine virtuelle Azure avec SQL Server Business Intelligence et SharePoint 2010
- Utiliser PowerShell pour créer une machine virtuelle Azure avec SQL Server Business Intelligence et SharePoint 2013
- Utiliser PowerShell pour créer une machine virtuelle Azure avec un serveur de rapports en mode natif

[Entreposage de données SQL Server dans Azure Virtual Machines](http://msdn.microsoft.com/library/windowsazure/dn387396.aspx)

[Articles techniques sur SQL Server dans Azure Virtual Machines](http://msdn.microsoft.com/library/azure/dn248435.aspx)

- [Livre blanc : Présentation de Azure SQL Database et SQL Server dans Azure Virtual Machines](http://azure.microsoft.com/fr-fr/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas/)

- [Livre blanc : Modèles d'application et stratégies de développement pour SQL Server dans Azure Virtual Machines](http://msdn.microsoft.com/library/azure/dn574746.aspx)

- [Livre blanc : Déploiement de Business Intelligence SQL Server dans Azure Virtual Machines](http://msdn.microsoft.com/library/windowsazure/dn321998.aspx)

- [Livre blanc : Guide des performances de SQL Server dans Azure Virtual Machines](http://msdn.microsoft.com/library/windowsazure/dn248436.aspx)

- [Livre blanc : Contrôle de la visionneuse de rapports Reporting Services et serveurs de rapports de machine virtuelle Microsoft Azure](http://msdn.microsoft.com/library/azure/dn753698.aspx)

[Image5]: ./media/virtual-machines-provision-sql-server/5VM-Mode.png
[Image5b]: ./media/virtual-machines-provision-sql-server/5VM-Connect.png
[Image6]: ./media/virtual-machines-provision-sql-server/6VM-Options.png
[Image8b]: ./media/virtual-machines-provision-sql-server/SQLServerinVMConnectionMap.png
[Image9]: ./media/virtual-machines-provision-sql-server/9Click-SSCM.png
[Image10]: ./media/virtual-machines-provision-sql-server/10Enable-TCP.png
[Image11]: ./media/virtual-machines-provision-sql-server/11Restart.png
[Image12]: ./media/virtual-machines-provision-sql-server/12Open-WF.png
[Image13]: ./media/virtual-machines-provision-sql-server/13New-FW-Rule.png
[Image14]: ./media/virtual-machines-provision-sql-server/14Port-1433.png
[Image15]: ./media/virtual-machines-provision-sql-server/15Allow-Connection.png
[Image16]: ./media/virtual-machines-provision-sql-server/16Public-Profile.png
[Image17]: ./media/virtual-machines-provision-sql-server/17Rule-Name.png
[Image18]: ./media/virtual-machines-provision-sql-server/18Start-SSMS.png
[Image19]: ./media/virtual-machines-provision-sql-server/19Connect-to-Server.png
[Image20]: ./media/virtual-machines-provision-sql-server/20Server-Properties.png
[Image21]: ./media/virtual-machines-provision-sql-server/21Mixed-Mode.png
[Image22]: ./media/virtual-machines-provision-sql-server/22Restart2.png
[Image23]: ./media/virtual-machines-provision-sql-server/23New-Login.png
[Image24]: ./media/virtual-machines-provision-sql-server/24Test-Login.png
[Image25]: ./media/virtual-machines-provision-sql-server/25sysadmin.png
[Image28]: ./media/virtual-machines-provision-sql-server/28Add-Endpoint.png
[Image29]: ./media/virtual-machines-provision-sql-server/29Add-Endpoint-to-VM.png
[Image30]: ./media/virtual-machines-provision-sql-server/30Endpoint-Details.png
[Image31]: ./media/virtual-machines-provision-sql-server/31VM-Connect.png
[Image32]: ./media/virtual-machines-provision-sql-server/32DNS-Name.png
[Image33]: ./media/virtual-machines-provision-sql-server/33Connect-SSMS.png


<!--HONumber=35.1-->
