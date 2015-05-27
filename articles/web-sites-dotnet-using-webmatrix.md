<properties 
	pageTitle="Développement et déploiement d’une application Web avec Microsoft WebMatrix" 
	description="Découvrez comment développer et déployer une application Web ASP.NET sur Azure App Service Web Apps avec Microsoft WebMatrix." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="tomfitz"/>


# Développement et déploiement d’une application Web avec Microsoft WebMatrix

## Vue d'ensemble

Ce guide explique comment utiliser Microsoft WebMatrix pour créer et déployer un site Web .NET sur [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Vous allez utiliser un exemple d'application à partir d'un modèle de site WebMatrix.

Vous apprendrez à effectuer les opérations suivantes :

* vous connecter à Azure à partir de WebMatrix ;
* créer un site à l'aide d'un modèle intégré avec WebMatrix ; 
* déployer le site web personnalisé directement à partir de WebMatrix sur Azure.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Connexion à Azure

1. Lancez WebMatrix.
2. Si vous utilisez WebMatrix 3 pour la première fois, vous êtes invité à vous connecter à Azure. Sinon, vous pouvez cliquer sur le bouton **Se connecter**, puis choisir **Ajouter un compte**. Choisissez **Se connecter** à l'aide de votre compte Microsoft.

	![Ajouter un compte][addaccount]

3. Si vous disposez d'un compte Azure, vous pouvez vous connecter avec votre compte Microsoft :

	![Connexion à Azure][signin]


## Création d'un site à l'aide d'un modèle intégré pour Azure

1. Sur l'écran d'accueil, cliquez sur le bouton **Nouveau**, puis choisissez **Galerie de modèles** pour créer un site à partir de la galerie de modèles :

	![Nouveau site à partir de la galerie de modèles][sitefromtemplate]

2. La galerie de modèles affiche une liste des modèles disponibles pouvant s'exécuter en local ou sur Azure. Sélectionnez **Bakery** dans la liste de modèles, entrez **bakerysample** pour le **Nom du site**, puis cliquez sur **Suivant**.

	![Créer un site à partir d'un modèle][sitefromtemplatedetails]

3. Si vous êtes connecté à Azure, vous avez maintenant la possibilité de créer une instance Azure App Service Web Apps pour votre site local. Choisissez un nom unique, puis sélectionnez le centre de données sur lequel vous voulez que votre instance Web Apps soit créée :

	![Créer un site sur Azure][sitefromtemplateazure]

	Dès que WebMatrix finit de créer le site local et l’instance Web Apps dans Azure, l’IDE WebMatrix s’affiche.

	![IDE WebMatrix][howtowebmatrixide]

## Configurer la messagerie électronique

L'exemple « Bakery » comprend un formulaire de commande simulée qui envoie un message électronique avec l'article commandé. Vous utiliserez le service de messagerie SendGrid dans Azure pour envoyer des messages électroniques à partir de votre site.

1. Suivez les étapes de la procédure du didacticiel [Envoi de courriers électroniques à l’aide de SendGrid avec Azure][sendgridexample] pour configurer un compte SendGrid et récupérer les informations de connexion. Il est inutile de faire tout le didacticiel ; il vous suffit d'aller jusqu'à l'obtention des informations de connexion.

2. Ajoutez le package NuGet SendGrid à votre projet WebMatrix. Tout d'abord, cliquez sur le bouton NuGet.

    ![Ajouter SendGrid][addsendgrid]

    Recherchez SendGrid et installez-le.

    ![Installer SendGrid][installsendgrid]

    Une fois le package installé, notez que les assemblys SendGrid ont été ajoutés dans bin.

    ![SendGrid ajouté][binsendgrid]

3. Ouvrez la page *Order.cshtml* en double-cliquant sur le nom du fichier.

	![][modify2]

4. Au début du fichier, ajoutez le code suivant :

        @using SendGrid;
        @using System.Net.Mail;

4. Recherchez le commentaire « //SMTP Configuration for Hotmail » et supprimez ou convertissez en commentaires l'ensemble du code relatif à l'utilisation de WebMail.

        /*
        //SMTP Configuration for Hotmail
        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort = 25;
        WebMail.EnableSsl = true;

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

        if (WebMail.UserName.IsEmpty() || WebMail.Password.IsEmpty() || WebMail.From.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        } 
        else {
            try {
                WebMail.Send(to: customerEmail, subject: "Fourth Coffee - New Order", body: body);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }*/


5. Ajoutez le code pour utiliser SendGrid au lieu de WebMail pour envoyer des messages électroniques. Ajoutez le code suivant à la place du code que vous avez supprimé à l'étape précédente.

		 if (email.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        }
        else {
            // Create the email object first, then add the properties.
            SendGridMessage myMessage = new SendGridMessage();
            myMessage.AddTo(email);
            myMessage.From = new MailAddress("FourthCoffee@example.com", "Fourth Coffee");
            myMessage.Subject = "Fourth Coffee - New Order";
            myMessage.Text = body;

            // Create credentials, specifying your user name and password.
            var credentials = new NetworkCredential("[your user name", "[your password]");

            // Create an Web transport for sending email.
            var transportWeb = new Web(credentials);

            // Send the email.
            try {
                transportWeb.Deliver(myMessage);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }


6. Sur le ruban WebMatrix, cliquez sur **Exécuter** pour tester le site.

	![][modify4]

7. Cliquez sur **Order Now** sur l'un des produits et envoyez-vous une commande.

8. Vérifiez vos courriers électroniques et assurez-vous de recevoir la confirmation de la commande. Si vous rencontrez des difficultés avec l'envoi du courrier électronique, consultez la section [Problèmes avec l'envoi de courrier électronique][sendmailissues] du guide de résolution des problèmes ASP.NET Web Pages (Razor).
 

## Déployer le site web personnalisé à partir de WebMatrix sur Azure

1. Dans WebMatrix, cliquez sur **Publier** dans le ruban **Accueil** pour afficher la boîte de dialogue **Publier l'aperçu** pour le site web.

	![WebMatrix Publier l'aperçu][howtopublishpreview]

2. Cliquez pour activer la case à cocher en regard de bakery.sdf, puis cliquez sur **Continuer**. Une fois la publication terminée, l’URL de l’application Web mise à jour dans Azure App Service s’affiche en bas de l’IDE WebMatrix.

	![Publication terminée][publishcomplete]

3. Cliquez sur le lien pour ouvrir le site Web (une instance Web Apps dans Azure) dans votre navigateur :

	![Exemple de site Bakery][bakerysample]

	Vous trouverez également l’URL de l’instance Web Apps sur le [portail Azure](https://portal.azure.com) en cliquant sur **Parcourir** > **Applications Web** afin d’afficher toutes les instances Web Apps associées à votre abonnement, puis en sélectionnant une application Web. L’URL de l’application Web s’affiche sur le panneau de l’application Web.

## Modification du site Web et republication sur les applications Web

Vous pouvez utiliser WebMatrix pour modifier le site et le republier sur votre instance Web Apps. Dans la procédure suivante, vous allez ajouter une case à cocher pour indiquer si la commande est un cadeau.

1. Ouvrez la page *Order.cshtml*.

2. Recherchez la définition de formulaire de la classe « shiping ». Insérez le code suivant juste après le bloc &lt;li&gt;.
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. Recherchez la ligne « var shipping = Request["orderShipping"]; » dans le fichier et insérez la ligne de code suivante juste après.

		var gift = Request["isGift"];

4. Juste après le bloc de code qui valide que l'adresse d'expédition n'est pas vide dans l'extrait de code suivant.

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	Votre fichier *order.cshtml* doit ressembler à l'image suivante.

	![][modify6]

5. Enregistrez le fichier, puis exécutez le site en local et envoyez-vous une commande test. Pensez à tester la nouvelle case à cocher.

	![][modify7]

6. Republiez le site en cliquant sur **Publier** sur le ruban **Accueil**.

7. Dans la boîte de dialogue **Publier l'aperçu**, vérifiez que les deux fichiers Order.cshtml sont activés, puis cliquez sur Continuer.

8. Cliquez sur le lien pour ouvrir le site Web dans votre navigateur et tester la mise à jour sur votre instance Web Apps.

## Étapes suivantes

* [Site Web WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l'ancien et le nouveau portail, consultez : [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715)




[howtowebmatrixide]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide2.png
[howtopublishpreview]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
[addsendgrid]: ./media/web-sites-dotnet-using-webmatrix/addsendgridpackage.png
[installsendgrid]: ./media/web-sites-dotnet-using-webmatrix/installsendgrid.png
[binsendgrid]: ./media/web-sites-dotnet-using-webmatrix/sendgridbin.png

[publishcomplete]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
[bakerysample]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
[addaccount]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
[sitefromtemplatedetails]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
[sitefromtemplateazure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png

[modify1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
[modify2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
[modify3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
[modify4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
[modify5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
[modify6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
[modify7]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png



[sendmailissues]: http://go.microsoft.com/fwlink/?LinkId=253001#email
[sendgridexample]: http://azure.microsoft.com/documentation/articles/sendgrid-dotnet-how-to-send-email/

<!--HONumber=54-->