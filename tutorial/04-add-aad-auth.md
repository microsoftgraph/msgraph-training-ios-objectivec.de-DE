<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph abzurufen. Dazu integrieren Sie die [Microsoft Authentication Library (MSAL) für iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) in die Anwendung.

1. Erstellen Sie eine neue **Eigenschaftslistendatei** im **GraphTutorial-Projekt** mit dem Namen **AuthSettings.plist**.
1. Fügen Sie der Datei im Stammverzeichnis die folgenden **Elemente** hinzu.

    | Key | Typ | Wert |
    |-----|------|-------|
    | `AppId` | Zeichenfolge | Die Anwendungs-ID aus dem Azure-Portal |
    | `GraphScopes` | Array | Drei Zeichenfolgenwerte: `User.Read` `MailboxSettings.Read` , und `Calendars.ReadWrite` |

    ![Screenshot der Datei "AuthSettings.plist" in Xcode](./images/auth-settings.png)

> [!IMPORTANT]
> Wenn Sie Quellcodeverwaltung wie Git verwenden, wäre es jetzt ein guter Zeitpunkt, die **Datei "AuthSettings.plist"** aus der Quellcodeverwaltung auszuschließen, um zu verhindern, dass Ihre App-ID versehentlich ins Spiel kommt.

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

In diesem Abschnitt konfigurieren Sie das Projekt für MSAL, erstellen eine Authentifizierungs-Manager-Klasse und aktualisieren die App so, dass sie sich anmeldet und abmeldet.

### <a name="configure-project-for-msal"></a>Konfigurieren des Projekts für MSAL

1. Fügen Sie den Projektfunktionen eine neue Schlüsselbundgruppe hinzu.
    1. Wählen Sie **das GraphTutorial-Projekt** aus, und signieren **& Funktionen.**
    1. Wählen **Sie +Funktion** aus, und doppelklicken Sie dann **auf Schlüsselbundfreigabe.**
    1. Fügen Sie eine Schlüsselbundgruppe mit dem Wert `com.microsoft.adalcache` hinzu.

1. Steuerelement klicken **Sie auf "Info.plist",** und wählen **Sie "Öffnen als"** und dann **"Quellcode" aus.**
1. Fügen Sie Folgendes innerhalb des Elements `<dict>` hinzu.

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

1. Öffnen **Sie "AppDelegate.m",** und fügen Sie die folgende Import-Anweisung am Anfang der Datei hinzu.

    ```objc
    #import <MSAL/MSAL.h>
    ```

1. Fügen Sie die folgende Funktion zur `AppDelegate`-Klasse hinzu:

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AppDelegate.m" id="HandleMsalResponseSnippet":::

### <a name="create-authentication-manager"></a>Erstellen eines Authentifizierungs-Managers

1. Erstellen Sie eine neue **Cocoa Touch-Klasse** im **GraphTutorial-Projekt** mit dem Namen **AuthenticationManager**. Wählen Sie in der Unterklasse des Felds **"NSObject"** aus. 
1. Öffnen **Sie "AuthenticationManager.h",** und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AuthenticationManager.h" id="AuthManagerSnippet":::

1. Öffnen **Sie "AuthenticationManager.m",** und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AuthenticationManager.m" id="AuthManagerSnippet":::

### <a name="add-sign-in-and-sign-out"></a>Hinzufügen von Anmeldung und Abmelden

1. Öffnen Sie **die Datei SignInViewController.m,** und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/SignInViewController.m" id="SignInViewSnippet":::

1. Öffnen **Sie WelcomeViewController.m,** und fügen Sie die folgende Anweisung `import` am Oberen Rand der Datei hinzu.

    ```objc
    #import "AuthenticationManager.h"
    ```

1. Ersetzen Sie die vorhandene `signOut`-Funktion durch Folgendes.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.m" id="SignOutSnippet":::

1. Speichern Sie Ihre Änderungen, und starten Sie die Anwendung im Simulator neu.

Wenn Sie sich bei der App anmelden, sollte ein Zugriffstoken im Ausgabefenster in Xcode angezeigt werden.

![Screenshot des Ausgabefensters in Xcode mit einem Zugriffstoken](./images/access-token-output.png)

## <a name="get-user-details"></a>Benutzerdetails abrufen

In diesem Abschnitt erstellen Sie eine Hilfsklasse für alle Aufrufe von Microsoft Graph und aktualisieren die Klasse so, dass sie diese neue Klasse verwendet, um den angemeldeten `WelcomeViewController` Benutzer zu erhalten.

1. Erstellen Sie eine neue **Cocoa Touch-Klasse** im **GraphTutorial-Projekt** mit dem Namen **GraphManager**. Wählen Sie in der Unterklasse des Felds **"NSObject"** aus. 
1. Öffnen **Sie "GraphManager.h",** und ersetzen Sie den Inhalt durch den folgenden Code.

    ```objc
    #import <Foundation/Foundation.h>
    #import <MSGraphClientSDK/MSGraphClientSDK.h>
    #import <MSGraphClientModels/MSGraphClientModels.h>
    #import <MSGraphClientModels/MSCollection.h>
    #import "AuthenticationManager.h"

    NS_ASSUME_NONNULL_BEGIN

    typedef void (^GetMeCompletionBlock)(MSGraphUser* _Nullable user,
                                         NSError* _Nullable error);

    @interface GraphManager : NSObject

    + (id) instance;
    - (void) getMeWithCompletionBlock: (GetMeCompletionBlock) completion;

    @end

    NS_ASSUME_NONNULL_END
    ```

1. Öffnen **Sie GraphManager.m,** und ersetzen Sie den Inhalt durch den folgenden Code.

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

    - (void) getMeWithCompletionBlock: (GetMeCompletionBlock) completion {
        // GET /me
        NSString* meUrlString = [NSString stringWithFormat:@"%@/me?%@",
                                 MSGraphBaseURL,
                                 @"$select=displayName,mail,mailboxSettings,userPrincipalName"];
        NSURL* meUrl = [[NSURL alloc] initWithString:meUrlString];
        NSMutableURLRequest* meRequest = [[NSMutableURLRequest alloc] initWithURL:meUrl];

        MSURLSessionDataTask* meDataTask =
        [[MSURLSessionDataTask alloc]
            initWithRequest:meRequest
            client:self.graphClient
            completion:^(NSData *data, NSURLResponse *response, NSError *error) {
                if (error) {
                    completion(nil, error);
                    return;
                }

                // Deserialize the response as a user
                NSError* graphError;
                MSGraphUser* user = [[MSGraphUser alloc] initWithData:data error:&graphError];

                if (graphError) {
                    completion(nil, graphError);
                } else {
                    completion(user, nil);
                }
            }];

        // Execute the request
        [meDataTask execute];
    }

    @end
    ```

1. Öffnen **Sie WelcomeViewController.m,** und fügen Sie die folgenden Anweisungen `#import` am Anfang der Datei hinzu.

    ```objc
    #import "SpinnerViewController.h"
    #import "GraphManager.h"
    #import <MSGraphClientModels/MSGraphClientModels.h>
    ```

1. Fügen Sie der Schnittstellendeklaration die folgende `WelcomeViewController` Eigenschaft hinzu.

    ```objc
    @property SpinnerViewController* spinner;
    ```

1. Ersetzen Sie das vorhandene `viewDidLoad` durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.m" id="ViewDidLoadSnippet":::

Wenn Sie Ihre Änderungen speichern und die App jetzt neu starten, wird die Benutzeroberfläche nach der Anmeldung mit dem Anzeigenamen und der E-Mail-Adresse des Benutzers aktualisiert.
