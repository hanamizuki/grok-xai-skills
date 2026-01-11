#### [Grok Business / Enterprise](#grok-business--enterprise)

# [Google Drive Integration with Grok](#google-drive-integration-with-grok)

## [Overview: Connect Google Drive to Grok](#overview-connect-google-drive-to-grok)

Seamlessly search and reference your Google Drive files directly in Grok chats. This integration lets Grok access your team's shared files and your personal files to provide more accurate, grounded responses—reducing hallucinations and helping you work faster.

Powered by xAI's Collections API, the connector indexes files securely while respecting Google Drive permissions. Grok only retrieves content you can view. Files you don't have permission to view are never indexed or returned.

**Key benefits**:

*   Get summaries, analyses, or answers with direct citations to your files.
*   No need to manually upload or attach files. Grok searches automatically when relevant.
*   Query files by content or metadata (filename, folder, owner, modification dates).

This feature is available in Grok Business and Enterprise plans. xAI doesn't use customer Google Drive data to train its models.

## [Using Google Drive Files in Grok Chats](#using-google-drive-files-in-grok-chats)

Once connected, Grok automatically searches relevant files—no extra steps needed.

**Examples of what to ask**:

*   "Summarize the Q4 sales report from the Finance team documents."
*   "What does our employee handbook say about remote work policies according to our company documents?"
*   "Summarize my Go-to-market strategy document."

**Grok will**:

*   Search content and metadata.
*   Provide answers with inline citations linking back to the source file.
*   Reason over multiple files when needed.

![Google Drive results in Grok Business conversations](/_next/image?url=%2Fassets%2Fdocs%2Fgrok-business%2Fexample-google-drive-query.png&w=3840&q=75)

## [Setting Up the Integration](#setting-up-the-integration)

Setup combines admin configuration for team shared files and optional user connections for personal files.

### [Admin Setup: Enable for Shared Files](#admin-setup-enable-for-shared-files)

Team admins configure the connector once at the workspace level.

**Prerequisites**:

*   You must be a Grok Business or Enterprise team admin.
*   You must have purchased Grok Business or Enterprise licenses for your team.

**Steps**:

1.  Log in to the xAI Console and go to **[Grok Business Apps](https://console.x.ai/team/default/grok-business/apps)**
2.  Click **[Add to team](https://console.x.ai/team/default/grok-business/apps?add-connector-type=CONNECTOR_TYPE_GOOGLE_DRIVE)** for the Google Drive app.
3.  Specify your Google Workspace domain.
4.  Choose who can use the connector: everyone in the workspace or specific allowed users.
5.  Sign in with your Google account and grant permissions. The OAuth authentication provides a secure way to allow access without sharing passwords.

![Google Drive setup page in xAI Console](/_next/image?url=%2Fassets%2Fdocs%2Fgrok-business%2Fgoogle-drive-setup.png&w=3840&q=75)

Once connected, Grok immediately begins syncing files accessible to the admin's account. Shared files become available to authorized users right away.

![Google Drive app syncing on grok web app](/_next/image?url=%2Fassets%2Fdocs%2Fgrok-business%2Fgoogle-drive-syncing-grok-web.png&w=3840&q=75)

Admins can later edit allowed users or remove the connector entirely from the same settings page.

### [User Setup: Connect Your Personal Drive](#user-setup-connect-your-personal-drive)

End users can optionally connect their own Google Drive for searching their private files.

![Google Drive connector prompt on grok web app](/_next/image?url=%2Fassets%2Fdocs%2Fgrok-business%2Fgoogle-drive-connect-personal-oauth.png&w=3840&q=75)

**Steps**:

1.  On grok.com, go to **[Settings > Connected apps](https://grok.com/?_s=grok-business-connected-apps)**.
2.  Select **Google Drive > Connect**.
3.  Sign in with your Google account and grant permissions.
4.  Your private files will sync and become searchable in your Grok chats.

![How to connect on grok web app](/_next/image?url=%2Fassets%2Fdocs%2Fgrok-business%2Fgoogle-drive-how-to-connect.png&w=3840&q=75)

To disconnect: Return to **[Settings > Connected apps](https://grok.com/?_s=grok-business-connected-apps)** and revoke access.

## [Managing Your Integration](#managing-your-integration)

*   Admins can view sync status and the list of users who have authenticated with Google Drive from **[Apps Settings page](https://console.x.ai/team/default/grok-business/apps)**.
*   Admins or members can disconnect anytime to stop syncing their files.

## [How Indexing and Syncing Works](#how-indexing-and-syncing-works)

*   Initial sync starts immediately after admin setup.
*   Ongoing: Grok checks for changes (new/updated/deleted files, permission changes) every hour.
*   Permissions are always enforced. Grok only shows you files you can view in Google Drive.
*   No inclusion/exclusion rules beyond the admin's initial access scope and user permissions.

**Supported file formats**:

Grok indexes a wide range of common file types from Google Drive, including native Google formats, Microsoft Office documents, PDFs, code files, and more.

Category

File Formats

Documents & Presentations

Google Docs, Sheets, and Slides, Microsoft Word (.doc, .docx), Microsoft Excel (.xls, .xlsx, including macro-enabled workbooks), Microsoft PowerPoint (.ppt, .pptx, including macro-enabled presentations and slideshows), Microsoft Outlook (.msg, .pst), PDFs, OpenDocument Text (.odt), Rich Text Format (.rtf), EPUB e-books

Data & Structured Files

CSV (comma-separated values), JSON, XML

Web & Markup

HTML, CSS, Markdown (.md)

Code Files

Python, JavaScript, TypeScript, C/C++ header and source files, SQL, YAML, TOML, Shell scripts, Ruby, Scala, Swift, Kotlin, Lua, PHP, Perl

Notebooks

Jupyter Notebooks (.ipynb), Google Colab notebooks

Email & Other

Email messages (.eml, RFC822 format), Plain text (.txt), TeXmacs

## [Limitations](#limitations)

*   For files exceeding 128MB, Grok only indexes the first 128 MB of content.
*   Sync checks hourly. Some recent changes may take up to an hour to appear.
*   Only supported file types are indexed and are searchable (see list above)

## [Frequently Asked Questions](#frequently-asked-questions)

**1\. Why aren't my files appearing?**

Wait up to an hour for sync, or check permissions in Google Drive.

**2\. Do I need to connect my personal Drive?**

No, shared files work via admin setup. Connect personal for your private files only.

**3\. Can Grok edit files?**

No, read-only access for search and reference.

**4\. How do I see which files were used?**

Grok includes citations in responses.

For troubleshooting or white-glove onboarding, please contact xAI support via **[support@x.ai](mailto:support@x.ai)**.