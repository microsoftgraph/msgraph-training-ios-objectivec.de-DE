# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="d6205-101">Vorgehensweise Ausführen des abgeschlossenen Projekts</span><span class="sxs-lookup"><span data-stu-id="d6205-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d6205-102">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d6205-102">Prerequisites</span></span>

<span data-ttu-id="d6205-103">Um das abgeschlossene Projekt in diesem Ordner auszuführen, benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d6205-103">To run the completed project in this folder, you need the following:</span></span>

- [<span data-ttu-id="d6205-104">Xcode</span><span class="sxs-lookup"><span data-stu-id="d6205-104">Xcode</span></span>](https://developer.apple.com/xcode/)
- [<span data-ttu-id="d6205-105">CocoaPods</span><span class="sxs-lookup"><span data-stu-id="d6205-105">CocoaPods</span></span>](https://cocoapods.org)
- <span data-ttu-id="d6205-106">Entweder ein persönliches Microsoft-Konto mit einem Postfach auf Outlook.com oder ein Microsoft-Arbeits-oder Schulkonto.</span><span class="sxs-lookup"><span data-stu-id="d6205-106">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

> <span data-ttu-id="d6205-107">**Hinweis:** Dieses Lernprogramm wurde mit Xcode Version 10,3 und CocoaPods Version 1.7.5 geschrieben.</span><span class="sxs-lookup"><span data-stu-id="d6205-107">**Note:** This tutorial was written using Xcode version 10.3 and CocoaPods version 1.7.5.</span></span> <span data-ttu-id="d6205-108">Die Schritte in diesem Leitfaden funktionieren möglicherweise mit anderen Versionen, jedoch nicht getestet.</span><span class="sxs-lookup"><span data-stu-id="d6205-108">The steps in this guide may work with other versions, but that has not been tested.</span></span>

<span data-ttu-id="d6205-109">Wenn Sie kein Microsoft-Konto haben, gibt es mehrere Optionen, um ein kostenloses Konto zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="d6205-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="d6205-110">Sie können [sich für ein neues persönliches Microsoft-Konto registrieren](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="d6205-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="d6205-111">Sie können sich [für das Office 365 Entwicklerprogramm registrieren](https://developer.microsoft.com/office/dev-program) , um ein kostenloses Office 365-Abonnement zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="d6205-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="d6205-112">Registrieren einer Webanwendung im Azure-Active Directory Admin Center</span><span class="sxs-lookup"><span data-stu-id="d6205-112">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="d6205-113">Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="d6205-113">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="d6205-114">Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.</span><span class="sxs-lookup"><span data-stu-id="d6205-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="d6205-115">Wählen Sie in der linken Navigationsleiste **Azure Active Directory** aus, und wählen Sie dann **App-Registrierungen** unter **Manage**aus.</span><span class="sxs-lookup"><span data-stu-id="d6205-115">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="d6205-116">Ein Screenshot der APP-Registrierungen</span><span class="sxs-lookup"><span data-stu-id="d6205-116">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="d6205-117">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="d6205-117">Select **New registration**.</span></span> <span data-ttu-id="d6205-118">Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.</span><span class="sxs-lookup"><span data-stu-id="d6205-118">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="d6205-119">Legen Sie **Name** auf `iOS Objective-C Graph Tutorial` fest.</span><span class="sxs-lookup"><span data-stu-id="d6205-119">Set **Name** to `iOS Objective-C Graph Tutorial`.</span></span>
    - <span data-ttu-id="d6205-120">Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.</span><span class="sxs-lookup"><span data-stu-id="d6205-120">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="d6205-121">Ändern Sie unter **Umleitungs-URI**das Dropdown-Menü auf **Public Client (Mobile #a0 Desktop)**, `msauth.YOUR_BUNDLE_ID://auth`und legen `YOUR_BUNDLE_ID` Sie den Wert auf ersetzt durch die Bundle-ID für Ihre APP fest.</span><span class="sxs-lookup"><span data-stu-id="d6205-121">Under **Redirect URI**, change the dropdown to **Public client (mobile & desktop)**, and set the value to `msauth.YOUR_BUNDLE_ID://auth`, replacing `YOUR_BUNDLE_ID` with the bundle ID for your app.</span></span>

    ![Screenshot der Seite "Anwendung registrieren"](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="d6205-123">Wählen Sie **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="d6205-123">Choose **Register**.</span></span> <span data-ttu-id="d6205-124">Kopieren Sie auf der Seite **IOS Objective-C Graph Tutorial** den Wert der **Anwendungs-ID (Client)** , und speichern Sie Sie, um Sie im nächsten Schritt zu benötigen.</span><span class="sxs-lookup"><span data-stu-id="d6205-124">On the **iOS Objective-C Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Ein Screenshot der Anwendungs-ID der neuen App-Registrierung](/tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a><span data-ttu-id="d6205-126">Konfigurieren des Beispiels</span><span class="sxs-lookup"><span data-stu-id="d6205-126">Configure the sample</span></span>

1. <span data-ttu-id="d6205-127">Benennen Sie `AuthSettings.plist.example` die Datei `AuthSettings.plist`in.</span><span class="sxs-lookup"><span data-stu-id="d6205-127">Rename the `AuthSettings.plist.example` file to `AuthSettings.plist`.</span></span>
1. <span data-ttu-id="d6205-128">Bearbeiten Sie `AuthSettings.plist` die Datei, und nehmen Sie die folgenden Änderungen vor.</span><span class="sxs-lookup"><span data-stu-id="d6205-128">Edit the `AuthSettings.plist` file and make the following changes.</span></span>
    1. <span data-ttu-id="d6205-129">Ersetzen `YOUR_APP_ID_HERE` Sie durch die **Anwendungs-ID** , die Sie im App-Registrierungs Portal erhalten haben.</span><span class="sxs-lookup"><span data-stu-id="d6205-129">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="d6205-130">Öffnen Sie Terminal im **GraphTutorial** -Verzeichnis, und führen Sie den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="d6205-130">Open Terminal in the **GraphTutorial** directory and run the following command.</span></span>

    ```Shell
    pod install
    ```

## <a name="run-the-sample"></a><span data-ttu-id="d6205-131">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="d6205-131">Run the sample</span></span>

<span data-ttu-id="d6205-132">Wählen Sie in Xcode das Menü **Produkt** aus, und wählen Sie dann **Ausführen**aus.</span><span class="sxs-lookup"><span data-stu-id="d6205-132">In Xcode, select the **Product** menu, then select **Run**.</span></span>
