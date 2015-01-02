Suivez cette procédure pour installer et exécuter MongoDB sur une machine virtuelle exécutant Windows Server.

<div class="dev-callout">
<b>Important</b>
<p>Les fonctionnalités de sécurité MongoDB, comme l'authentification et la liaison d'adresse IP, ne sont pas activées par défaut. Elles doivent être activées avant le déploiement de MongoDB dans un environnement de production.  Consultez <a href="http://www.mongodb.org/display/DOCS/Security+and+Authentication">Sécurité et authentification</a> pour plus d'informations.</p>
</div>

1. Une fois que vous êtes connecté à la machine virtuelle à l'aide du Bureau à distance, ouvrez Internet Explorer à partir du menu **Démarrer**.
2. Sélectionnez le bouton **Outils** dans le coin supérieur droit.  Dans **Options Internet**, sélectionnez l'onglet **Sécurité**, puis l'icône **Sites de confiance**, enfin cliquez sur le bouton **Sites**. Ajoutez *http://\*.mongodb.org* à la liste des sites de confiance.
3. Accédez à [Téléchargements - MongoDB] [MongoDownloads].
4. Recherchez la version la plus récente sous la section **Version de production (Recommandé)** et cliquez sur le lien ***2008+** dans la colonne Windows 64 bits.  Cliquez sur **Enregistrer sous**, puis enregistrez le fichier .zip sur le Bureau.
5. Cliquez avec le bouton droit sur le fichier .zip, puis sélectionnez **Extraire tout...**  Spécifiez " C: ", puis cliquez sur **Extraire**.  Une fois les fichiers extraits, vous pouvez renommer le dossier d'installation pour lui donner un nom plus simple.  " MongoDB ", par exemple.
6. Créez les répertoires de données et du journal MongoDB dans le disque de données (lecteur **F:**, par exemple) créé lors des étapes précédentes. Dans le menu **Démarrer**, sélectionnez **Invite de commandes** pour ouvrir une fenêtre d'invite de commandes.  Type :

		C:\> F:
		F:\> mkdir \MongoData
		F:\> mkdir \MongoLogs

7. Pour exécuter la base de données, entrez : 

		F:\> C:
		C:\> cd \MongoDB\bin
		C:\my_mongo_dir\bin> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

	Tous les messages du journal sont dirigés vers le fichier *F:\MongoLogs\mongolog.log* lorsque le serveur mongod.exe démarre et préalloue les fichiers journaux. Il se peut que plusieurs minutes soient nécessaires pour que MongaDB préalloue les fichiers journaux et commence à écouter les connexions.

8. Pour lancer l'interpréteur de commandes d'administration de MongoDB, ouvrez une autre fenêtre de commande à partir de **Démarrer** et entrez le type suivant :

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
9. (Facultatif) mongod.exe prend en charge l'installation et l'exécution comme un service Windows. Pour installer mongod.exe en tant que service, exécutez la commande suivante à partir de l'invite de commandes :

		C:\mongodb\bin>mongod --logpath "c:\mongodb\logs\logfile.log" --logappend --dbpath "c:\data" --install 

	Un service nommé " Mongo DB " est créé avec " Mongo DB " comme description. L'option **--logpath** permet de spécifier un fichier journal puisque le service en cours d'exécution n'a pas de fenêtre de commande pour afficher la sortie.  L'option **--logpath** spécifie qu'au redémarrage du service, la sortie est ajoutée au fichier journal existant.  L'option **--dbpath** spécifie l'emplacement du répertoire de données. Pour plus d'informations sur les options de ligne de commande associées au service, consultez la page [Options de ligne de commande associées au service] [MongoWindowsSvcOptions].
10. Maintenant que MongoDB est installé et en cours d'exécution, vous devez ouvrir un port dans le Pare-feu Windows pour vous connecter à distance à MongoDB.  Dans le menu **Démarrer**, sélectionnez **Outils d'administration**, puis **Pare-feu Windows avec fonctions avancées de sécurité**. 

11. Dans le volet gauche, sélectionnez **Règles de trafic entrant**.  Dans le volet **Actions** situé à droite, sélectionnez **Nouvelle règle...**.
	
	![Windows Firewall][Image1]

	Dans l'**Assistant Nouvelle règle de trafic entrant**, sélectionnez **Port**, puis cliquez sur **Suivant**.
	
	![Windows Firewall][Image2]

	Sélectionnez **TCP**, puis **Ports locaux spécifiques**.  Spécifiez le numéro de port " 27017 " (celui sur lequel MongoDB écoute), puis cliquez sur **Suivant**.

	![Windows Firewall][Image3]

	Sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.

	![Windows Firewall][Image4]

	Cliquez à nouveau sur **Suivant**.
	
	![Windows Firewall][Image5]

	Spécifiez un nom pour la règle, comme " MongoPort ", puis cliquez sur **Terminer**.

	![Windows Firewall][Image6]
	
12. Si vous n'avez configuré aucun point de terminaison pour la base de données MongoDB lors de la création de la machine virtuelle, vous pouvez le faire maintenant. La règle de pare-feu et le point de terminaison sont tous deux nécessaires pour vous connecter à distance à la base de données MongoDB. Dans le portail de gestion, cliquez sur **Machines virtuelles**, sur le nom de la nouvelle machine virtuelle, puis sur **Points de terminaison**.

	![Endpoints][Image7]
13. Cliquez sur **Ajouter un point de terminaison** au bas de la page. Sélectionnez **Ajouter un point de terminaison**, puis cliquez sur **Suivant**.
	
	![Endpoints][Image8]

14. Ajoutez un point de terminaison avec le nom " Mongo ", le protocole **TCP**, ainsi que les ports **Public** et **Privé** définis sur " 27017 ". Cela permet d'accéder à distance à MongoDB.

	![Endpoints][Image9]

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

<!--HONumber=35_1-->
