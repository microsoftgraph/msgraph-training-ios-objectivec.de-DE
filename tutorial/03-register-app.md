<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5f899-101">In dieser Übung erstellen Sie mithilfe des Azure Active Directory Admin Center eine neue Azure AD systemeigene Anwendung.</span><span class="sxs-lookup"><span data-stu-id="5f899-101">In this exercise you will create a new Azure AD native application using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="5f899-102">Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com). Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.</span><span class="sxs-lookup"><span data-stu-id="5f899-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="5f899-103">Wählen Sie in der linken Navigationsleiste **Azure Active Directory** aus, und wählen Sie dann **App-Registrierungen** unter **Manage**aus.</span><span class="sxs-lookup"><span data-stu-id="5f899-103">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="5f899-104">Ein Screenshot der APP-Registrierungen</span><span class="sxs-lookup"><span data-stu-id="5f899-104">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="5f899-105">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="5f899-105">Select **New registration**.</span></span> <span data-ttu-id="5f899-106">Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.</span><span class="sxs-lookup"><span data-stu-id="5f899-106">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="5f899-107">Legen Sie **Name** auf `iOS Objective-C Graph Tutorial` fest.</span><span class="sxs-lookup"><span data-stu-id="5f899-107">Set **Name** to `iOS Objective-C Graph Tutorial`.</span></span>
    - <span data-ttu-id="5f899-108">Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.</span><span class="sxs-lookup"><span data-stu-id="5f899-108">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="5f899-109">Ändern Sie unter **Umleitungs-URI**das Dropdown-Menü auf **Public Client (Mobile #a0 Desktop)**, `msauth.YOUR_BUNDLE_ID://auth`und legen `YOUR_BUNDLE_ID` Sie den Wert auf ersetzt durch die Bundle-ID für Ihre APP fest.</span><span class="sxs-lookup"><span data-stu-id="5f899-109">Under **Redirect URI**, change the dropdown to **Public client (mobile & desktop)**, and set the value to `msauth.YOUR_BUNDLE_ID://auth`, replacing `YOUR_BUNDLE_ID` with the bundle ID for your app.</span></span>

    ![Screenshot der Seite "Anwendung registrieren"](./images/aad-register-an-app.png)

1. <span data-ttu-id="5f899-111">Wählen Sie **registrieren**aus.</span><span class="sxs-lookup"><span data-stu-id="5f899-111">Select **Register**.</span></span> <span data-ttu-id="5f899-112">Kopieren Sie auf der Seite **IOS Objective-C Graph Tutorial** den Wert der **Anwendungs-ID (Client)** , und speichern Sie Sie, um Sie im nächsten Schritt zu benötigen.</span><span class="sxs-lookup"><span data-stu-id="5f899-112">On the **iOS Objective-C Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Ein Screenshot der Anwendungs-ID der neuen App-Registrierung](./images/aad-application-id.png)
