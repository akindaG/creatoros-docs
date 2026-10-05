# CreatorOS AI Data Model

## Source of truth

The current database schema is defined by:

1. SQLAlchemy models in creatoros-api/app/models
2. Alembic migrations in creatoros-api/alembic/versions

Older SQL exports and ER diagrams are historical design artifacts. They should not be used as the final implementation schema without updating them to match the current models and migrations.

## Current primary entities

### users

Purpose:

- Authentication identity
- Profile data
- Password hash
- Account timestamps

Relationships:

- One user can own many posts.
- One user can own social connections.
- One user can own analytics snapshots.
- One user can own recommendations.
- One user can own AI generation logs.
- One user can own notifications.

### social_accounts

Current important fields:

- id
- user_id
- platform
- platform_account_id
- account_name
- username
- access_token
- refresh_token
- token_expires_at
- status
- created_at

Important rule:

- Unique user_id + platform

Access and refresh credentials are encrypted before storage.

Automatic platform values used by the current backend:

- facebook
- instagram

Facebook personal-profile sharing does not require a stored automatic API account record because it is an assisted workflow rather than an automatic publishing connection.

### posts

Important fields:

- id
- user_id
- title
- caption
- media_url
- platform
- status
- scheduled_time
- created_at

Current platform values used by application logic:

- facebook
- instagram
- facebook_profile

Typical states include draft, scheduled, published, failed, and shared where appropriate to the workflow.

### scheduled_posts

Important fields:

- id
- post_id
- schedule_group_id
- schedule_time
- publish_state
- platform

Platform constraint:

- instagram
- facebook
- facebook_profile

Publish-state constraint:

- scheduled
- queued
- published
- failed
- ready_to_share
- shared

schedule_group_id groups platform-specific copies created by one multi-platform scheduling action.

### analytics

Stores social performance snapshots associated with users and posts.

The analytics service uses snapshot data to produce dashboard metrics, platform totals, time series, and top-post views.

### recommendations

Stores recommendation records associated with a user.

The recommendation service also computes best posting-time and growth guidance from available evidence.

### ai_generation_logs

Important fields:

- id
- user_id
- ai_type
- input_text
- output_text
- created_at

Purpose:

- Record caption, hashtag, and analysis generation activity
- Preserve structured AI output for traceability

### notifications

Represents user-facing notification records used by the broader backend model.

## Main relationships

~~~text
users
  |
  +--> social_accounts
  +--> posts
  |      |
  |      +--> scheduled_posts
  |      +--> analytics
  |
  +--> recommendations
  +--> ai_generation_logs
  +--> notifications
~~~

## Multi-platform scheduling model

A single user action can schedule Facebook and Instagram together.

CreatorOS handles this by:

1. Fanning one draft into platform-specific post records.
2. Creating one scheduled_posts row for each platform-specific post.
3. Giving those schedule rows the same schedule_group_id.
4. Using the group identifier to keep calendar editing, cancellation, and rescheduling consistent.

## Facebook personal-profile scheduling model

A facebook_profile post can be scheduled without an automatic Page connection.

At the due time:

- it is not silently sent through the Facebook Page API
- its scheduled state becomes ready_to_share
- the user completes the share manually
- CreatorOS can then mark the item shared

## ER diagram guidance for the final report

Regenerate the final ERD from the current entity list and fields above.

Do not use an older ERD unchanged if it:

- uses capitalized platform values that no longer match the backend
- omits platform_account_id
- omits username
- omits refresh_token
- omits token_expires_at
- omits schedule_group_id
- omits ready_to_share or shared schedule states
- implies automatic Facebook personal-profile publishing
