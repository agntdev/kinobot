# CinemaFlow — Bot specification

**Archetype:** content

**Voice:** warm and concise — write every user-facing message, button label, error, and empty state in this voice.

A Telegram bot offering free in-bot streaming of movies and TV shows with personalized profiles, searchable catalogs, and integrated playback. Features include viewing history, watch-later lists, and admin-controlled content updates.

> This is the complete contract for the bot. Implement EVERY entry point, flow, feature, integration, and edge case below. The completeness review checks the bot against this document after each build pass.

## Primary audience

- casual viewers
- cinephiles
- Russian-speaking users

## Success criteria

- Users can stream content inline without external apps
- User profiles persist across sessions
- Admin can manage content via private chat

## Entry points

Every feature must be reachable from the bot's command/button surface (button-first; only /start and /help are slash commands).

- **/start** (command, actor: user, command: /start) — Open the main menu
- **Catalog** (button, actor: user, callback: catalog:main) — Browse movies/series by categories
- **Search** (button, actor: user, callback: search:init) — Search with filters
- **My Profile** (button, actor: user, callback: profile:view) — View history/watch-later
- **Watch Later** (button, actor: user, callback: watch_later:list) — Manage saved titles
- **/admin** (command, actor: admin, command: /admin) — Access admin controls

## Flows

### Onboarding
_Trigger:_ /start

1. Welcome message
2. Profile creation prompt
3. Main menu display

_Data touched:_ User Profile

### Catalog Browsing
_Trigger:_ catalog:main

1. Show category buttons
2. Load title cards
3. Handle play/add-to-watch-later actions

_Data touched:_ Title, User Profile

### Playback
_Trigger:_ play:title

1. Send streaming media
2. Track playback progress
3. Offer resume option

_Data touched:_ Playback Session, User Profile

### Admin Content Management
_Trigger:_ /admin

1. Authenticating admin
2. Content CRUD operations
3. Send alerts to users

_Data touched:_ Admin Content Records

## Data entities

Durable data (must survive a restart) uses the toolkit's persistent store, never in-memory maps.

- **User Profile** _(retention: persistent)_ — User-specific preferences and history
  - fields: telegram_id, watch_history, watch_later, notification_prefs
- **Title** _(retention: persistent)_ — Movie/series metadata
  - fields: id, type, title, description, genres, stream_links, poster_url
- **Playback Session** _(retention: session)_ — Temporary playback tracking
  - fields: user_id, current_position, quality_selected
- **Admin Content Records** _(retention: persistent)_ — Content management entries
  - fields: action_type, content_id, timestamp

## Integrations

- **Telegram** (required) — Bot API messaging and media delivery
Call external APIs against their real contract (correct endpoints, ids, params); credentials from env. Do not fake responses.

## Owner controls

- Admin commands for content CRUD
- Private chat alerts for system events
- Notification opt-in management

## Notifications

- New-release alerts (opt-in)
- Playback resume reminders
- Admin content update alerts

## Permissions & privacy

- User profiles stored securely
- Viewing history anonymized by default
- Admin access requires private chat authentication

## Edge cases

- Offline playback handling
- Invalid stream URLs
- Admin command authorization failures

## Required tests

- End-to-end playback from catalog to resume
- Profile persistence across sessions
- Admin content update workflow

## Assumptions

- Owner provides licensed media URLs
- Telegram's media limits accommodate content
- Admin will handle content moderation
