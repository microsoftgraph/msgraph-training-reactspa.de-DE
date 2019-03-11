<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b7923-101">In diesem Tutorial erfahren Sie, wie Sie eine einseitige app erstellen, die die Microsoft Graph-API zum Abrufen von Kalenderinformationen für einen Benutzer verwendet.</span><span class="sxs-lookup"><span data-stu-id="b7923-101">This tutorial teaches you how to build a React single-page app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="b7923-102">Wenn Sie das fertige Tutorial lieber herunterladen möchten, können Sie das [GitHub-Repository](https://github.com/microsoftgraph/msgraph-training-reactspa)herunterladen oder Klonen.</span><span class="sxs-lookup"><span data-stu-id="b7923-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-reactspa).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b7923-103">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="b7923-103">Prerequisites</span></span>

<span data-ttu-id="b7923-104">Bevor Sie dieses Lernprogramm starten, sollte [node. js](https://nodejs.org) auf dem Entwicklungscomputer installiert sein.</span><span class="sxs-lookup"><span data-stu-id="b7923-104">Before you start this tutorial, you should have [Node.js](https://nodejs.org) installed on your development machine.</span></span> <span data-ttu-id="b7923-105">Wenn Sie nicht über Node. js verfügen, besuchen Sie den vorherigen Link für Downloadoptionen.</span><span class="sxs-lookup"><span data-stu-id="b7923-105">If you do not have Node.js, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="b7923-106">Dieses Lernprogramm wurde mit Node Version 10.7.0 geschrieben.</span><span class="sxs-lookup"><span data-stu-id="b7923-106">This tutorial was written with Node version 10.7.0.</span></span> <span data-ttu-id="b7923-107">Die Schritte in diesem Handbuch funktionieren möglicherweise mit anderen Versionen, jedoch nicht getestet.</span><span class="sxs-lookup"><span data-stu-id="b7923-107">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="b7923-108">Feedback</span><span class="sxs-lookup"><span data-stu-id="b7923-108">Feedback</span></span>

<span data-ttu-id="b7923-109">Feedback zu diesem Tutorial finden Sie im GitHub- [Repository](https://github.com/microsoftgraph/msgraph-training-reactspa).</span><span class="sxs-lookup"><span data-stu-id="b7923-109">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-reactspa).</span></span>