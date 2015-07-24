<properties 
	pageTitle="Approvisionnement d’une machine virtuelle SQL Server dans Azure" 
	description="Didacticiel qui explique la création et la configuration d’une machine virtuelle SQL Server dans Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg" 
	editor="monicar"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/17/2015" 
	ms.author="jroth"/>

# Configuration d'une machine virtuelle SQL Server sur Azure #

La galerie de machines virtuelles Azure inclut différentes images contenant Microsoft SQL Server. Vous pouvez sélectionner l'une des images de machine virtuelle de cette galerie et la configurer en quelques clics pour votre environnement Azure.

Ce didacticiel présente les procédures suivantes :

* [Se connecter au Portail de gestion Azure et approvisionner une machine virtuelle à partir de la galerie](#Provision)
* [Ouvrir la machine virtuelle à l’aide du Bureau à distance et achever la configuration](#RemoteDesktop)
* [Suivre la procédure de configuration pour se connecter à la machine virtuelle en utilisant SQL Server Management Studio sur un autre ordinateur](#SSMS)
* [Étapes suivantes](#Optional)

##<a id="Provision">Déployer une machine virtuelle SQL Server depuis la galerie</a>

1. Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com) avec votre compte. Si vous n'avez pas de compte Azure, visitez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

2. Dans le portail de gestion Azure, en bas à gauche de la page Web, cliquez sur **+NEW**, sur **COMPUTE**, sur **VIRTUAL MACHINE**, puis sur **FROM GALLERY**.

3. Sur la page **Choisir une image**, cliquez sur **SQL SERVER**. Sélectionnez ensuite une image SQL Server. Cliquez sur la flèche suivante en bas à droite de la page. 
![Choisir une image][Image34]


Pour obtenir les informations les plus récentes sur les images SQL Server prises en charge sur Azure, consultez la page [Mise en route de SQL Server sur les machines virtuelles Windows Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720) dans l'ensemble de documentation [SQL Server dans les machines virtuelles Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719).

   
>[AZURE.NOTE] Si vous avez créé une machine virtuelle à l’aide de l’image de plateforme de la version d’évaluation de SQL Server, vous ne pouvez pas la mettre à jour vers une image de la version payante à la minute dans la galerie. Deux options s’offrent à vous : vous pouvez créer une machine virtuelle à l’aide de la version payante à la minute de SQL Server à partir de la galerie et faire migrer les fichiers de votre base de données vers cette nouvelle machine virtuelle en suivant les instructions de l’article [Procédure de migration des fichiers et du schéma de base de données SQL Server entre des ordinateurs virtuels dans Azure en utilisant les disques de données](http://go.microsoft.com/fwlink/p/?LinkId=294738) ; **ou bien** vous pouvez mettre à niveau une instance existante de la version d’évaluation de SQL Server vers une autre version de SQL Server dans le cadre de l’accord [License Mobility via Software Assurance sur Azure](http://azure.microsoft.com/pricing/license-mobility/) en suivant la procédure de l’article [Mettre à niveau vers une autre édition de SQL Server 2014](http://go.microsoft.com/fwlink/?LinkId=396915). Pour plus d'informations sur l'achat d'une copie sous licence de SQL Server, consultez la page [Comment acheter SQL Server ?](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx).


4. Dans la première page **Configuration de la machine virtuelle**, entrez les informations suivantes :
	- Une **DATE DE SORTIE DE LA VERSION**. Si plusieurs images sont disponibles, sélectionnez la dernière.
	- Un **NOM DE MACHINE VIRTUELLE** unique.
	- Un nom d’utilisateur unique pour le compte administrateur local de la machine dans la zone **NOUVEAU NOM D’UTILISATEUR**.
	- Dans la zone **NEW PASSWORD**, entrez un mot de passe fort. 
	- Dans la zone **CONFIRM PASSWORD** entrez de nouveau le mot de passe.
	- Sélectionnez la **taille** adéquate dans le menu déroulant. 

	>[AZURE.NOTE]La taille de la machine virtuelle est spécifiée lors de l’approvisionnement :
 	> A2 est la taille minimale recommandée pour les charges de travail de production. 
    > La taille minimale recommandée pour une machine virtuelle utilisant SQL Server Édition Entreprise est A3. Sélectionnez A3 ou plus lorsque vous utilisez SQL Server Enterprise Edition. 
    > Sélectionnez A4 lorsque vous utilisez des images SQL Server 2012 ou 2014 Enterprise optimisées pour les charges de travail transactionnelles.
   	> Sélectionnez A7 lorsque vous utilisez des images SQL Server 2012 ou 2014 Enterprise optimisées pour les charges de travail pour l’entreposage de données.  
   	> La taille sélectionnée limite le nombre de disques de données que vous pouvez configurer. Pour obtenir les informations les plus récentes sur les tailles de machines virtuelles disponibles et le nombre de disques de données que vous pouvez attribuer à une machine virtuelle, consultez la page [Tailles de machines virtuelles pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).

	Cliquez sur la flèche Suivant située en bas à droite pour continuer.

	![Configuration de machine virtuelle][Image4]


5. Dans la deuxième page **Configuration de la machine virtuelle**, configurez les ressources pour la mise en réseau, le stockage et la disponibilité :
	- Dans la zone **Service cloud**, sélectionnez **Créer un nouveau service de cloud computing**.
	- Dans la zone **Nom du cloud Service DNS**, entrez la première partie d'un nom DNS de votre choix, pour qu'il complète un nom au format **TESTNAME.cloudapp.net** 
	- Sélectionnez un **ABONNEMENT** s’il en existe plusieurs. Ce choix détermine les **comptes de stockage** disponibles. - Dans la zone **REGION/AFFINITY GROUP/VIRTUAL NETWORK**, sélectionnez une région d'hébergement pour cette image virtuelle.
	- Dans la zone **Compte de stockage**, générez automatiquement un compte ou sélectionnez-en un dans la liste. Modifiez l’**ABONNEMENT** pour voir plus de comptes. 
	- Dans la zone **AVAILABILITY SET**, sélectionnez **(none)**.
	- Lisez et acceptez les conditions juridiques.
	

6. Cliquez sur la flèche Suivant pour continuer.


7. Pour continuer, cliquez sur la coche dans le coin en inférieur droit.

8. Patientez pendant qu'Azure prépare votre machine virtuelle. L'état de la machine virtuelle doit normalement passer par les phases suivantes :

	- Démarrage (configuration)
	- Arrêté
	- Démarrage (configuration)
	- Exécution (configuration)
	- Exécution
	

##<a id="RemoteDesktop">Ouvrir la machine virtuelle à l’aide du Bureau à distance pour achever la configuration</a>

1. Une fois l’approvisionnement terminé, cliquez sur le nom de votre machine virtuelle pour accéder à la page TABLEAU DE BORD. En bas de la page, cliquez sur **Connect**.
2. Cliquez sur le bouton **Ouvrir**. ![Cliquer sur le bouton Ouvrir][Image37]

3. Dans la boîte de dialogue **Sécurité de Windows**, cliquez sur **Utiliser un autre compte**. ![Cliquer sur Utiliser un autre compte][Image38]
4. Utilisez le nom de la machine en tant que nom de domaine, suivi de votre nom d’administrateur au format suivant : `machinename\username`. Saisissez votre mot de passe et connectez-vous à la machine.

4. Lors de la première connexion, plusieurs processus doivent s’effectuer, notamment la configuration de votre bureau, les mises à jour de Windows et l’achèvement des tâches de configuration initiales de Windows (sysprep). Une fois Windows sysprep terminé, le programme d’installation de SQL Server effectue les tâches de configuration. L’exécution de ces tâches peut nécessiter quelques minutes. `SELECT @@SERVERNAME` peut ne pas renvoyer le nom correct tant que la configuration de SQL Server n’est pas terminée, et SQL Server Management Studio peut ne pas être visible sur la page de démarrage.

Une fois que vous êtes connecté à la machine virtuelle avec le Bureau à distance Windows, celle-ci fonctionne comme un autre ordinateur. Connectez-vous normalement à l’instance par défaut de SQL Server avec SQL Server Management Studio (exécuté sur la machine virtuelle).

##<a id="SSMS">Se connecter à l’instance de machine virtuelle SQL Server en utilisant SQL Server Management Studio (SSMS) sur un autre ordinateur</a>

Les étapes suivantes montrent comment se connecter à l’instance SQL Server sur Internet à l’aide de SQL Server Management Studio (SSMS). Toutefois, les mêmes étapes s’appliquent pour rendre votre machine virtuelle SQL Server accessible pour vos applications exécutées en local et dans Azure.

Avant de pouvoir vous connecter à l’instance de SQL Server à partir d’une autre machine virtuelle ou d’Internet, vous devez effectuer les tâches suivantes, comme indiqué dans les sections ci-dessous :

- [Créer un point de terminaison TCP pour la machine virtuelle](#Endpoint)
- [Ouvrir des ports TCP dans le pare-feu Windows](#FW)
- [Configurer SQL Server pour l’écoute du protocole TCP](#TCP)
- [Configurer SQL Server pour l’authentification en mode mixte](#Mixed)
- [Créer des connexions d’authentification SQL Server](#Logins)
- [Déterminer le nom DNS de la machine virtuelle](#DNS)
- [Se connecter au moteur de base de données à partir d’un autre ordinateur](#cde)
- [Se connecter au moteur de base de données à partir de votre application](#cdea)

Le chemin de connexion est résumé dans le schéma suivant :

![Connexion à une machine virtuelle SQL Server][Image8b]

###<a id="Endpoint">Créer un point de terminaison TCP pour la machine virtuelle</a>

Pour accéder à SQL Server depuis Internet, la machine virtuelle doit avoir un point de terminaison pour écouter les communications TCP entrantes. Dans cette étape de configuration Azure, le trafic du port TCP entrant est dirigé vers un port TCP accessible à la machine virtuelle.

>[AZURE.NOTE] Si vous vous connectez dans le même service cloud ou réseau virtuel, vous n’avez pas besoin de créer un point de terminaison accessible publiquement. Dans ce cas, vous pouvez passer à l’étape suivante. Pour plus d’informations, consultez la page [Considérations relatives à la connectivité de SQL Server sur les machines virtuelles Azure](https://msdn.microsoft.com/library/azure/dn133152.aspx).

1. Dans le portail de gestion Azure, cliquez sur **VIRTUAL MACHINES**.
	
2. Cliquez sur la machine virtuelle que vous venez de créer. Les informations relatives à la machine virtuelle sont affichées.
	
3. En haut de la page, sélectionnez la page **POINTS DE TERMINAISON**, puis en bas de la page, cliquez sur **AJOUTER**.
	
4. Dans la page **Ajouter un point de terminaison à la machine virtuelle**, cliquez sur **Ajouter un point de terminaison autonome**, puis sur la flèche Suivant.
	
5. Sur la page **Specify the details of the endpoint**, entrez les informations suivantes.

	- Dans la zone **NAME**, entrez un nom pour le point de terminaison.
	- Dans la zone **PROTOCOL**, sélectionnez **TCP**. Vous pouvez taper la valeur **57500** dans la zone **PORT PUBLIC**. De la même façon, vous pouvez indiquer le port d’écoute par défaut de SQL Server **1433** dans la zone **Port privé**. Notez que plusieurs organisations sélectionnent différents numéros de ports pour éviter les attaques de sécurité. 

6. Cliquez sur la coche pour continuer. Le point de terminaison est créé.

###<a id="FW">Ouvrir des ports TCP dans le pare-feu Windows pour l’instance par défaut du moteur de base de données</a>

1. Connectez-vous à la machine virtuelle via le Bureau à distance Windows. Une fois connecté, sur l’écran d’accueil, tapez **WF.msc**, puis appuyez sur ENTRÉE. 

	![Démarrer le programme du pare-feu][Image12]
2. Dans le **Pare-feu Windows avec fonctions avancées de sécurité**, dans le volet gauche, cliquez avec le bouton droit sur **Règles de trafic entrant**, puis sur **Nouvelle règle** dans le volet d'action.

	![Nouvelle règle][Image13]

3. Dans la boîte de dialogue de l’**Assistant Nouvelle règle de trafic entrant**, sous **Type de règle**, sélectionnez **Port**, puis cliquez sur **Suivant**.

4. Dans la boîte de dialogue **Protocole et ports**, utilisez le paramètre par défaut **TCP**. Dans la boîte de dialogue **Ports locaux spécifiques**, entrez le numéro de port de l’instance du moteur de base de données (**1433** pour l’instance par défaut, ou le numéro que vous avez choisi pour le port privé à l’étape du point de terminaison).

	![Port TCP 1433][Image14]

5. Cliquez sur **Suivant**.

6. Dans la boîte de dialogue **Action**, sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.

	**Remarque relative à la sécurité** : la sélection de l’option **Autoriser la connexion si elle est sécurisée** peut renforcer la sécurité. Sélectionnez cette option si vous voulez configurer des options de sécurité supplémentaires dans votre environnement.

	![Autoriser les connexions][Image15]

7. Dans la boîte de dialogue **Profil**, sélectionnez **Public**, **Privé** et **Domaine**. Cliquez ensuite sur **Suivant**.

    **Remarque relative à la sécurité** : la sélection de l’option **Public** autorise l’accès via Internet. Lorsque cela est possible, sélectionnez un profil plus restrictif.

	![Profil public][Image16]

8. Dans la boîte de dialogue **Nom**, entrez un nom et une description pour cette règle, puis cliquez sur **Terminer**.

	![Nom de la règle][Image17]

Le cas échéant, ouvrez des ports supplémentaires pour les autres composants. Pour plus d'informations, consultez la page [Configurer le Pare-feu Windows pour autoriser l'accès à SQL Server](http://msdn.microsoft.com/library/cc646023.aspx).


###<a id="TCP">Configurer SQL Server pour l’écoute du protocole TCP</a>

1. Lorsque vous êtes connecté à la machine virtuelle, sur la page de démarrage, tapez **Gestionnaire de configuration SQL Server** et appuyez sur ENTRÉE.
	
	![Ouvrir le gestionnaire de configuration SQL Server][Image9]

2. Dans le Gestionnaire de configuration SQL Server, dans le volet de la console, développez **Configuration du réseau SQL Server**.

3. Dans le volet de la console, cliquez sur **Protocoles pour MSSQLSERVER** (le nom d’instance par défaut). Dans le volet d’informations, cliquez avec le bouton droit sur TCP qui doit être activé pour les images de la galerie par défaut. Pour vos images personnalisées, cliquez sur **Activer** (si l'état est Désactivé).

	![Activer TCP][Image10]

5. Dans le volet de la console, cliquez sur **Services SQL Server** Dans le volet d’informations, cliquez avec le bouton droit sur **SQL Server (_nom de l’instance_)** (l’instance par défaut est **SQL Server (MSSQLSERVER)**), puis cliquez sur **Redémarrer** pour arrêter et redémarrer l’instance de SQL Server.

	![Redémarrer le moteur de base de données][Image11]

7. Fermez le Gestionnaire de configuration de SQL Server.

Pour plus d'informations sur l'activation des protocoles pour le moteur de base de données SQL Server, consultez la page [Activer ou désactiver un protocole réseau de serveur](http://msdn.microsoft.com/library/ms191294.aspx).

###<a id="Mixed">Configurer SQL Server pour l’authentification en mode mixte</a>

Le moteur de base de données de SQL Server ne peut pas utiliser l’authentification Windows sans un environnement de domaine. Pour vous connecter au moteur de base de données à partir d'un autre ordinateur, configurez SQL Server pour l'authentification en mode mixte qui permet l’authentification SQL Server et l’authentification Windows

>[AZURE.NOTE](il n’est pas nécessaire de configurer l’authentification en mode mixte si vous avez configuré un réseau virtuel Azure avec un environnement de domaine configuré).

1. Lorsque vous êtes connecté à la machine virtuelle, sur la page de démarrage, tapez **SQL Server 2014 Management Studio** et cliquez sur l’icône sélectionnée.

	![Démarrer SSMS][Image18]

	Lorsque vous ouvrez Management Studio pour la première fois, il doit créer l’environnement Management Studio pour les utilisateurs. Cette opération peut prendre du temps.

2. Management Studio affiche la boîte de dialogue **Se connecter au serveur**. Dans la zone **Nom du serveur**, entrez le nom de la machine virtuelle à connecter au moteur de base de données avec Object Explorer (au lieu d'entrer le nom de la machine virtuelle, vous pouvez également utiliser **(local)** ou un simple point pour le **nom du serveur**). Sélectionnez **Authentification Windows** et laissez **_your_VM_name_\\your_local_administrator** dans la zone **Nom d'utilisateur**. Cliquez sur **Connecter**.

	![Se connecter au serveur][Image19]

3. Dans SQL Server Management Studio Object Explorer, cliquez avec le bouton droit sur le nom de l'instance de SQL Server (le nom de la machine virtuelle), puis cliquez sur **Propriétés**.

	![Propriétés de serveur][Image20]

4. Sur la page **Sécurité**, sous **Authentification du serveur**, sélectionnez **Mode d'authentification SQL Server et Windows**, puis cliquez sur **OK**.

	![Sélectionner le mode d'authentification][Image21]

5. Dans la boîte de dialogue SQL Server Management Studio, cliquez sur **OK** pour confirmer l'obligation de redémarrer SQL Server.

6. Dans Object Explorer, cliquez avec le bouton droit sur votre serveur, puis cliquez sur **Redémarrer** (si SQL Server Agent est exécuté, il doit également être redémarré).

	![Redémarrer][Image22]

7. Dans la boîte de dialogue SQL Server Management Studio, cliquez sur **Oui** pour confirmer que vous voulez redémarrer SQL Server.

###<a id="Logins">Créer des connexions d’authentification SQL Server</a>

Pour vous connecter au moteur de base de données à partir d’un autre ordinateur, vous devez créer au moins une connexion d’authentification SQL Server.

1. Dans SQL Server Management Studio Object Explorer, développez le dossier de l'instance de serveur dans laquelle vous voulez créer la connexion.

2. Cliquez avec le bouton droit sur le dossier **Sécurité**, pointez sur **Nouveau**, puis sélectionnez **Connexion...**.

	![Nouvelle connexion][Image23]

3. Dans la boîte de dialogue **Nouvelle connexion**, sur la page **Général**, entrez le nom du nouvel utilisateur dans la zone **Nom de connexion**.

4. Sélectionnez **Authentification SQL Server**.

5. Dans la zone **Mot de passe**, entrez un mot de passe pour le nouvel utilisateur. Entrez de nouveau ce mot de passe dans la zone **Confirmer le mot de passe**.

6. Pour renforcer la complexité et la sécurité des options de stratégie de mot de passe, sélectionnez **Conserver la stratégie de mot de passe** (recommandé). Il s'agit d'une option par défaut lorsque l'authentification SQL Server est sélectionnée.

7. Pour conserver les options de stratégie de mot de passe après expiration, sélectionnez **Conserver l'expiration du mot de passe** (recommandé). La stratégie de conservation de mot de passe doit être sélectionnée pour activer cette case à cocher. Il s'agit d'une option par défaut lorsque l'authentification SQL Server est sélectionnée.

8. Pour forcer l'utilisateur à créer un mot de passe après sa première connexion, sélectionnez **L'utilisateur doit changer de mot de passe à la prochaine connexion** (recommandé si cette connexion est destinée à une autre personne. Si la connexion est destinée à votre propre usage, ne sélectionnez pas cette option). L'option Conserver l'expiration du mot de passe doit être sélectionnée pour activer cette case à cocher. Il s'agit d'une option par défaut lorsque l'authentification SQL Server est sélectionnée.

9. Dans la liste **Base de données par défaut**, sélectionnez une base de données par défaut pour la connexion. Par défaut, cette option est réglée sur **principale**. Si vous n'avez pas encore créé de base de données d'utilisateur, gardez la valeur par défaut **master**.

10. Dans la liste **Langue par défaut**, conservez la valeur **par défaut**.
    
	![Propriétés de connexion][Image24]

11. S’il s’agit de votre première connexion, vous pouvez désigner cette connexion en tant qu’administrateur SQL Server. Si cela est le cas, sur la page **Rôles du serveur**, activez la case à cocher **administrateur système**.

	**Remarque relative à la sécurité** : les membres du rôle serveur fixe administrateur système contrôlent complètement le moteur de base de données. Vous devez limiter soigneusement l'appartenance à ce rôle.

	![administrateur système][Image25]

12. Cliquez sur OK.

Pour plus d'informations sur les connexions SQL Server, consultez la page [Créer un compte de connexion](http://msdn.microsoft.com/library/aa337562.aspx).



###<a id="DNS">Déterminer le nom DNS de la machine virtuelle</a>

Pour vous connecter au moteur de base de données SQL Server à partir d’un autre ordinateur, vous devez connaître le nom DNS de la machine virtuelle. Il s'agit du nom utilisé par Internet pour identifier une machine virtuelle. Vous pouvez utiliser l'adresse IP, mais celle-ci peut être modifiée lorsqu'Azure déplace des ressources pour des raisons de redondance ou de maintenance. Le nom DNS reste stable, car il peut être redirigé vers une nouvelle adresse IP.

1. Dans le portail de gestion Azure (ou à partir de l'étape précédente), sélectionnez **VIRTUAL MACHINES**. 

2. Sur la page **INSTANCES DE MACHINE VIRTUELLE**, dans la colonne **Aperçu rapide**, recherchez et copiez le nom DNS de la machine virtuelle.

![Nom DNS][Image35]
	

### <a id="cde">Se connecter au moteur de base de données à partir d’un autre ordinateur</a>
 
1. Sur un ordinateur connecté à Internet, ouvrez SQL Server Management Studio.
2. Dans la boîte de dialogue **Se connecter au serveur** ou **Se connecter au moteur de base de données**, dans la zone **Nom du serveur**, entrez le nom DNS de la machine virtuelle (déterminé pendant la tâche précédente) et le numéro d’un port de point de terminaison public au format *NomDNS,NuméroPort*, par exemple **tutorialtestVM.cloudapp.net,57500**. Pour obtenir le numéro de port, connectez-vous au portail de gestion Azure et cherchez la machine virtuelle. Dans le tableau de bord, cliquez sur **POINTS DE TERMINAISON** et utilisez le **PORT PUBLIC** affecté à **MSSQL**. ![Port public][Image36]
3. Dans la zone **Authentification**, sélectionnez **Authentification SQL Server**.
5. Dans la zone **Connexion**, entrez le nom d'une connexion créée lors d'une tâche précédente.
6. Dans la zone **Mot de passe**, entrez le mot de passe de connexion que vous avez créé lors d'une tâche précédente.
7. Cliquez sur **Connecter**.

	![Connexion en utilisant SSMS][Image33]

## <a id="cdea">Se connecter au moteur de base de données à partir de votre application</a>

Si vous pouvez vous connecter à une instance de SQL Server exécutée sur une machine virtuelle Azure en utilisant Management Studio, vous devriez pouvoir vous connecter en utilisant une chaîne de connexion semblable à la suivante :

	connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Pour plus d'informations, consultez la page [Résolution des problèmes de connexion au moteur de base de données SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

##<a id="Optional">Étapes suivantes</a>
Vous avez vu comment créer et configurer une instance de SQL Server sur une machine virtuelle Azure en utilisant une image de plateforme. Lorsque vous utilisez SQL Server dans des machines virtuelles Azure, nous vous recommandons de suivre les informations d'utilisation fournies dans la documentation [SQL Server dans des machines virtuelles Windows Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719) de la bibliothèque Azure. Cet ensemble de documentation inclut une série d'articles et de didacticiels fournissant des informations d'utilisation détaillées. Cette série inclut les sections suivantes :

[SQL Server dans des machines virtuelles Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719)

[Mise en route de SQL Server sur les machines virtuelles Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720)

[Se préparer à migrer vers SQL Server dans les machines virtuelles Azure](http://go.microsoft.com/fwlink/p/?LinkId=294721)

- Procédure de migration des fichiers et du schéma de base de données SQL Server entre des machines virtuelles dans Azure en utilisant les disques de données

[Déploiement de SQL Server dans des machines virtuelles Azure](http://go.microsoft.com/fwlink/p/?LinkId=294722)

- Procédure de copie des fichiers de données et d’installation de SQL Server dans un disque de données local vers Azure en utilisant CSUpload
- Procédure de création d'une machine virtuelle de base locale en utilisant Hyper-V
- Procédure de création d'une machine virtuelle SQL Server dans Azure à l'aide du disque SQL Server local existant
- Procédure de création d'une machine virtuelle SQL Server dans Azure à l'aide d'une machine virtuelle SQL Server locale existante 
- Procédure : utiliser PowerShell pour configurer une machine virtuelle SQL Server dans Azure 
- Utilisation d’un disque de données attaché pour stocker des fichiers de base de données

[Considérations relatives à la connectivité de SQL Server sur les machines virtuelles Azure](http://go.microsoft.com/fwlink/p/?LinkId=294723)

[Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](http://go.microsoft.com/fwlink/?LinkId=294724)

[Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](http://go.microsoft.com/fwlink/p/?LinkId=294725)

[Résolution des problèmes et surveillance de SQL Server dans les machines virtuelles Azure](http://go.microsoft.com/fwlink/p/?LinkId=294726)

[Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure](http://go.microsoft.com/fwlink/p/?LinkId=294727)

- Didacticiel : groupes de disponibilité AlwaysOn dans Azure (GUI)
- Didacticiel : groupes de disponibilité AlwaysOn dans Azure (PowerShell)
- Didacticiel : Configuration d’un écouteur pour les groupes de disponibilité AlwaysOn
- Didacticiel : Assistant Ajouter un réplica Azure
- dépannage d’écouteur de groupes de disponibilité dans Azure

[Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](http://go.microsoft.com/fwlink/p/?LinkId=294728)

[Business Intelligence de SQL Server dans les machines virtuelles Azure](http://go.microsoft.com/fwlink/p/?LinkId=294729)

- Utiliser PowerShell pour créer une machine virtuelle Azure avec SQL Server Business Intelligence et SharePoint 2010
- Utiliser PowerShell pour créer une machine virtuelle Azure avec SQL Server Business Intelligence et SharePoint 2013
- Utiliser PowerShell pour créer une machine virtuelle Azure avec un serveur de rapports en mode natif

[SQL Server Data Warehousing dans des machines virtuelles Azure](http://msdn.microsoft.com/library/windowsazure/dn387396.aspx)

[Articles techniques relatifs aux performances de SQL Server sur les machines virtuelles Azure](http://msdn.microsoft.com/library/azure/dn248435.aspx)

- [Livre blanc : Présentation de Base de données SQL Azure et de SQL Server dans les machines virtuelles Azure](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [Livre blanc : Modèles d’application et stratégies de développement pour SQL Server dans Azure Virtual Machines](http://msdn.microsoft.com/library/azure/dn574746.aspx)

- [Déployer Business Intelligence de SQL Server dans les machines virtuelles Azure](http://msdn.microsoft.com/library/windowsazure/dn321998.aspx)

- [Livre blanc : Contrôle de visionneuse de rapports Reporting Services et serveurs de rapports basés sur une machine virtuelle Microsoft Azure](http://msdn.microsoft.com/library/azure/dn753698.aspx)

[Image4]: ./media/virtual-machines-provision-sql-server/4VM-Config.png
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
[Image16]: ./media/virtual-machines-provision-sql-server/16Public-Private-Domain-Profile.png
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
[Image34]: ./media/virtual-machines-provision-sql-server/choose-sql-vm.png
[Image35]: ./media/virtual-machines-provision-sql-server/sql-vm-dns-name.png
[Image36]: ./media/virtual-machines-provision-sql-server/sql-vm-port-number.png
[Image37]: ./media/virtual-machines-provision-sql-server/click-open-to-connect.png
[Image38]: ./media/virtual-machines-provision-sql-server/credentials.png
 

<!------HONumber=July15_HO2-->