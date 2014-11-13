<properties title="Analyse des donn&eacute;es de capteur avec Storm et HDInsight" pageTitle="Analyse des donn&eacute;es de capteur avec Apache Storm et Microsoft Azure HDInsight (Hadoop)" description="D&eacute;couvrez comment utiliser Apache Storm pour traiter des donn&eacute;es de capteur en temps r&eacute;el avec HDInsight (Hadoop)" metaKeywords="Azure hdinsight storm, Azure hdinsight realtime, azure hadoop storm, azure hadoop realtime, azure hadoop real-time, azure hdinsight real-time" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="larryfr" />

# Analyse des données de capteur avec Storm et HBase dans HDInsight (Hadoop)

Découvrez comment utiliser et développer une solution utilisant un cluster Storm HDInsight pour traiter les données de capteur à partir du concentrateur d'événements Azure. Durant le traitement, la topologie Storm archive les données entrantes dans un cluster HBase. Elle utilise également SignalR pour fournir des informations en temps réel via un tableau de bord web hébergé sur Sites Web Azure.

> [AZURE.NOTE] Une version complète de ce projet est disponible sur la page <https://github.com/Blackmist/hdinsight-eventhub-example>.

## Configuration requise

-   Un abonnement Azure

-   Visual Studio avec le [Kit de développement logiciel (SDK) Microsoft Azure pour .NET][Kit de développement logiciel (SDK) Microsoft Azure pour .NET]

-   [Java et JDK][Java et JDK]

-   [Maven][Maven]

-   [Git][Git]

> [AZURE.NOTE] Java, JDK, Maven et Git sont également disponibles via le gestionnaire de package [Chocolatey NuGet][Chocolatey NuGet].

## Création du tableau de bord

Le tableau de bord permet d'afficher des informations de capteur en temps quasi-réel. Dans ce cas, le tableau de bord est une application ASP.NET hébergée dans un site web Azure. Le principal objectif de l'application est de servir de concentrateur [SignalR][SignalR] pour recevoir les informations d'une topologie Storm qui traite des messages.

Le site web contient également un fichier index.html statique, également connecté à SignalR, et utilise D3.js pour créer un graphique des données transmises par la topologie Storm.

> [WACOM.NOTE] Même si vous pouvez également utiliser une version brute de WebSockets au lieu de SignalR, sachez que WebSockets ne fournit pas de mécanisme de mise à l'échelle intégré si vous devez faire évoluer votre site web. SignalR peut être mis à l'échelle en utilisant Azure Service Bus (<http://www.asp.net/signalr/overview/performance/scaleout-with-windows-azure-service-bus>).
>
> Pour obtenir un exemple d'utilisation d'une topologie Storm pour communiquer avec un site web Python en utilisant une version brute de WebSockets, consultez le projet [Storm Tweet Sentiment D3 Visualization][Storm Tweet Sentiment D3 Visualization] (en anglais).

1.  Dans Visual Studio, créez une application C# en utilisant le modèle de projet **Application Web ASP.NET**. Nommez la nouvelle application **Tableau de bord**.

2.  Dans la fenêtre **Nouveau projet ASP.NET**, sélectionnez le modèle d'application **Vide**. Dans la section **Windows Azure**, sélectionnez **Héberger dans le cloud** puis **Site web**. Enfin, cliquez sur **Ok**.

    > [AZURE.NOTE] Si vous y êtes invité, connectez-vous à votre abonnement Azure.

3.  Dans la boîte de dialogue **Configurer le site Windows Azure**, entrez un **Nom de site** et une **Région** pour votre site web, puis cliquez sur **OK**. Le site web Azure hébergeant le tableau de bord est créé.

4.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter | Classe de concentrateur SignalR (v2)**. Nommez la classe **DashHub.cs** puis ajoutez-la au projet. Elle contient le concentrateur SignalR utilisé pour communiquer des données entre HDInsight et la page web du tableau de bord.

    > [AZURE.NOTE] Si vous utilisez Visual Studio 2012, le modèle **SignalR Hub Class (v2)** ne sera pas disponible. Vous pouvez ajouter à la place une **Classe** complète nommée DashHub. Vous devrez aussi installer manuellement le package SignalR en ouvrant **Outils | Gestionnaire de package de bibliothèque | Console du Gestionnaire de package**, puis en exécutant la commande suivante :
    >
    > `install-package Microsoft.AspNet.SignalR`

5.  Remplacez le code de **DashHub.cs** par le suivant.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.AspNet.SignalR;

        namespace dashboard
        {
            public class DashHub : Hub
            {
                public void Send(string message)
                {
                    // Call the broadcastMessage method to update clients.
                    Clients.All.broadcastMessage(message);
                }
            }
        }

6.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter | Classe de démarrage OWIN**. Nommez la nouvelle classe **Startup.cs**.

    > [AZURE.NOTE] Si vous utilisez Visual Studio 2012, le modèle **Classe de démarrage OWIN** ne sera pas disponible. Vous pouvez créer à la place une **Classe** nommée Startup.

7.  Remplacez le contenu de **Startup.cs** par le code suivant.

        using System;
        using System.Threading.Tasks;
        using Microsoft.Owin;
        using Owin;

        [assembly: OwinStartup(typeof(dashboard.Startup))]

        namespace dashboard
        {
            public class Startup
            {
                public void Configuration(IAppBuilder app)
                {
                    // For more information on how to configure your application, visit http://go.microsoft.com/fwlink/?LinkID=316888
                    app.MapSignalR();
                }
            }
        }

8.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis cliquez sur **Ajouter | Page HTML**. Nommez la nouvelle page **index.html**. Cette page contiendra le tableau de bord en temps réel pour ce projet. Elle recevra les informations de DashHub et affichera un graphique en utilisant D3.js.

9.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur la page **index.html** et sélectionnez **Définir comme page de démarrage**.

10. Remplacez le code présent dans le fichier **index.html** par le code suivant.

        <!DOCTYPE html>
        <html xmlns="http://www.w3.org/1999/xhtml">
        <head>
            <title>Dashboard</title>
            <style>

                .x.axis line {
                    shape-rendering: auto;
                }

                .line {
                    fill: none;
                    stroke-width: 1.5px;
                }

            </style>
            <!--Script references. -->
            <!--Reference the jQuery library. -->
            <script src="Scripts/jquery-1.10.2.min.js"></script>
            <!--Reference the SignalR library. -->
            <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
            <!--Reference the autogenerated SignalR hub script. -->
            <script src="signalr/hubs"></script>
            <!--Reference d3.js.-->
            <script src="http://d3js.org/d3.v3.min.js"></script>
        </head>
        <body>
            <script>
                $(function () {
                    //Huge thanks to Mike Bostok for his Path Transitions article - http://bost.ocks.org/mike/path/
                    var n = 243,                                 //number of x coordinates in the graph
                    duration = 750,                          //duration for transitions
                    deviceValue=[0,0,0,0,0,0,0,0,0,0],       //temp holding for each device value
                    now = new Date(Date.now() - duration),   //Now
                    //fill an array of arrays with dummy data to start the chart
                    //each item in the top-level array is a line
                    //each item in the line arrays represents the X coordinate across a graph
                    //The 'value' within each line array represents the Y coordinate for that point
                    data = [                                 
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; }),
                        d3.range(n).map(function () { return { value: 0 }; })
                    ];

                    //Color scale for 10 items
                    var color = d3.scale.category10();
                    //The domain for color (the device IDs)
                    var devices = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"];
                    //This will auto-generate colors for this range of IDs
                    color.domain(devices);

                    //set margins and figure out width/height
                    var margin = {top: 6, right: 0, bottom: 20, left: 40},
                        width = 960 - margin.right,
                        height = 240 - margin.top - margin.bottom;

                    //the time scale for the X axis
                    var x = d3.time.scale()
                        .domain([now - (n - 2) * duration, now - duration])
                        .range([0, width]);

                    //the numerical scale for the Y axis
                    var y = d3.scale.linear()
                        .domain([100, 0])
                        .range([0, height]);

                    //The line, which is really just a
                    //couple functions that we can pass data to
                    //in order to get back x/y coords.
                    var line = d3.svg.line()
                        .interpolate("basis")
                        .x(function (d, i) { return x(now - (n - 1 - i) * duration); })
                        .y(function (d, i) { return y(d.value); });

                    //Find the HTML body element and add a child SVG element
                    var svg = d3.select("body").append("svg")
                        .attr("width", width + margin.left + margin.right)
                        .attr("height", height + margin.top + margin.bottom)
                      .append("g")
                        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

                    //Define a clipping path, because we need to clip
                    //the graph to render only the bits we want to see
                    //as it moves
                    svg.append("defs").append("clipPath")
                        .attr("id", "clip")
                      .append("rect")
                        .attr("width", width)
                        .attr("height", height);

                    //Append the x axis
                    var axis = svg.append("g")
                        .attr("class", "x axis")
                        .attr("transform", "translate(0," + height + ")")
                        .call(x.axis = d3.svg.axis().scale(x).orient("bottom"));

                    //append the y axis
                    var yaxis = svg.append("g")
                        .attr("class", "y axis")
                        .call(y.axis = d3.svg.axis().scale(y).orient("left").ticks(5));

                    //append the clipping path
                    var linegroup = svg.append("g")
                      .attr("clip-path", "url(#clip)");

                    //magic. Select all paths with a class of .line
                    //if they don't exist, make them.
                    //use the points in the line object to define
                    //the paths
                    //set the color to the cooresponding auto-generated coclor
                    var path = linegroup.selectAll(".line")
                      .data(data)
                      .enter().append("path")
                      .attr("class", "line")
                      .attr("d", line)
                      .style("stroke", function (d, i) { return color(i); });

                    //We need to transition the graph after all
                    //lines have been updated. There's no
                    //built-in for this, so this function
                    //does reference counting on end events
                    //for each line, then applies whatever
                    //callback when all are finished.
                    function endall(transition, callback) {
                        var n = 0;
                        transition
                            .each(function () { ++n; })
                            .each("end", function () { if (!--n) callback.apply(this, arguments); });
                    }

                    //wire up the SignalR client and listen for messages
                    var chat = $.connection.dashHub;
                    chat.client.broadcastMessage = function (message) {
                        //parse the JSON data
                        var incomingData = JSON.parse(message);
                        //stuff it in the global array slot for the device ID
                        deviceValue[incomingData.device] = incomingData.temperature;

                    };
                    //start listening
                    $.connection.hub.start();
                    //tick for D3 graphics
                    tick();

                    function tick() {
                        // update the domains
                        now = new Date();
                        x.domain([now - (n - 2) * duration, now - duration]);

                        //push the (presumably) fresh data deviceValue array onto
                        //the arrays that define the lines.
                        for (i = 0; i < 10; i++) {
                            data[i].push({ value: deviceValue[i] });
                            //data[1].push({ value: maxValue });
                        }
                        //slide the x-axis left
                        axis.transition()
                            .duration(duration)
                            .ease("linear")
                            .call(x.axis);

                        //Update the paths based on the updated line data
                        //and slide left
                        path
                            .attr("d", line)
                            .attr("transform", null)
                        .transition()
                            .duration(duration)
                            .ease("linear")
                            .attr("transform", "translate(" + x(now - (n - 1) * duration) + ",0)")
                            .call(endall, tick);

                        // pop the old data point off the front
                        // of the arrays
                        for (var i = 0; i < data.length; i++) {
                            data[i].shift();
                        };
                    };
                 })()
                </script>
            </body>
        </html>

    > [AZURE.NOTE] Une version ultérieure des scripts SignalR peut être installée par le gestionnaire de package. Vérifiez que les références du script ci-dessous correspondent aux versions des fichiers de script du projet (elles seront différentes si vous avez ajouté SignalR en utilisant NuGet plutôt qu'en ajoutant un concentrateur.)

11. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis cliquez sur **Ajouter | Page HTML**. Nommez la nouvelle page **test.html**. Cette page permet de tester DashHub et le tableau de bord en envoyant et en recevant des messages.

12. Remplacez le code présent dans le fichier **test.html** par le code suivant.

        <!DOCTYPE html>
        <html>
        <head>
            <title>Test</title>
            <style type="text/css">
                .container {
                    background-color: #99CCFF;
                    border: thick solid #808080;
                    padding: 20px;
                    margin: 20px;
                }
            </style>
        </head>
        <body>
            <div class="container">
                <input type="text" id="message" />
                <input type="button" id="sendmessage" value="Send" />
                <input type="hidden" id="displayname" />
                <ul id="discussion"></ul>
            </div>
            <!--Script references. -->
            <!--Reference the jQuery library. -->
            <script src="Scripts/jquery-1.10.2.min.js"></script>
            <!--Reference the SignalR library. -->
            <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
            <!--Reference the autogenerated SignalR hub script. -->
            <script src="signalr/hubs"></script>
            <!--Add script to update the page and send messages.-->
            <script type="text/javascript">
                $(function () {
                    // Declare a proxy to reference the hub.
                    var chat = $.connection.dashHub;
                    // Create a function that the hub can call to broadcast messages.
                    chat.client.broadcastMessage = function (message) {
                        // Html encode display the message.
                        var encodedMsg = $('<div />').text(message).html();
                        // Add the message to the page.
                        $('#discussion').append('<li>' + encodedMsg + '</li>');
                    };
                    // Set initial focus to message input box.
                    $('#message').focus();
                    // Start the connection.
                    $.connection.hub.start().done(function () {
                        $('#sendmessage').click(function () {
                            // Call the Send method on the hub.
                            chat.server.send($('#message').val());
                            // Clear text box and reset focus for next comment.
                            $('#message').val('').focus();
                        });
                    });
                });
            </script>
        </body>
        </html>

13. **Enregistrez tout** le projet.

14. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Dashboard** et sélectionnez **Publier**. Sélectionnez le site web créé pour ce projet, puis cliquez sur **Publier**.

15. Une fois le site publié, une page web s'ouvre, contenant une chronologie mobile.

### Test du tableau de bord

1.  Pour vérifier que SignalR fonctionne correctement et que le tableau de bord affiche les lignes de graphique pour les données envoyées vers SignalR, ouvrez une nouvelle fenêtre de navigateur vers la page **test.html** de ce site web. Par exemple, **http://mydashboard.azurewebsites.net/test.html**.

2.  Le tableau de bord doit contenir des données au format JSON, avec les valeurs **ID de périphérique** et **Température**. Par exemple **{"device":0, "temperature":80}**. Entrez certaines valeurs de test sur la page **test.html** en utilisant des ID de périphériques allant de 0 à 9, tandis que le tableau de bord reste ouvert dans l'autre page. Notez que les lignes de chaque ID de périphérique sont représentées dans des couleurs différentes.

## Configuration du concentrateur d'événements

Le concentrateur d'événements permet de recevoir des messages (événements) à partir des capteurs. Procédez comme suit pour créer un nouveau concentrateur d'événements.

1.  ﻿À partir du [portail Azure][portail Azure], sélectionnez **NOUVEAU | Service Bus | Concentrateur d'événements | Création personnalisée**.

2.  Dans la boîte de dialogue **Ajouter un nouveau concentrateur d'événements**, entrez un **Nom de concentrateur d'événements**, sélectionnez la **Région** dans laquelle créer le concentrateur, puis créez un espace de noms ou sélectionnez-en un existant. Enfin, cliquez sur la **Flèche**.

3.  Dans la boîte de dialogue **Configurer le concentrateur d'événements**, entrez les valeurs pour **Nombre de partitions** et **Conservation des messages**. Pour cet exemple, entrez 10 pour le nombre de partitions et 1 pour la conservation des messages.

4.  Une fois le concentrateur d'événements créé, sélectionnez l'espace de noms puis **Concentrateurs d'événements**. Enfin, sélectionnez le concentrateur d'événements créé précédemment.

5.  Sélectionnez **Configurer**, puis créez deux nouvelles stratégies d'accès en utilisant les informations suivantes.

    | Nom           | Autorisations |
    |---------------|---------------|
    | périphériques | Envoyer       |
    | storm         | Écouter       |

    Après la création des autorisations, sélectionnez l'icône **Enregistrer** au bas de la page. Les stratégies d'accès partagées sont créées. Elles sont utilisées pour envoyer et lire des messages à partir de ce concentrateur.

6.  Après l'enregistrement des stratégies, utilisez le **Générateur de clés d'accès partagé** en bas de la page pour récupérer les clés des stratégies **périphériques** et **storm**. Enregistrez-les, car elles serviront plus tard.

### Envoi de messages vers le concentrateur d'événements

Comme il n'y a pas de jeu de capteurs standard et simple d'utilisation disponible pour tout le monde, une application .NET est utilisée pour générer des nombres aléatoires. L'application .NET créée en procédant comme suit va générer des événements pour 10 périphériques à chaque seconde, jusqu'à ce que vous l'arrêtiez en appuyant sur une touche.

1.  Dans Visual Studio, créez un projet **Windows Desktop** et sélectionnez le modèle de projet **Application console**. Nommez le projet **SendEvents**, puis cliquez sur **OK**.

2.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **SendEvents**, puis cliquez sur **Gérer les packages NuGet**.

3.  Dans **Gérer les packages NuGet**, recherchez et installez les packages suivants.

    -   **Microsoft Azure Service Bus**
    -   **JSON.Net**

    Après l'installation des packages, **fermez** le gestionnaire de package.

4.  Remplacez le contenu de **Program.cs** par le code suivant.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using Newtonsoft.Json;
        using Microsoft.ServiceBus;
        using System.Threading;

        namespace SendEvents
        {
            class Program
            {

                static int numberOfDevices = 10;
                static string eventHubName = "temperature";
                static string eventHubNamespace = "namespace";
                static string sharedAccessPolicyName = "devices";
                static string sharedAccessPolicyKey = "key for devices policy";

                static void Main(string[] args)
                {
                    var settings = new MessagingFactorySettings()
                    {
                        TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(sharedAccessPolicyName, sharedAccessPolicyKey),
                        TransportType = TransportType.Amqp
                    };
                    var factory = MessagingFactory.Create(
                         ServiceBusEnvironment.CreateServiceUri("sb", eventHubNamespace, ""), settings);

                    EventHubClient client = factory.CreateEventHubClient(eventHubName);

                    try
                    {

                        List<Task> tasks = new List<Task>();
                        // Send messages to Event Hub
                        Console.WriteLine("Sending messages to Event Hub {0}", client.Path);
                        Random random = new Random();
                        //for (int i = 0; i < numberOfMessages; ++i)
                        while(!Console.KeyAvailable)
                        {
                            // One event per device
                            for(int devices = 0; devices < numberOfDevices; devices++)
                            {
                                // Create the device/temperature metric
                                Event info = new Event() { 
                                    TimeStamp = DateTime.UtcNow,
                                    DeviceId = random.Next(numberOfDevices),
                                    Temperature = random.Next(100)
                                };
                                // Serialize to JSON
                                var serializedString = JsonConvert.SerializeObject(info);
                                Console.WriteLine(serializedString);
                                EventData data = new EventData(Encoding.UTF8.GetBytes(serializedString))
                                {
                                    PartitionKey = info.DeviceId.ToString()
                                };

                                // Send the metric to Event Hub
                                tasks.Add(client.SendAsync(data));
                            }
                            // Sleep a second
                            Thread.Sleep(1000);
                        };

                        Task.WaitAll(tasks.ToArray());
                    }
                    catch (Exception exp)
                    {
                        Console.WriteLine("Error on send: " + exp.Message);
                    }

                }
            }
        }

    Pour le moment, vous allez recevoir un avertissement relatif à des lignes référençant la classe Event. Ignorez ceci pour le moment.

5.  Dans le fichier **Program.cs**, définissez la valeur des variables suivantes au début du fichier sur les valeurs correspondantes récupérées à partir de votre concentrateur d'événements dans le portail de gestion Azure.

    | Définissez ceci...     | Sur cela...                                                                     |
    |------------------------|---------------------------------------------------------------------------------|
    | eventHubName           | Nom de votre concentrateur d'événements. Par exemple, **température**.          |
    | eventHubNamespace      | Espace de nom de votre concentrateur d'événements. Par exemple, **sensors-ns**. |
    | sharedAccessPolicyName | Stratégie créée avec l'accès d'envoi. Par exemple, **périphériques**.           |
    | sharedAccessPolicyKey  | Clé de la stratégie avec l'accès d'envoi.                                       |

6.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **SendEvents**, puis sur **Ajouter | Classe**. Nommez la nouvelle classe **Event.cs**. Elle décrit le message envoyé vers le concentrateur d'événements.

7.  Remplacez le contenu de **Event.cs** par le code suivant.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Runtime.Serialization;
        using System.Text;
        using System.Threading.Tasks;

        namespace SendEvents
        {
            [DataContract]
            public class Event
            {
                [DataMember]
                public DateTime TimeStamp { get; set; }
                [DataMember]
                public int DeviceId { get; set; }
                [DataMember]
                public int Temperature { get; set; }
            }
        }

    Cette classe décrit les données que nous envoyons : l'horodatage, l'ID du périphérique et la valeur de la température.

8.  **Enregistrez tout**, puis exécutez l'application pour remplir le concentrateur d'événements avec des messages.

## Création d'un réseau Azure Virtual Network

Pour exécuter la topologie sur le cluster Storm afin de communiquer directement avec HBase, vous devez configurer les serveurs dans un réseau Azure Virtual Network.

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  Dans le bas de la page, cliquez sur **+NOUVEAU**, **Services de réseau**, **Réseau virtuel**, puis sur **Création rapide**.

3.  Tapez ou sélectionnez les valeurs suivantes :

    -   **Nom** : nom de votre réseau virtuel.
    -   **Espace d'adressage** : choisissez un espace d'adressage pour le réseau virtuel qui est suffisant grand pour fournir des adresses pour tous les nœuds du cluster. Si ce n'est pas le cas, l'approvisionnement échoue.
    -   **Nombre maximal de machines virtuelles** : choisissez l'un des nombres maximum de machines virtuelles.
    -   **Emplacement** : l'emplacement doit être le même que celui du cluster HBase que vous allez créer.
    -   **Serveur DNS** : étant donné que cet article utilise le serveur DNS interne fourni par Azure, vous pouvez choisir **Aucun**. Des procédures de configuration de réseau plus avancées avec des serveurs DNS personnalisés sont également prises en charge. Pour des instructions détaillées, consultez la page <http://msdn.microsoft.com/library/azure/jj156088.aspx>.

4.  Cliquez sur **Créer un réseau virtuel**. Le nom du nouveau réseau virtuel apparaît dans la liste. Attendez que la valeur **Créé** apparaisse dans la colonne Statut.

5.  Dans le volet principal, cliquez sur le réseau virtuel que vous venez de créer.

6.  En haut de la page, cliquez sur **TABLEAU DE BORD**.

7.  Sous **aperçu rapide**, prenez note de l'**ID RÉSEAU VIRTUEL**. Vous en aurez besoin lors de l'approvisionnement des clusters Storm et HBase.

8.  En haut de la page, cliquez sur **CONFIGURER**.

9.  En bas de la page, le nom du sous-réseau par défaut est **Sous-réseau-1**. Utilisez le bouton **Ajouter un sous-réseau** pour ajouter **Subnet-2**. Ces sous-réseaux vont héberger les clusters Storm et HBase.

    > [WACOM.NOTE] Dans cet article, nous allons utiliser des clusters avec un seul nœud. Si vous créez des clusters multi-nœuds, vous devez vérifier la valeur de **CIDR (NOMBRE D'ADRESSES)** pour le sous-réseau qui sera utilisé pour le cluster. Le nombre d'adresses doit être supérieur au nombre de nœuds de travail plus 7 (Gateway : 2, Headnode : 2, Zookeeper : 3). Par exemple, si vous avez besoin d'un cluster HBase à 10 nœuds, le nombre d'adresses pour le sous-réseau doit être supérieur à 17 (10+7). Si ce n'est pas le cas, le déploiement échoue.
    >
    > Il est vivement recommandé de désigner un seul sous-réseau pour un cluster.

10. Cliquez sur **Enregistrer** en bas de la page.

## Création d'un cluster Storm HDInsight

1.  Connectez-vous au [portail de gestion Azure][azureportal]

2.  Cliquez sur **HDInsight** sur la gauche, puis sur **+NOUVEAU** dans le coin inférieur gauche de la page.

3.  Cliquez sur l'icône HDInsight dans la seconde colonne, puis sélectionnez **Personnaliser**.

4.  Sur la page **Détails du cluster**, entrez le nom du nouveau cluster, puis sélectionnez **Storm** pour le **Type de cluster**. Sélectionnez la flèche pour continuer.

5.  Entrez 1 pour la valeur de **Nœuds de données** à utiliser pour ce cluster. Pour **Région/Réseau virtuel**, sélectionnez le réseau Azure Virtual Network créé précédemment. Pour **Sous-réseaux du réseau virtuel**, sélectionnez **Subnet-2**.

    > [WACOM.NOTE] Pour réduire les coûts pour le cluster utilisé pour cet article, réduisez la **Taille du cluster** sur 1, puis supprimez le cluster après avoir fini de l'utiliser.

6.  Entrez le **Nom d'utilisateur** d'administrateur et un **Mot de passe**, puis sélectionnez la flèche pour continuer.

7.  Pour **Compte de stockage**, sélectionnez **Créer un nouveau stockage** ou sélectionnez un compte de stockage existant. Sélectionnez ou entrez le **Nom de compte** et le **Conteneur par défaut** à utiliser. Cliquez sur l'icône en forme de coche en bas à gauche pour créer le cluster Storm.

## Création d'un cluster HDInsight HBase

1.  Connectez-vous au [portail de gestion Azure][azureportal]

2.  Cliquez sur **HDInsight** sur la gauche, puis sur **+NOUVEAU** dans le coin inférieur gauche de la page.

3.  Cliquez sur l'icône HDInsight dans la seconde colonne, puis sélectionnez **Personnaliser**.

4.  Sur la page **Détails du cluster**, entrez le nom du nouveau cluster, puis sélectionnez **HBase** pour le **Type de cluster**. Sélectionnez la flèche pour continuer.

5.  Entrez 1 pour la valeur de **Nœuds de données** à utiliser pour ce cluster. Pour **Région/Réseau virtuel**, sélectionnez le réseau Azure Virtual Network créé précédemment. Pour **Sous-réseaux du réseau virtuel**, sélectionnez **Subnet-1**.

    > [WACOM.NOTE] Pour réduire les coûts pour le cluster utilisé pour cet article, réduisez la **Taille du cluster** sur 1, puis supprimez le cluster après avoir fini de l'utiliser.

6.  Entrez le **Nom d'utilisateur** d'administrateur et un **Mot de passe**, puis sélectionnez la flèche pour continuer.

7.  Pour **Compte de stockage**, sélectionnez **Créer un nouveau stockage** ou sélectionnez un compte de stockage existant. Sélectionnez ou entrez le **Nom de compte** et le **Conteneur par défaut** à utiliser. Cliquez sur l'icône en forme de coche en bas à gauche pour créer le cluster Storm.

    > [WACOM.NOTE] Nous vous conseillons d'utiliser un autre conteneur que celui utilisé pour le cluster Storm.

### Activation du Bureau à distance

Pour ce didacticiel, nous devons utiliser le Bureau à distance pour accéder aux clusters Storm et HBase. Procédez comme suit pour activer le Bureau à distance sur ces deux clusters.

1.  Connectez-vous au [portail de gestion Azure][azureportal].

2.  Sur la gauche, sélectionnez **HDInsight**, puis votre cluster Storm à partir de la liste. Enfin, sélectionnez **Configurer** en haut de la page.

3.  En bas de la page, sélectionnez **Activer Distant**. Lorsque vous y êtes invité, saisissez un nom d'utilisateur, un mot de passe et une date d'expiration pour l'accès au Bureau à distance. Cliquez sur la coche pour activer le Bureau à distance.

Une fois le Bureau à distance activé, vous pouvez sélectionner **Connecter** en bas de la page. Suivez les instructions de l’invite pour vous connecter au cluster.

### Découverte du suffixe DNS de HBase

Pour écrire sur HBase à partir du cluster Storm, vous devez utiliser le nom de domaine complet (FQDN) du cluster HBase. Procédez comme suit pour trouver cette information.

1.  Connectez-vous au cluster HBase en utilisant le Bureau à distance.

2.  Une fois connecté au cluster, ouvrez la ligne de commande Hadoop et exécutez la commande **ipconfig** pour obtenir le suffixe DNS. Le **suffixe DNS de connexion** contient la valeur de suffixe. Par exemple, **mycluster.b4.internal.cloudapp.net**. Enregistrez ces informations.

## Développement de la topologie Storm

> [WACOM.NOTE] La procédure de cette section doit être suivie sur votre environnement de développement local.

### Téléchargement et développement de dépendances externes

Plusieurs des dépendances utilisées dans ce projet doivent être téléchargées et développées individuellement, puis installées dans le référentiel Maven local de votre environnement de développement. Dans cette section, vous allez télécharger et installer :

-   le spout du concentrateur d'événements lisant les messages à partir du concentrateur d'événements ;

-   le Kit de développement logiciel (SDK) du client Java SignalR.

#### Téléchargement et développement du spout du concentrateur d'événements

Pour recevoir les données du concentrateur d'événements, nous allons utiliser la commande **eventhubs-storm-spout**.

1.  Utilisez le Bureau à distance pour vous connecter à votre cluster Storm, puis copiez le fichier **%STORM\_HOME%\\examples\\eventhubspout\\eventhubs-storm-spout-0.9-jar-with-dependencies.jar** sur votre environnement de développement local. Il contient la commande **events-storm-spout**.

2.  Utilisez la commande suivante pour installer le package dans le magasin Maven local. Ceci nous permettra de l'ajouter facilement en tant que référence dans le projet Storm à l'étape suivante.

        mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

#### Téléchargement et développement du client SignalR

Pour envoyer des messages vers le tableau de bord ASP.NET, utilisez le [Kit de développement logiciel (SDK) du client SignalR pour Java][Kit de développement logiciel (SDK) du client SignalR pour Java].

1.  Ouvrez une invite de commandes.

2.  Sélectionnez les répertoires où vous voulez télécharger et archiver le projet du Kit de développement logiciel (SDK) du client SignalR.

3.  Utilisez la commande suivante pour télécharger le projet à partir de GitHub.

    git clone https://github.com/SignalR/java-client

4.  Modifiez les répertoires dans le répertoire **java-client\\signalr-client-sdk** et compilez le projet dans un fichier JAR en utilisant les commandes suivantes.

        cd java-client\signalr-client-sdk
        mvn package

    > [WACOM.NOTE] Si vous recevez une erreur indiquant que la dépendance **gson** ne peut pas être téléchargée, supprimez les lignes suivantes du fichier **java-client\\signalr-client-sdk\\pom.xml**.
    >
    > ``` <repositories>
    > <repository>
    > <id>central</id>
    > <name>Central</name>
    > <url>http://maven.eclipse.org/build</url>
    > </repository>
    > </repositories>
    > ```
    >
    > Une fois ces lignes supprimées, Maven va extraire le fichier du référentiel central (le comportement par défaut). Pour forcer Maven à retenter le référentiel, utilisez la commande `-U`. Par exemple, `mvn package -U`

5.  Utilisez la commande suivante pour installer le package dans le magasin Maven local. Ceci nous permettra de l'ajouter facilement en tant que référence dans le projet Storm à l'étape suivante.

        mvn install:install-file -Dfile=target\signalr-client-sdk-1.0.jar -DgroupId=microsoft.aspnet.signalr -DartifactId=signalr-client-sdk -Dversion=1.0 -Dpackaging=jar

### Structure du projet de topologie Storm

Maintenant que vous avez installé le spout du concentrateur d'événements et du client SignalR dans le référentiel local, utilisez Maven pour créer la structure du projet de topologie Storm.

1.  Ouvrez une invite de commandes, un interpréteur ou une session terminale ou ce que vous avez l'habitude d'utiliser pour taper des commandes sur votre système.

2.  Définissez les répertoires sur l'emplacement sur lequel vous souhaitez créer ce projet. Par exemple, si vous avez un répertoire sur lequel vous stockez tous vos projets de code.

3.  Utilisez la commande Maven suivante pour créer une structure de base pour votre application.

        mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.examples -DartifactId=TemperatureMonitor -DinteractiveMode=false

    Cette commande va...

    -   Créer un nouveau répertoire en utilisant l'*artifactId* spécifié. Dans ce cas, **Température**.
    -   Créer un fichier **pom.xml** contenant les informations Maven pour ce projet.
    -   Créer une structure de répertoires **src** contenant certains codes et tests de base.

### Ajout de dépendances et de plug-ins

Ensuite, modifiez le fichier **pom.xml** pour référencer les dépendances de ce projet, ainsi que les plug-ins Maven à utiliser lors du développement et de la mise en package.

1.  ﻿À l'aide d'un éditeur de texte, ouvrez le fichier **pom.xml** et ajoutez l'élément suivant dans la section **\<dependencies\>**. Vous pouvez les ajouter à la fin de la section, après la dépendance pour junit.

        <dependency>
          <groupId>org.apache.storm</groupId>
          <artifactId>storm-core</artifactId>
          <version>0.9.2-incubating</version>
          <!-- keep storm out of the jar-with-dependencies -->
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>microsoft.aspnet.signalr</groupId>
          <artifactId>signalr-client-sdk</artifactId>
          <version>1.0</version>
        </dependency>
        <dependency>
          <groupId>com.microsoft.eventhubs</groupId>
          <artifactId>eventhubs-storm-spout</artifactId>
          <version>0.9</version>
        </dependency>
        <dependency>
          <groupId>com.google.code.gson</groupId>
          <artifactId>gson</artifactId>
          <version>2.2.2</version>
        </dependency>
        <dependency>
          <groupId>com.github.ptgoetz</groupId>
          <artifactId>storm-hbase</artifactId>
          <version>0.1.2</version>
        </dependency>
        <dependency>
          <groupId>com.netflix.curator</groupId>
          <artifactId>curator-framework</artifactId>
          <version>1.3.3</version>
          <exclusions>
            <exclusion>
              <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
              </exclusion>
            <exclusion>
              <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
          </exclusions>
          <scope>provided</scope>
        </dependency>

    Ceci ajoute des dépendances pour...

    -   eventhubs-storm-spout : le spout du concentrateur d'événements
    -   signalr-client-sdk : le client SignalR
    -   gson : il s'agit d'une dépendance du client SignalR, qui sera également utilisée pour créer le format JSON lors de l'écriture de SignalR
    -   storm-core : fournit la fonctionnalité principale pour les applications Storm
    -   slf4j : fournit des capacités de journalisation et est utilisée par eventhubs-storm-spout
    -   curator-framework : utilisée par eventhubs-storm-spout
    -   storm-core : classes principales pour Storm
    -   storm-hbase : classes autorisant l'écriture sur HBase

    > [WACOM.NOTE] Notez que certaines dépendances sont dotées de l'étendue **fournie**, ce qui indique que ces dépendances doivent être téléchargées à partir du référentiel Maven et utilisées pour développer et tester l'application localement, mais qu'elles sont également disponibles dans votre environnement d'exécution et que vous n'avez ni besoin de les compiler, ni de les inclure dans le JAR créé par ce projet.

2.  ﻿À la fin du fichier **pom.xml**, juste avant l'entrée **\</project\>**, ajoutez le code suivant.

          <build>
            <plugins>
              <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.3.2</version>
                <configuration>
                  <source>1.7</source>
                  <target>1.7</target>
                </configuration>
              </plugin>
              <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                  <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                  </transformers>
                </configuration>
                <executions>
                  <execution>
                    <phase>package</phase>
                    <goals>
                      <goal>shade</goal>
                    </goals>
                  </execution>
                </executions>
              </plugin>
              <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>1.2.1</version>
                <executions>
                  <execution>
                  <goals>
                    <goal>exec</goal>
                  </goals>
                  </execution>
                </executions>
                <configuration>
                  <executable>java</executable>
                  <includeProjectDependencies>true</includeProjectDependencies>
                  <includePluginDependencies>false</includePluginDependencies>
                  <classpathScope>compile</classpathScope>
                  <mainClass>${storm.topology}</mainClass>
                </configuration>
              </plugin>
            </plugins>
            <resources>
              <resource>
                <directory>${basedir}/conf</directory>
                <filtering>false</filtering>
                <includes>
                  <include>Config.properties</include>
                  <include>hbase-site.xml</include>
                </includes>
              </resource>
            </resources>
          </build>

    Ceci indique à Maven de procéder comme suit lors du développement du projet :

    -   Inclure le fichier de ressource **/conf/Config.properties**. Ce fichier sera créé ultérieurement, mais il contiendra les informations de configuration pour la connexion au concentrateur d'événements Azure.
    -   Inclure le fichier de ressource **/conf/hbase-site.xml**. Ce fichier sera créé ultérieurement, mais il contiendra les informations de connexion à HBase.
    -   Utiliser la commande **maven-compiler-plugin** pour compiler l'application.
    -   Utiliser la commande **maven-shade-plugin** pour développer un uberjar ou un fat jar, qui contiendra le projet et les dépendances requises.
    -   Utiliser la commande **exec-maven-plugin**, qui vous permet d'exécuter l'application localement, sans un cluster Hadoop.

### Ajout de fichiers de configuration

**eventhubs-storm-spout** lit les informations de configuration à partir d'un fichier **Config.properties**. Ceci vous indique à quel concentrateur d'événements vous connecter. Même si vous pouvez indiquer un fichier de configuration lors du démarrage de la topologie sur un cluster, le fait d'en inclure un dans le projet vous permet de bénéficier d'une configuration par défaut connue.

1.  Dans le répertoire **Température**, créez un répertoire appelé **conf**.

2.  Dans le répertoire **conf**, créez deux fichiers :

    -   **Config.properties**, qui contient les paramètres du concentrateur d'événements ;
    -   **hbase-site.xml**, qui contient les paramètres pour la connexion à HBase.

3.  Utilisez les données suivantes comme contenu du fichier **Config.properties**.

        eventhubspout.username = storm

        eventhubspout.password = <the key of the 'storm' policy>

        eventhubspout.namespace = <the event hub namespace>

        eventhubspout.entitypath = temperature

        eventhubspout.partitions.count = <the number of partitions for the event hub>

        # if not provided, will use storm's zookeeper settings
        # zookeeper.connectionstring=localhost:2181

        eventhubspout.checkpoint.interval = 10

        eventhub.receiver.credits = 1024

    Remplacez le **mot de passe** par la clé de la stratégie **storm** créée précédemment dans le concentrateur d'événements. Remplacez l'**espace de noms** par l'espace de noms de votre concentrateur d'événements.

4.  Utilisez les données suivantes comme contenu du fichier **hbase-site.xml**.

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
         * Copyright 2010 The Apache Software Foundation
         *
         * Licensed to the Apache Software Foundation (ASF) under one
         * or more contributor license agreements.  See the NOTICE file
         * distributed with this work for additional information
         * regarding copyright ownership.  The ASF licenses this file
         * to you under the Apache License, Version 2.0 (the
         * "License"); you may not use this file except in compliance
         * with the License.  You may obtain a copy of the License at
         *
         *     http://www.apache.org/licenses/LICENSE-2.0
         *
         * Unless required by applicable law or agreed to in writing, software
         * distributed under the License is distributed on an "AS IS" BASIS,
         * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
         * See the License for the specific language governing permissions and
         * limitations under the License.
         */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

5.  Dans le fichier **hbase-site.xml**, remplacez la valeur de **suffixe** des entrées zookeeper par le suffixe DNS récupéré précédemment pour HBase. Par exemple, **zookeeper0.mycluster.b4.internal.cloudapp.net, zookeeper1.mycluster.b4.internal.cloudapp.net, zookeeper2.mycluster.b4.internal.cloudapp.net**.

6.  Enregistrez les fichiers.

### Ajout d'aides

Pour prendre en charge la sérialisation vers et depuis JSON, vous aurez besoin de quelques classes d'assistance pour définir la structure d'objet.

1.  Dans le répertoire **\\temperaturemonitor\\src\\main\\java\\com\\microsoft\\examples**, créez un répertoire nommé **aides**.

2.  Dans le répertoire **aides**, créez deux fichiers :

    -   **EventHubMessage.java**, qui définit le format des messages du concentrateur d'événements ;

    -   **SignalRMessage.java**, qui définit le format des messages envoyés à SignalR.

3.  Utilisez les données suivantes comme contenu pour le fichier **EventHubMessage.java**.

        package com.microsoft.examples;

        public class EventHubMessage {
          String TimeStamp;
          int DeviceId;
          int Temperature;
        }

4.  Utilisez les données suivantes comme contenu pour le fichier **SignalRMessage.java**.

        package com.microsoft.examples;

        public class SignalRMessage {
          int device;
          int temperature;
        }

5.  Enregistrez et fermez ces fichiers.

### Ajout de bolts

Les bolts s'occupent du traitement principal d'une topologie. Dans cette topologie, il y a trois bolts, bien que l'un d'eux soit un hbase-bolt, qui sera téléchargé automatiquement une fois le projet développé.

1.  Dans le répertoire **\\temperaturemonitor\\src\\main\\java\\com\\microsoft\\examples**, créez un répertoire nommé **bolts**.

2.  Dans le répertoire **bolts**, créez deux fichiers :

    -   **ParserBolt.java**, qui analyse le message entrant provenant du concentrateur d'événements pour aller vers les champs individuels, avant l'émission des deux flux ;
    -   **DashboardBolt.java**, qui journalise les informations dans le tableau de bord web via SignalR.

3.  Utilisez les données suivantes comme contenu pour le fichier **ParserBolt.java**.

        package com.microsoft.examples;

        import backtype.storm.topology.base.BaseBasicBolt;
        import backtype.storm.topology.BasicOutputCollector;
        import backtype.storm.topology.OutputFieldsDeclarer;
        import backtype.storm.tuple.Tuple;
        import backtype.storm.tuple.Fields;
        import backtype.storm.tuple.Values;

        import com.google.gson.Gson;
        import com.google.gson.GsonBuilder;

        public class ParserBolt extends BaseBasicBolt {

          //Declare output fields & streams
          //hbasestream is all fields, and goes to hbase
          //dashstream is just the device and temperature, and goes to the dashboard
          @Override
          public void declareOutputFields(OutputFieldsDeclarer declarer) {
            declarer.declareStream("hbasestream", new Fields("timestamp", "deviceid", "temperature"));
            declarer.declareStream("dashstream", new Fields("deviceid", "temperature"));
          }

          //Process tuples
          @Override
          public void execute(Tuple tuple, BasicOutputCollector collector) {
            Gson gson = new Gson();
            //Should only be one tuple, which is the JSON message from the spout
            String value = tuple.getString(0);

            //Convert it from JSON to an object
            EventHubMessage evMessage = gson.fromJson(value, EventHubMessage.class);

            //Pull out the values and emit as a stream
            String timestamp = evMessage.TimeStamp;
            int deviceid = evMessage.DeviceId;
            int temperature = evMessage.Temperature;
            collector.emit("hbasestream", new Values(timestamp, deviceid, temperature));
            collector.emit("dashstream", new Values(deviceid, temperature));
          }
        }

4.  Utilisez les données suivantes comme contenu pour le fichier **DashboardBolt.java**.

        package com.microsoft.examples;

        import backtype.storm.topology.BasicOutputCollector;
        import backtype.storm.topology.OutputFieldsDeclarer;
        import backtype.storm.topology.base.BaseBasicBolt;
        import backtype.storm.tuple.Tuple;
        import backtype.storm.task.TopologyContext;
        import backtype.storm.Config;
        import backtype.storm.Constants;

        import microsoft.aspnet.signalr.client.Action;
        import microsoft.aspnet.signalr.client.ErrorCallback;
        import microsoft.aspnet.signalr.client.LogLevel;
        import microsoft.aspnet.signalr.client.Logger;
        import microsoft.aspnet.signalr.client.MessageReceivedHandler;
        import microsoft.aspnet.signalr.client.hubs.HubConnection;
        import microsoft.aspnet.signalr.client.hubs.HubProxy;

        import com.google.gson.Gson;
        import com.google.gson.GsonBuilder;

        import java.util.Map;

        public class DashboardBolt extends BaseBasicBolt {
          //Connection and proxy for SignalR hub
          private HubConnection conn;
          private HubProxy proxy;

          //Declare output fields
          @Override
          public void declareOutputFields(OutputFieldsDeclarer declarer) {
            //no stream output - we talk directly to SignalR
          }

          @Override
          public void prepare(Map config, TopologyContext context) {

            // Connect to the DashHub SignalR server
            conn = new HubConnection("http://dashboard.azurewebsites.net/");
            // Create the hub proxy
            proxy = conn.createHubProxy("DashHub");
            // Subscribe to the error event
            conn.error(new ErrorCallback() {
              @Override
              public void onError(Throwable error) {
                error.printStackTrace();
              }
            });
            // Subscribe to the connected event
            conn.connected(new Runnable() {
              @Override
              public void run() {
                System.out.println("CONNECTED");
              }
            });
            // Subscribe to the closed event
            conn.closed(new Runnable() {
              @Override
              public void run() {
                System.out.println("DISCONNECTED");
              }
            });
            // Start the connection
            conn.start()
              .done(new Action<Void>() {
                @Override
                public void run(Void obj) throws Exception {
                  System.out.println("Done Connecting!");
                }
            });
          }

          //Process tuples
          @Override
          public void execute(Tuple tuple, BasicOutputCollector collector) {
            Gson gson = new Gson();
            try {
              //Get the deviceid and temperature by field name
              int deviceid = tuple.getIntegerByField("deviceid");
              int temperature = tuple.getIntegerByField("temperature");
              //Construct the SignalR message
              SignalRMessage srMessage = new SignalRMessage();
              srMessage.device = deviceid;
              srMessage.temperature = temperature;
              // send it as JSON
              proxy.invoke("send", gson.toJson(srMessage));
            } catch (Exception e) {
               // LOG.error("Bolt execute error: {}", e);
               collector.reportError(e);
            }
          }
        }

    Remplacez `http://yourwebsiteaddress` par l'adresse du site web Azure sur lequel vous avez publié le tableau de bord. Par exemple, http://mydashboard.azurewebsites.net.

5.  Enregistrez et fermez les fichiers.

### Définition de la topologie

La topologie permet de décrire la façon dont les données sont diffusées entre les spouts et les bolts dans une topologie, ainsi que le degré de parallélisme entre la topologie et ses composants.

1.  Dans le répertoire **\\temperaturemonitor\\src\\main\\java\\com\\microsoft\\examples**, créez un fichier nommé **Temperature.java**.

2.  Ouvrez le fichier **Temperature.java** et remplacez son contenu par les données suivantes.

        package com.microsoft.examples;

        import backtype.storm.Config;
        import backtype.storm.LocalCluster;
        import backtype.storm.StormSubmitter;
        import backtype.storm.generated.StormTopology;
        import backtype.storm.topology.TopologyBuilder;
        import backtype.storm.tuple.Fields;
        import com.microsoft.eventhubs.spout.EventHubSpout;
        import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

        import java.io.FileReader;
        import java.util.Properties;

        //hbase
        import org.apache.storm.hbase.bolt.mapper.SimpleHBaseMapper;
        import org.apache.storm.hbase.bolt.HBaseBolt;
        import java.util.Map;
        import java.util.HashMap;
        import backtype.storm.tuple.Fields;

        public class Temperature
        {
          protected EventHubSpoutConfig spoutConfig;
          protected int numWorkers;

          // Reads the configuration information for the Event Hub spout
          protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if(args.length > 1) {
              properties.load(new FileReader(args[1]));
            }
            else {
              properties.load(Temperature.class.getClassLoader().getResourceAsStream(
                "Config.properties"));
            }

            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties.getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties.getProperty("zookeeper.connectionstring");
            int partitionCount = Integer.parseInt(properties.getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties.getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties.getProperty("eventhub.receiver.credits"));
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: " + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
            spoutConfig = new EventHubSpoutConfig(username, password,
              namespaceName, entityPath, partitionCount, zkEndpointAddress,
              checkpointIntervalInSeconds, receiverCredits);

            //set the number of workers to be the same as partition number.
            //the idea is to have a spout and a partial count bolt co-exist in one
            //worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();

            if(args.length > 0) {
              //set topology name so that sample Trident topology can use it as stream name.
              spoutConfig.setTopologyName(args[0]);
            }
          }

          // Create the spout using the configuration
          protected EventHubSpout createEventHubSpout() {
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            return eventHubSpout;
          }

          // Build the topology
          protected StormTopology buildTopology(EventHubSpout eventHubSpout, SimpleHBaseMapper mapper) {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
            // Name the spout 'EventHubsSpout', and set it to create
            // as many as we have partition counts in the config file
            topologyBuilder.setSpout("EventHub", eventHubSpout, spoutConfig.getPartitionCount())
              .setNumTasks(spoutConfig.getPartitionCount());
            // Create the parser bolt, which subscribes to the stream from EventHub
            topologyBuilder.setBolt("Parser", new ParserBolt(), spoutConfig.getPartitionCount())
              .localOrShuffleGrouping("EventHub").setNumTasks(spoutConfig.getPartitionCount());
            // Create the dashboard bolt, which subscribes to the stream from Parser
            topologyBuilder.setBolt("Dashboard", new DashboardBolt(), spoutConfig.getPartitionCount())
              .fieldsGrouping("Parser", "dashstream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
            // Create the HBase bolt, which subscribes to the stream from Parser
            // WARNING - uncomment the following two lines when deploying
            // leave commented when testing locally
            // topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
            //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
          }

          protected void submitTopology(String[] args, StormTopology topology, Config config) throws Exception {
            // Config config = new Config();
            config.setDebug(false);

            //Enable metrics
            config.registerMetricsConsumer(backtype.storm.metric.LoggingMetricsConsumer.class, 1);

            // Is this running locally, or on an HDInsight cluster?
            if (args != null && args.length > 0) {
              config.setNumWorkers(numWorkers);
              StormSubmitter.submitTopology(args[0], config, topology);
            } else {
              config.setMaxTaskParallelism(2);

              LocalCluster localCluster = new LocalCluster();
              localCluster.submitTopology("test", config, topology);

              Thread.sleep(5000000);

              localCluster.shutdown();
            }
          }

          // Loads the configuration, creates the spout, builds the topology,
          // and then submits it
          protected void runScenario(String[] args) throws Exception{
            readEHConfig(args);
            Config config = new Config();

            //hbase configuration
            Map<String, Object> hbConf = new HashMap<String, Object>();
            if(args.length > 0) {
              hbConf.put("hbase.rootdir", args[0]);
            }
            config.put("hbase.conf", hbConf);
            SimpleHBaseMapper mapper = new SimpleHBaseMapper()
                  .withRowKeyField("deviceid")
                  .withColumnFields(new Fields("timestamp", "temperature"))
                  .withColumnFamily("cf");

            EventHubSpout eventHubSpout = createEventHubSpout();
            StormTopology topology = buildTopology(eventHubSpout, mapper);
            submitTopology(args, topology, config);
          }

          public static void main(String[] args) throws Exception {
            Temperature scenario = new Temperature();
            scenario.runScenario(args);
          }
        }

    > [AZURE.NOTE] Notez que les lignes de **HBaseBolt** sont commentées. Ceci est dû au fait que la prochaine étape consiste à exécuter la topologie en local. Comme HBaseBolt s'adresse directement à HBase, des erreurs surviendront s'il est activé, sauf si vous avez configuré un réseau virtuel avec un serveur DNS et également connecté votre ordinateur local au réseau virtuel.

### Test local de la topologie

Pour compiler et tester le fichier sur votre ordinateur de développement, procédez comme suit :

1.  Démarrez l'application .NET **SendEvent** pour commencer l'envoi des événements, pour avoir quelque chose à lire à partir du concentrateur d'événements.

2.  Ouvrez un navigateur web pour accéder au tableau de bord web que vous avez déployé précédemment dans un site web Azure. Ceci va vous permettre de voir une représentation graphique des valeurs durant leur diffusion dans la topologie.

3.  Démarrez la topologie localement en utilisant la commande suivante.

    mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

    Ceci démarre la topologie, la lecture des fichiers à partir du concentrateur d'événements et leur envoi vers le tableau de bord exécuté dans Sites Web Azure. Vous devriez voir des lignes apparaître dans le tableau de bord web.

4.  Après avoir vérifié que tout fonctionne, arrêtez la topologie en appuyant sur Ctrl+C. Pour arrêter l'application SendEvent, sélectionnez la fenêtre et appuyez sur n'importe quelle touche.

### Activation de HBaseBolt et préparation de HBase

1.  Ouvrez le fichier **Temperature.java** et supprimez le commentaire (//) des lignes suivantes :

        //topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
        //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

    Ceci active le bolt HBase.

2.  Enregistrez **Temperature.java**.

3.  Connectez-vous au cluster HBase en utilisant le Bureau à distance.

4.  ﻿À partir du bureau, démarrez la ligne de commande HDInsight, puis entrez les commandes suivantes :

        cd %hbase_home%
        bin\hbase shell

5.  ﻿À partir de l'environnement de ligne de commande HBase, entrez la commande suivante pour créer une table dans laquelle seront stockées les données du capteur.

        create 'SensorData', 'cf'

6.  Vérifiez que la table ne contient aucune donnée en entrant la commande suivante.

        scan 'SensorData'

Pour l'instant, laissez cette invite ouverte dans l'environnement de ligne de commande.

## Mise en package et déploiement de la topologie vers HDInsight

Dans votre environnement de développement, procédez comme suit pour exécuter la topologie de température sur votre cluster Storm HDInsight.

1.  Utilisez la commande suivante pour créer un package JAR depuis votre projet.

        mvn package

    Ceci va créer un fichier nommé **TemperatureMonitor-1.0-SNAPSHOT.jar** dans le répertoire **target** de votre projet.

2.  Sur votre ordinateur de développement local, démarrez l'application .NET **SendEvents** pour avoir certains événements à lire.

3.  Connectez-vous à votre cluster Storm HDInsight en utilisant le Bureau à distance, puis copiez le fichier **TemperatureMonitor-1.0-SNAPSHOT.jar** vers le répertoire **c:\\apps\\dist\\storm\<version number\>**.

4.  Utilisez l'icône **Ligne de commande HDInsight** sur le bureau de cluster pour ouvrir une nouvelle invite de commandes, puis utilisez les commandes suivantes pour exécuter la topologie.

        cd %storm_home%
        bin\storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature Temperature

5.  Une fois la topologie démarrée, il est possible que vous deviez attendre quelques secondes avant que des éléments commencent à apparaître sur le tableau de bord web.

6.  Une fois les éléments affichés sur le tableau de bord, basculez vers la session Bureau à distance sur le cluster HBase.

7.  ﻿À partir de l'environnement de ligne de commande, entrez la commande suivante.

        scan 'SensorData'

    Notez qu'à présent, plusieurs lignes de données écrites par la topologie Storm sont renvoyées.

8.  Pour arrêter la topologie, accédez à la session Bureau à distance en utilisant le cluster Storm, puis entrez les données suivantes dans la ligne de commande HDInsight.

        bin\storm kill Temperature

    Après quelques secondes, la topologie va s'arrêter.

## Résumé

Vous avez à présent appris à utiliser Storm pour lire des données à partir du concentrateur d'événements, à stocker des données dans HBase et à afficher des informations à partir de Storm sur un tableau de bord externe en utilisant SignalR et D3.js.

-   Pour plus d'informations sur Apache Storm, veuillez consulter <https://storm.incubator.apache.org/>

-   Pour plus d'informations sur HBase avec HDInsight, veuillez consulter la [Présentation de HBase avec HDInsight][Présentation de HBase avec HDInsight]

-   Pour plus d'informations sur SignalR, veuillez consulter [ASP.NET SignalR][ASP.NET SignalR].

-   Pour plus d'informations sur D3.js, consultez la page [D3.js : documents pilotés par les données][D3.js : documents pilotés par les données].

-   Pour plus d'informations sur la création des topologies dans .NET, consultez la page [Développement d'applications de traitement des données de diffusion avec SCP.NET et C# sur Storm dans HDInsight][Développement d'applications de traitement des données de diffusion avec SCP.NET et C# sur Storm dans HDInsight].

  [Kit de développement logiciel (SDK) Microsoft Azure pour .NET]: http://azure.microsoft.com/fr-fr/downloads/archive-net-downloads/
  [Java et JDK]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [Maven]: http://maven.apache.org/what-is-maven.html
  [Git]: http://git-scm.com/
  [Chocolatey NuGet]: http://chocolatey.org/
  [SignalR]: http://www.asp.net/signalr/overview/getting-started/introduction-to-signalr
  [Storm Tweet Sentiment D3 Visualization]: https://github.com/P7h/StormTweetsSentimentD3Viz
  [portail Azure]: https://manage.windowsazure.com
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [Kit de développement logiciel (SDK) du client SignalR pour Java]: https://github.com/SignalR/java-client
  [Présentation de HBase avec HDInsight]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-hbase-overview/
  [ASP.NET SignalR]: http://signalr.net/
  [D3.js : documents pilotés par les données]: http://d3js.org/
  [Développement d'applications de traitement des données de diffusion avec SCP.NET et C# sur Storm dans HDInsight]: /fr-fr/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/
