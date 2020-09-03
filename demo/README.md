# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="eeef7-101">Vorgehensweise Ausführen des abgeschlossenen Projekts</span><span class="sxs-lookup"><span data-stu-id="eeef7-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eeef7-102">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="eeef7-102">Prerequisites</span></span>

<span data-ttu-id="eeef7-103">Um das abgeschlossene Projekt in diesem Ordner auszuführen, benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="eeef7-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="eeef7-104">[Node.js](https://nodejs.org) auf dem Entwicklungscomputer installiert.</span><span class="sxs-lookup"><span data-stu-id="eeef7-104">[Node.js](https://nodejs.org) installed on your development machine.</span></span> <span data-ttu-id="eeef7-105">Wenn Sie nicht über Node.js verfügen, besuchen Sie den vorherigen Link für Downloadoptionen.</span><span class="sxs-lookup"><span data-stu-id="eeef7-105">If you do not have Node.js, visit the previous link for download options.</span></span> <span data-ttu-id="eeef7-106">(**Hinweis:** dieses Lernprogramm wurde mit Node Version 12.16.1 geschrieben.</span><span class="sxs-lookup"><span data-stu-id="eeef7-106">(**Note:** This tutorial was written with Node version 12.16.1.</span></span> <span data-ttu-id="eeef7-107">Die Schritte in diesem Leitfaden funktionieren möglicherweise mit anderen Versionen, aber das wurde nicht getestet.)</span><span class="sxs-lookup"><span data-stu-id="eeef7-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="eeef7-108">Entweder ein persönliches Microsoft-Konto mit einem Postfach auf Outlook.com oder ein Microsoft-Arbeits-oder Schulkonto.</span><span class="sxs-lookup"><span data-stu-id="eeef7-108">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="eeef7-109">Wenn Sie kein Microsoft-Konto haben, gibt es mehrere Optionen, um ein kostenloses Konto zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="eeef7-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="eeef7-110">Sie können [sich für ein neues persönliches Microsoft-Konto registrieren](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="eeef7-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="eeef7-111">Sie können sich [für das Office 365 Entwicklerprogramm registrieren](https://developer.microsoft.com/office/dev-program) , um ein kostenloses Office 365-Abonnement zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="eeef7-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="eeef7-112">Registrieren einer Webanwendung im Azure-Active Directory Admin Center</span><span class="sxs-lookup"><span data-stu-id="eeef7-112">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="eeef7-113">Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="eeef7-113">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="eeef7-114">Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.</span><span class="sxs-lookup"><span data-stu-id="eeef7-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="eeef7-115">Wählen Sie in der linken Navigationsleiste **Azure Active Directory** aus, und wählen Sie dann **App-Registrierungen** unter **Verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="eeef7-115">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="eeef7-116">Screenshot der APP-Registrierungen</span><span class="sxs-lookup"><span data-stu-id="eeef7-116">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

    > <span data-ttu-id="eeef7-117">**Hinweis:** Azure AD B2C-Benutzern werden möglicherweise nur **App-Registrierungen (Legacy)** angezeigt.</span><span class="sxs-lookup"><span data-stu-id="eeef7-117">**Note:** Azure AD B2C users may only see **App registrations (legacy)**.</span></span> <span data-ttu-id="eeef7-118">Wechseln Sie in diesem Fall direkt zu [https://aka.ms/appregistrations](https://aka.ms/appregistrations) .</span><span class="sxs-lookup"><span data-stu-id="eeef7-118">In this case, please go directly to [https://aka.ms/appregistrations](https://aka.ms/appregistrations).</span></span>

1. <span data-ttu-id="eeef7-119">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="eeef7-119">Select **New registration**.</span></span> <span data-ttu-id="eeef7-120">Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.</span><span class="sxs-lookup"><span data-stu-id="eeef7-120">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="eeef7-121">Legen Sie **Name** auf `React Graph Tutorial` fest.</span><span class="sxs-lookup"><span data-stu-id="eeef7-121">Set **Name** to `React Graph Tutorial`.</span></span>
    - <span data-ttu-id="eeef7-122">Legen Sie **Unterstützte Kontotypen** auf **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** fest.</span><span class="sxs-lookup"><span data-stu-id="eeef7-122">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="eeef7-123">Legen Sie unter **Umleitungs-URI** die erste Dropdownoption auf `Single-page application (SPA)` fest, und legen Sie den Wert auf `http://localhost:3000` fest.</span><span class="sxs-lookup"><span data-stu-id="eeef7-123">Under **Redirect URI**, set the first drop-down to `Single-page application (SPA)` and set the value to `http://localhost:3000`.</span></span>

    ![Screenshot der Seite "Anwendung registrieren"](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="eeef7-125">Wählen Sie **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="eeef7-125">Choose **Register**.</span></span> <span data-ttu-id="eeef7-126">Kopieren Sie auf der Seite mit dem **Winkel Diagramm-Lernprogramm** den Wert der **Anwendungs-ID (Client)** , und speichern Sie Sie, um Sie im nächsten Schritt zu benötigen.</span><span class="sxs-lookup"><span data-stu-id="eeef7-126">On the **Angular Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Screenshot der Anwendungs-ID der neuen App-Registrierung](/tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a><span data-ttu-id="eeef7-128">Konfigurieren des Beispiels</span><span class="sxs-lookup"><span data-stu-id="eeef7-128">Configure the sample</span></span>

1. <span data-ttu-id="eeef7-129">Benennen `./graph-tutorial/src/Config.example.ts` Sie die Datei in `./graph-tutorial/src/Config.ts` .</span><span class="sxs-lookup"><span data-stu-id="eeef7-129">Rename the `./graph-tutorial/src/Config.example.ts` file to `./graph-tutorial/src/Config.ts`.</span></span>
1. <span data-ttu-id="eeef7-130">Bearbeiten Sie die `./graph-tutorial/src/Config.ts` Datei, und nehmen Sie die folgenden Änderungen vor.</span><span class="sxs-lookup"><span data-stu-id="eeef7-130">Edit the `./graph-tutorial/src/Config.ts` file and make the following changes.</span></span>
    1. <span data-ttu-id="eeef7-131">Ersetzen `YOUR_APP_ID_HERE` Sie durch die **Anwendungs-ID** , die Sie im App-Registrierungs Portal erhalten haben.</span><span class="sxs-lookup"><span data-stu-id="eeef7-131">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="eeef7-132">Navigieren Sie in der Befehlszeilenschnittstelle (CLI) zum Verzeichnis, `graph-tutorial` und führen Sie den folgenden Befehl aus, um Anforderungen zu installieren.</span><span class="sxs-lookup"><span data-stu-id="eeef7-132">In your command-line interface (CLI), navigate to the `graph-tutorial` directory and run the following command to install requirements.</span></span>

    ```Shell
    npm install
    ```

## <a name="run-the-sample"></a><span data-ttu-id="eeef7-133">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="eeef7-133">Run the sample</span></span>

1. <span data-ttu-id="eeef7-134">Führen Sie den folgenden Befehl in der CLI aus, um die Anwendung zu starten.</span><span class="sxs-lookup"><span data-stu-id="eeef7-134">Run the following command in your CLI to start the application.</span></span>

    ```Shell
    npm start
    ```

1. <span data-ttu-id="eeef7-135">Öffnen Sie einen Browser, und navigieren Sie zu `http://localhost:3000`.</span><span class="sxs-lookup"><span data-stu-id="eeef7-135">Open a browser and browse to `http://localhost:3000`.</span></span>
