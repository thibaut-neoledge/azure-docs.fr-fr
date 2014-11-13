Suivez cette procédure pour installer et exécuter MongoDB sur une machine virtuelle exécutant Windows Server.

<div class="dev-callout">
<b>Important</b>
<p>Les fonctionnalit&eacute;s de s&eacute;curit&eacute; MongoDB, comme l&rsquo;authentification et la liaison d&rsquo;adresse&nbsp;IP, ne sont pas activ&eacute;es par d&eacute;faut. Elles doivent &ecirc;tre activ&eacute;es avant le d&eacute;ploiement de MongoDB dans un environnement de production.  Pour plus d&rsquo;informations, consultez la page <a href="http://www.mongodb.org/display/DOCS/Security+and+Authentication">S&eacute;curit&eacute; et authentification</a>.</p>
</div>

1.  Une fois que vous êtes connecté à la machine virtuelle à l’aide du Bureau à distance, ouvrez Internet Explorer à partir du menu **Démarrer**.
2.  Sélectionnez le bouton **Outils** dans le coin supérieur droit. Dans **Options Internet**, sélectionnez l'onglet **Sécurité**, l'icône **Sites de confiance**, puis cliquez sur le bouton **Sites**. Ajoutez *http://\*.mongodb.org* à la liste des sites de confiance.
3.  Accédez à [Téléchargements - MongoDB][Téléchargements - MongoDB].
4.  Recherchez la version la plus récente sous la section **Version de production (Recommandé)** et cliquez sur le lien \***2008+** dans la colonne Windows 64 bits. Cliquez sur **Enregistrer sous**, puis enregistrez le fichier .zip sur le Bureau.
5.  Cliquez avec le bouton droit sur le fichier .zip, puis sélectionnez **Extraire tout...** Spécifiez « C: » , puis cliquez sur **Extraire**. Une fois les fichiers extraits, vous pouvez renommer le dossier d’installation pour lui donner un nom plus simple. « MongoDB », par exemple.
6.  Créez les répertoires de données et du journal MongoDB dans le disque de données (lecteur **F:**, par exemple) créé lors des étapes précédentes. Dans **Démarrer**, sélectionnez **Invite de commandes** pour ouvrir une fenêtre d’invite de commandes. Type :

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs

7.  Pour exécuter la base de données, entrez :

        F:\> C:
        C:\> cd \MongoDB\bin
        C:\my_mongo_dir\bin> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    Tous les messages du journal sont dirigés vers le fichier *F:\\MongoLogs\\mongolog.log* lorsque le serveur mongod.exe démarre et préalloue les fichiers journaux. Il se peut que plusieurs minutes soient nécessaires pour que MongaDB préalloue les fichiers journaux et commence à écouter les connexions.

8.  Pour lancer l'interpréteur de commandes d'administration de MongoDB, ouvrez une autre fenêtre de commande à partir de **Démarrer** et entrez le type suivant :

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

    La base de données est créée par l’insertion.

9.  (Facultatif) mongod.exe prend en charge l’installation et l’exécution comme un service Windows. Pour installer mongod.exe en tant que service, exécutez la commande suivante à partir de l’invite de commandes :

        C:\mongodb\bin>mongod --logpath "c:\mongodb\logs\logfile.log" --logappend --dbpath "c:\data" --install 

    Un service nommé « Mongo DB » est créé avec « Mongo DB » comme description. L’option **--logpath** permet de spécifier un fichier journal puisque le service en cours d’exécution n’a pas de fenêtre de commande pour afficher la sortie. L’option **--logpath** spécifie qu’au redémarrage du service, la sortie est ajoutée au fichier journal existant. L’option **--dbpath** spécifie l’emplacement du répertoire de données. Pour plus d’informations sur les options de ligne de commande associées au service, consultez la page [Options de ligne de commande associées au service][Options de ligne de commande associées au service].

10. Maintenant que MongoDB est installé et en cours d'exécution, vous devez ouvrir un port dans le Pare-feu Windows pour vous connecter à distance à MongoDB. À partir du menu **Démarrer**, sélectionnez **Outils d'administration**, puis **Pare-feu Windows avec fonctions avancées de sécurité**.

11. Dans le volet gauche, sélectionnez **Règles de trafic entrant**. Dans le volet **Actions** situé à droite, sélectionnez **Nouvelle règle...**.

    ![Pare-feu Windows][Pare-feu Windows]

    Dans l'**Assistant Nouvelle règle de trafic entrant**, sélectionnez **Port**, puis cliquez sur **Suivant**.

    ![Pare-feu Windows][1]

    Sélectionnez **TCP**, puis **Ports locaux spécifiques**. Spécifiez le numéro de port « 27107 » (celui sur lequel MongoDB écoute), puis cliquez sur **Suivant**.

    ![Pare-feu Windows][2]

    Sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.

    ![Pare-feu Windows][3]

    Cliquez à nouveau sur **Suivant**.

    ![Pare-feu Windows][4]

    Spécifiez un nom pour la règle, comme « MongoPort », puis cliquez sur **Terminer**

    ![Pare-feu Windows][5]

12. Si vous n'avez configuré aucun point de terminaison pour la base de données MongoDB lors de la création de la machine virtuelle, vous pouvez le faire maintenant. La règle de pare-feu et le point de terminaison sont tous deux nécessaires pour vous connecter à distance à la base de données MongoDB. Dans le portail de gestion, cliquez sur **Machines virtuelles**, sur le nom de la nouvelle machine virtuelle, puis sur **Points de terminaison**.

    ![Points de terminaison][Points de terminaison]

13. Cliquez sur **Add Endpoint** en bas de la page. Sélectionnez **Add Endpoint**, puis cliquez sur **Suivant**.

    ![Points de terminaison][6]

14. Ajoutez un point de terminaison avec le nom « Mongo », le protocole **TCP** ainsi que les ports **Public** et **Privé** définis sur « 27017 ». Cela permet d’accéder à distance à MongoDB.

    ![Points de terminaison][7]

  [Téléchargements - MongoDB]: http://www.mongodb.org/downloads
  [Options de ligne de commande associées au service]: http://www.mongodb.org/display/DOCS/Windows+Service
  [Pare-feu Windows]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
  [1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
  [2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
  [3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
  [4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
  [5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
  [Points de terminaison]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint.png
  [6]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
  [7]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint3.png
