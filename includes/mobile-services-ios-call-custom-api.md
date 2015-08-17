
## <a name="update-app"></a> Appel d'une API personnalisée à partir d'une application iOS

Pour appeler cette API personnalisée à partir d'un client iOS, utilisez la méthode `MSClient invokeAPI`. Il existe deux versions de cette méthode, une relative aux requêtes au format JSON et une relative à tout type de données :

	/// Invokes a user-defined API of the Mobile Service.  The HTTP request and
	/// response content will be treated as JSON.
	-(void)invokeAPI:(NSString *)APIName
	            body:(id)body
	      HTTPMethod:(NSString *)method
	      parameters:(NSDictionary *)parameters
	         headers:(NSDictionary *)headers
	      completion:(MSAPIBlock)completion;

	/// Invokes a user-defined API of the Mobile Service.  The HTTP request and
	/// response content can be of any media type.
	-(void)invokeAPI:(NSString *)APIName
	            data:(NSData *)data
	      HTTPMethod:(NSString *)method
	      parameters:(NSDictionary *)parameters
	         headers:(NSDictionary *)headers
	      completion:(MSAPIDataBlock)completion;


Par exemple, pour envoyer une requête JSON à une API personnalisée nommée « sendEmail », transmettez un `NSDictionary` pour les paramètres de requête :

	NSDictionary *emailHeader = @{ @"to": @"email.com", @"subject" : @"value" };

	[self.client invokeAPI:@"sendEmail"
	     body:emailBody
	     HTTPMethod:@"POST"
	     parameters:emailHeader
	     headers:nil
	     completion:completion ];
		

<!---HONumber=August15_HO6-->