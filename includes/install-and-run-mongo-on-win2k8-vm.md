Suivez cette procédure pour installer et exécuter MongoDB sur une machine virtuelle exécutant Windows Server.

> [!IMPORTANT]
> Les fonctionnalités de sécurité MongoDB, comme l’authentification et la liaison d’adresse IP, ne sont pas activées par défaut. Elles doivent être activées avant le déploiement de MongoDB dans un environnement de production.  Pour en savoir plus, consultez la page [Sécurité et authentification](http://www.mongodb.org/display/DOCS/Security+and+Authentication).
> 
> 

1. Une fois que vous êtes connecté à la machine virtuelle à l’aide du Bureau à distance, ouvrez Internet Explorer à partir du menu **Démarrer** de la machine virtuelle.
2. Sélectionnez le bouton **Outils** dans le coin supérieur droit.  Dans **Options Internet**, sélectionnez l’onglet **Sécurité**, puis l’icône **Sites de confiance**, et cliquez sur le bouton **Sites**. Ajoutez le site *https://\*.mongodb.org* à la liste des sites de confiance.
3. Accédez à [Téléchargements - MongoDB](https://www.mongodb.com/download-center#community).
4. Recherchez la **version stable actuelle** de l’élément **Serveur de la communauté** et sélectionnez la dernière version **64 bits** dans la colonne Windows. Téléchargez et exécutez le programme d’installation MSI.
5. MongoDB est généralement installé sur C:\Program Files\MongoDB. Recherchez des variables d’environnement sur le bureau et ajoutez le chemin d’accès aux fichiers binaires de MongoDB à la variable PATH. Par exemple, vous trouverez les fichiers binaires dans C:\Program Files\MongoDB\Server\3.2\bin sur votre ordinateur.
6. Créez les répertoires de données et de journaux MongoDB dans le disque de données (lecteur **F:**, par exemple) créé lors des étapes précédentes. Dans **Démarrer**, sélectionnez **Invite de commandes** pour ouvrir une fenêtre d’invite de commandes.  Tapez :
   
        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs
7. Pour exécuter la base de données, entrez :
   
        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
   
    Tous les messages du journal sont dirigés vers le fichier *F:\MongoLogs\mongolog.log* lorsque le serveur mongod.exe démarre et préalloue les fichiers journaux. Il se peut que plusieurs minutes soient nécessaires pour que MongaDB préalloue les fichiers journaux et commence à écouter les connexions. L’invite de commandes reste axée sur cette tâche pendant que votre instance MongoDB est en cours d’exécution.
8. Pour démarrer l’interpréteur de commandes d’administration de MongoDB, ouvrez une autre fenêtre de commande à partir de **Démarrer** et saisissez les commandes suivantes :
   
        C:\> cd \my_mongo_dir\bin  
        C:\my_mongo_dir\bin> mongo  
        >db  
        test
        > db.foo.insert( { a : 1 } )  
        > db.foo.find()  
        { _id : ..., a : 1 }  
        > show dbs  
        ...  
        > show collections  
        ...  
        > help  
   
    La base de données est créée par l'insertion.
9. Vous pouvez également installer mongod.exe en tant que service :
   
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
   
    Un service nommé « MongoDB » est installé et associé à la description « Mongo DB ». L’option `--logpath` permet de spécifier un fichier journal puisque le service en cours d’exécution n’a pas de fenêtre de commande pour afficher la sortie.  L’option `--logappend` spécifie qu’au redémarrage du service, la sortie est ajoutée au fichier journal existant.  L’option `--dbpath` spécifie l’emplacement du répertoire de données. Pour accéder à d’autres options de ligne de commande relatives au service, consultez la page [Options de ligne de commande relatives au service][MongoWindowsSvcOptions].
   
    Pour démarrer le service, exécutez cette commande :
   
        C:\> net start MongoDB
10. Maintenant que MongoDB est installé et en cours d’exécution, vous devez ouvrir un port dans le Pare-feu Windows pour vous connecter à distance à MongoDB.  Dans le menu **Démarrer**, sélectionnez **Outils d’administration**, puis **Pare-feu Windows avec fonctions avancées de sécurité**.
11. a) Dans le volet de gauche, sélectionnez **Règles de trafic entrant**.  Dans le volet **Actions** situé à droite, sélectionnez **Nouvelle règle...**.
    
    ![Pare-feu Windows][Image1]
    
    b) Dans l’**Assistant Nouvelle règle de trafic entrant**, sélectionnez **Port**, puis cliquez sur **Suivant**.
    
    ![Pare-feu Windows][Image2]
    
    c) Sélectionnez **TCP**, puis **Ports locaux spécifiques**.  Spécifiez le numéro de port « 27017 » (port par défaut sur lequel MongoDB écoute), puis cliquez sur **Suivant**.
    
    ![Pare-feu Windows][Image3]
    
    d) Sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.
    
    ![Pare-feu Windows][Image4]
    
    e) Cliquez à nouveau sur **Suivant**.
    
    ![Pare-feu Windows][Image5]
    
    f) Spécifiez un nom pour la règle, comme « MongoPort », puis cliquez sur **Terminer**.
    
    ![Pare-feu Windows][Image6]
12. Si vous n’avez configuré aucun point de terminaison pour la base de données MongoDB lors de la création de la machine virtuelle, vous pouvez le faire maintenant. La règle de pare-feu et le point de terminaison sont tous deux nécessaires pour vous connecter à distance à la base de données MongoDB. Dans le portail de gestion, cliquez sur **Machines virtuelles**, sur le nom de la nouvelle machine virtuelle, puis sur **Points de terminaison**.
    
    ![Points de terminaison][Image7]
13. Cliquez sur **Ajouter** en bas de la page. Sélectionnez **Ajouter un point de terminaison autonome**, puis cliquez sur **Suivant**.
    
    ![Points de terminaison][Image8]
14. Ajoutez un point de terminaison avec le nom « Mongo », le protocole **TCP** ainsi que les ports **Public** et **Privé** définis sur « 27017 ». En ouvrant ce port, vous pouvez accéder à distance à MongoDB.
    
    ![Points de terminaison][Image9]

> [!NOTE]
> Le port 27017 est le port par défaut utilisé par MongoDB. Vous pouvez le modifier en spécifiant le paramètre `--port` au démarrage du serveur mongod.exe. Veillez à donner le même numéro de port au pare-feu et au point de terminaison « Mongo » dans les instructions ci-dessus.
> 
> 

[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint3.png


<!--HONumber=Oct16_HO2-->


