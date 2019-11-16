<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ffdbf-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="ffdbf-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="ffdbf-103">Hierzu integrieren Sie die [Microsoft-Authentifizierungsbibliothek (MSAL) für IOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) in die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) into the application.</span></span>

1. <span data-ttu-id="ffdbf-104">Erstellen Sie eine neue **Eigenschaftenlisten** Datei im **GraphTutorial** -Projekt mit dem Namen **AuthSettings. plist**.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-104">Create a new **Property List** file in the **GraphTutorial** project named **AuthSettings.plist**.</span></span>
1. <span data-ttu-id="ffdbf-105">Fügen Sie die folgenden Elemente zur Datei im **Stamm** Wörterbuch hinzu.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-105">Add the following items to the file in the **Root** dictionary.</span></span>

    | <span data-ttu-id="ffdbf-106">Key</span><span class="sxs-lookup"><span data-stu-id="ffdbf-106">Key</span></span> | <span data-ttu-id="ffdbf-107">Typ</span><span class="sxs-lookup"><span data-stu-id="ffdbf-107">Type</span></span> | <span data-ttu-id="ffdbf-108">Wert</span><span class="sxs-lookup"><span data-stu-id="ffdbf-108">Value</span></span> |
    |-----|------|-------|
    | `AppId` | <span data-ttu-id="ffdbf-109">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="ffdbf-109">String</span></span> | <span data-ttu-id="ffdbf-110">Die Anwendungs-ID aus dem Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="ffdbf-110">The application ID from the Azure portal</span></span> |
    | `GraphScopes` | <span data-ttu-id="ffdbf-111">Array</span><span class="sxs-lookup"><span data-stu-id="ffdbf-111">Array</span></span> | <span data-ttu-id="ffdbf-112">Zwei Zeichenfolgenwerte `User.Read` : und`Calendars.Read`</span><span class="sxs-lookup"><span data-stu-id="ffdbf-112">Two String values: `User.Read` and `Calendars.Read`</span></span> |

    ![Ein Screenshot der Datei AuthSettings. plist in Xcode](./images/auth-settings.png)

> [!IMPORTANT]
> <span data-ttu-id="ffdbf-114">Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es nun ein guter Zeitpunkt, die Datei **AuthSettings. plist** aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-114">If you're using source control such as git, now would be a good time to exclude the **AuthSettings.plist** file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="ffdbf-115">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="ffdbf-115">Implement sign-in</span></span>

<span data-ttu-id="ffdbf-116">In diesem Abschnitt Konfigurieren Sie das Projekt für MSAL, erstellen eine Authentifizierungs-Manager-Klasse und aktualisieren die APP, um sich anzumelden und abzumelden.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-116">In this section you will configure the project for MSAL, create an authentication manager class, and update the app to sign in and sign out.</span></span>

### <a name="configure-project-for-msal"></a><span data-ttu-id="ffdbf-117">Konfigurieren des Projekts für MSAL</span><span class="sxs-lookup"><span data-stu-id="ffdbf-117">Configure project for MSAL</span></span>

1. <span data-ttu-id="ffdbf-118">Fügen Sie der Projektfunktion eine neue Schlüsselbund Gruppe hinzu.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-118">Add a new keychain group to your project's capabilities.</span></span>
    1. <span data-ttu-id="ffdbf-119">Wählen Sie das **GraphTutorial** -Projekt aus, und **Signieren Sie #a0 Funktionen**.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-119">Select the **GraphTutorial** project, then **Signing & Capabilities**.</span></span>
    1. <span data-ttu-id="ffdbf-120">Wählen Sie **+-Funktion**aus, und doppelklicken Sie dann auf **Schlüsselbund Freigabe**.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-120">Select **+ Capability**, then double-click **Keychain Sharing**.</span></span>
    1. <span data-ttu-id="ffdbf-121">Fügen Sie eine Schlüsselbund Gruppe mit `com.microsoft.adalcache`dem Wert hinzu.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-121">Add a keychain group with the value `com.microsoft.adalcache`.</span></span>

1. <span data-ttu-id="ffdbf-122">Steuerelement klicken Sie auf **Info. plist** , und wählen Sie dann **Öffnen als**, dann **Quellcode**aus.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-122">Control click **Info.plist** and select **Open As**, then **Source Code**.</span></span>
1. <span data-ttu-id="ffdbf-123">Fügen Sie das folgende innerhalb `<dict>` des-Elements hinzu.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-123">Add the following inside the `<dict>` element.</span></span>

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

1. <span data-ttu-id="ffdbf-124">Öffnen Sie **AppDelegate. m** , und fügen Sie die folgende Importanweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-124">Open **AppDelegate.m** and add the following import statement at the top of the file.</span></span>

    ```objc
    #import <MSAL/MSAL.h>
    ```

1. <span data-ttu-id="ffdbf-125">Fügen Sie die folgende Funktion zur `AppDelegate`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="ffdbf-125">Add the following function to the `AppDelegate` class.</span></span>

    ```objc
    - (BOOL)application:(UIApplication *)app
                openURL:(NSURL *)url
                options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url
                                             sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```

### <a name="create-authentication-manager"></a><span data-ttu-id="ffdbf-126">Erstellen des Authentifizierungs-Managers</span><span class="sxs-lookup"><span data-stu-id="ffdbf-126">Create authentication manager</span></span>

1. <span data-ttu-id="ffdbf-127">Erstellen Sie eine neue **Cocoa Touch-Klasse** im **GraphTutorial** -Projekt mit dem Namen **AuthenticationManager**.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-127">Create a new **Cocoa Touch Class** in the **GraphTutorial** project named **AuthenticationManager**.</span></span> <span data-ttu-id="ffdbf-128">Wählen Sie **NSObject** in der unter **Klasse von Field aus** .</span><span class="sxs-lookup"><span data-stu-id="ffdbf-128">Choose **NSObject** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="ffdbf-129">Öffnen Sie **AuthenticationManager. h** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-129">Open **AuthenticationManager.h** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="ffdbf-130">Öffnen Sie **AuthenticationManager. m** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-130">Open **AuthenticationManager.m** and replace its contents with the following code.</span></span>

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

### <a name="add-sign-in-and-sign-out"></a><span data-ttu-id="ffdbf-131">Hinzufügen von Anmelde-und Abmeldungen</span><span class="sxs-lookup"><span data-stu-id="ffdbf-131">Add sign-in and sign-out</span></span>

1. <span data-ttu-id="ffdbf-132">Öffnen Sie die Datei **SignInViewController. m** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-132">Open the **SignInViewController.m** file and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="ffdbf-133">Öffnen Sie **WelcomeViewController. m** , und ersetzen `signOut` Sie die vorhandene Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-133">Open **WelcomeViewController.m** and replace the existing `signOut` function with the following.</span></span>

    ```objc
    - (IBAction)signOut {
        [AuthenticationManager.instance signOut];
        [self performSegueWithIdentifier: @"userSignedOut" sender: nil];
    }
    ```

1. <span data-ttu-id="ffdbf-134">Speichern Sie Ihre Änderungen, und starten Sie die Anwendung im Simulator neu.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-134">Save your changes and restart the application in Simulator.</span></span>

<span data-ttu-id="ffdbf-135">Wenn Sie sich bei der App anmelden, sollten Sie ein Zugriffstoken sehen, das im Ausgabefenster von Xcode angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-135">If you sign in to the app, you should see an access token displayed in the output window in Xcode.</span></span>

![Screenshot des Ausgabefensters in Xcode mit einem Zugriffstoken](./images/access-token-output.png)

## <a name="get-user-details"></a><span data-ttu-id="ffdbf-137">Abrufen von Benutzer Details</span><span class="sxs-lookup"><span data-stu-id="ffdbf-137">Get user details</span></span>

<span data-ttu-id="ffdbf-138">In diesem Abschnitt erstellen Sie eine Hilfsklasse, die alle Aufrufe von Microsoft Graph enthält, und aktualisieren Sie `WelcomeViewController` , um die neue Klasse zum Abrufen des angemeldeten Benutzers zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-138">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `WelcomeViewController` to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="ffdbf-139">Erstellen Sie eine neue **Cocoa Touch-Klasse** im **GraphTutorial** -Projekt mit dem Namen **graphmanager**.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-139">Create a new **Cocoa Touch Class** in the **GraphTutorial** project named **GraphManager**.</span></span> <span data-ttu-id="ffdbf-140">Wählen Sie **NSObject** in der unter **Klasse von Field aus** .</span><span class="sxs-lookup"><span data-stu-id="ffdbf-140">Choose **NSObject** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="ffdbf-141">Öffnen Sie **graphmanager. h** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-141">Open **GraphManager.h** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="ffdbf-142">Öffnen Sie **graphmanager. m** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-142">Open **GraphManager.m** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="ffdbf-143">Öffnen Sie **WelcomeViewController. m** , und fügen `#import` Sie die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-143">Open **WelcomeViewController.m** and add the following `#import` statements at the top of the file.</span></span>

    ```objc
    #import "SpinnerViewController.h"
    #import "GraphManager.h"
    #import <MSGraphClientModels/MSGraphClientModels.h>
    ```

1. <span data-ttu-id="ffdbf-144">Fügen Sie die folgende Eigenschaft zur `WelcomeViewController` Schnittstellendeklaration hinzu.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-144">Add the following property to the `WelcomeViewController` interface declaration.</span></span>

    ```objc
    @property SpinnerViewController* spinner;
    ```

1. <span data-ttu-id="ffdbf-145">Ersetzen Sie den `viewDidLoad` vorhandenen durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-145">Replace the existing `viewDidLoad` with the following code.</span></span>

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

<span data-ttu-id="ffdbf-146">Wenn Sie Ihre Änderungen speichern und die APP jetzt neu starten, wird die Benutzeroberfläche nach der Anmeldung mit dem Anzeigenamen und der e-Mail-Adresse des Benutzers aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="ffdbf-146">If you save your changes and restart the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
