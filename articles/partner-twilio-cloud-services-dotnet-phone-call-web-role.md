<properties linkid="develop-net-how-to-twilio-phone-call" urlDisplayName="Twilio Phone Call" pageTitle="How to make a phone call from Twilio (.NET) - Azure" metaKeywords="Azure .NET Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, ASP.NET twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to make a phone call using Twilio in a web role on Azure" authors="MicrosoftHelp@twilio.com; larryf" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; larryf"></tags>

# Exécution d'un appel téléphonique à l'aide de Twilio dans un rôle Web sur Azure

Ce guide montre comment utiliser Twilio pour passer un appel à partir d'une page Web hébergée dans Azure. L'application finale invite l'utilisateur à fournir des valeurs d'appel téléphonique, comme indiqué dans la capture d'écran suivante.

![Formulaire d'appel Azure utilisant Twilio et ASP.NET][]

## Sommaire

-   [Conditions préalables][]
-   [Création d'un formulaire Web pour passer un appel][]
-   [Création du code pour passer l'appel][]
-   [Étapes suivantes][]
-   [Voir aussi][]

## <a name="twilio-prereqs"></a>Conditions préalables

Pour pouvoir utiliser le code de cette rubrique, vous devez préalablement :

1.  Obtenir un compte Twilio et un jeton d'authentification. Pour utiliser Twilio, commencez par vous inscrire sur [][]<https://www.twilio.com/try-twilio></a>. Pour vous faire une idée des tarifs, consultez la page [][1]<http://www.twilio.com/pricing></a>. Pour plus d'informations sur l'API fournie par Twilio, consultez la page [][2]<http://www.twilio.com/voice/api></a> ;
2.  Vérifier votre numéro de téléphone avec Twilio. Pour plus d'informations sur la vérification d'un numéro de téléphone, consultez la page [][3]<https://www.twilio.com/user/account/phone-numbers/verified>\#</a>. Au lieu d'utiliser un numéro existant, vous pouvez acheter un numéro de téléphone Twilio.
    Dans cet exemple, vous allez utiliser le numéro de téléphone de bac à sable (sandbox) Twilio pour envoyer un message au numéro de téléphone vérifié. Vous ne pouvez utiliser le numéro de téléphone de bac à sable (sandbox) que pour les envois aux numéros de téléphone vérifiés.
3.  Ajouter la bibliothèque .NET Twilio à votre rôle Web. Reportez-vous à « Pour ajouter les bibliothèques Twilio à votre projet de rôle Web » plus loin dans cette rubrique.

Vous devriez également savoir comment créer un rôle Web de base sur Azure.

## <a name="howtocreateform"></a> Création d'un formulaire Web pour passer un appel

### <span id="use_nuget"></span></a>Pour ajouter les bibliothèques Twilio à votre projet de rôle Web :

1.  Ouvrez votre solution dans Visual Studio.
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

## <span id="howtocreatecode"></span></a> Création du code pour passer l'appel

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
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
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
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Diplay the enpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Tilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values into a hash map.
                    // This sample uses the sandbox number provided by Twilio
                    // to make the call.
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

L'appel est passé, et le point de terminaison Twilio, la version de l'API et l'état de l'appel sont affichés. La capture d'écran ci-dessous présente la sortie d'un exemple d'exécution.

![Réponse Azure à un appel à l'aide de Twilio et ASP.NET][]

Pour plus d'informations sur TwiML, consultez la page [][4]<http://www.twilio.com/docs/api/twiml></a>. Pour plus d'informations sur \<Say\> et d'autres verbes Twilio, consultez la page [][5]<http://www.twilio.com/docs/api/twiml/say></a>.

## <span id="nextsteps"></span></a>Étapes suivantes

Ce code vous est fourni afin de vous présenter les fonctions de base de l'utilisation de Twilio dans un rôle Web ASP.NET sur Azure. Avant d'effectuer le déploiement de production sur Azure, vous pouvez ajouter d'autres fonctionnalités telles que la gestion des erreurs. Par exemple :

-   Au lieu d'utiliser un formulaire Web, vous pouvez utiliser un stockage d'objets blob Azure ou une instance de base de données SQL Azure pour stocker les numéros de téléphone et le texte d'appel. Pour plus d'informations sur l'utilisation des objets blob dans Azure, consultez la page [Utilisation du service de stockage d'objets blob Azure dans .NET][]. Pour plus d'informations sur l'utilisation des bases de données SQL, consultez la page [Utilisation d'une base de données SQL Azure dans les applications .NET][].
-   Vous pouvez utiliser RoleEnvironment.getConfigurationSettings pour récupérer l'ID du compte Twilio et le jeton d'authentification à partir des paramètres de configuration de votre déploiement, au lieu de coder les valeurs de votre formulaire en dur. Pour plus d'informations sur la classe RoleEnvironment, consultez [Espace de noms Microsoft.WindowsAzure.ServiceRuntime][].
-   Consultez les instructions de sécurité Twilio sur la page [][6]<https://www.twilio.com/docs/security></a>.
-   Pour en savoir plus sur Twilio, consultez la page [][7]<https://www.twilio.com/docs></a>.

## <a name="seealso"></a>Voir aussi

-   [Utilisation de Twilio pour les fonctionnalités vocales et de SMS depuis Azure][]

  [Formulaire d'appel Azure utilisant Twilio et ASP.NET]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
  [Conditions préalables]: #twilio-prereqs
  [Création d'un formulaire Web pour passer un appel]: #howtocreateform
  [Création du code pour passer l'appel]: #howtocreatecode
  [Étapes suivantes]: #nextsteps
  [Voir aussi]: #seealso
  []: http://www.twilio.com/try-twilio
  [1]: http://www.twilio.com/pricing
  [2]: http://www.twilio.com/voice/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Réponse Azure à un appel à l'aide de Twilio et ASP.NET]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png
  [4]: http://www.twilio.com/docs/api/twiml
  [5]: http://www.twilio.com/docs/api/twiml/say
  [Utilisation du service de stockage d'objets blob Azure dans .NET]: https://www.windowsazure.com/fr-fr/develop/net/how-to-guides/blob-storage/
  [Utilisation d'une base de données SQL Azure dans les applications .NET]: https://www.windowsazure.com/fr-fr/develop/net/how-to-guides/sql-database/
  [Espace de noms Microsoft.WindowsAzure.ServiceRuntime]: http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
  [6]: http://www.twilio.com/docs/security
  [7]: http://www.twilio.com/docs
  [Utilisation de Twilio pour les fonctionnalités vocales et de SMS depuis Azure]: ../twilio-dotnet-how-to-use-for-voice-sms/
