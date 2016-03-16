### Enregistrement des informations d’identification de votre compte de stockage dans le fichier app.config

Enregistrez ensuite vos informations d’identification dans le fichier app.config du projet. Modifiez le fichier app.config de façon à ce qu’il soit similaire à l’exemple ci-après, en remplaçant `myaccount` par le nom de votre compte de stockage et `mykey` par la clé de votre compte de stockage.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

<!---HONumber=AcomDC_0309_2016-->