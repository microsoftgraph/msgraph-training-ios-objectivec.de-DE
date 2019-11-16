<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten. Hierzu integrieren Sie die [Microsoft-Authentifizierungsbibliothek (MSAL) für IOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) in die Anwendung.

1. Erstellen Sie eine neue **Eigenschaftenlisten** Datei im **GraphTutorial** -Projekt mit dem Namen **AuthSettings. plist**.
1. Fügen Sie die folgenden Elemente zur Datei im **Stamm** Wörterbuch hinzu.

    | Key | Typ | Wert |
    |-----|------|-------|
    | `AppId` | Zeichenfolge | Die Anwendungs-ID aus dem Azure-Portal |
    | `GraphScopes` | Array | Zwei Zeichenfolgenwerte `User.Read` : und`Calendars.Read` |

    ![Ein Screenshot der Datei AuthSettings. plist in Xcode](./images/auth-settings.png)

> [!IMPORTANT]
> Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es nun ein guter Zeitpunkt, die Datei **AuthSettings. plist** aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

In diesem Abschnitt Konfigurieren Sie das Projekt für MSAL, erstellen eine Authentifizierungs-Manager-Klasse und aktualisieren die APP, um sich anzumelden und abzumelden.

### <a name="configure-project-for-msal"></a>Konfigurieren des Projekts für MSAL

1. Fügen Sie der Projektfunktion eine neue Schlüsselbund Gruppe hinzu.
    1. Wählen Sie das **GraphTutorial** -Projekt aus, und **Signieren Sie #a0 Funktionen**.
    1. Wählen Sie **+-Funktion**aus, und doppelklicken Sie dann auf **Schlüsselbund Freigabe**.
    1. Fügen Sie eine Schlüsselbund Gruppe mit `com.microsoft.adalcache`dem Wert hinzu.

1. Steuerelement klicken Sie auf **Info. plist** , und wählen Sie dann **Öffnen als**, dann **Quellcode**aus.
1. Fügen Sie das folgende innerhalb `<dict>` des-Elements hinzu.

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        </array>
      </dict>
    </array>
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauthv2</string>
        <string>msauthv3</string>
    </array>
    ```

1. Öffnen Sie **AppDelegate. m** , und fügen Sie die folgende Importanweisung am Anfang der Datei hinzu.

    ```objc
    #import <MSAL/MSAL.h>
    ```

1. Fügen Sie die folgende Funktion zur `AppDelegate`-Klasse hinzu:

    ```objc
    - (BOOL)application:(UIApplication *)app
                openURL:(NSURL *)url
                options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url
                                             sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```

### <a name="create-authentication-manager"></a>Erstellen des Authentifizierungs-Managers

1. Erstellen Sie eine neue **Cocoa Touch-Klasse** im **GraphTutorial** -Projekt mit dem Namen **AuthenticationManager**. Wählen Sie **NSObject** in der unter **Klasse von Field aus** .
1. Öffnen Sie **AuthenticationManager. h** , und ersetzen Sie den Inhalt durch den folgenden Code.

    ```objc
    #import <Foundation/Foundation.h>
    #import <MSAL/MSAL.h>
    #import <MSGraphClientSDK/MSGraphClientSDK.h>

    NS_ASSUME_NONNULL_BEGIN

    typedef void (^GetTokenCompletionBlock)(NSString* _Nullable accessToken, NSError* _Nullable error);

    @interface AuthenticationManager : NSObject<MSAuthenticationProvider>

    + (id) instance;
    - (void) getTokenInteractivelyWithParentView: (UIViewController*) parentView andCompletionBlock: (GetTokenCompletionBlock)completionBlock;
    - (void) getTokenSilentlyWithCompletionBlock: (GetTokenCompletionBlock)completionBlock;
    - (void) signOut;
    - (void) getAccessTokenForProviderOptions:(id<MSAuthenticationProviderOptions>)authProviderOptions andCompletion:(void (^)(NSString *, NSError *))completion;

    @end

    NS_ASSUME_NONNULL_END
    ```

1. Öffnen Sie **AuthenticationManager. m** , und ersetzen Sie den Inhalt durch den folgenden Code.

    ```objc
    #import "AuthenticationManager.h"

    @interface AuthenticationManager()

    @property NSString* appId;
    @property NSArray<NSString*>* graphScopes;
    @property MSALPublicClientApplication* publicClient;

    @end

    @implementation AuthenticationManager

    + (id) instance {
        static AuthenticationManager *singleInstance = nil;
        static dispatch_once_t onceToken;
        dispatch_once(&onceToken, ^ {
            singleInstance = [[self alloc] init];
        });

        return singleInstance;
    }

    - (id) init {
        if (self = [super init]) {
            // Get app ID and scopes from AuthSettings.plist
            NSString* authConfigPath =
            [NSBundle.mainBundle pathForResource:@"AuthSettings" ofType:@"plist"];
            NSDictionary* authConfig = [NSDictionary dictionaryWithContentsOfFile:authConfigPath];

            self.appId = authConfig[@"AppId"];
            self.graphScopes = authConfig[@"GraphScopes"];

            // Create the MSAL client
            self.publicClient = [[MSALPublicClientApplication alloc] initWithClientId:self.appId error:nil];
        }

        return self;
    }

    - (void) getAccessTokenForProviderOptions:(id<MSAuthenticationProviderOptions>)authProviderOptions andCompletion:(void (^)(NSString * _Nonnull, NSError * _Nonnull))completion {
        [self getTokenSilentlyWithCompletionBlock:completion];
    }

    - (void) getTokenInteractivelyWithParentView:(UIViewController *)parentView andCompletionBlock:(GetTokenCompletionBlock)completionBlock {
        MSALWebviewParameters* webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:parentView];
        MSALInteractiveTokenParameters* interactiveParameters =
        [[MSALInteractiveTokenParameters alloc]initWithScopes:self.graphScopes webviewParameters:webParameters];

        // Call acquireToken to open a browser so the user can sign in
        [self.publicClient
         acquireTokenWithParameters:interactiveParameters
         completionBlock:^(MSALResult * _Nullable result, NSError * _Nullable error) {

            // Check error
            if (error) {
                completionBlock(nil, error);
                return;
            }

            // Check result
            if (!result) {
                NSMutableDictionary* details = [NSMutableDictionary dictionary];
                [details setValue:@"No result was returned" forKey:NSDebugDescriptionErrorKey];
                completionBlock(nil, [NSError errorWithDomain:@"AuthenticationManager" code:0 userInfo:details]);
                return;
            }

            NSLog(@"Got token interactively: %@", result.accessToken);
            completionBlock(result.accessToken, nil);
        }];
    }

    - (void) getTokenSilentlyWithCompletionBlock:(GetTokenCompletionBlock)completionBlock {
        // Check if there is an account in the cache
        NSError* msalError;
        MSALAccount* account = [self.publicClient allAccounts:&msalError].firstObject;

        if (msalError || !account) {
            NSMutableDictionary* details = [NSMutableDictionary dictionary];
            [details setValue:@"Could not retrieve account from cache" forKey:NSDebugDescriptionErrorKey];
            completionBlock(nil, [NSError errorWithDomain:@"AuthenticationManager" code:0 userInfo:details]);
            return;
        }

        MSALSilentTokenParameters* silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:self.graphScopes
                                                                                                account:account];

        // Attempt to get token silently
        [self.publicClient
         acquireTokenSilentWithParameters:silentParameters
         completionBlock:^(MSALResult * _Nullable result, NSError * _Nullable error) {
             // Check error
             if (error) {
                 completionBlock(nil, error);
                 return;
             }

             // Check result
             if (!result) {
                 NSMutableDictionary* details = [NSMutableDictionary dictionary];
                 [details setValue:@"No result was returned" forKey:NSDebugDescriptionErrorKey];
                 completionBlock(nil, [NSError errorWithDomain:@"AuthenticationManager" code:0 userInfo:details]);
                 return;
             }

             NSLog(@"Got token silently: %@", result.accessToken);
             completionBlock(result.accessToken, nil);
         }];
    }

    - (void) signOut {
        NSError* msalError;
        NSArray* accounts = [self.publicClient allAccounts:&msalError];

        if (msalError) {
            NSLog(@"Error getting accounts from cache: %@", msalError.debugDescription);
            return;
        }

        for (id account in accounts) {
            [self.publicClient removeAccount:account error:nil];
        }
    }

    @end
    ```

### <a name="add-sign-in-and-sign-out"></a>Hinzufügen von Anmelde-und Abmeldungen

1. Öffnen Sie die Datei **SignInViewController. m** , und ersetzen Sie den Inhalt durch den folgenden Code.

    ```objc
    #import "SignInViewController.h"
    #import "SpinnerViewController.h"
    #import "AuthenticationManager.h"

    @interface SignInViewController ()
    @property SpinnerViewController* spinner;
    @end

    @implementation SignInViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.

        self.spinner = [SpinnerViewController alloc];
        [self.spinner startWithContainer:self];

        [AuthenticationManager.instance
         getTokenSilentlyWithCompletionBlock:^(NSString * _Nullable accessToken, NSError * _Nullable error) {
             dispatch_async(dispatch_get_main_queue(), ^{
                 [self.spinner stop];

                 if (error || !accessToken) {
                     // If there is no token or if there's an error,
                     // no user is signed in, so stay here
                     return;
                 }

                 // Since we got a token, user is signed in
                 // Go to welcome page
                 [self performSegueWithIdentifier: @"userSignedIn" sender: nil];
             });
        }];
    }

    - (IBAction)signIn {
        self.spinner = [SpinnerViewController alloc];
        [self.spinner startWithContainer:self];

        [AuthenticationManager.instance
         getTokenInteractivelyWithParentView:self
         andCompletionBlock:^(NSString * _Nullable accessToken, NSError * _Nullable error) {
             dispatch_async(dispatch_get_main_queue(), ^{
                 [self.spinner stop];

                 if (error || !accessToken) {
                     // Show the error and stay on the sign-in page
                     UIAlertController* alert = [UIAlertController
                                                 alertControllerWithTitle:@"Error signing in"
                                                 message:error.debugDescription
                                                 preferredStyle:UIAlertControllerStyleAlert];

                     UIAlertAction* okButton = [UIAlertAction
                                                actionWithTitle:@"OK"
                                                style:UIAlertActionStyleDefault
                                                handler:nil];

                     [alert addAction:okButton];
                     [self presentViewController:alert animated:true completion:nil];
                     return;
                 }

                 // Since we got a token, user is signed in
                 // Go to welcome page
                 [self performSegueWithIdentifier: @"userSignedIn" sender: nil];
             });
         }];
    }
    @end
    ```

1. Öffnen Sie **WelcomeViewController. m** , und ersetzen `signOut` Sie die vorhandene Funktion durch Folgendes.

    ```objc
    - (IBAction)signOut {
        [AuthenticationManager.instance signOut];
        [self performSegueWithIdentifier: @"userSignedOut" sender: nil];
    }
    ```

1. Speichern Sie Ihre Änderungen, und starten Sie die Anwendung im Simulator neu.

Wenn Sie sich bei der App anmelden, sollten Sie ein Zugriffstoken sehen, das im Ausgabefenster von Xcode angezeigt wird.

![Screenshot des Ausgabefensters in Xcode mit einem Zugriffstoken](./images/access-token-output.png)

## <a name="get-user-details"></a>Abrufen von Benutzer Details

In diesem Abschnitt erstellen Sie eine Hilfsklasse, die alle Aufrufe von Microsoft Graph enthält, und aktualisieren Sie `WelcomeViewController` , um die neue Klasse zum Abrufen des angemeldeten Benutzers zu verwenden.

1. Erstellen Sie eine neue **Cocoa Touch-Klasse** im **GraphTutorial** -Projekt mit dem Namen **graphmanager**. Wählen Sie **NSObject** in der unter **Klasse von Field aus** .
1. Öffnen Sie **graphmanager. h** , und ersetzen Sie den Inhalt durch den folgenden Code.

    ```objc
    #import <Foundation/Foundation.h>
    #import <MSGraphClientSDK/MSGraphClientSDK.h>
    #import <MSGraphClientModels/MSGraphClientModels.h>
    #import "AuthenticationManager.h"

    NS_ASSUME_NONNULL_BEGIN

    typedef void (^GetMeCompletionBlock)(MSGraphUser* _Nullable user, NSError* _Nullable error);

    @interface GraphManager : NSObject

    + (id) instance;
    - (void) getMeWithCompletionBlock: (GetMeCompletionBlock)completionBlock;

    @end

    NS_ASSUME_NONNULL_END
    ```

1. Öffnen Sie **graphmanager. m** , und ersetzen Sie den Inhalt durch den folgenden Code.

    ```objc
    #import "GraphManager.h"

    @interface GraphManager()

    @property MSHTTPClient* graphClient;

    @end

    @implementation GraphManager

    + (id) instance {
        static GraphManager *singleInstance = nil;
        static dispatch_once_t onceToken;
        dispatch_once(&onceToken, ^ {
            singleInstance = [[self alloc] init];
        });

        return singleInstance;
    }

    - (id) init {
        if (self = [super init]) {
            // Create the Graph client
            self.graphClient = [MSClientFactory
                                createHTTPClientWithAuthenticationProvider:AuthenticationManager.instance];
        }

        return self;
    }

    - (void) getMeWithCompletionBlock:(GetMeCompletionBlock)completionBlock {
        // GET /me
        NSString* meUrlString = [NSString stringWithFormat:@"%@/me", MSGraphBaseURL];
        NSURL* meUrl = [[NSURL alloc] initWithString:meUrlString];
        NSMutableURLRequest* meRequest = [[NSMutableURLRequest alloc] initWithURL:meUrl];

        MSURLSessionDataTask* meDataTask =
        [[MSURLSessionDataTask alloc]
            initWithRequest:meRequest
            client:self.graphClient
            completion:^(NSData *data, NSURLResponse *response, NSError *error) {
                if (error) {
                    completionBlock(nil, error);
                    return;
                }

                // Deserialize the response as a user
                NSError* graphError;
                MSGraphUser* user = [[MSGraphUser alloc] initWithData:data error:&graphError];

                if (graphError) {
                    completionBlock(nil, graphError);
                } else {
                    completionBlock(user, nil);
                }
            }];

        // Execute the request
        [meDataTask execute];
    }

    @end
    ```

1. Öffnen Sie **WelcomeViewController. m** , und fügen `#import` Sie die folgenden Anweisungen am Anfang der Datei hinzu.

    ```objc
    #import "SpinnerViewController.h"
    #import "GraphManager.h"
    #import <MSGraphClientModels/MSGraphClientModels.h>
    ```

1. Fügen Sie die folgende Eigenschaft zur `WelcomeViewController` Schnittstellendeklaration hinzu.

    ```objc
    @property SpinnerViewController* spinner;
    ```

1. Ersetzen Sie den `viewDidLoad` vorhandenen durch den folgenden Code.

    ```objc
    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.

        self.spinner = [SpinnerViewController alloc];
        [self.spinner startWithContainer:self];

        self.userProfilePhoto.image = [UIImage imageNamed:@"DefaultUserPhoto"];

        [GraphManager.instance
         getMeWithCompletionBlock:^(MSGraphUser * _Nullable user, NSError * _Nullable error) {
            dispatch_async(dispatch_get_main_queue(), ^{
                [self.spinner stop];

                if (error) {
                    // Show the error
                    UIAlertController* alert = [UIAlertController
                                                alertControllerWithTitle:@"Error getting user profile"
                                                message:error.debugDescription
                                                preferredStyle:UIAlertControllerStyleAlert];

                    UIAlertAction* okButton = [UIAlertAction
                                               actionWithTitle:@"OK"
                                               style:UIAlertActionStyleDefault
                                               handler:nil];

                    [alert addAction:okButton];
                    [self presentViewController:alert animated:true completion:nil];
                    return;
                }

                // Set display name
                self.userDisplayName.text = user.displayName ? : @"Mysterious Stranger";
                [self.userDisplayName sizeToFit];

                // AAD users have email in the mail attribute
                // Personal accounts have email in the userPrincipalName attribute
                self.userEmail.text = user.mail ? : user.userPrincipalName;
                [self.userEmail sizeToFit];
            });
         }];
    }
    ```

Wenn Sie Ihre Änderungen speichern und die APP jetzt neu starten, wird die Benutzeroberfläche nach der Anmeldung mit dem Anzeigenamen und der e-Mail-Adresse des Benutzers aktualisiert.
