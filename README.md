```markdown
# june14937 (SumoSignal: AppSumo Deal Alerter)

**Project Type:** Chrome Extension
**Project Description Document:** [SumoSignal Project Details](https://docs.google.com/document/d/1fMbHgmictfGj9fiAoU-nuqo4JR86W948VrnN8QzQ1ZY/)

## Overview

SumoSignal is a Chrome extension designed to help users track AppSumo.com deals. It allows users to save deals to a personal watchlist, automatically extracts expiry dates, provides timely expiry and restock notifications, and enables exporting the watchlist to a CSV file. The primary goal is to ensure users never miss out on desired AppSumo deals.

This extension is built using Manifest V3 for Chrome Extensions.

## Features

*   **Save Deals:** Save AppSumo deals (title, URL, date saved) to a personal watchlist.
*   **Auto Expiry Date Extraction:** Automatically attempts to extract and store deal expiry dates from AppSumo pages.
*   **Expiry Notifications:** Receive timely browser notifications before watched deals expire.
*   **Mark Deals:** Enable users to mark deals they've missed or purchased.
*   **Restock Notifications:** Receive restock notifications if 'missed' deals or very similar deals become available again.
*   **CSV Export:** Export the entire watchlist (title, URL, date saved, expiry date, status) as a CSV file.
*   **Easy Save Button:** Injects a "Save to SumoSignal" button on AppSumo.com product pages for one-click saving.
*   **Watchlist Management:** View, manage (edit, delete), and filter/sort watched deals through a comprehensive Options page.
*   **Configurable Settings:** User settings panel within the Options page for preferences like notification lead time, restock check frequency, and notification toggles.
*   **Quick Actions Popup:** Browser action popup for quick actions like CSV export and a link to the Options page.
*   **Onboarding:** A simple onboarding experience for new users (e.g., welcome message or options page redirect upon installation).

## Architecture Overview

SumoSignal follows a standard Chrome Extension architecture (Manifest V3):

1.  **Content Script (`contentscraper.js`):** Injected into `AppSumo.com` pages to scrape deal information and inject the "Save to SumoSignal" button.
2.  **Background Service Worker (`backgroundworker.js`):** The central hub that manages the watchlist, user settings (via `chrome.storage.local`), scheduled tasks (`chrome.alarms`) for expiry/restock checks, notifications, and CSV exports.
3.  **User Interface Components:**
    *   **Popup (`popup.html`):** Quick access to essential functions.
    *   **Options Page (`optionsui.html`):** Comprehensive interface for managing the watchlist and extension settings.
4.  **Shared Utility & Configuration Modules:** A set of JavaScript modules for storage management, notification services, CSV exporting, shared helper functions, and application configuration.

## How It Works (User Flow)

1.  **First Run/Onboarding:** Upon installation, new users are guided through a brief onboarding process.
2.  **Adding a Deal:** While browsing AppSumo.com, click the "Save to SumoSignal" button next to a deal. The extension captures the deal's title, URL, and attempts to find its expiry date, then saves it to your local watchlist.
3.  **Expiry Notifications:** The extension periodically checks your watchlist. If a deal is nearing its expiry (based on your settings), you'll receive a browser notification.
4.  **Managing Watchlist:** Access the Options page to view all saved deals. Here, you can edit details, mark deals as 'missed' or 'purchased', or delete them.
5.  **Restock Notifications:** If a deal you marked as 'missed' becomes available again, or a very similar deal appears, you'll receive a restock notification.
6.  **Exporting Watchlist:** From the Popup or Options page, you can export your entire watchlist to a CSV file.
7.  **Configuring Settings:** Customize notification lead times, check frequencies, and other preferences in the Options page.

## Installation

To install SumoSignal (from source):

1.  **Download or Clone:** Download the project files or clone the repository to your local machine.
    ```bash
    git clone <repository_url> june14937
    cd june14937
    ```
2.  **Open Chrome Extensions:** Open Google Chrome, navigate to `chrome://extensions`.
3.  **Enable Developer Mode:** Toggle the "Developer mode" switch, usually found in the top-right corner.
4.  **Load Unpacked:** Click the "Load unpacked" button and select the directory where you downloaded/cloned the project files (the root `june14937` folder containing `manifest.json`).

The SumoSignal extension icon should now appear in your Chrome toolbar.

## Usage

*   **Saving a Deal:**
    *   Navigate to an AppSumo deal page.
    *   Click the "Save to SumoSignal" button injected onto the page by the extension.
    *   The deal will be added to your watchlist.

*   **Accessing the Popup:**
    *   Click the SumoSignal extension icon in your Chrome toolbar.
    *   From the popup, you can quickly initiate a CSV export or navigate to the Options page.

*   **Managing Your Watchlist (Options Page):**
    *   Open the Options page (either via the popup or by right-clicking the extension icon and selecting "Options").
    *   Here you can:
        *   View all your saved deals.
        *   Edit deal details (e.g., manually set/correct expiry date).
        *   Mark deals as 'missed', 'purchased', etc.
        *   Delete deals from your watchlist.

*   **Exporting to CSV:**
    *   Click the "Export to CSV" button in the Popup or on the Options page.
    *   A CSV file named `sumosignal_watchlist.csv` containing your watchlist data will be downloaded.

*   **Configuring Settings:**
    *   Navigate to the "Settings" section within the Options page.
    *   Adjust preferences such as:
        *   Lead time for expiry notifications.
        *   Frequency of restock checks.
        *   Enable/disable specific notification types.

## Core Components & File Structure

Here's a list of the main files and their roles in the extension:

*   **`manifest.json`**:
    *   Defines the Chrome extension's properties, permissions, and entry points. Specifies background service worker, content scripts for AppSumo, browser action with popup, and options page. Requests necessary permissions like 'storage', 'notifications', 'alarms', 'scripting', 'downloads', 'tabs', and host permissions for `*://*.appsumo.com/*`.
*   **`backgroundworker.js` (coreBackgroundManager)**:
    *   Core background service worker. Initializes alarms, listens for messages from content scripts and UI components, manages watchlist operations (via `storagemanager.js`), performs expiry and restock checks, and triggers notifications (via `notificationservice.js`) and CSV downloads (via `csvexporterservice.js` and `chrome.downloads`).
*   **`contentscraper.js` (appSumoDealCapture)**:
    *   Injects into AppSumo pages. Detects deal elements (titles, cards), adds 'Save to SumoSignal' buttons/event listeners. Scrapes deal title, URL, and attempts to find expiry dates from the DOM. Sends collected data to the background worker.
*   **`storagemanager.js`**:
    *   Handles all interactions with `chrome.storage.local` for storing and retrieving watchlist data and user settings.
*   **`notificationservice.js`**:
    *   Manages the creation and display of browser notifications for expiries and restocks.
*   **`csvexporterservice.js`**:
    *   Contains the logic for generating CSV formatted data from the watchlist.
*   **`sharedutilities.js`**:
    *   A collection of common helper functions for date/time manipulation, string similarity calculations (for restock feature), and basic error logging.
*   **`appconfig.js`**:
    *   Stores application-wide constants, default settings, and crucially, DOM selectors for scraping AppSumo.com. This allows for easier updates if AppSumo's site structure changes.
*   **`onboardingmanager.js`**:
    *   Manages the first-run experience for new users, potentially opening the Options page or displaying a welcome notification.
*   **UI Files:**
    *   `popup.html`: Structure for the browser action popup.
    *   `popupmanager.js`: Logic for the popup UI.
    *   `popupstyles.css`: Styles for the popup.
    *   `optionsui.html`: Structure for the Options page.
    *   `optionsmanager.js`: Logic for the Options page UI, including watchlist display and settings management.
    *   `optionsstyles.css`: Styles for the Options page.
    *   `injecteduistyles.css`: Styles for UI elements injected into AppSumo pages (e.g., the "Save to SumoSignal" button).
*   **Assets:**
    *   `icon16.png`, `icon48.png`, `icon128.png`: Standard extension icons for browser integration.

## Technical Notes & Considerations

*   **Manifest V3:** The project is built based on Chrome's Manifest V3, which introduces service workers for background tasks and other API changes.
*   **DOM Scraping Fragility:** The reliability of scraping deal information (especially expiry dates) from AppSumo.com depends heavily on the site's HTML structure. Selectors are managed in `appconfig.js` for easier updates, but changes to AppSumo's website may break this functionality. Regular testing and updates to selectors will be necessary.
*   **Restock Feature:** The "very similar titles" feature for restock alerts relies on a string similarity algorithm and a method for scanning current AppSumo listings. The effectiveness of this will depend on the chosen algorithm and the reliability of the listing scanning method.
*   **Error Handling:** Robust error handling and clear user feedback mechanisms are important across all components for a good user experience.
*   **Chrome APIs:** Ensure robust use of `chrome.storage` (data validation, quota limits) and `chrome.alarms` (correct scheduling, clearing obsolete alarms), handling potential errors and edge cases.
*   **Data Structure:** Watchlist items should have a well-defined structure (e.g., unique ID, title, URL, dateSaved, expiryDate, status, user notes). User settings should be stored separately for clarity.
*   **Testing:** Regular testing on the live AppSumo.com website is crucial to identify and address issues arising from site changes.

## Dependencies

*   This is a Chrome Extension and relies on various **Chrome Extension APIs**, including:
    *   `chrome.storage`
    *   `chrome.notifications`
    *   `chrome.alarms`
    *   `chrome.scripting`
    *   `chrome.runtime`
    *   `chrome.downloads`
    *   `chrome.action`
    *   `chrome.tabs`

No external JavaScript libraries are explicitly mentioned for core functionality, but could be incorporated if needed (e.g., a specific date manipulation or string similarity library).
```