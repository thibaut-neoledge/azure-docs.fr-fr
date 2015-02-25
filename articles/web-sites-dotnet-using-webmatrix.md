<properties 
	pageTitle="Site Web .NET avec WebMatrix - Didacticiels Azure" 
	description="Découvrez comment développer et déployer un site Web Azure avec WebMatrix." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/27/2014" 
	ms.author="tomfitz"/>





#Développer et déployer un site Web avec Microsoft WebMatrix
Ce guide explique comment utiliser Microsoft WebMatrix pour créer et déployer un site Web sur Azure.  Vous allez utiliser un exemple d'application à partir d'un modèle de site WebMatrix.

Vous apprendrez à effectuer les opérations suivantes :

* vous connecter à Azure à partir de WebMatrix ;
* créer un site à l'aide d'un modèle intégré avec WebMatrix ; 
* déployer le site Web personnalisé directement à partir de WebMatrix sur Azure.

> [AZURE.NOTE]
> Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Vous pouvez <a href="http://azure.microsoft.com/fr-fr/pricing/member-offers/msdn-benefits-details/">activer les avantages de votre abonnement MSDN</a> ou <a href="http://azure.microsoft.com/fr-fr/pricing/free-trial/">obtenir une version d'évaluation gratuite</a>.
> Si vous voulez prendre en main Azure Web Sites avant de créer un compte, accédez à <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, où vous pouvez immédiatement et gratuitement créer un site de départ ASP.NET de courte durée dans Azure Web Sites. Aucune carte de crédit n'est requise, vous ne prenez aucun engagement.

## Connexion à Azure

1. Lancez WebMatrix.
2. Si vous utilisez WebMatrix 3 pour la première fois, vous êtes invité à vous connecter à Azure.  Sinon, vous pouvez cliquer sur le bouton **Se connecter**, puis choisir **Ajouter un compte**.  Choisissez **Se connecter** à l'aide de votre compte Microsoft.

	![Add Account][addaccount]

3. Si vous disposez d'un compte Azure, vous pouvez vous connecter avec votre compte Microsoft :

	![Sign into Azure][signin]	


## Création d'un site à l'aide d'un modèle intégré pour Azure

1. Dans l'écran d'accueil, cliquez sur le bouton **Nouveau**, puis choisissez **Galerie de modèles** pour créer un site à partir de la galerie de modèles :

	![New site from Template Gallery][sitefromtemplate]

2. La galerie de modèles affiche une liste des modèles disponibles pouvant s'exécuter en local ou sur Azure.  Sélectionnez **Bakery** dans la liste de modèles, entrez **bakerysample** pour le **Nom du site**, puis cliquez sur **Suivant**.

	![Create Site from Template][sitefromtemplatedetails]

3. Si vous êtes connecté à Azure, vous avez maintenant la possibilité de créer un site Web Azure pour votre site local.  Choisissez un nom unique, puis sélectionnez le centre de données dans lequel vous voulez créer votre site : 

	![Create site on Azure][sitefromtemplateazure]

	Une fois que WebMatrix a fini de créer le site Web, l'environnement de développement intégré (IDE) WebMatrix s'affiche :

	![WebMatrix IDE][howtowebmatrixide] 

## Configurer le courrier électronique

L'exemple " Bakery " comprend un formulaire de commande simulée qui envoie un message électronique avec l'article commandé. Vous utiliserez le service de messagerie SendGrid dans Azure pour envoyer des messages électroniques à partir de votre site.

1. Suivez les étapes du didacticiel [Comment envoyer du courrier électronique à l'aide de SendGrid avec Azure][sendgridexample] pour configurer un compte SendGrid et récupérer les informations de connexion. Il est inutile de faire tout le didacticiel ; il vous suffit d'aller jusqu'à l'obtention des informations de connexion.

2. Ajoutez le package NuGet SendGrid à votre projet WebMatrix. Tout d'abord, cliquez sur le bouton NuGet.

    ![Add SendGrid][addsendgrid]

    Recherchez SendGrid et installez-le.

    ![Install SendGrid][installsendgrid]

    Une fois l'installation du package terminée, notez que les assemblys SendGrid ont été ajoutés dans bin.

    ![SendGrid added][binsendgrid]

3. Ouvrez la page  *Order.cshtml* en double-cliquant sur le nom du fichier.

	![][modify2]

4. En haut du fichier, ajoutez le code ci-dessous :

        @using SendGrid;
        @using System.Net.Mail;	

4. Recherchez le commentaire indiquant //SMTP Configuration for Hotmail et supprimez ou commentez le code pour l'utilisation de WebMail.

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


6. Dans le ruban WebMatrix, cliquez sur **Exécuter** pour tester le site.

	![][modify4]

7. Cliquez sur **Order Now** sur l'un des produits et envoyez-vous une commande.

8. Vérifiez vos courriers électroniques et assurez-vous de recevoir la confirmation de la commande. Si vous rencontrez des difficultés avec l'envoi du courrier électronique, consultez la section [Problèmes avec l'envoi de courrier électronique][sendmailissues] du guide de résolution des problèmes ASP.NET Web Pages (Razor).
 

## Déployer le site Web personnalisé à partir de WebMatrix sur Azure

1. Dans WebMatrix, cliquez sur **Publier** dans le ruban **Accueil** pour afficher la boîte de dialogue **Publier l'aperçu** pour le site Web.

	![WebMatrix Publish Preview][howtopublishpreview]

2. Cochez la case en regard de bakery.sdf, puis cliquez sur **Continuer**.  Une fois la publication terminée, l'URL du site Web mis à jour sur Azure s'affiche en bas de l'IDE WebMatrix.  

	![Publishing Complete][publishcomplete]

3. Cliquez sur le lien pour ouvrir le site Web dans votre navigateur :

	![Bakery Sample Site][bakerysample]

	Vous pouvez également trouver l'URL du site Web dans le portail Azure en cliquant sur **Sites Web** pour afficher tous les sites Web de votre abonnement. L'URL de chaque site Web s'affiche dans la colonne URL de la page des sites Web.

## Modifier le site Web et le republier sur le site Web Azure

Vous pouvez utiliser WebMatrix pour changer le site et le republier sur votre site Web Azure. Dans la procédure suivante, vous allez ajouter une case à cocher pour indiquer si la commande est un cadeau.

1. Ouvrez la page  *Order.cshtml*.

2. Recherchez la définition de formulaire de la classe " shiping ". Insérez le code suivant juste après le bloc &lt;li&gt;.
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. Recherchez la ligne "var shipping = Request["orderShipping"];" dans le fichier et insérez la ligne de code suivante juste après.

		var gift = Request["isGift"];

4. Juste après le bloc de code qui valide que l'adresse d'expédition n'est pas vide dans l'extrait de code suivant.

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	Votre fichier  *order.cshtml* doit ressembler à l'image suivante.

	![][modify6]

5. Enregistrez le fichier, puis exécutez le site en local et envoyez-vous une commande test. Pensez à tester la nouvelle case à cocher.

	![][modify7]

6. Republiez le site en cliquant sur **Publier** dans le ruban **Accueil**.

7. Dans la boîte de dialogue **Publier l'aperçu**, vérifiez que les deux fichiers Order.cshtml sont activés, puis cliquez sur Continuer.

8. Cliquez sur le lien pour ouvrir le site Web dans votre navigateur et tester la mise à jour sur votre site Web Azure.

# Étapes suivantes

Vous savez désormais comment créer et déployer un site Web de WebMatrix vers Azure. Pour en savoir plus sur WebMatrix, consultez les ressources suivantes :

* [WebMatrix pour Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Site Web WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





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
[sendgridexample]: http://azure.microsoft.com/fr-fr/documentation/articles/sendgrid-dotnet-how-to-send-email/


<!--HONumber=42-->
