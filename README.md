# 🎮 XboxUnity Scraped Data Archive

This archive preserves essential assets and metadata from [XboxUnity.net](http://xboxunity.net), organized for ease of use in research, dashboard modding, and archival purposes.

> 🕓 **Last scraped:** June 16, 2025  
> ♻️ **Repo last updated:** June 16, 2025

---

## 📑 Table of Contents

- [🖼️ Image Categories](#%EF%B8%8F-image-categories)
  - [Icons](#-icons)
  - [Covers](#%EF%B8%8F-covers)
- [📊 Image Directory Table](#-image-directory-table)
- [👥 User Data](#-user-data)
- [📚 API Documentation](#-api-documentation)
- [📄 License](#-license)
- [🙌 Credits](#-credits)
- [⚖️ Legal Notice](#%EF%B8%8F-legal-notice)

---

## 🖼️ Image Categories

### 🧩 Icons

- **Path:** `Icons/*.png`
- **Description:** Individual PNG icon files for Xbox 360 titles, used throughout XboxUnity.
- **Example:** `53450812.png` corresponds to a specific Xbox 360 title.
- **Additional:** [Website icon](./UnityBanner.png)

---

### 🖼️ Covers

- **Path:** `Covers/[TitleID]/`
- **Contents:** Each TitleID folder contains:  
  - `Small/`: Thumbnail versions (180×120 px).  
  - `Medium/`: Mid-resolution images (300×200 px).  
  - `Large/`: Full-resolution cover art (900×600 px, default).  
  - **PNG files:** Named by `CoverID` (e.g., `1091.png`).  
  - `metadata.json`: Metadata describing the covers, including:  
    - **CoverID:** Unique identifier for the cover image (e.g., `"1091"`).  
    - **Official:** Indicates whether the cover is official (`"1"`) or non-official (`"0"`).  
  - `covers.csv`: CSV summary of covers, including:  
    - **CoverID:** Unique identifier for each cover (e.g., `"1091"`).  
    - **Official:** `"Yes"` if official, `"No"` if non-official.

---

## 📊 Image Directory Table

The following table lists all image files (icons and covers) along with their file paths and associated metadata for each title.  
Please see it here:  
- [Part 1](./IMAGE_DIRECTORY_TABLE_1.md)
- [Part 2](./IMAGE_DIRECTORY_TABLE_2.md)

---

## 👥 User Data

- **Path:** `Users/`
  - `all.json`: Full dump of XboxUnity user data (usernames, IDs, etc.).
  - `list.csv`: Simplified list for spreadsheet tools.

> **Note:** The Users datasets included do not contain any personal or personally identifiable information.

---

## 📚 API Documentation  
*Unofficial reference based on structured data collected from publicly available endpoints. This documentation may not cover all fields or possible behavior.*

---

## 🌐 Base URL

All API requests should be made relative to the following base URL:

```
BASE_URL = http://xboxunity.net/Resources/Lib
```

### 🔐 User Authentication

#### `GET /login.php`

Authenticates a user against XboxUnity’s system using provided credentials.

#### 🧾 Query Parameters

| **FIELD**   | **TYPE**   | **REQUIRED** | **DESCRIPTION**                                                                 |
|------------|------------|--------------|---------------------------------------------------------------------------------|
| `user`     | `string`   | ✅ Yes        | The XboxUnity username.                                                        |
| `passwd`   | `string`   | ✅ Yes        | The corresponding account password.                                            |
| `remember` | `boolean`  | ❌ Optional   | Whether the session should persist (`true`) or not (`false`). If omitted, default behavior applies. |

#### 📤 Example Request

```http
GET http://xboxunity.net/Resources/Lib/login.php?user=ExampleUser&passwd=ExamplePass&remember=true
```

#### 📥 Response

A JSON object containing a `Result` field with user details.

##### Response Fields

| **VALUE**     | **DESCRIPTION**                     |
|---------------|-------------------------------------|
| `User Object`   | Login was successful.               |
| `"Error"`   | Invalid username or password.       |

##### Example Response
```json
{
  "Result": {
    "ID": "1403164",
    "Name": "Isaac",
    "Approved": "1",
    "Level": "1",
    "Username": "IsaacXenon",
    "GamerTag": null,
    "APIKey": "REDACTED",
    "MAC": null,
    "RemoteIP": null,
    "LocalIP": null,
    "LocationID": null,
    "Email": "example_xboxunity_mail@gmail.com",
    "SavePassword": "0"
  }
}
```

> ℹ️ **Note:** Only valid credentials will return a populated `Result` object, the "Result" field will contain "Error" if the login fails due to invalid credentials.

### 🎮 Title List Search

#### `GET /TitleList.php`

Fetches a paginated, sortable list of Xbox titles from XboxUnity’s database, optionally filtered by platform or category. This is the primary endpoint for searching or browsing titles.

#### 🧾 Query Parameters

| **FIELD**     | **TYPE**   | **REQUIRED** | **DESCRIPTION**                                                                                                                                           |
|--------------|------------|--------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `page`       | `integer`  | ❌ Optional   | Page index (starting from 0).                                                                                                                             |
| `count`      | `integer`  | ❌ Optional   | Number of titles to return per page.                                                                                                                      |
| `search`     | `string`   | ❌ Optional   | Title name, TitleID, or MediaID. If left empty, returns all titles.                                                                                       |
| `sort`       | `integer`  | ❌ Optional   | Sort method: `0 = Name`, `1 = Covers`, `2 = Updates`, `3 = Updated`, `4 = Link Users`.                                                                    |
| `direction`  | `integer`  | ❌ Optional   | Sort direction: `1 = Descending`, `0 = Ascending`.                                                                                                        |
| `category`   | `integer`  | ✅ Yes        | Category filter: `0 = All`, `3 = Titles with Link`.                                                                                                       |
| `filter`     | `integer`  | ❌ Optional   | Platform filter: `0 = All`, `1 = Xbox 360`, `2 = XBLA`, `3 = Original Xbox`, `4 = Homebrew`.                                                              |

#### 📤 Example Request

```http
GET http://xboxunity.net/Resources/Lib/TitleList.php?page=0&count=10&search=Sonic+Unleashed&sort=3&direction=1&category=0&filter=0
```

#### 📥 Response

Returns a JSON object with an array of title entries and pagination metadata.

##### Response Fields

| **FIELD**          | **TYPE**     | **DESCRIPTION**                                                     |
|-------------------|--------------|---------------------------------------------------------------------|
| `Items`           | `array`      | List of title result objects.                                       |
| `Count`           | `integer`    | Total number of titles matched.                                     |
| `Pages`           | `integer`    | Total number of pages based on `count`.                             |
| `Page`            | `integer`    | Current page index.                                                 |
| `Filter`          | `string`     | Echo of the `filter` value used.                                    |
| `Category`        | `string`     | Echo of the `category` value used.                                  |
| `Sort`            | `string`     | Echo of the `sort` value used.                                      |
| `Direction`       | `string`     | Echo of the `direction` value used.                                 |

##### Title Object Fields (`Items[]`)

| **FIELD**         | **TYPE**     | **DESCRIPTION**                                                                 |
|------------------|--------------|---------------------------------------------------------------------------------|
| `TitleID`         | `string`     | Xbox Title ID (e.g., `53450812`).                                              |
| `HBTitleID`       | `string`     | Homebrew Title ID (if any), or `00000000`.                                     |
| `Name`            | `string`     | Display name of the title.                                                     |
| `LinkEnabled`     | `string`     | `"1"` if link feature is enabled, `"0"` otherwise.                             |
| `TitleType`       | `string`     | Type of title: `"360"`, `"XBLA"`, `"Xbox1"`, `"Homebrew"`, etc.                   |
| `Covers`          | `string`     | Number of cover images available.                                              |
| `Updates`         | `string`     | Number of title update packages available.                                     |
| `MediaIDCount`    | `string`     | Count of associated Media IDs.                                                 |
| `UserCount`       | `string`     | Number of users linked to this title (for link-enabled titles).                |
| `NewestContent`   | `string`     | Date of the newest available content (format: `YYYY-MM-DD`).                   |

##### Example Response

```json
{
  "Items": [
    {
      "TitleID": "53450812",
      "HBTitleID": "00000000",
      "Name": "SONIC UNLEASHED",
      "LinkEnabled": "0",
      "TitleType": "360",
      "Covers": "11",
      "Updates": "2",
      "MediaIDCount": "2",
      "UserCount": "0",
      "NewestContent": "2025-05-28"
    }
  ],
  "Count": 1,
  "Pages": 1,
  "Page": 0,
  "Filter": "0",
  "Category": "0",
  "Sort": "0",
  "Direction": "0"
}
```

> ℹ️ **Note:** To retrieve **all XboxUnity titles**, simply call the endpoint with `search` left empty (e.g., `search=`).

### 📈 XboxUnity Live Stats

#### `GET /Stats.php`

Returns live user activity statistics from XboxUnity, including the number of users connected, currently online, and active game rooms.

#### 🧾 Query Parameters

This endpoint does **not** require any query parameters.

#### 📤 Example Request

```http
GET http://xboxunity.net/Resources/Lib/Stats.php
```

#### 📥 Response

Returns a JSON object containing general user activity stats and active LiNK rooms.

##### Response Fields

| **FIELD**   | **TYPE**   | **DESCRIPTION**                                                                 |
|------------|------------|---------------------------------------------------------------------------------|
| `Result`   | `string`   | `"Success"` if the request was successful.                                      |
| `Info`     | `string`   | Description of how many users connected in the last 24 hours.                   |
| `Link`     | `string`   | Number of users currently using LiNK (Xbox Unity’s system link feature).       |
| `Online`   | `string`   | Number of users actively pinging the service.                                   |
| `Rooms`    | `array`    | List of active game rooms with titles.                                          |

##### Room Object Fields (`Rooms[]`)

| **FIELD**    | **TYPE**   | **DESCRIPTION**                                  |
|-------------|------------|--------------------------------------------------|
| `Count`     | `string`   | Number of users in the room.                     |
| `Name`      | `string`   | Game title of the room.                          |
| `TitleID`   | `string`   | Title ID of the game.                            |

##### Example Response

```json
{
  "Result": "Success",
  "Info": "44906 of 2097101 Users connected for online update in the last 24 hours",
  "Link": "1 Users on LiNK right now",
  "Online": "64 Users Actively pinging",
  "Rooms": [
    {
      "Count": "1",
      "Name": "Red Dead Redemption",
      "TitleID": "5454082B"
    }
  ]
}
```

### 👥 Manage User Relations

> ⚠️ Requires an active login session (you must be authenticated).

#### `GET /SetRelation.php`

Sets or updates the relationship status between the authenticated user and another XboxUnity user.

#### 🧾 Query Parameters

| **FIELD**   | **TYPE**   | **REQUIRED** | **DESCRIPTION**                                                      |
|------------|------------|--------------|----------------------------------------------------------------------|
| `target`   | `string`   | ✅ Yes        | The User ID of the target user.                                     |
| `Type`     | `integer`  | ✅ Yes        | Type of relation to set: `0 = Unfriend`, `1 = Friend`, `2 = Block`. |

#### 📤 Example Request

```http
GET http://xboxunity.net/Resources/Lib/SetRelation.php?target=1403164&Type=1
```

#### 📥 Response

Returns a JSON object showing the updated user relation.

##### Response Fields

| **FIELD**     | **TYPE**   | **DESCRIPTION**                                |
|--------------|------------|------------------------------------------------|
| `Users`      | `array`    | Array of user relation objects.               |
| `Result`     | `string`   | `"Success"` if the operation was successful.  |

##### User Object Fields (`Users[]`)

| **FIELD**       | **TYPE**   | **DESCRIPTION**                            |
|----------------|------------|--------------------------------------------|
| `ID`           | `string`   | The user ID of the related user.           |
| `Username`     | `string`   | Username of the related user.              |
| `GamerTag`     | `string`   | GamerTag (usually `"N/A"` if not set).     |
| `TUVer`        | `null`     | Title update version (if applicable).      |
| `RelationType` | `string`   | Current relationship type (e.g., `"Friend"`). |
| `Activity`     | `string`   | User activity status (typically `"N/A"`).  |

##### Example Response

```json
{
  "Users": [
    {
      "ID": "1403164",
      "Username": "IsaacXenon",
      "GamerTag": "N\/A",
      "TUVer": null,
      "RelationType": "Friend",
      "Activity": "N\/A"
    }
  ],
  "Result": "Success"
}
```

### 🧑‍🤝‍🧑 Retrieve Friend List

> ⚠️ Requires an active login session (you must be authenticated).

#### `GET /GetFriendList.php`

Fetches the authenticated user's full XboxUnity friend list, including basic relationship details and activity.

#### 🧾 Query Parameters

This endpoint does **not** require any query parameters.

#### 📤 Example Request

```http
GET http://xboxunity.net/Resources/Lib/GetFriendList.php
```

#### 📥 Response

Returns a JSON object containing a list of friends and the result status.

##### Response Fields

| **FIELD**   | **TYPE**   | **DESCRIPTION**                                |
|------------|------------|------------------------------------------------|
| `Result`   | `string`   | `"Success"` if the request was successful.    |
| `Users`    | `array`    | List of friend user objects.                  |

##### User Object Fields (`Users[]`)

| **FIELD**       | **TYPE**   | **DESCRIPTION**                             |
|----------------|------------|---------------------------------------------|
| `ID`           | `string`   | Unique user ID of the friend.               |
| `Username`     | `string`   | Friend’s XboxUnity username.                |
| `GamerTag`     | `string`   | GamerTag (typically `"N/A"` if not set).    |
| `RelationType` | `string`   | Relation type (e.g., `"Friend"`).           |
| `Activity`     | `string`   | User's current activity (often `"N/A"`).    |

##### Example Response

```json
{
  "Result": "Success",
  "Users": [
    {
      "ID": "1403379",
      "Username": "DoeSmithGamer",
      "GamerTag": "N\/A",
      "RelationType": "Friend",
      "Activity": "N\/A"
    },
    {
      "ID": "1403164",
      "Username": "IsaacXenon",
      "GamerTag": "N\/A",
      "RelationType": "Friend",
      "Activity": "N\/A"
    }
  ]
}
```

> ℹ️ **Note:** A valid session is required to retrieve the user's friend list.

### 🧩 Fetch Game Icon

#### `GET /Icon.php`

Downloads the icon image for a specific Xbox title using its TitleID.

#### 🧾 Query Parameters

| **FIELD**  | **TYPE**   | **REQUIRED** | **DESCRIPTION**                                              |
|------------|------------|--------------|--------------------------------------------------------------|
| `tid`      | `string`   | ✅ Yes        | 8-character TitleID of the game (e.g., `584111F1`).           |

#### 📤 Example Request

```http
GET http://xboxunity.net/Resources/Lib/Icon.php?tid=584111F1
```

#### 📥 Response

Returns a raw PNG icon image for the specified TitleID.

There is no JSON response—this endpoint returns **binary image data**.

> ℹ️ **Note:** Icons are 64x64 PNG files and stored in the `Icons/` directory under the filename `[TitleID].png`.

### 🖼️ Retrieve Cover Art Info

#### `GET /CoverInfo.php`

Returns metadata for all available cover art entries associated with a given Xbox 360 TitleID.

#### 🧾 Query Parameters

| **FIELD**    | **TYPE**   | **REQUIRED** | **DESCRIPTION**                                  |
|-------------|------------|--------------|--------------------------------------------------|
| `titleid`   | `string`   | ✅ Yes        | The 8-character hexadecimal TitleID (e.g., `584111F1`). |

#### 📤 Example Request

```http
GET http://xboxunity.net/Resources/Lib/CoverInfo.php?titleid=584111F1
```

#### 📥 Response

Returns a JSON object containing a list of covers and the total count.

##### Response Fields

| **FIELD**       | **TYPE**   | **DESCRIPTION**                            |
|----------------|------------|--------------------------------------------|
| `Covers`       | `array`    | List of cover entries for the specified title. |
| `CoversCount`  | `integer`  | Total number of cover entries returned.     |

##### Cover Object Fields (`Covers[]`)

| **FIELD**        | **TYPE**     | **DESCRIPTION**                                            |
|------------------|--------------|------------------------------------------------------------|
| `CoverID`        | `string`     | Unique ID of the cover image.                             |
| `Rating`         | `integer` or `null` | User rating of the cover (0–5 stars), or `null` if unrated. |
| `Official`       | `string`     | `"1"` if official, `"0"` otherwise.                       |
| `Username`       | `string`     | Username of the uploader.                                |
| `FileSize`       | `string`     | Size of the file in kilobytes (as a string).             |
| `UploadedDate`   | `string`     | Timestamp of when the cover was uploaded.                |
| `NoRate`         | `boolean`    | Whether the cover can be rated or not.                   |

##### Example Response

```json
{
  "Covers": [
    {
      "CoverID": "2933",
      "Rating": "5",
      "Official": "0",
      "Username": "weber",
      "FileSize": "373",
      "UploadedDate": "2012-01-25 00:00:00",
      "NoRate": false
    },
    {
      "CoverID": "3410",
      "Rating": "0",
      "Official": "1",
      "Username": "TraJiK",
      "FileSize": "437",
      "UploadedDate": "2012-05-30 00:00:00",
      "NoRate": false
    }
    // ...additional covers omitted for brevity
  ],
  "CoversCount": 11
}
```

### 🖼️ Fetch Cover Image File

#### `GET /Cover.php`

Downloads a specific cover image file from XboxUnity based on its CoverID and size.

#### 🧾 Query Parameters

| **FIELD**  | **TYPE**   | **REQUIRED** | **DESCRIPTION**                                                                 |
|------------|------------|--------------|---------------------------------------------------------------------------------|
| `cid`      | `string`   | ✅ Yes        | CoverID of the image to retrieve (e.g., `1091`).                                |
| `size`     | `string`   | ❌ Optional   | Size of the image to return: `small`, `medium`, or `large`. Defaults to `medium`. |

> ⚠️ If `size` is omitted, **`medium`** will be returned even though `large` is considered the visual default in the cover archive.

#### 📤 Example Requests

```http
GET http://xboxunity.net/Resources/Lib/Cover.php?cid=1091
GET http://xboxunity.net/Resources/Lib/Cover.php?cid=1091&size=large
```

#### 📥 Response

Returns a raw PNG image file corresponding to the specified cover ID and size.

There is no JSON response—this endpoint returns **binary image data**.

---

## 📄 License

All original content in this repository is licensed under the  
**Creative Commons Attribution-NonCommercial 4.0 International License (CC BY-NC 4.0)**.

You are free to share and adapt that content for non-commercial purposes with proper attribution.

> **Note:** This license **does not apply** to original XboxUnity data, which remain property of their respective owners.

---

## 🙌 Credits

- **Data Source:** [XboxUnity.net](http://xboxunity.net) — community-run resource for Xbox 360/OG Xbox title metadata.
- **Maintained by:** [UncreativeXenon](https://github.com/UncreativeXenon).
- **Tools Used:**
  - Node.js for scrapping & converting
- **Special Thanks:**
  - The community of cover contributors — kindly see [COVER_CONTRIBUTORS.md](./COVER_CONTRIBUTORS.md) for acknowledgments, they really deserve it for their amazing work!
  - [rh45-one](https://github.com/rh45-one).

---

## ⚖️ Legal Notice

This repository is provided for **archival, educational, and research purposes only.** All trademarks, logos, and images are the property of their respective copyright holders.

- No commercial use is permitted.
- This project is not affiliated with or endorsed by Microsoft, XboxUnity.net, or any other entity.
