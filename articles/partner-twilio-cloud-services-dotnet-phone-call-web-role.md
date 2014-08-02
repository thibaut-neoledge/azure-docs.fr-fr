<properties linkid="develop-net-how-to-twilio-phone-call" urlDisplayName="Twilio Phone Call" pageTitle="How to make a phone call from Twilio (.NET) - Azure" metaKeywords="Azure .NET Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, ASP.NET twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to make a phone call using Twilio in a web role on Azure" authors="" solutions="" manager="" editor="" />

Exécution d'un appel téléphonique à l'aide de Twilio dans un rôle Web sur Azure
===============================================================================

Ce guide montre comment utiliser Twilio pour passer un appel à partir d'une page Web hébergée dans Azure. L'application finale invite l'utilisateur à fournir des valeurs d'appel téléphonique, comme indiqué dans la capture d'écran suivante.

![Formulaire d'appel Azure utilisant Twilio et ASP.NET](./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png)

SommaireSommaire
----------------

-   [Configuration requise](#twilio-prereqs)
-   [Création d'un formulaire Web d'appel](#howtocreateform)
-   [Création du code d'appel](#howtocreatecode)
-   [Étapes suivantes](#nextsteps)
-   [Voir aussi](#seealso)

Configuration requiseConfiguration requise
------------------------------------------

Pour pouvoir utiliser le code de cette rubrique, vous devez préalablement :

1.  Obtenir un compte Twilio et un jeton d'authentification. Pour utiliser Twilio, commencez par vous inscrire sur [https://www.twilio.com/try-twilio](http://www.twilio.com/try-twilio). Pour vous faire une idée des tarifs, consultez la page <http://www.twilio.com/pricing>. Pour plus d'informations sur l'API fournie par Twilio, consultez la page <http://www.twilio.com/voice/api> ;
2.  Vérifier votre numéro de téléphone avec Twilio. Pour plus d'informations sur la vérification d'un numéro de téléphone, consultez la page <https://www.twilio.com/user/account/phone-numbers/verified#>. Si vous souhaitez utiliser un autre numéro, vous pouvez acheter un numéro de téléphone Twilio.
     Aux fins de cet exemple, vous allez utiliser le numéro de téléphone de bac à sable (sandbox) Twilio pour envoyer un message au numéro de téléphone vérifié. Vous ne pouvez utiliser le numéro de téléphone de bac à sable (sandbox) que pour les envois aux numéros de téléphone vérifiés.
3.  Ajouter la bibliothèque .NET Twilio à votre rôle Web. Reportez-vous à « Pour ajouter les bibliothèques Twilio à votre projet de rôle Web » plus loin dans cette rubrique.

Vous devriez également savoir comment créer un rôle Web de base sur Azure.

Création d'un formulaire Web pour passer un appel Création d'un formulaire Web pour passer un appel
---------------------------------------------------------------------------------------------------

### Pour ajouter les bibliothèques Twilio à votre projet de rôle Web :

1.  Ouvrez votre solution dans Visual Studio.
2.  Cliquez avec le bouton droit sur **Références**.
3.  Cliquez sur **Gérer les packages NuGet...**
4.  Cliquez sur **En ligne**.
5.  Dans la zone de recherche en ligne, tapez *twilio*.
6.  Cliquez sur le bouton **Installer** du package Twilio.

Le code qui suit présente la conception d'un formulaire Web qui extrait les données des utilisateurs pour passer un appel. Dans cet exemple, un rôle Web ASP.NET appelé **TwilioCloud** est créé.

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

Création du code Création du code pour passer l'appel
-----------------------------------------------------

Le code suivant, qui est appelé lorsque l'utilisateur remplit le formulaire, crée un message d'appel et génère l'appel. Dans cet exemple, le code est exécuté dans le gestionnaire d'événements sur clic du bouton du formulaire. Utilisez votre compte Twilio et votre jeton d'authentification plutôt que les valeurs par défaut utilisées dans **accountSID** et **authToken** dans le code qui suit.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Le traitement de l'appel s'effectue ici.

                // Utilisez vos SID de compte et jeton d'authentification à la place
                // des espaces réservés qui sont présentés ici.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Créez une instance du client Twilio.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Récupérez le compte, utilisé ultérieurement pour récupérer
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Récupérez les valeurs entrées par l'utilisateur.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Créez une URL à l'aide du message Twilio et du texte entré
                    // par l'utilisateur. Vous devez remplacer les espaces du texte de l'utilisateur par '%20'
                    // afin que le texte puisse être utilisé pour une URL.
                    String Url = "http://twimlets.com/message
        Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Affichez le point de terminaison, la version de l'API et l'URL du message.
                    this.varDisplay.Items.Add("Using Tilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instanciez les options de l'appel qui sont transmises
                    // à l'appel sortant.
                    CallOptions options = new CallOptions();

                    // Placez l'appel From, To et les valeurs d'URL dans une configuration de hachage.
                    // Cet exemple utilise le numéro de bac à sable (sandbox) fourni par
                    // Twilio pour passer l'appel.
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Passez l'appel.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

L'appel est passé, et le point de terminaison Twilio, la version de l'API et l'état de l'appel sont affichés. La capture d'écran ci-dessous présente la sortie d'un exemple d'exécution.

![Réponse Azure à un appel à l'aide de Twilio et ASP.NET](./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png)

Pour plus d'informations sur TwiML, consultez la page <http://www.twilio.com/docs/api/twiml>. Pour plus d'informations sur &lt;Say\> et d'autres verbes Twilio, consultez la page <http://www.twilio.com/docs/api/twiml/say>.

Étapes suivantesÉtapes suivantes
--------------------------------

Ce code vous est fourni afin de vous présenter les fonctions de base de l'utilisation de Twilio dans un rôle Web ASP.NET sur Azure. Avant d'effectuer le déploiement de production sur Azure, vous pouvez ajouter d'autres fonctionnalités telles que la gestion des erreurs. Par exemple :

-   Au lieu d'utiliser un formulaire Web, vous pouvez utiliser un stockage d'objets blob Azure ou une instance de base de données SQL Azure pour stocker les numéros de téléphone et le texte d'appel. Pour plus d'informations sur l'utilisation des objets blob dans Azure, consultez la page [Utilisation du service de stockage d'objets blob Azure dans .NET](https://www.windowsazure.com/en-us/develop/net/how-to-guides/blob-storage/). Pour plus d'informations sur l'utilisation des bases de données SQL, consultez la page [Utilisation d'une base de données SQL Azure dans les applications .NET](https://www.windowsazure.com/en-us/develop/net/how-to-guides/sql-database/).
-   Vous pouvez utiliser RoleEnvironment.getConfigurationSettings pour récupérer l'ID du compte Twilio et le jeton d'authentification à partir des paramètres de configuration de votre déploiement, au lieu de coder les valeurs de votre formulaire en dur. Pour plus d'informations sur la classe RoleEnvironment, consultez [Espace de noms Microsoft.WindowsAzure.ServiceRuntime](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx).
-   Consultez les instructions de sécurité Twilio sur la page [https://www.twilio.com/docs/security](http://www.twilio.com/docs/security).
-   Pour en savoir plus sur Twilio, consultez la page [https://www.twilio.com/docs](http://www.twilio.com/docs).

Voir aussiVoir aussi
--------------------

-   [Utilisation de Twilio pour les fonctionnalités vocales et de SMS dans un rôle Web](/en-us/develop/net/how-to-guides/twilio/)

