<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="60f4c-101">In diesem Abschnitt fügen Sie die Möglichkeit zum Erstellen von Ereignissen im Kalender des Benutzers hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="60f4c-102">Öffnen **Sie GraphManager.h,** und fügen Sie den folgenden Code über der `@interface` Deklaration hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-102">Open **GraphManager.h** and add the following code above the `@interface` declaration.</span></span>

    ```objc
    typedef void (^CreateEventCompletionBlock)(MSGraphEvent* _Nullable event,
                                               NSError* _Nullable error);
    ```

1. <span data-ttu-id="60f4c-103">Fügen Sie der Deklaration den folgenden Code `@interface` hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-103">Add the following code to the `@interface` declaration.</span></span>

    ```objc
    - (void) createEventWithSubject: (NSString*) subject
                           andStart: (NSDate*) start
                             andEnd: (NSDate*) end
                       andAttendees: (NSArray<NSString*>* _Nullable) attendees
                            andBody: (NSString* _Nullable) body
                 andCompletionBlock: (CreateEventCompletionBlock) completion;
    ```

1. <span data-ttu-id="60f4c-104">Öffnen **Sie GraphManager.m,** und fügen Sie die folgende Funktion hinzu, um ein neues Ereignis im Kalender des Benutzers zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="60f4c-104">Open **GraphManager.m** and add the following function to create a new event on the user's calendar.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphManager.m" id="CreateEventSnippet":::

1. <span data-ttu-id="60f4c-105">Erstellen Sie eine neue **Cocoa Touch Class-Datei** im **Ordner GraphTutorial** mit dem Namen `NewEventViewController` .</span><span class="sxs-lookup"><span data-stu-id="60f4c-105">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `NewEventViewController`.</span></span> <span data-ttu-id="60f4c-106">Wählen **Sie "UIViewController"** in der **Unterklasse des Felds** aus.</span><span class="sxs-lookup"><span data-stu-id="60f4c-106">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="60f4c-107">Öffnen **Sie NewEventViewController.h,** und fügen Sie den folgenden Code in der Deklaration `@interface` hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-107">Open **NewEventViewController.h** and add the following code inside the `@interface` declaration.</span></span>

    ```objectivec
    @property (nonatomic) IBOutlet UITextField* subject;
    @property (nonatomic) IBOutlet UITextField* attendees;
    @property (nonatomic) IBOutlet UIDatePicker* start;
    @property (nonatomic) IBOutlet UIDatePicker* end;
    @property (nonatomic) IBOutlet UITextView* body;
    ```

1. <span data-ttu-id="60f4c-108">Öffnen **Sie NewEventController.m,** und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="60f4c-108">Open **NewEventController.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/NewEventViewController.m" id="NewEventViewControllerSnippet":::

1. <span data-ttu-id="60f4c-109">Öffnen **Sie Main.storyboard**.</span><span class="sxs-lookup"><span data-stu-id="60f4c-109">Open **Main.storyboard**.</span></span> <span data-ttu-id="60f4c-110">Verwenden Sie die **Bibliothek,** um einen **Ansichtscontroller auf** das Storyboard zu ziehen.</span><span class="sxs-lookup"><span data-stu-id="60f4c-110">Use the **Library** to drag a **View Controller** onto the storyboard.</span></span>
1. <span data-ttu-id="60f4c-111">Fügen Sie **mithilfe der Bibliothek** dem Ansichtscontroller eine **Navigationsleiste** hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-111">Using the **Library**, add a **Navigation Bar** to the view controller.</span></span>
1. <span data-ttu-id="60f4c-112">Doppelklicken Sie auf **den Titel** in der Navigationsleiste, und aktualisieren Sie ihn in `New Event` .</span><span class="sxs-lookup"><span data-stu-id="60f4c-112">Double-click the **Title** in the navigation bar and update it to `New Event`.</span></span>
1. <span data-ttu-id="60f4c-113">Fügen Sie in  **der Bibliothek** links neben der Navigationsleiste ein Balkenschaltflächeelement hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-113">Using the **Library**, add a **Bar Button Item** to the left-hand side of the navigation bar.</span></span>
1. <span data-ttu-id="60f4c-114">Wählen Sie die Schaltfläche für die neue Leiste und dann den **Attributprüfungsinspektor aus.**</span><span class="sxs-lookup"><span data-stu-id="60f4c-114">Select the new bar button, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="60f4c-115">Ändern **Sie den Titel** in `Cancel` .</span><span class="sxs-lookup"><span data-stu-id="60f4c-115">Change **Title** to `Cancel`.</span></span>
1. <span data-ttu-id="60f4c-116">Fügen Sie **in der Bibliothek** rechts neben der Navigationsleiste ein Balkenschaltflächeelement hinzu. </span><span class="sxs-lookup"><span data-stu-id="60f4c-116">Using the **Library**, add a **Bar Button Item** to the right-hand side of the navigation bar.</span></span>
1. <span data-ttu-id="60f4c-117">Wählen Sie die Schaltfläche für die neue Leiste und dann den **Attributprüfungsinspektor aus.**</span><span class="sxs-lookup"><span data-stu-id="60f4c-117">Select the new bar button, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="60f4c-118">Ändern **Sie den Titel** in `Create` .</span><span class="sxs-lookup"><span data-stu-id="60f4c-118">Change **Title** to `Create`.</span></span>
1. <span data-ttu-id="60f4c-119">Wählen Sie den Ansichtscontroller und dann den **Identitätsinspektor aus.**</span><span class="sxs-lookup"><span data-stu-id="60f4c-119">Select the view controller, then select the **Identity Inspector**.</span></span> <span data-ttu-id="60f4c-120">Ändern **Sie die Klasse** in **NewEventViewController**.</span><span class="sxs-lookup"><span data-stu-id="60f4c-120">Change **Class** to **NewEventViewController**.</span></span>
1. <span data-ttu-id="60f4c-121">Fügen Sie der Ansicht die folgenden Steuerelemente **aus der Bibliothek** hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-121">Add the following controls from the **Library** to the view.</span></span>

    - <span data-ttu-id="60f4c-122">Fügen Sie **eine Bezeichnung unter** der Navigationsleiste hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-122">Add a **Label** under the navigation bar.</span></span> <span data-ttu-id="60f4c-123">Legen Sie den Text auf `Subject` .</span><span class="sxs-lookup"><span data-stu-id="60f4c-123">Set its text to `Subject`.</span></span>
    - <span data-ttu-id="60f4c-124">Fügen Sie **unter der Beschriftung** ein Textfeld hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-124">Add a **Text Field** under the label.</span></span> <span data-ttu-id="60f4c-125">Legen Sie das **Platzhalterattribut auf** `Subject` .</span><span class="sxs-lookup"><span data-stu-id="60f4c-125">Set its **Placeholder** attribute to `Subject`.</span></span>
    - <span data-ttu-id="60f4c-126">Fügen Sie eine **Beschriftung** unter dem Textfeld hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-126">Add a **Label** under the text field.</span></span> <span data-ttu-id="60f4c-127">Legen Sie den Text auf `Attendees` .</span><span class="sxs-lookup"><span data-stu-id="60f4c-127">Set its text to `Attendees`.</span></span>
    - <span data-ttu-id="60f4c-128">Fügen Sie **unter der Beschriftung** ein Textfeld hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-128">Add a **Text Field** under the label.</span></span> <span data-ttu-id="60f4c-129">Legen Sie das **Platzhalterattribut auf** `Separate multiple entries with ;` .</span><span class="sxs-lookup"><span data-stu-id="60f4c-129">Set its **Placeholder** attribute to `Separate multiple entries with ;`.</span></span>
    - <span data-ttu-id="60f4c-130">Fügen Sie eine **Beschriftung** unter dem Textfeld hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-130">Add a **Label** under the text field.</span></span> <span data-ttu-id="60f4c-131">Legen Sie den Text auf `Start` .</span><span class="sxs-lookup"><span data-stu-id="60f4c-131">Set its text to `Start`.</span></span>
    - <span data-ttu-id="60f4c-132">Fügen Sie **eine Datumsauswahl** unter der Bezeichnung hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-132">Add a **Date Picker** under the label.</span></span> <span data-ttu-id="60f4c-133">Legen Sie den **bevorzugten Stil auf** **"Komprimier",** das Intervall **auf 15** Minuten und die Höhe auf **35 fest.** </span><span class="sxs-lookup"><span data-stu-id="60f4c-133">Set its **Preferred Style** to **Compact**, its **Interval** to **15 minutes**, and its height to **35**.</span></span>
    - <span data-ttu-id="60f4c-134">Fügen Sie **eine Bezeichnung unter** der Datumsauswahl hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-134">Add a **Label** under the date picker.</span></span> <span data-ttu-id="60f4c-135">Legen Sie den Text auf `End` .</span><span class="sxs-lookup"><span data-stu-id="60f4c-135">Set its text to `End`.</span></span>
    - <span data-ttu-id="60f4c-136">Fügen Sie **eine Datumsauswahl unter** der Bezeichnung hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-136">Add a **Date Picker** under the label.</span></span> <span data-ttu-id="60f4c-137">Legen Sie den **bevorzugten Stil auf** **"Komprimier",** das Intervall **auf 15** Minuten und die Höhe auf **35 fest.** </span><span class="sxs-lookup"><span data-stu-id="60f4c-137">Set its **Preferred Style** to **Compact**, its **Interval** to **15 minutes**, and its height to **35**.</span></span>
    - <span data-ttu-id="60f4c-138">Fügen Sie **unter der Datumsauswahl** eine Textansicht hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-138">Add a **Text View** under the date picker.</span></span>

1. <span data-ttu-id="60f4c-139">Wählen Sie **den neuen Ereignisansichtscontroller aus,** und verwenden Sie den **Verbindungsinspektor,** um die folgenden Verbindungen herzustellen.</span><span class="sxs-lookup"><span data-stu-id="60f4c-139">Select the **New Event View Controller** and use the **Connection Inspector** to make the following connections.</span></span>

    - <span data-ttu-id="60f4c-140">Verbinden Sie die **Aktion "Empfangener** Abbruch" mit der Schaltfläche **"Abbrechen".**</span><span class="sxs-lookup"><span data-stu-id="60f4c-140">Connect the **cancel** received action to the **Cancel** bar button.</span></span>
    - <span data-ttu-id="60f4c-141">Verbinden Sie die **"createEvent** received"-Aktion mit der Schaltfläche **"Leiste** erstellen".</span><span class="sxs-lookup"><span data-stu-id="60f4c-141">Connect the **createEvent** received action to the **Create** bar button.</span></span>
    - <span data-ttu-id="60f4c-142">Verbinden Sie **den Betreff** mit dem ersten Textfeld.</span><span class="sxs-lookup"><span data-stu-id="60f4c-142">Connect the **subject** outlet to the first text field.</span></span>
    - <span data-ttu-id="60f4c-143">Verbinden Sie **die Teilnehmer mit** dem zweiten Textfeld.</span><span class="sxs-lookup"><span data-stu-id="60f4c-143">Connect the **attendees** outlet to the second text field.</span></span>
    - <span data-ttu-id="60f4c-144">Verbinden Sie **den Startausgang** mit der ersten Datumsauswahl.</span><span class="sxs-lookup"><span data-stu-id="60f4c-144">Connect the **start** outlet to the first date picker.</span></span>
    - <span data-ttu-id="60f4c-145">Schließen Sie **die Endauslaufe** mit der zweiten Datumsauswahl an.</span><span class="sxs-lookup"><span data-stu-id="60f4c-145">Connect the **end** outlet to the second date picker.</span></span>
    - <span data-ttu-id="60f4c-146">Verbinden Sie **den Textkörper** mit der Textansicht.</span><span class="sxs-lookup"><span data-stu-id="60f4c-146">Connect the **body** outlet to the text view.</span></span>

1. <span data-ttu-id="60f4c-147">Fügen Sie die folgenden Einschränkungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-147">Add the following constraints.</span></span>

    - <span data-ttu-id="60f4c-148">**Navigationsleiste**</span><span class="sxs-lookup"><span data-stu-id="60f4c-148">**Navigation Bar**</span></span>
        - <span data-ttu-id="60f4c-149">Führendes Leerzeichen zum sicheren Bereich, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-149">Leading space to Safe Area, value: 0</span></span>
        - <span data-ttu-id="60f4c-150">Nachgestellter Bereich zum sicheren Bereich, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-150">Trailing space to Safe Area, value: 0</span></span>
        - <span data-ttu-id="60f4c-151">Oberster Platz zum sicheren Bereich, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-151">Top space to Safe Area, value: 0</span></span>
        - <span data-ttu-id="60f4c-152">Höhe, Wert: 44</span><span class="sxs-lookup"><span data-stu-id="60f4c-152">Height, value: 44</span></span>
    - <span data-ttu-id="60f4c-153">**Betreffbezeichnung**</span><span class="sxs-lookup"><span data-stu-id="60f4c-153">**Subject Label**</span></span>
        - <span data-ttu-id="60f4c-154">Führendes Leerzeichen zum Rand "Ansicht", Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-154">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-155">Nachgestellter Abstand zum Rand anzeigen, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-155">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-156">Oberster Bereich zur Navigationsleiste, Wert: 20</span><span class="sxs-lookup"><span data-stu-id="60f4c-156">Top space to Navigation Bar, value: 20</span></span>
    - <span data-ttu-id="60f4c-157">**Betrefftextfeld**</span><span class="sxs-lookup"><span data-stu-id="60f4c-157">**Subject Text Field**</span></span>
        - <span data-ttu-id="60f4c-158">Führendes Leerzeichen zum Rand "Ansicht", Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-158">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-159">Nachgestellter Abstand zum Rand anzeigen, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-159">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-160">Oberster Platz bis Betreffbezeichnung, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="60f4c-160">Top space to Subject Label, value: Standard</span></span>
    - <span data-ttu-id="60f4c-161">**Bezeichnung "Teilnehmer"**</span><span class="sxs-lookup"><span data-stu-id="60f4c-161">**Attendees Label**</span></span>
        - <span data-ttu-id="60f4c-162">Führendes Leerzeichen zum Rand "Ansicht", Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-162">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-163">Nachgestellter Abstand zum Rand anzeigen, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-163">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-164">Oberster Abstand zum Betrefftextfeld, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="60f4c-164">Top space to Subject Text Field, value: Standard</span></span>
    - <span data-ttu-id="60f4c-165">**Teilnehmertextfeld**</span><span class="sxs-lookup"><span data-stu-id="60f4c-165">**Attendees Text Field**</span></span>
        - <span data-ttu-id="60f4c-166">Führendes Leerzeichen zum Rand "Ansicht", Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-166">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-167">Nachgestellter Abstand zum Rand anzeigen, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-167">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-168">Oberster Platz für "Attendees"-Bezeichnung, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="60f4c-168">Top space to Attendees Label, value: Standard</span></span>
    - <span data-ttu-id="60f4c-169">**Bezeichnung starten**</span><span class="sxs-lookup"><span data-stu-id="60f4c-169">**Start Label**</span></span>
        - <span data-ttu-id="60f4c-170">Führendes Leerzeichen zum Rand "Ansicht", Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-170">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-171">Nachgestellter Abstand zum Rand anzeigen, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-171">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-172">Oberster Abstand zum Betrefftextfeld, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="60f4c-172">Top space to Subject Text Field, value: Standard</span></span>
    - <span data-ttu-id="60f4c-173">**Auswahl für Startdatum**</span><span class="sxs-lookup"><span data-stu-id="60f4c-173">**Start Date Picker**</span></span>
        - <span data-ttu-id="60f4c-174">Führendes Leerzeichen zum Rand "Ansicht", Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-174">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-175">Nachgestellter Abstand zum Rand anzeigen, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-175">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-176">Oberster Platz für "Attendees"-Bezeichnung, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="60f4c-176">Top space to Attendees Label, value: Standard</span></span>
        - <span data-ttu-id="60f4c-177">Höhe, Wert: 35</span><span class="sxs-lookup"><span data-stu-id="60f4c-177">Height, value: 35</span></span>
    - <span data-ttu-id="60f4c-178">**Endbeschriftung**</span><span class="sxs-lookup"><span data-stu-id="60f4c-178">**End Label**</span></span>
        - <span data-ttu-id="60f4c-179">Führendes Leerzeichen zum Rand "Ansicht", Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-179">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-180">Nachgestellter Abstand zum Rand anzeigen, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-180">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-181">Oberster Platz bis Startdatumsauswahl, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="60f4c-181">Top space to Start Date Picker, value: Standard</span></span>
    - <span data-ttu-id="60f4c-182">**Enddatumsauswahl**</span><span class="sxs-lookup"><span data-stu-id="60f4c-182">**End Date Picker**</span></span>
        - <span data-ttu-id="60f4c-183">Führendes Leerzeichen zum Rand "Ansicht", Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-183">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-184">Nachgestellter Abstand zum Rand anzeigen, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-184">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-185">Oberster Abstand bis Ende der Bezeichnung, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="60f4c-185">Top space to End Label, value: Standard</span></span>
        - <span data-ttu-id="60f4c-186">Höhe: 35</span><span class="sxs-lookup"><span data-stu-id="60f4c-186">Height: 35</span></span>
    - <span data-ttu-id="60f4c-187">**Textkörperansicht**</span><span class="sxs-lookup"><span data-stu-id="60f4c-187">**Body Text View**</span></span>
        - <span data-ttu-id="60f4c-188">Führendes Leerzeichen zum Rand "Ansicht", Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-188">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-189">Nachgestellter Abstand zum Rand anzeigen, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-189">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="60f4c-190">Oberster Bereich bis Enddatumsauswahl, Wert: Standard</span><span class="sxs-lookup"><span data-stu-id="60f4c-190">Top space to End Date Picker, value: Standard</span></span>
        - <span data-ttu-id="60f4c-191">Unterer Bereich bis Rand anzeigen, Wert: 0</span><span class="sxs-lookup"><span data-stu-id="60f4c-191">Bottom space to View margin, value: 0</span></span>

    ![Screenshot des neuen Ereignisformulars im Storyboard](images/new-event-form.png)

1. <span data-ttu-id="60f4c-193">Wählen Sie **die Kalenderszene** und dann den **Connections Inspector aus.**</span><span class="sxs-lookup"><span data-stu-id="60f4c-193">Select the **Calendar Scene**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="60f4c-194">Ziehen **Sie unter "Ausgelöste Segues"** den nicht ausgefüllten Kreis neben **manuell** auf den **Neuen Ereignisansichtscontroller** im Storyboard.</span><span class="sxs-lookup"><span data-stu-id="60f4c-194">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **New Event View Controller** on the storyboard.</span></span> <span data-ttu-id="60f4c-195">Wählen **Sie im Popupmenü** "Modal präsentieren" aus.</span><span class="sxs-lookup"><span data-stu-id="60f4c-195">Select **Present Modally** in the pop-up menu.</span></span>
1. <span data-ttu-id="60f4c-196">Wählen Sie die gerade hinzugefügte Segue aus, und wählen Sie dann den **Attributes Inspector aus.**</span><span class="sxs-lookup"><span data-stu-id="60f4c-196">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="60f4c-197">Legen Sie das **Bezeichnerfeld** auf `showEventForm` .</span><span class="sxs-lookup"><span data-stu-id="60f4c-197">Set the **Identifier** field to `showEventForm`.</span></span>
1. <span data-ttu-id="60f4c-198">Verbinden Sie **die aktion "showNewEventForm** empfangen" mit der Schaltfläche der **+** Navigationsleiste.</span><span class="sxs-lookup"><span data-stu-id="60f4c-198">Connect the **showNewEventForm** received action to the **+** navigation bar button.</span></span>
1. <span data-ttu-id="60f4c-199">Speichern Sie die Änderungen, und starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="60f4c-199">Save your changes and restart the app.</span></span> <span data-ttu-id="60f4c-200">Wechseln Sie zur Kalenderseite, und tippen Sie auf die **+** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="60f4c-200">Go to the calendar page and tap the **+** button.</span></span> <span data-ttu-id="60f4c-201">Füllen Sie das Formular aus, und tippen **Sie auf "Erstellen",** um ein neues Ereignis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="60f4c-201">Fill in the form and tap **Create** to create a new event.</span></span>

    ![Screenshot des neuen Ereignisformulars](images/create-event.png)
