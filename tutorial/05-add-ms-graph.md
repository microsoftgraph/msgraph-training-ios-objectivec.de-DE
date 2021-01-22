<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5a034-101">In dieser Übung integrieren Sie Microsoft Graph in die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="5a034-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="5a034-102">Für diese Anwendung verwenden Sie das [Microsoft Graph SDK für Objective C,](https://github.com/microsoftgraph/msgraph-sdk-objc) um Aufrufe an Microsoft Graph zu senden.</span><span class="sxs-lookup"><span data-stu-id="5a034-102">For this application, you will use the [Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="5a034-103">Abrufen von Kalenderereignissen von Outlook</span><span class="sxs-lookup"><span data-stu-id="5a034-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="5a034-104">In diesem Abschnitt erweitern Sie die Klasse, um eine Funktion hinzuzufügen, um die Ereignisse des Benutzers für die aktuelle Woche zu erhalten, und aktualisieren, um diese `GraphManager` `CalendarViewController` neue Funktion zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="5a034-104">In this section you will extend the `GraphManager` class to add a function to get the user's events for the current week and update `CalendarViewController` to use this new function.</span></span>

1. <span data-ttu-id="5a034-105">Öffnen **Sie GraphManager.h,** und fügen Sie den folgenden Code über der `@interface` Deklaration hinzu.</span><span class="sxs-lookup"><span data-stu-id="5a034-105">Open **GraphManager.h** and add the following code above the `@interface` declaration.</span></span>

    ```objc
    typedef void (^GetCalendarViewCompletionBlock)(NSData* _Nullable data,
                                                   NSError* _Nullable error);
    ```

1. <span data-ttu-id="5a034-106">Fügen Sie der Deklaration den folgenden Code `@interface` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5a034-106">Add the following code to the `@interface` declaration.</span></span>

    ```objc
    - (void) getCalendarViewStartingAt: (NSString*) viewStart
                              endingAt: (NSString*) viewEnd
                   withCompletionBlock: (GetCalendarViewCompletionBlock) completion;
    ```

1. <span data-ttu-id="5a034-107">Öffnen **Sie GraphManager.m,** und fügen Sie der Klasse die folgende Funktion `GraphManager` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5a034-107">Open **GraphManager.m** and add the following function to the `GraphManager` class.</span></span>

    ```objc
    - (void) getCalendarViewStartingAt: (NSString *) viewStart
                              endingAt: (NSString *) viewEnd
                   withCompletionBlock: (GetCalendarViewCompletionBlock) completion {
        // Set calendar view start and end parameters
        NSString* viewStartEndString =
        [NSString stringWithFormat:@"startDateTime=%@&endDateTime=%@",
         viewStart,
         viewEnd];

        // GET /me/calendarview
        NSString* eventsUrlString =
        [NSString stringWithFormat:@"%@/me/calendarview?%@&%@&%@&%@",
         MSGraphBaseURL,
         viewStartEndString,
         // Only return these fields in results
         @"$select=subject,organizer,start,end",
         // Sort results by start time
         @"$orderby=start/dateTime",
         // Request at most 25 results
         @"$top=25"];

        NSURL* eventsUrl = [[NSURL alloc] initWithString:eventsUrlString];
        NSMutableURLRequest* eventsRequest = [[NSMutableURLRequest alloc] initWithURL:eventsUrl];

        // Add the Prefer: outlook.timezone header to get start and end times
        // in user's time zone
        NSString* preferHeader =
        [NSString stringWithFormat:@"outlook.timezone=\"%@\"",
         self.graphTimeZone];
        [eventsRequest addValue:preferHeader forHTTPHeaderField:@"Prefer"];

        MSURLSessionDataTask* eventsDataTask =
        [[MSURLSessionDataTask alloc]
         initWithRequest:eventsRequest
         client:self.graphClient
         completion:^(NSData *data, NSURLResponse *response, NSError *error) {
             if (error) {
                 completion(nil, error);
                 return;
             }

             // TEMPORARY
             completion(data, nil);
         }];

        // Execute the request
        [eventsDataTask execute];
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="5a034-108">Überlegen Sie, was der Code `getCalendarViewStartingAt` gerade macht.</span><span class="sxs-lookup"><span data-stu-id="5a034-108">Consider what the code in `getCalendarViewStartingAt` is doing.</span></span>
    >
    > - <span data-ttu-id="5a034-109">Die URL, die aufgerufen wird, lautet `/v1.0/me/calendarview`.</span><span class="sxs-lookup"><span data-stu-id="5a034-109">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
    >   - <span data-ttu-id="5a034-110">Die `startDateTime` Parameter `endDateTime` und Abfrageparameter definieren den Anfang und das Ende der Kalenderansicht.</span><span class="sxs-lookup"><span data-stu-id="5a034-110">The `startDateTime` and `endDateTime` query parameters define the start and end of the calendar view.</span></span>
    >   - <span data-ttu-id="5a034-111">Der Abfrageparameter beschränkt die für jedes Ereignis zurückgegebenen Felder auf die Felder, die `select` tatsächlich von der Ansicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5a034-111">The `select` query parameter limits the fields returned for each events to just those the view will actually use.</span></span>
    >   - <span data-ttu-id="5a034-112">Der `orderby` Abfrageparameter sortiert die Ergebnisse nach Startzeit.</span><span class="sxs-lookup"><span data-stu-id="5a034-112">The `orderby` query parameter sorts the results by start time.</span></span>
    >   - <span data-ttu-id="5a034-113">Der `top` Abfrageparameter fordert 25 Ergebnisse pro Seite an.</span><span class="sxs-lookup"><span data-stu-id="5a034-113">The `top` query parameter requests 25 results per page.</span></span>
    >   - <span data-ttu-id="5a034-114">Der Header bewirkt, dass Microsoft Graph die Start- und Endzeiten der einzelnen Ereignisse in der Zeitzone des `Prefer: outlook.timezone` Benutzers zurück gibt.</span><span class="sxs-lookup"><span data-stu-id="5a034-114">the `Prefer: outlook.timezone` header causes the Microsoft Graph to return the start and end times of each event in the user's time zone.</span></span>

1. <span data-ttu-id="5a034-115">Erstellen Sie eine neue **Cocoa Touch-Klasse** im **GraphTutorial-Projekt** mit dem Namen **GraphToIana**.</span><span class="sxs-lookup"><span data-stu-id="5a034-115">Create a new **Cocoa Touch Class** in the **GraphTutorial** project named **GraphToIana**.</span></span> <span data-ttu-id="5a034-116">Wählen Sie in der Unterklasse des Felds **"NSObject"** aus. </span><span class="sxs-lookup"><span data-stu-id="5a034-116">Choose **NSObject** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="5a034-117">Öffnen **Sie GraphToIana.h,** und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="5a034-117">Open **GraphToIana.h** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphToIana.h" id="GraphToIanaSnippet":::

1. <span data-ttu-id="5a034-118">Öffnen **Sie GraphToIana.m,** und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="5a034-118">Open **GraphToIana.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphToIana.m" id="GraphToIanaSnippet":::

    <span data-ttu-id="5a034-119">Dies führt eine einfache Suche durch, um einen IANA-Zeitzonenbezeichner basierend auf dem von Microsoft Graph zurückgegebenen Zeitzonennamen zu finden.</span><span class="sxs-lookup"><span data-stu-id="5a034-119">This does a simple lookup to find an IANA time zone identifier based on the time zone name returned by Microsoft Graph.</span></span>

1. <span data-ttu-id="5a034-120">Öffnen **Sie CalendarViewController.m,** und ersetzen Sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="5a034-120">Open **CalendarViewController.m** and replace its entire contents with the following code.</span></span>

    ```objc
    #import "CalendarViewController.h"
    #import "SpinnerViewController.h"
    #import "GraphManager.h"
    #import "GraphToIana.h"
    #import <MSGraphClientModels/MSGraphClientModels.h>

    @interface CalendarViewController ()

    @property SpinnerViewController* spinner;

    @end

    @implementation CalendarViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.

        self.spinner = [SpinnerViewController alloc];
        [self.spinner startWithContainer:self];

        // Calculate the start and end of the current week
        NSString* timeZoneId = [GraphToIana
                                getIanaIdentifierFromGraphIdentifier:
                                [GraphManager.instance graphTimeZone]];

        NSDate* now = [NSDate date];
        NSCalendar* calendar = [NSCalendar calendarWithIdentifier:NSCalendarIdentifierGregorian];
        NSTimeZone* timeZone = [NSTimeZone timeZoneWithName:timeZoneId];
        [calendar setTimeZone:timeZone];

        NSDateComponents* startOfWeekComponents = [calendar
                                                   components:NSCalendarUnitCalendar |
                                                   NSCalendarUnitYearForWeekOfYear |
                                                   NSCalendarUnitWeekOfYear
                                                   fromDate:now];
        NSDate* startOfWeek = [startOfWeekComponents date];
        NSDate* endOfWeek = [calendar dateByAddingUnit:NSCalendarUnitDay
                                                 value:7
                                                toDate:startOfWeek
                                               options:0];

        // Convert start and end to ISO 8601 strings
        NSISO8601DateFormatter* isoFormatter = [[NSISO8601DateFormatter alloc] init];
        NSString* viewStart = [isoFormatter stringFromDate:startOfWeek];
        NSString* viewEnd = [isoFormatter stringFromDate:endOfWeek];

        [GraphManager.instance
         getCalendarViewStartingAt:viewStart
         endingAt:viewEnd
         withCompletionBlock:^(NSData * _Nullable data, NSError * _Nullable error) {
             dispatch_async(dispatch_get_main_queue(), ^{
                 [self.spinner stop];

                 if (error) {
                     // Show the error
                     UIAlertController* alert = [UIAlertController
                                                 alertControllerWithTitle:@"Error getting events"
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

                 // TEMPORARY
                 self.calendarJSON.text = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                 [self.calendarJSON sizeToFit];
             });
         }];
    }

    @end
    ```

1. <span data-ttu-id="5a034-121">Führen Sie die App aus, melden Sie sich an, und tippen Sie im Menü auf das **Navigationselement** "Kalender".</span><span class="sxs-lookup"><span data-stu-id="5a034-121">Run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="5a034-122">Es sollte ein JSON-Dump der Ereignisse in der App angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="5a034-122">You should see a JSON dump of the events in the app.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="5a034-123">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="5a034-123">Display the results</span></span>

<span data-ttu-id="5a034-124">Jetzt können Sie das JSON-Dump durch eine benutzerfreundliche Anzeige der Ergebnisse ersetzen.</span><span class="sxs-lookup"><span data-stu-id="5a034-124">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="5a034-125">In diesem Abschnitt ändern Sie die Funktion so, dass stark typierte Objekte zurückgeben werden, und ändern sie so, dass zum Rendern der Ereignisse eine `getCalendarViewStartingAt` `CalendarViewController` Tabellenansicht verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5a034-125">In this section, you will modify the `getCalendarViewStartingAt` function to return strongly-typed objects, and modify `CalendarViewController` to use a table view to render the events.</span></span>

### <a name="update-getcalendarviewstartingat"></a><span data-ttu-id="5a034-126">GetCalendarViewStartingAt aktualisieren</span><span class="sxs-lookup"><span data-stu-id="5a034-126">Update getCalendarViewStartingAt</span></span>

1. <span data-ttu-id="5a034-127">Öffnen **Sie GraphManager.h**.</span><span class="sxs-lookup"><span data-stu-id="5a034-127">Open **GraphManager.h**.</span></span> <span data-ttu-id="5a034-128">Ändern Sie `GetCalendarViewCompletionBlock` die Typdefinition wie folgt.</span><span class="sxs-lookup"><span data-stu-id="5a034-128">Change the `GetCalendarViewCompletionBlock` type definition to the following.</span></span>

    ```objc
    typedef void (^GetCalendarViewCompletionBlock)(NSArray<MSGraphEvent*>* _Nullable events, NSError* _Nullable error);
    ```

1. <span data-ttu-id="5a034-129">Öffnen **Sie GraphManager.m**.</span><span class="sxs-lookup"><span data-stu-id="5a034-129">Open **GraphManager.m**.</span></span> <span data-ttu-id="5a034-130">Ersetzen Sie die vorhandene `getCalendarViewStartingAt`-Funktion durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="5a034-130">Replace the existing `getCalendarViewStartingAt` function with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphManager.m" id="GetCalendarViewSnippet" highlight="42-61":::

### <a name="update-calendarviewcontroller"></a><span data-ttu-id="5a034-131">Aktualisieren von CalendarViewController</span><span class="sxs-lookup"><span data-stu-id="5a034-131">Update CalendarViewController</span></span>

1. <span data-ttu-id="5a034-132">Erstellen Sie eine neue **Cocoa Touch Class-Datei** im **GraphTutorial-Projekt** mit dem Namen `CalendarTableViewCell` .</span><span class="sxs-lookup"><span data-stu-id="5a034-132">Create a new **Cocoa Touch Class** file in the **GraphTutorial** project named `CalendarTableViewCell`.</span></span> <span data-ttu-id="5a034-133">Wählen **Sie "UITableViewCell"** in der **Unterklasse des Felds** aus.</span><span class="sxs-lookup"><span data-stu-id="5a034-133">Choose **UITableViewCell** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="5a034-134">Öffnen **Sie CalendarTableViewCell.h,** und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="5a034-134">Open **CalendarTableViewCell.h** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.h" id="CalendarTableCellSnippet":::

1. <span data-ttu-id="5a034-135">Öffnen **Sie CalendarTableViewCell.m,** und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="5a034-135">Open **CalendarTableViewCell.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.m" id="CalendarTableCellSnippet":::

1. <span data-ttu-id="5a034-136">Erstellen Sie eine neue **Cocoa Touch Class-Datei** im **GraphTutorial-Projekt** mit dem Namen `CalendarTableViewController` .</span><span class="sxs-lookup"><span data-stu-id="5a034-136">Create a new **Cocoa Touch Class** file in the **GraphTutorial** project named `CalendarTableViewController`.</span></span> <span data-ttu-id="5a034-137">Wählen **Sie "UITableViewController"** in der **Unterklasse des Felds** aus.</span><span class="sxs-lookup"><span data-stu-id="5a034-137">Choose **UITableViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="5a034-138">Öffnen **Sie CalendarTableViewController.h,** und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="5a034-138">Open **CalendarTableViewController.h** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewController.h" id="CalendarTableViewControllerSnippet":::

1. <span data-ttu-id="5a034-139">Öffnen **Sie CalendarTableViewController.m,** und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="5a034-139">Open **CalendarTableViewController.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewController.m" id="CalendarTableViewControllerSnippet":::

1. <span data-ttu-id="5a034-140">Öffnen **Sie Main.storyboard,** und suchen Sie **die Kalender-Szene.**</span><span class="sxs-lookup"><span data-stu-id="5a034-140">Open **Main.storyboard** and locate the **Calendar Scene**.</span></span> <span data-ttu-id="5a034-141">Löschen Sie die Bildlaufansicht aus der Stammansicht.</span><span class="sxs-lookup"><span data-stu-id="5a034-141">Delete the scroll view from the root view.</span></span>
1. <span data-ttu-id="5a034-142">Fügen Sie **in der Bibliothek** oben in der **Ansicht** eine Navigationsleiste hinzu.</span><span class="sxs-lookup"><span data-stu-id="5a034-142">Using the **Library**, add a **Navigation Bar** to the top of the view.</span></span>
1. <span data-ttu-id="5a034-143">Doppelklicken Sie auf **den Titel** in der Navigationsleiste, und aktualisieren Sie ihn in `Calendar` .</span><span class="sxs-lookup"><span data-stu-id="5a034-143">Double-click the **Title** in the navigation bar and update it to `Calendar`.</span></span>
1. <span data-ttu-id="5a034-144">Fügen Sie **in der Bibliothek** rechts neben der Navigationsleiste ein Balkenschaltflächeelement hinzu. </span><span class="sxs-lookup"><span data-stu-id="5a034-144">Using the **Library**, add a **Bar Button Item** to the right-hand side of the navigation bar.</span></span>
1. <span data-ttu-id="5a034-145">Wählen Sie die Schaltfläche für die neue Leiste und dann den **Attributprüfungsinspektor aus.**</span><span class="sxs-lookup"><span data-stu-id="5a034-145">Select the new bar button, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="5a034-146">Bild **in** Plus **ändern.**</span><span class="sxs-lookup"><span data-stu-id="5a034-146">Change **Image** to **plus**.</span></span>
1. <span data-ttu-id="5a034-147">Fügen Sie der  **Ansicht unter der Navigationsleiste** eine Containeransicht aus der Bibliothek hinzu.</span><span class="sxs-lookup"><span data-stu-id="5a034-147">Add a **Container View** from the **Library** to the view under the navigation bar.</span></span> <span data-ttu-id="5a034-148">Ändern Sie die Größe der Containeransicht, um den verbleibenden Platz in der Ansicht zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="5a034-148">Resize the container view to take all of the remaining space in the view.</span></span>
1. <span data-ttu-id="5a034-149">Legen Sie Einschränkungen für die Navigationsleiste und die Containeransicht wie folgt ein.</span><span class="sxs-lookup"><span data-stu-id="5a034-149">Set constraints on the navigation bar and container view as follows.</span></span>

    - <span data-ttu-id="5a034-150">**Navigationsleiste**</span><span class="sxs-lookup"><span data-stu-id="5a034-150">**Navigation Bar**</span></span>
        - <span data-ttu-id="5a034-151">Einschränkung hinzufügen: Höhe, Wert: 44</span><span class="sxs-lookup"><span data-stu-id="5a034-151">Add constraint: Height, value: 44</span></span>
        - <span data-ttu-id="5a034-152">Einschränkung hinzufügen: Führendes Leerzeichen zum sicheren Bereich, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-152">Add constraint: Leading space to Safe Area, value: 0</span></span>
        - <span data-ttu-id="5a034-153">Einschränkung hinzufügen: Nachgestellter Speicherplatz zum sicheren Bereich, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-153">Add constraint: Trailing space to Safe Area, value: 0</span></span>
        - <span data-ttu-id="5a034-154">Einschränkung hinzufügen: Oberster Platz zum sicheren Bereich, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-154">Add constraint: Top space to Safe Area, value: 0</span></span>
    - <span data-ttu-id="5a034-155">**Containeransicht**</span><span class="sxs-lookup"><span data-stu-id="5a034-155">**Container View**</span></span>
        - <span data-ttu-id="5a034-156">Einschränkung hinzufügen: Führendes Leerzeichen zum sicheren Bereich, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-156">Add constraint: Leading space to Safe Area, value: 0</span></span>
        - <span data-ttu-id="5a034-157">Einschränkung hinzufügen: Nachgestellter Speicherplatz zum sicheren Bereich, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-157">Add constraint: Trailing space to Safe Area, value: 0</span></span>
        - <span data-ttu-id="5a034-158">Einschränkung hinzufügen: Oberster Platz auf der Navigationsleiste unten, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-158">Add constraint: Top space to Navigation Bar Bottom, value: 0</span></span>
        - <span data-ttu-id="5a034-159">Einschränkung hinzufügen: Unterer Platz zum sicheren Bereich, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-159">Add constraint: Bottom space to Safe Area, value: 0</span></span>

1. <span data-ttu-id="5a034-160">Suchen Sie den zweiten Ansichtscontroller, der dem Storyboard hinzugefügt wurde, wenn Sie die Containeransicht hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="5a034-160">Locate the second view controller added to the storyboard when you added the container view.</span></span> <span data-ttu-id="5a034-161">Es wird durch eine **Einbettungs-Segue** mit der Kalenderszene verbunden.</span><span class="sxs-lookup"><span data-stu-id="5a034-161">It is connected to the **Calendar Scene** by an embed segue.</span></span> <span data-ttu-id="5a034-162">Wählen Sie diesen Controller aus, und verwenden Sie den **Identitätsinspektor,** um **die Klasse in** **CalendarTableViewController zu ändern.**</span><span class="sxs-lookup"><span data-stu-id="5a034-162">Select this controller and use the **Identity Inspector** to change **Class** to **CalendarTableViewController**.</span></span>
1. <span data-ttu-id="5a034-163">Löschen Sie **die Ansicht** aus dem **Kalender tabellenansichtscontroller**.</span><span class="sxs-lookup"><span data-stu-id="5a034-163">Delete the **View** from the **Calendar Table View Controller**.</span></span>
1. <span data-ttu-id="5a034-164">Fügen Sie dem  **Tabellenansichtscontroller** des Kalenders eine Tabellenansicht aus der **Bibliothek hinzu.**</span><span class="sxs-lookup"><span data-stu-id="5a034-164">Add a **Table View** from the **Library** to the **Calendar Table View Controller**.</span></span>
1. <span data-ttu-id="5a034-165">Wählen Sie die Tabellenansicht und dann den **Attributes Inspector aus.**</span><span class="sxs-lookup"><span data-stu-id="5a034-165">Select the table view, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="5a034-166">Set **Prototype Cells** to **1**.</span><span class="sxs-lookup"><span data-stu-id="5a034-166">Set **Prototype Cells** to **1**.</span></span>
1. <span data-ttu-id="5a034-167">Ziehen Sie den unteren Rand der Prototypzelle, um Ihnen einen größeren Bereich für die Arbeit zu bieten.</span><span class="sxs-lookup"><span data-stu-id="5a034-167">Drag the bottom edge of the prototype cell to give you a larger area to work with.</span></span>
1. <span data-ttu-id="5a034-168">Verwenden Sie die **Bibliothek,** um **der** Prototypzelle drei Beschriftungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="5a034-168">Use the **Library** to add three **Labels** to the prototype cell.</span></span>
1. <span data-ttu-id="5a034-169">Wählen Sie die Prototypzelle und dann den **Identitätsinspektor aus.**</span><span class="sxs-lookup"><span data-stu-id="5a034-169">Select the prototype cell, then select the **Identity Inspector**.</span></span> <span data-ttu-id="5a034-170">Ändern **Sie die Klasse** in **CalendarTableViewCell**.</span><span class="sxs-lookup"><span data-stu-id="5a034-170">Change **Class** to **CalendarTableViewCell**.</span></span>
1. <span data-ttu-id="5a034-171">Wählen Sie den **Attributes Inspector aus,** und legen **Sie den Bezeichner auf** . `EventCell`</span><span class="sxs-lookup"><span data-stu-id="5a034-171">Select the **Attributes Inspector** and set **Identifier** to `EventCell`.</span></span>
1. <span data-ttu-id="5a034-172">Wenn Die **EventCell ausgewählt ist,** wählen Sie den **Connections Inspector** und verbinden , und mit den Beschriftungen, die Sie der Zelle `durationLabel` im `organizerLabel` `subjectLabel` Storyboard hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="5a034-172">With the **EventCell** selected, select the **Connections Inspector** and connect `durationLabel`, `organizerLabel`, and `subjectLabel` to the labels you added to the cell on the storyboard.</span></span>
1. <span data-ttu-id="5a034-173">Legen Sie die Eigenschaften und Einschränkungen für die drei Beschriftungen wie folgt dar.</span><span class="sxs-lookup"><span data-stu-id="5a034-173">Set the properties and constraints on the three labels as follows.</span></span>

    - <span data-ttu-id="5a034-174">**Betreffbezeichnung**</span><span class="sxs-lookup"><span data-stu-id="5a034-174">**Subject Label**</span></span>
        - <span data-ttu-id="5a034-175">Einschränkung hinzufügen: Führendes Leerzeichen zum führenden Rand der Inhaltsansicht, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-175">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="5a034-176">Einschränkung hinzufügen: Nachgestellter Speicherplatz zum nachgestellten Rand der Inhaltsansicht, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-176">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="5a034-177">Einschränkung hinzufügen: Oberster Platz am oberen Rand der Inhaltsansicht, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-177">Add constraint: Top space to Content View Top Margin, value: 0</span></span>
    - <span data-ttu-id="5a034-178">**Bezeichnung des Organisators**</span><span class="sxs-lookup"><span data-stu-id="5a034-178">**Organizer Label**</span></span>
        - <span data-ttu-id="5a034-179">Schriftart: System 12.0</span><span class="sxs-lookup"><span data-stu-id="5a034-179">Font: System 12.0</span></span>
        - <span data-ttu-id="5a034-180">Einschränkung hinzufügen: Höhe, Wert: 15</span><span class="sxs-lookup"><span data-stu-id="5a034-180">Add constraint: Height, value: 15</span></span>
        - <span data-ttu-id="5a034-181">Einschränkung hinzufügen: Führendes Leerzeichen zum führenden Rand der Inhaltsansicht, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-181">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="5a034-182">Einschränkung hinzufügen: Nachgestellter Speicherplatz zum nachgestellten Rand der Inhaltsansicht, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-182">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="5a034-183">Einschränkung hinzufügen: Oberster Platz im unteren Bereich der Betreffbezeichnung, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="5a034-183">Add constraint: Top space to Subject Label Bottom, value: Standard</span></span>
    - <span data-ttu-id="5a034-184">**Bezeichnung "Dauer"**</span><span class="sxs-lookup"><span data-stu-id="5a034-184">**Duration Label**</span></span>
        - <span data-ttu-id="5a034-185">Schriftart: System 12.0</span><span class="sxs-lookup"><span data-stu-id="5a034-185">Font: System 12.0</span></span>
        - <span data-ttu-id="5a034-186">Farbe: Dunkelgrau</span><span class="sxs-lookup"><span data-stu-id="5a034-186">Color: Dark Gray Color</span></span>
        - <span data-ttu-id="5a034-187">Einschränkung hinzufügen: Höhe, Wert: 15</span><span class="sxs-lookup"><span data-stu-id="5a034-187">Add constraint: Height, value: 15</span></span>
        - <span data-ttu-id="5a034-188">Einschränkung hinzufügen: Führendes Leerzeichen zum führenden Rand der Inhaltsansicht, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-188">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="5a034-189">Einschränkung hinzufügen: Nachgestellter Speicherplatz zum nachgestellten Rand der Inhaltsansicht, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-189">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="5a034-190">Einschränkung hinzufügen: Oberster Platz am unteren Rand der Bezeichnung "Organizer", Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="5a034-190">Add constraint: Top space to Organizer Label Bottom, value: Standard</span></span>
        - <span data-ttu-id="5a034-191">Einschränkung hinzufügen: Unterer Bereich zum unteren Rand der Inhaltsansicht, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="5a034-191">Add constraint: Bottom space to Content View Bottom Margin, value: 0</span></span>

1. <span data-ttu-id="5a034-192">Select the **EventCell,** then select the **Size Inspector**.</span><span class="sxs-lookup"><span data-stu-id="5a034-192">Select the **EventCell**, then select the **Size Inspector**.</span></span> <span data-ttu-id="5a034-193">Aktivieren **Sie "Automatisch** für **Zeilenhöhe".**</span><span class="sxs-lookup"><span data-stu-id="5a034-193">Enable **Automatic** for **Row Height**.</span></span>

    ![Screenshot der Ansichtscontroller für Kalender- und Kalendertabelle](images/calendar-table-storyboard.png)

1. <span data-ttu-id="5a034-195">Öffnen **Sie CalendarViewController.h,** und entfernen Sie die `calendarJSON` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="5a034-195">Open **CalendarViewController.h** and remove the `calendarJSON` property.</span></span>

1. <span data-ttu-id="5a034-196">Öffnen **Sie CalendarViewController.m,** und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="5a034-196">Open **CalendarViewController.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarViewController.m" id="CalendarViewControllerSnippet":::

1. <span data-ttu-id="5a034-197">Führen Sie die App aus, melden Sie sich an, und tippen Sie auf die **Registerkarte "Kalender".** Die Liste der Ereignisse sollte angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="5a034-197">Run the app, sign in, and tap the **Calendar** tab. You should see the list of events.</span></span>

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/calendar-list.png)
