# Backend API Documentation — Wellness Reader

**Source:** `Wellness Reader.html` mockup (Home, Reader, Player, Account, Library, Legal, Language, Support)
**Base path:** every endpoint is prefixed with `/api/`
**Principle:** no user-facing text is hardcoded in the client — article copy, category names, legal content, FAQ text, and language labels are all served by these endpoints so they can be edited/localized without an app release.

---

## Conventions

- **Base URL:** `https://<host>/api/`
- **Auth:** `Authorization: Bearer <token>` header, required on every endpoint below unless marked **Public**.
- **Content type:** `application/json` for all requests and responses.
- **Envelope:** single-resource responses return `{ "data": {...} }`; list responses return `{ "data": [...], "meta": {...} }`.
- **Errors:** non-2xx responses return
  ```json
  { "error": { "code": "not_found", "message": "Article not found" } }
  ```
- **Dates/times:** ISO 8601 strings (`"2026-08-01T00:00:00Z"`).
- **Text/content model:** wherever the mockup shows a body of paragraphs (article body, legal document sections), the API returns a `paragraphs: string[]` array. This one shape is reused everywhere so the client can render body copy with a single component.

---

## 1. Categories

Backs the four category tiles/carousels on Home and the category chips in Library.

### `GET /api/categories`
Returns all wellness categories.

**Response 200**
```json
{
  "data": [
    {
      "id": "nut",
      "name": "Nutrition",
      "shortName": "Nutrition",
      "description": "What you eat, when you eat it, and which claims survive contact with evidence.",
      "accentColor": "oklch(0.60 0.11 130)",
      "accentColorDark": "oklch(0.40 0.08 132)",
      "articleCount": 3
    },
    {
      "id": "neuro",
      "name": "Neurological & Hormonal Health",
      "shortName": "Neuro & Hormonal",
      "description": "Sleep, stress, mood and the chemical signals that keep them in conversation.",
      "accentColor": "oklch(0.60 0.11 300)",
      "accentColorDark": "oklch(0.40 0.08 300)",
      "articleCount": 2
    }
  ],
  "meta": { "count": 4 }
}
```

---

## 2. Articles

Backs the Home carousels/chips list, the Reader screen, and the Player screen.

### `GET /api/articles`
List articles, optionally filtered.

**Query params**

| Param | Values | Notes |
|---|---|---|
| `category` | category id (e.g. `nut`) | filters to one category |
| `shelf` | `saved` \| `listened` \| `finished` | filters to the current user's library shelf |
| `sort` | `recommended` (default) | ordering used by the "Recommended" chip |

**Response 200**
```json
{
  "data": [
    {
      "id": "nut-1",
      "categoryId": "nut",
      "title": "Protein Timing, Honestly",
      "dek": "The anabolic window is wider and duller than the supplement aisle suggests.",
      "authorName": "Dr. Ana Reyes",
      "readTimeMinutes": 9,
      "heroImageUrl": null,
      "isSaved": false
    }
  ],
  "meta": { "count": 11 }
}
```
> `heroImageUrl: null` tells the client to render the category-color gradient placeholder (the "photo slot" pattern in the mockup) instead of a photo.

### `GET /api/articles/{articleId}`
Full article for the Reader screen.

**Response 200**
```json
{
  "data": {
    "id": "nut-1",
    "categoryId": "nut",
    "title": "Protein Timing, Honestly",
    "dek": "The anabolic window is wider and duller than the supplement aisle suggests.",
    "authorName": "Dr. Ana Reyes",
    "readTimeMinutes": 9,
    "heroImageUrl": null,
    "isSaved": false,
    "paragraphs": [
      "The idea that muscle repair depends on a thirty-minute window after training has been repeated so often that it now functions as folklore. The underlying research is real, but it was mostly conducted in fasted subjects, and the effect it measured was modest.",
      "What matters more is the total protein you take in across a day, and how evenly you spread it. Four servings of roughly thirty grams outperform one large evening meal, not because the timing is magic, but because muscle protein synthesis responds to repeated signals rather than a single large one.",
      "The practical version is unexciting: aim for a consistent daily target, include a protein source at each meal, and stop optimising the clock. Consistency beats precision at every level of training experience."
    ]
  }
}
```

### `GET /api/articles/{articleId}/narration`
Player metadata — narration is generated to match `readTimeMinutes`, per the app's design assumption.

**Response 200**
```json
{
  "data": {
    "articleId": "nut-1",
    "audioUrl": "https://cdn.wellnessreader.example/narration/nut-1.m4a",
    "durationSeconds": 540,
    "voice": "editorial-female-1"
  }
}
```

### `PUT /api/articles/{articleId}/save`
Save (star) an article to the "Saved" shelf. Idempotent.

**Response 204** — no body.

### `DELETE /api/articles/{articleId}/save`
Remove an article from "Saved".

**Response 204** — no body.

### `PATCH /api/articles/{articleId}/progress`
Update reading or listening progress (drives the reader's scroll-progress bar and the player's scrub bar).

**Request body**
```json
{ "type": "listening", "positionSeconds": 96 }
```

**Response 200**
```json
{ "data": { "articleId": "nut-1", "type": "listening", "positionSeconds": 96, "completed": false } }
```

---

## 3. Library

Backs the Library chips (Recommended / category / Saved / In progress / Finished) on Home.

### `GET /api/library/{shelf}`
`shelf` = `saved` | `listened` | `finished`.

**Response 200**
```json
{
  "data": [
    { "id": "cell-1", "categoryId": "cell", "title": "Autophagy After Dark", "readTimeMinutes": 8, "categoryShortName": "Cellular" },
    { "id": "imm-1", "categoryId": "imm", "title": "The Vaccine Memory Effect", "readTimeMinutes": 7, "categoryShortName": "Immune & Prevention" }
  ],
  "meta": { "count": 2 }
}
```

---

## 4. Account

Backs the Account screen (sponsor card + profile) and User Account sub-screen.

### `GET /api/account`
**Response 200**
```json
{
  "data": {
    "displayName": "Mira",
    "sponsorName": "Meridian Health Partners",
    "sponsorMessage": "Your membership is sponsored by your employer, and it is valid until 31-12-2026.",
    "licenseNumber": "4821 3390 5567 1042 8873",
    "membershipValidUntil": "2026-12-31"
  }
}
```

### `GET /api/account/profile`
**Response 200**
```json
{
  "data": { "name": "Mira Alston", "email": "mira.alston@example.com", "avatarUrl": null }
}
```

### `PATCH /api/account/profile`
**Request body**
```json
{ "name": "Mira Alston", "email": "mira.alston@example.com" }
```
**Response 200** — updated profile object, same shape as above.

### `POST /api/account/change-password`
**Request body**
```json
{ "currentPassword": "••••••••", "newPassword": "••••••••" }
```
**Response 204** — no body.

### `DELETE /api/account`
Deletes the user's account (destructive action in User Account screen).

**Response 202**
```json
{ "data": { "status": "deletion_scheduled", "effectiveAt": "2026-08-17T00:00:00Z" } }
```

### `POST /api/account/logout`
**Response 204** — no body.

---

## 5. Reader & language preferences

Backs the Aa panel (theme/size/typeface — device-local, not listed here) and the Language screen (server-persisted since it affects content localization).

### `GET /api/languages`
**Public.** Supported app languages.

**Response 200**
```json
{
  "data": [
    { "code": "en", "label": "English" },
    { "code": "de", "label": "German" },
    { "code": "fr", "label": "French" },
    { "code": "es", "label": "Spanish" }
  ]
}
```

### `PUT /api/account/language`
**Request body**
```json
{ "code": "de" }
```
**Response 200**
```json
{ "data": { "code": "de", "label": "German" } }
```

---

## 6. Support

Backs the Support screen's FAQ list.

### `GET /api/support/faqs`
**Public.**

**Response 200**
```json
{
  "data": [
    {
      "id": "f1",
      "question": "How does the narration stay in sync with the article?",
      "answer": "Narration length is generated from the estimated reading time, so switching between reading and listening never loses your place."
    },
    {
      "id": "f3",
      "question": "Who reviews the health content?",
      "answer": "Every article is written or reviewed by a licensed clinician before publishing, and is revisited whenever guidance changes."
    }
  ],
  "meta": { "count": 4 }
}
```

---

## 7. Legal

Backs the Legal list and the three legal detail screens (Privacy Policy, Terms of Service, Medical Disclaimer). All copy is served by the API — no legal text is bundled in the client.

### `GET /api/legal`
**Public.** List of legal documents (drives the Legal menu rows).

**Response 200**
```json
{
  "data": [
    { "id": "privacy", "label": "Privacy Policy", "updatedAt": "2026-08-01" },
    { "id": "terms", "label": "Terms of Service", "updatedAt": "2026-08-01" },
    { "id": "disclaimer", "label": "Medical Disclaimer", "updatedAt": "2026-08-01" }
  ]
}
```

### `GET /api/legal/{documentId}`
**Public.** `documentId` = `privacy` | `terms` | `disclaimer`. Full document for the detail screen.

**Response 200**
```json
{
  "data": {
    "id": "privacy",
    "title": "Privacy Policy",
    "updatedAt": "2026-08-01",
    "sections": [
      {
        "heading": "Information We Collect",
        "paragraphs": [
          "We collect the information you give us directly, such as your name, email address, and any profile details you choose to add.",
          "We also collect usage data automatically, including which articles you read or listen to, playback progress, and general device information, so the app works reliably across sessions."
        ]
      },
      {
        "heading": "Contact Us",
        "paragraphs": [
          "Questions about this policy can be sent to privacy@wellnessreader.example, and we aim to respond within five business days."
        ]
      }
    ]
  }
}
```

**Errors**
```json
{ "error": { "code": "not_found", "message": "Unknown legal document: refund-policy" } }
```
Returned as `404` for any `documentId` outside `privacy` / `terms` / `disclaimer`.

---

## 8. Endpoint summary

| Method | Path | Purpose |
|---|---|---|
| GET | `/api/categories` | List categories |
| GET | `/api/articles` | List articles (filter by category/shelf) |
| GET | `/api/articles/{articleId}` | Article detail (Reader) |
| GET | `/api/articles/{articleId}/narration` | Narration audio metadata (Player) |
| PUT | `/api/articles/{articleId}/save` | Save article |
| DELETE | `/api/articles/{articleId}/save` | Unsave article |
| PATCH | `/api/articles/{articleId}/progress` | Update read/listen progress |
| GET | `/api/library/{shelf}` | Saved / In progress / Finished shelves |
| GET | `/api/account` | Sponsor + membership summary |
| GET | `/api/account/profile` | User profile |
| PATCH | `/api/account/profile` | Update profile |
| POST | `/api/account/change-password` | Change password |
| DELETE | `/api/account` | Delete account |
| POST | `/api/account/logout` | Sign out |
| GET | `/api/languages` | Supported languages |
| PUT | `/api/account/language` | Set preferred language |
| GET | `/api/support/faqs` | FAQ list |
| GET | `/api/legal` | Legal document list |
| GET | `/api/legal/{documentId}` | Legal document detail |
