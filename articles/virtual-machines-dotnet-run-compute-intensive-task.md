<properties 
	pageTitle="Tâche de calcul intensif .NET sur une machine virtuelle - Azure" 
	description="Apprenez à déployer et à exécuter une application de calcul intensif .NET sur une machine virtuelle Azure et à utiliser des files d'attente Service Bus pour surveiller la progression à distance." 
	services="virtual-machines" 
	documentationCenter=".net" 
	authors="" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="wpickett"/>

# Exécution d'une tâche nécessitant beaucoup de ressources dans .NET sur une machine virtuelle Azure

Avec Azure, vous pouvez utiliser une machine virtuelle pour gérer des tâches nécessitant beaucoup de ressources ; par exemple, une machine virtuelle peut gérer des tâches et fournir des résultats à des ordinateurs clients ou à des applications mobiles. Ce guide vous permettra de créer une machine virtuelle exécutée sur une application .NET nécessitant beaucoup de ressources et pouvant être surveillée par une autre application .NET.

Ce didacticiel part du principe que vous savez créer des applications console .NET. Aucune connaissance d'Azure n'est nécessaire. 

Vous apprendrez à effectuer les opérations suivantes :

* création d'une machine virtuelle ;
* se connecter à distance à votre machine virtuelle ;
* créer un espace de noms Service Bus ;
* création d'une application .NET exécutant une tâche qui nécessite beaucoup de ressources ;
* création d'une application .NET surveillant la progression de la tâche qui nécessite beaucoup de ressources ;
* exécution des applications .NET ;
* arrêt des applications .NET.

Dans le cadre de ce didacticiel, la tâche nécessitant beaucoup de ressources repose sur le problème du voyageur de commerce. Vous trouverez ci-dessous un exemple d'application .NET qui exécute la tâche nécessitant beaucoup de ressources :

![Traveling Salesman Problem solver][solver_output]

Vous trouverez ci-dessous un exemple d'application .NET qui surveille la tâche nécessitant beaucoup de ressources :

![Traveling Salesman Problem client][client_output]

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

<h2>Création d'une machine virtuelle</h2>

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur **Nouveau**.
3. Cliquez sur **Machine virtuelle**.
4. Cliquez sur **Création rapide**.
5. Dans l'écran **Créer une machine virtuelle**, entrez une valeur pour **Nom DNS**.
6. Dans la liste déroulante **Image**, sélectionnez une image, comme **Windows Server 2012**.
7. Entrez un nom pour l'administrateur dans le champ **Nom d'utilisateur**. Notez le nom et le mot de passe que vous allez entrer, car vous les utiliserez pour vous connecter à distance à votre machine virtuelle.
8. Entrez un mot de passe dans le champ **Nouveau mot de passe**, puis entrez-le de nouveau dans le champ **Confirmer**.
9. Dans la liste déroulante **Emplacement**, sélectionnez l'emplacement du centre de données pour votre machine virtuelle.
10. Cliquez sur **Créer une machine virtuelle**. La création de votre machine virtuelle démarre. Vous pouvez surveiller l'état dans la section **Machines virtuelles** du portail de gestion. Lorsque son état s'affiche comme **Actif**, vous pouvez vous connecter à la machine virtuelle.

<h2>Connexion distante à votre machine virtuelle</h2>

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com).
2. Cliquez sur **Machines virtuelles**.
3. Cliquez sur le nom de la machine virtuelle à laquelle vous voulez vous connecter.
4. Cliquez sur **Connecter**.
5. Répondez aux invites pour vous connecter à la machine virtuelle. Lorsque vous y êtes invité, entrez le nom d'administrateur et le mot de passe fournis lors de la création de la machine virtuelle.

<h2>Création d'un espace de noms Service Bus</h2>

Pour commencer à utiliser les files d'attente Service Bus dans Azure, vous devez d'abord créer un espace de noms de service. Ce dernier fournit un conteneur d'étendue pour l'adressage des ressources Service Bus au sein de votre application.

Pour créer un espace de noms de service :

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2.  Dans le volet de navigation gauche du portail de gestion, cliquez sur **Bus des services**.
3.  Dans le volet inférieur du portail de gestion, cliquez sur **Créer**.

    ![Create new service bus][create_service_bus]
4.  Dans la boîte de dialogue **Création d'un espace de noms**, entrez un nom d'espace de no
	ms. Le système vérifie immédiatement si le nom est disponible, puisqu'il doit être unique.

    ![Create a namespace dialog][create_namespace_dialog]
5.  Après vous être assuré que le nom de l'espace de noms est disponible, choisissez la région où votre espace de noms doit être hébergé (veillez à utiliser la région dans laquelle votre machine virtuelle est hébergée).

    > [AZURE.IMPORTANT] Sélectionnez la **région** que vous utilisez ou celle que vous prévoyez d'utiliser pour votre machine virtuelle. Vous bénéficiez ainsi des meilleures performances.

6. Si vous disposez de plusieurs abonnements Azure pour le compte avec lequel vous vous connectez, sélectionnez celui qui utilisera l'espace de no
	ms. Si vous ne disposez que d'un abonnement pour le compte avec lequel vous vous connectez, vous ne voyez pas de liste déroulante contenant vos abonnements.
7. Cliquez sur la coche. Le système crée l'espace de noms de service et l'active. Vous devrez peut-être attendre plusieurs minutes afin que le système approvisionne des ressources pour votre compte.

	![Click create screenshot][click_create]

L'espace de noms que vous avez créé apparaît alors dans le portail de gestion. Son activation peut prendre un peu de temps. Attendez que l'état soit **Actif** avant de passer à l'étape suivante.

<h2>Obtention d'informations d'identification de gestion par défaut pour l'espace de noms</h2>

Pour pouvoir effectuer des opérations de gestion telles que la création d'une file d'attente sur le nouvel espace de noms, vous devez obtenir les informations d'identification de gestion associées.

1.  Dans le volet de navigation gauche, cliquez sur le nœud **Bus de service** node, pour
    afficher la liste des espaces de noms disponibles :   
    ![Available namespaces screenshot][available_namespaces]
2.  Sélectionnez l'espace de noms que vous venez de créer dans la liste affichée :   
    ![Namespace list screenshot][namespace_list]
3. Cliquez sur **Clé d'accès**.   
    ![Access key button][access_key_button]
4.  Dans la boîte de dialogue, recherchez les entrées **Émetteur par défaut** et **Clé par défaut**. Notez ces valeurs, car vous les utiliserez ci-dessous pour effectuer des opérations avec l'espace de no
	ms. 

<h2>Création d'une application .NET exécutant une tâche qui nécessite beaucoup de ressources</h2>

1. Sur votre ordinateur de développement (qui n'est pas forcément celui où se trouve la machine virtuelle que vous avez créée), téléchargez le [Kit de développement logiciel (SDK) Azure pour .NET](http://azure.microsoft.com/develop/net/).
2. Créez une application console .NET avec le projet nommé **TSPSolver**. Assurez-vous que l'infrastructure cible est définie sur .**NET Framework 4** ou version ultérieure (et non **.NET Framework 4 Client Profile**). Elle peut être définie après la création d'un projet comme suit : dans le menu de Visual Studio, cliquez successivement sur **Projets**, sur **Propriétés** et sur l'onglet **Application**, puis définissez la valeur pour **Framework cible**.
3. Ajoutez la bibliothèque Microsoft ServiceBus. Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur **TSPSolver**, cliquez sur **Ajouter une référence**, sur l'onglet **Parcourir**, accédez au Kit SDK Azure .NET (par exemple à l'emplacement **C:\Program Files\Microsoft SDKs\Azure\.NET SDK\v2.5\ToolsRef**) et sélectionnez **Microsoft.ServiceBus.dll** comme référence.
4. Ajoutez la bibliothèque System Runtime Serialization. Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur **TSPSolver**, cliquez sur **Ajouter une référence**, sur l'onglet **.NET**, puis sélectionnez **System.Runtime.Serialization** comme référence.
5. Utilisez l'exemple de code disponible à la fin de cette section pour le contenu de **Program.cs**.
6. Modifiez les espaces réservés **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** et **your\_service\_bus\_key** pour utiliser vos valeurs d'**Espace de noms**, d'**Émetteur par défaut** et de **Clé par défaut** Service Bus, respectivement.
7. Compilez l'application. **TSPSolver.exe** est créé dans le dossier **bin** de votre projet (**bin\release** ou **bin\debug**, en fonction de ce que vous ciblez : version Release ou de débogage). Vous allez copier ultérieurement cet exécutable et Microsoft.ServiceBus.dll dans votre machine virtuelle.

<p/>

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.IO;
	
	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Messaging;
	
	namespace TSPSolver
	{
	    class Program
	    {
	        // Value specifying how often to provide an update to the console.
	        private static long loopCheck = 100000000;
	        private static long nTimes = 0, nLoops = 0;
	
	        private static double[,] distances;
	        private static String[] cityNames;
	        private static int[] bestOrder;
	        private static double minDistance;
	
	        private static NamespaceManager namespaceManager;
	        private static QueueClient queueClient;
	        private static String queueName = "TSPQueue";
	
	        private static void BuildDistances(String fileLocation, int numCities)
	        {
	
	            try
	            {
	                StreamReader sr = new StreamReader(fileLocation);
	                String[] sep1 = { ", " };
	
	                double[,] cityLocs = new double[numCities, 2];
	
	                for (int i = 0; i < numCities; i++)
	                {
	                    String[] line = sr.ReadLine().Split(sep1, StringSplitOptions.None);
	                    cityNames[i] = line[0];
	                    cityLocs[i, 0] = Convert.ToDouble(line[1]);
	                    cityLocs[i, 1] = Convert.ToDouble(line[2]);
	                }
	                sr.Close();
	
	                for (int i = 0; i < numCities; i++)
	                {
	                    for (int j = i; j < numCities; j++)
	                    {
	                        distances[i, j] = hypot(Math.Abs(cityLocs[i, 0] - cityLocs[j, 0]), Math.Abs(cityLocs[i, 1] - cityLocs[j, 1]));
	                        distances[j, i] = distances[i, j];
	                    }
	                }
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }
	
	        private static double hypot(double x, double y)
	        {
	            return Math.Sqrt(x * x + y * y);
	        }
	
	        private static void permutation(List<int> startCities, double distSoFar, List<int> restCities)
	        {
	            try
	            {
	
	                nTimes++;
	                if (nTimes == loopCheck)
	                {
	                    nLoops++;
	                    nTimes = 0;
	                    DateTime dateTime = DateTime.Now;
	                    Console.Write("Current time is {0}.", dateTime);
	                    Console.WriteLine(" Completed {0} iterations of size of {1}.", nLoops, loopCheck);
	                }
	
	                if ((restCities.Count == 1) && ((minDistance == -1) || (distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 1]] < minDistance)))
	                {
	                    startCities.Add(restCities[0]);
	                    newBestDistance(startCities, distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 2]]);
	                    startCities.Remove(startCities[startCities.Count - 1]);
	                }
	                else
	                {
	                    for (int i = 0; i < restCities.Count; i++)
	                    {
	                        startCities.Add(restCities[0]);
	                        restCities.Remove(restCities[0]);
	                        permutation(startCities, distSoFar + distances[startCities[startCities.Count - 1], startCities[startCities.Count - 2]], restCities);
	                        restCities.Add(startCities[startCities.Count - 1]);
	                        startCities.Remove(startCities[startCities.Count - 1]);
	                    }
	                }
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }
	
	        private static void newBestDistance(List<int> cities, double distance)
	        {
	            try
	            {
	                minDistance = distance;
	                String cityList = "Shortest distance is " + minDistance + ", with route: ";
	
	                for (int i = 0; i < bestOrder.Length; i++)
	                {
	                    bestOrder[i] = cities[i];
	                    cityList += cityNames[bestOrder[i]];
	                    if (i != bestOrder.Length - 1)
	                        cityList += ", ";
	                }
	                Console.WriteLine(cityList);
	                queueClient.Send(new BrokeredMessage(cityList));
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }
	
	        static void Main(string[] args)
	        {
	            try
	            {
	
	                String serviceBusNamespace = "your_service_bus_namespace";
	                String issuer = "your_service_bus_owner";
	                String key = "your_service_bus_key";
	
	                String connectionString = @"Endpoint=sb://" +
	                       serviceBusNamespace +
	                       @".servicebus.windows.net/;SharedSecretIssuer=" +
	                       issuer + @";SharedSecretValue=" + key;
	
	                int numCities = 10; // Use as the default, if no value is specified
	                // at the command line.
	                if (args.Count() != 0)
	                {
	
	                    if (args[0].ToLower().CompareTo("createqueue") == 0)
	                    {
	                        // No processing to occur other than creating the queue.
	                        namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
	                        namespaceManager.CreateQueue(queueName);
	                        Console.WriteLine("Queue named {0} was created.", queueName);
	                        Environment.Exit(0);
	                    }
	
	                    if (args[0].ToLower().CompareTo("deletequeue") == 0)
	                    {
	                        // No processing to occur other than deleting the queue.
	                        namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
	                        namespaceManager.DeleteQueue("TSPQueue");
	                        Console.WriteLine("Queue named {0} was deleted.", queueName);
	                        Environment.Exit(0);
	                    }
	
	                    // Neither creating or deleting a queue.
	                    // Assume the value passed in is the number of cities to solve.
	                    numCities = Convert.ToInt32(args[0]);
	                }
	
	                Console.WriteLine("Running for {0} cities.", numCities);
	
	                queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");
	
	                List<int> startCities = new List<int>();
	                List<int> restCities = new List<int>();
	
	                startCities.Add(0);
	                for (int i = 1; i < numCities; i++)
	                {
	                    restCities.Add(i);
	                }
	                distances = new double[numCities, numCities];
	                cityNames = new String[numCities];
	                BuildDistances(@"c:\tsp\cities.txt", numCities);
	                minDistance = -1;
	                bestOrder = new int[numCities];
	                permutation(startCities, 0, restCities);
	                Console.WriteLine("Final solution found!");
	                queueClient.Send(new BrokeredMessage("Complete"));
	
	                queueClient.Close();
	                Environment.Exit(0);

	            }
	            catch (ServerBusyException serverBusyException)
	            {
	                Console.WriteLine("ServerBusyException encountered");
	                Console.WriteLine(serverBusyException.Message);
	                Console.WriteLine(serverBusyException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (ServerErrorException serverErrorException)
	            {
	                Console.WriteLine("ServerErrorException encountered");
	                Console.WriteLine(serverErrorException.Message);
	                Console.WriteLine(serverErrorException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (Exception exception)
	            {
	                Console.WriteLine("Exception encountered");
	                Console.WriteLine(exception.Message);
	                Console.WriteLine(exception.StackTrace);
	                Environment.Exit(-1);
	            }
	        }
	    }
	}



<h2>Création d'une application .NET surveillant la progression de la tâche qui nécessite beaucoup de ressources</h2>

1. Sur votre ordinateur de développement, créez une application console .NET avec **TSPClient** comme nom de projet. Assurez-vous que l'infrastructure cible est définie sur .**NET Framework 4** ou version ultérieure (et non **.NET Framework 4 Client Profile**). Elle peut être définie après la création d'un projet comme suit : dans le menu de Visual Studio, cliquez successivement sur **Projets**, sur **Propriétés** et sur l'onglet **Application**, puis définissez la valeur pour **Framework cible**.
2. Ajoutez la bibliothèque Microsoft ServiceBus. Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur **TSPSolver**, cliquez sur **Ajouter une référence**, sur l'onglet **Parcourir**, accédez au Kit SDK Azure .NET (par exemple à l'emplacement **C:\Program Files\Microsoft SDKs\Azure\.NET SDK\v2.5\ToolsRef**) et sélectionnez **Microsoft.ServiceBus.dll** comme référence.
3. Ajoutez la bibliothèque System Runtime Serialization. Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur **TSPClient**, cliquez sur **Ajouter une référence**, sur l'onglet **.NET**, puis sélectionnez **System.Runtime.Serialization** comme référence.
4. Utilisez l'exemple de code disponible à la fin de cette section pour le contenu de **Program.cs**.
5. Modifiez les espaces réservés **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** et **your\_service\_bus\_key** pour utiliser vos valeurs d'**Espace de noms**, d'**Émetteur par défaut** et de **Clé par défaut** Service Bus, respectivement.
5. Compilez l'application. **TSPClient.exe** est créé dans le dossier **bin** de votre projet (**bin\release** ou **bin\debug**, en fonction de ce que vous ciblez : version Release ou de débogage). Vous pouvez exécuter ce code sur votre ordinateur de développement, ou copier cet exécutable et Microsoft.ServiceBus.dll sur un ordinateur qui exécute l'application cliente (il n'est pas nécessaire que ce soit votre machine virtuelle).

<p/>

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.IO;
	
	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Messaging;
	using System.Threading; // For Thread.Sleep
	
	namespace TSPClient
	{
	    class Program
	    {
	
	        static void Main(string[] args)
	        {
	
	            try
	            {
	
	                Console.WriteLine("Starting at {0}", DateTime.Now);
	
	                String serviceBusNamespace = "your_service_bus_namespace";
	                String issuer = "your_service_bus_owner";
	                String key = "your_service_bus_key";
	
	                String connectionString = @"Endpoint=sb://" +
	                       serviceBusNamespace +
	                       @".servicebus.windows.net/;SharedSecretIssuer=" +
	                       issuer + @";SharedSecretValue=" + key;
	
	                QueueClient queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");
	
	                BrokeredMessage message;
	
	                int waitMinutes = 3;  // Use as the default, if no value
	                // is specified at command line.
	
	                if (0 != args.Length)
	                {
	                    waitMinutes = Convert.ToInt16(args[0]);
	                }
	
	                String waitString;
	                waitString = (waitMinutes == 1) ? "minute" : waitMinutes.ToString() + " minutes";
	
	                while (true)
	                {
	                    message = queueClient.Receive();
	
	                    if (message != null)
	                    {
	                        try
	                        {
	                            string str = message.GetBody<string>();
	                            Console.WriteLine(str);
	
	                            // Remove message from queue
	                            message.Complete();
	
	                            if ("Complete" == str)
	                            {
	                                Console.WriteLine("Finished at {0}.", DateTime.Now);
	                                break;
	                            }
	                        }
	                        catch (Exception e)
	                        {
	                            // Indicates a problem. Unlock the message in the queue.
	                            message.Abandon();
	                            throw e;
	                        }
	                    }
	                    else
	                    {
	                        // The queue is empty.
	                        Console.WriteLine("Queue is empty. Sleeping for another {0}.", waitString);
	                        System.Threading.Thread.Sleep(60000 * waitMinutes);
	                    }
	                }
	                queueClient.Close();
	                Environment.Exit(0);
	            }
	            catch (ServerBusyException serverBusyException)
	            {
	                Console.WriteLine("ServerBusyException encountered");
	                Console.WriteLine(serverBusyException.Message);
	                Console.WriteLine(serverBusyException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (ServerErrorException serverErrorException)
	            {
	                Console.WriteLine("ServerErrorException encountered");
	                Console.WriteLine(serverErrorException.Message);
	                Console.WriteLine(serverErrorException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (Exception exception)
	            {
	                Console.WriteLine("Exception encountered");
	                Console.WriteLine(exception.Message);
	                Console.WriteLine(exception.StackTrace);
	                Environment.Exit(-1);
	            }
	        }
	    }
	}

<h2>Exécution des applications .NET</h2>
Exécutez l'application nécessitant beaucoup de ressources pour créer la file d'attente, puis pour résoudre le problème du voyageur de commerce afin d'ajouter le meilleur itinéraire actuel à la file d'attente Service Bus. Pendant (ou après) l'exécution de l'application nécessitant beaucoup de ressources, exécutez le client pour afficher les résultats de la file d'attente Service Bus.

<h3>Exécution de l'application nécessitant beaucoup de ressources</h3>

1. Connectez-vous à votre machine virtuelle.
2. Créez un dossier intitulé **c:\TSP**. C'est celui où vous exécuterez votre application.
3. Copiez TSPSolver.exe et Microsoft.ServiceBus.dll, tous deux disponibles dans le dossier **bin** de votre projet TSPSolver, dans **c:\TSP**.
4. Créez un fichier intitulé **c:\TSP\cities.txt** avec le contenu suivant :

		City_1, 1002.81, -1841.35
		City_2, -953.55, -229.6
		City_3, -1363.11, -1027.72
		City_4, -1884.47, -1616.16
		City_5, 1603.08, -1030.03
		City_6, -1555.58, 218.58
		City_7, 578.8, -12.87
		City_8, 1350.76, 77.79
		City_9, 293.36, -1820.01
		City_10, 1883.14, 1637.28
		City_11, -1271.41, -1670.5
		City_12, 1475.99, 225.35
		City_13, 1250.78, 379.98
		City_14, 1305.77, 569.75
		City_15, 230.77, 231.58
		City_16, -822.63, -544.68
		City_17, -817.54, -81.92
		City_18, 303.99, -1823.43
		City_19, 239.95, 1007.91
		City_20, -1302.92, 150.39
		City_21, -116.11, 1933.01
		City_22, 382.64, 835.09
		City_23, -580.28, 1040.04
		City_24, 205.55, -264.23
		City_25, -238.81, -576.48
		City_26, -1722.9, -909.65
		City_27, 445.22, 1427.28
		City_28, 513.17, 1828.72
		City_29, 1750.68, -1668.1
		City_30, 1705.09, -309.35
		City_31, -167.34, 1003.76
		City_32, -1162.85, -1674.33
		City_33, 1490.32, 821.04
		City_34, 1208.32, 1523.3
		City_35, 18.04, 1857.11
		City_36, 1852.46, 1647.75
		City_37, -167.44, -336.39
		City_38, 115.4, 0.2
		City_39, -66.96, 917.73
		City_40, 915.96, 474.1
		City_41, 140.03, 725.22
		City_42, -1582.68, 1608.88
		City_43, -567.51, 1253.83
		City_44, 1956.36, 830.92
		City_45, -233.38, 909.93
		City_46, -1750.45, 1940.76
		City_47, 405.81, 421.84
		City_48, 363.68, 768.21
		City_49, -120.3, -463.13
		City_50, 588.51, 679.33
	
5. Depuis une invite de commandes, accédez au répertoire c:\TSP.
6. Vous devez créer la file d'attente Service Bus avant d'exécuter les permutations de solveur TSP. Exécutez la commande suivante pour créer la file d'attente Service Bus :

        TSPSolver createqueue

7. Maintenant que la file d'attente est créée, vous pouvez exécuter les permutations de solveur TSP. Par exemple, exécutez la commande suivante afin d'exécuter le solveur pour 8 villes. 

        TSPSolver 8

 Si vous n'entrez aucun nombre, l'exécution portera sur 10 villes. Dès que le solveur trouve les itinéraires les plus courts, il les ajoute à la file d'attente.

Le solveur s'exécutera jusqu'à ce qu'il ait examiné tous les itinéraires.

> [AZURE.NOTE]
> Plus le nombre spécifié est élevé, plus l'exécution du solveur est longue. Par exemple, une exécution portant sur 14 villes peut prendre quelques minutes, et une exécution portant sur 15 villes peut prendre des heures. Au-delà de 16 villes, l'exécution peut prendre des jours (voire des semaines, des mois et des années). Cette lenteur est due à la hausse rapide du nombre de permutations évaluées par le solveur à mesure que le nombre de villes augmente.
 
<h3>Exécution de la surveillance de l'application cliente</h3>
1. Connectez-vous à l'ordinateur où vous exécuterez l'application cliente. Il ne doit pas nécessairement s'agir de l'ordinateur qui exécute l'application **TSPSolver**.
2. Créez un dossier où vous exécuterez votre application. Par exemple, **c:\TSP**.
3. Copiez **TSPClient.exe** et Microsoft.ServiceBus.dll, qui se trouvent tous deux dans le dossier **bin** de votre projet TSPClient, dans le dossier c:\TSP folder.
4. Depuis une invite de commandes, accédez au répertoire c:\TSP.
5. Exécutez la commande suivante :

        TSPClient

    Vous pouvez éventuellement spécifier le nombre de minutes de veille entre les vérifications de la file d'attente via un argument de ligne de commande. La période de veille par défaut de la vérification de la file d'attente est de 3 minutes et elle est appliquée si aucun argument de ligne de commande n'est transmis à **TSPClient**. Si vous souhaitez utiliser une autre valeur pour l'intervalle de veille (une minute, par exemple), exécutez :

	    TSPClient 1

    Le client s'exécutera jusqu'à ce qu'il voie le message de file d'attente " Terminé ". Notez que si vous exécutez plusieurs occurrences du solveur sans exécuter le client, vous serez peut-être amené à exécuter le client plusieurs fois pour vider entièrement la file d'attente. Vous pouvez également supprimer la file d'attente puis la recréer. Pour supprimer la file d'attente, exécutez la commande **TSPSolver** (et non **TSPClient**) :

        TSPSolver deletequeue

<h2>Arrêt des applications .NET.</h2>

Pour quitter les applications solveur et cliente avant la fin normale, vous pouvez appuyer sur **Ctrl+C**.

<h2>Alternative pour créer et supprimer la file d'attente avec TSPSolver</h2>
Au lieu d'utiliser TSPSolver pour créer et supprimer la file d'attente, vous pouvez effectuer ces tâches à l'aide du [portail de gestion Azure](https://manage.windowsazure.com). Consultez la section Service Bus du portail de gestion pour accéder aux interfaces utilisateur afin de créer ou de supprimer une file d'attente, et d'extraire la chaîne de connexion, l'émetteur et la clé d'accès. Vous pouvez également afficher un tableau de bord de vos files d'attente Service Bus, vous permettant de consulter les mesures de vos messages entrants et sortants. 

[solver_output]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPSolver.png
[client_output]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPClient.png
[create_service_bus]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ServiceBusCreateNew.png
[create_namespace_dialog]: ./media/virtual-machines-dotnet-run-compute-intensive-task/CreateNameSpaceDialog.png
[available_namespaces]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AvailableNamespaces.png
[click_create]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ClickCreate.png
[namespace_list]: ./media/virtual-machines-dotnet-run-compute-intensive-task/NamespaceList.png
[access_key_button]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AccessKey.png




<!--HONumber=42-->
