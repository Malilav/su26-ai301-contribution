# Contribution 1: Add Microsoft Teams as notification provider

**Contribution Number:** 1  
**Student:** Ignacio De La Cruz  
**Issue:** [Link](https://github.com/Portabase/portabase/issues/260)   
**Status:** Phase IV 

---

## Why I Chose This Issue

I am interested in tackling the "Add Microsoft Teams as notification provider" issue because it aligns well with my experience building full-stack applications. Throughout various projects, I have worked with React-based frontends, REST APIs, and backend development. Since this issue involves both implementing backend notification logic and creating frontend configuration components, it closely matches the types of technologies and challenges I have worked with in my coursework and personal projects.

By contributing to this issue, I hope to gain experience working within an established open-source codebase and strengthen my TypeScript skills in a real-world development environment. I am also interested in learning more about extending modular application architectures and integrating third-party services into a production application.

---

## Understanding the Issue

### Problem Description

Portabase uses several third-party providers to send alerts to users. 

### Expected Behavior

Microsoft teams should be added as an option to send alerts to users.

### Current Behavior

Currently Microsoft teams is not an option for sending alerts to users. 

### Affected Components

1. Create Microsoft Teams notification provider
   - Implement sendTeams in:
src/features/notifications/providers/teams.ts

2. Register provider in notification system
   - Add sendTeams to provider handlers: src/features/notifications/providers/index.ts

Extend provider type system by adding teams to ProviderKind:
src/features/notifications/types.ts

3. Implement Teams configuration form (schema + UI)
   - Create validation schema: src/components/wrappers/dashboard/admin/channels/channel/channel-form/providers/notifications/forms/teams.schema.ts

   - Create UI form component: src/components/wrappers/dashboard/admin/channels/channel/channel-form/providers/notifications/forms/teams.form.tsx

4. Integrate Teams into UI provider system
   - Update channel form schema: src/components/wrappers/dashboard/admin/channels/channel/channel-form/channel-form.schema.ts

   - Update provider registry: src/components/wrappers/dashboard/admin/channels/helpers/notification.tsx
Remove preview: true for Teams provider.

   - Update form renderer: Add teams case in renderChannelForm in: src/components/wrappers/dashboard/admin/channels/helpers/common.tsx

---

## Reproduction Process

### Environment Setup


1. Clone the repository
   - git clone https://github.com/Portabase/portabase.git
   - cd portabase

2. Install dependencies
   - install nvm
      - install homebrew
         - /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
     - install nvm via homebrew: 
        - brew install nvm
     - Create the NVM directory:
        - mkdir ~/.nvm
     - Reload your terminal configuration so the changes take effect:
        - source ~/.zshrc
   - install node via nvm
      - nvm install --lts
      - nvm use --lts
   - pnpm install
      - For macos apple silicon:
         - curl -fsSL https://get.pnpm.io/install.sh | sh -


3. Environment configuration
   - Copy the example environment file and adjust values if necessary:
      - cp .env.example .env

4. Start in development mode
   - make up

The above four steps are the instructions given in the project wiki for contributors. However before starting in development mode you first need to get the database running locally: 
- docker compose up -d db


### Steps to Reproduce

1. Log onto app using credentials from .env
2. On left under Administration, navigate to Notifications > Channels.
3. Click + Add Notification Channel
4. Microsoft Teams button is unclickable

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork](https://github.com/Portabase/portabase/pull/313/commits)
- **Screenshots/logs:** ![Microsoft teams coming soon](./Microsoft_teams_pre.png)
- **My findings:** Microsoft teams option for notification provider is greyed out and labled as coming soon.

---

## Solution Approach

### Analysis

Feature is not yet implemented.

### Proposed Solution

Add Microsoft Teams as notification provider

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** 
Portabase currently supports multiple third-party webhook integrations (like Slack, Telegram, and Discord) to alert users about database backup statuses and agent connectivity. However, it currently lacks support for Microsoft Teams.

**Match:** 
I will use the existing slack.ts and discord.ts backend provider files (located in src/features/notifications/providers/) as a structural blueprint for how to format the outbound fetch request and error handling. For the frontend, I will look at the existing Zod schemas and ShadcnUI form components used for Discord/Slack to ensure my new Teams configuration form matches the project's design system and validation standards.

**Plan:** [Step-by-step implementation plan]
1. What is the root cause/context?
   - The codebase utilizes a strictly typed registry system (ProviderKind) and a unified channel form renderer. To add a new provider, it must be injected into multiple specific layers of the application—from the TypeScript definitions up to the React switch statements that render the UI.

2. What is the proposed fix/implementation?
   - I will implement this feature in two main slices:

      - Backend: Define the new teams type, create the sendTeams webhook execution logic, and register it in the main provider handler.

      - Frontend: Write a Zod validation schema for the Teams configuration (requiring a valid Webhook URL string), build the React form UI component, and update the global channel form schema and renderer switch statement to display the new UI.

3. What files will I touch?
I will create two new files and modify five existing files:

   - (New) src/features/notifications/providers/teams.ts

   - (New) src/components/wrappers/dashboard/admin/channels/channel/channel-form/providers/notifications/forms/teams.schema.ts

   - (New) src/components/wrappers/dashboard/admin/channels/channel/channel-form/providers/notifications/forms/teams.form.tsx

   - (Modify) src/features/notifications/providers/index.ts

   - (Modify) src/features/notifications/types.ts

   - (Modify) src/components/wrappers/dashboard/admin/channels/channel/channel-form/channel-form.schema.ts

   - (Modify) src/components/wrappers/dashboard/admin/channels/helpers/notification.tsx

   - (Modify) src/components/wrappers/dashboard/admin/channels/helpers/common.tsx

**Implement:** [Add Microsoft Teams as notification provider](https://github.com/Portabase/portabase/pull/313)

**Review:** 
I will self-review my code against Portabase's CONTRIBUTING.md. Specifically, I will ensure that my new sendTeams function is strictly typed using TypeScript, that the React components utilize the existing UI component library properly without inline styling, and that I have run the project's linter/formatter before committing. I will also verify that preview: true is fully removed from the Teams provider so it registers as an active feature

**Evaluate:** 
1. To test this feature end-to-end, I will:

2. Create a free Microsoft Teams workspace and generate a live Incoming Webhook URL.

3. Spin up the local Portabase Next.js and PostgreSQL development environment.

4. Navigate to the local dashboard and configure a new Microsoft Teams notification channel using my live webhook URL.

5. Trigger a test notification from the Portabase system and verify that the payload successfully appears in my external Microsoft Teams desktop client.

6. Capture the mandatory validation screenshots (Add dialog, Create mode, Edit mode) to append to my Pull Request.

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: Zod Schema Validation: Verified that TeamsChannelConfigSchema correctly enforces a valid URL format and rejects malformed webhook strings.
- [ ] Test case 2: Component Rendering Logic: Verified that the UI switch statement correctly maps the teams provider state to render the NotifierTeamsForm component.
- [ ] Test case 3: Database Enum Acceptance: Verified that the PostgreSQL provider_kind enum successfully accepts the new "teams" value without throwing a 42703 missing column or enum error.

### Integration Tests

- [ ] Integration scenario 1: Create Invalid Channel (Automated): Playwright navigates the dashboard, attempts to create a Microsoft Teams channel with a mocked invalid webhook URL, and verifies that the frontend correctly displays the server-side error toast.
- [ ] Integration scenario 2: Delete Invalid Channel (Automated): Playwright successfully deletes the mocked Teams channel and verifies it is removed from the UI and the database.
- [ ] Integration scenario 3: Valid Channel Flow (Manual/Commented): Successfully generated a mock URL via webhook.site, bypassed Next.js caching, inserted the valid configuration into the fresh database, and verified the successful creation toast in the UI.

### Manual Testing

1. Created a free Microsoft Teams workspace and generate a live Incoming Webhook URL.

2. Spun up the local Portabase Next.js and PostgreSQL development environment.

3. Navigated to the local dashboard and configure a new Microsoft Teams notification channel using my live webhook URL.

4. Triggered a test notification from the Portabase system and verify that the payload successfully appears in my external Microsoft Teams desktop client.

5. Captured the mandatory validation screenshots (Add dialog, Create mode, Edit mode) to append to my Pull Request.

---

## Implementation Notes

### Week [3] Progress

What you built: 
Added microsoft teams as notification provider and updated database schema.

Challenges face:
Merging local feature branch with remote main branch prior to creating PR led to breaking the feature I had implemented. Spent several hours troubleshooting, problem ended up being that the database had been updated while I was working on the feature branch and my local database in conflict with the updated code. Resolved by wiping local databse volume and rebuilding using the updated schema. 

Decisions made:
Original issue listed out implementation details for adding feature. I followed the instructions but feature was not working. Decided updating the database schema was within issue scope, in order to successfully implement feature. 


### Code Changes

- **Files modified:**
   1. Created Microsoft Teams notification provider
      - Implemented sendTeams in: src/features/channel/notifications/teams.ts
   2. Registered provider in notification system
      - Added sendTeams to provider handlers: src/features/channel/notifications/index.ts
      - Extended provider type system by adding teams to ProviderKind: src/features/notifications/notifications.types.ts
   3. Implemented Teams configuration form (schema + UI)
      - Created validation schema: src/features/channel/notifications/teams.schema.ts
      - Created UI form component: src/features/channel/notifications/teams.form.tsx
   4. Integrated Teams into UI provider system
      - Updated channel form schema: src/features/channel/channel-form.schema.ts
      - Updated provider registry: src/features/channel/channels-notification-helper.tsx
         - Removed preview: true for Teams provider.
      - Updated form renderer:
         - Added teams case in renderChannelForm in: src/features/channel/channels-helpers.tsx
   5. Updated database to include teams as provider
      - Added migration instruction: src/db/migrations/0062_futuristic_dragon_lord.sql
      - Updated schema: src/db/schema/09_notification-channel.ts
   6. Updated end to end testing
      - Added teams tests to : e2e/notification/teams.spec.ts

- **Key commits:**
   - [feat: Add Microsoft Teams as notification provider (issue 260)](https://github.com/Portabase/portabase/pull/313/changes/2328fa70bc2e29e24625736ea977e4603e149e0e)
      - committed main changes to implement feature
   - [Merge updates from main](https://github.com/Portabase/portabase/pull/313/changes/5b6399891e8e122eae851ff9a938ff726b77ba0a)
      - updated local feature branch with changes from main, broke feature when manually testing
   - [Update database migration](https://github.com/Portabase/portabase/pull/313/changes/7132db9b450fc8e89f5b4ff0cd735bd422eaed7f)
      - added database migration so that remote database implements changes I added to database schema
   - [add  Microsoft Teams notification provider E2E tests](https://github.com/Portabase/portabase/pull/313/changes/f3b581f2a1d1c922dd1cfc8710a18d92c4296524)
      - added end to end tests for microsoft teams notification feature


- **Approach decisions:**
   - For all changes I followed the existing coding patterns of the other notification providers previously implemented in order to maintain consistency in the codebase. 

---

## Pull Request

**PR Link:** [feat: Add Microsoft Teams as notification provider (issue 260)](https://github.com/Portabase/portabase/pull/313)

**PR Description:** 
- Adds Microsoft Teams as notification provider #260 

### Implementation
#### 1. Created Microsoft Teams notification provider
- Implemented sendTeams in: src/features/channel/notifications/teams.ts
#### 2. Registered provider in notification system
- Added sendTeams to provider handlers: src/features/channel/notifications/index.ts
- Extended provider type system by adding teams to ProviderKind:
src/features/notifications/notifications.types.ts
#### 3. Implemented Teams configuration form (schema + UI)
- Created validation schema: src/features/channel/notifications/teams.schema.ts
- Created UI form component: src/features/channel/notifications/teams.form.tsx
#### 4. Integrated Teams into UI provider system
- Updated channel form schema: src/features/channel/channel-form.schema.ts
- Updated provider registry: src/features/channel/channels-notification-helper.tsx
   - Removed preview: true for Teams provider.
- Updated form renderer:
   - Added teams case in renderChannelForm in: src/features/channel/channels-helpers.tsx
#### 5. Updated database to include teams as provider
- Added migration instruction: src/db/migrations/0062_futuristic_dragon_lord.sql
- Updated schema: src/db/schema/09_notification-channel.ts

### Validation 
- Screen recording successful testing of Microsoft Teams notification:

https://github.com/user-attachments/assets/994edebb-cce2-4d74-b983-87081d31168d

- Screenshots demonstrating:
#### “Notification Channel” dialog showing Microsoft Teams as an available provider
<img width="493" height="485" alt="“Notification Channel” dialog showing Microsoft Teams as an available provider" src="https://github.com/user-attachments/assets/7559b5c1-207d-4518-9d91-5fabb0a7ac31" />

#### Microsoft Teams configuration form in create mode
<img width="493" height="485" alt="Microsoft Teams configuration form in create mode" src="https://github.com/user-attachments/assets/a93cd0c2-d9e8-429c-a612-51bbfc59d592" />

#### Microsoft Teams configuration form in edit mode

<img width="493" height="485" alt="Microsoft Teams configuration form in edit mode" src="https://github.com/user-attachments/assets/a0521f36-1116-450a-a06e-bb368f3552aa" />




**Maintainer Feedback:**
- [6/9/26]: [Maintainer asked to attach screenshots of feature integration and asked if I had access to a Microsoft teams account so they could use it for the review.](https://github.com/Portabase/portabase/issues/260#issuecomment-4663777829)
- [6/10/26]: [I attached screenshots to reply, let them know I did not have access to a microsoft test account, informed them of the alternative I used for testing, and let them know I would do research on how to get hands on a Microsoft account that would grant ability to test the feature.](https://github.com/Portabase/portabase/issues/260#issuecomment-4671727831)

- [6/18/26]: [Maintainer asked if I had been able to find a test account](https://github.com/Portabase/portabase/issues/260#issuecomment-4745488986)
- [6/18/26] [ Informed them not yet, but that I had come accross a method and would try it out. ](https://github.com/Portabase/portabase/issues/260#issuecomment-4745615972)
-  [6/18/26] [Maintainer informed me that they would insist on testing with a real Microsoft Teams webhook to avoid issues in production later](https://github.com/Portabase/portabase/issues/260#issuecomment-4745638123)
- [6/18/26] [Maintainer assurred me that PR seemed good and would only need testing with a real webhook to be approved](https://github.com/Portabase/portabase/pull/313#issuecomment-4745792973)
- [6/18/26] [I informed maintainer that I had been able to test the feature with a Microsoft Teams webhook, provided screen recording of test, and provided instructions on how to gain access to a Microsoft Teams business account in order to replicate testing.](https://github.com/Portabase/portabase/pull/313#issuecomment-4746813192)


**Status:** [ Merged]

---

## Learnings & Reflections

### Technical Skills Gained

- Drizzle ORM & PostgreSQL Migrations: Learned how Drizzle tracks state via snapshot.json files, how to generate new SQL migration files (drizzle-kit generate), and the difference between applying migrations normally versus force-syncing the schema (drizzle-kit push) when local volumes get out of sync.
- Playwright E2E Testing: Gained exposure to end-to-end testing architecture, specifically how Playwright interacts with the UI, mocks user inputs using regex locators (getByLabel(/Teams Webhook URL/)), and tests both valid and invalid server-action flows.
- Docker Volume Management: Learned how to safely destroy and rebuild local PostgreSQL data volumes (docker-compose down -v) to reset corrupted local migration states.

### Challenges Overcome

- Testing Infrastructure Access: 
   - The most significant external hurdle was securing access to a Microsoft account with the appropriate permissions to generate a live Microsoft Teams webhook. Neither I nor the reviewers initally had access to such an account, but it was critical for manually verifying the end-to-end integration before merging the PR.
- Silent Data Mismatches: 
   - Faced a bug where the form wouldn't render or validate because the internal state ("microsoft-teams") didn't match the strict Zod literal and switch statement ("teams"). Solved this by auditing the string identifiers across the UI helpers, schemas, and action files to ensure absolute uniformity.

### What I'd Do Differently Next Time

- Pull Upstream Earlier: 
   - To avoid massiveconflicts, I will frequently git pull from the main branch during development, especially before generating database migrations, to ensure my local repo is perfectly synchronized with the rest of the team's work.

---

## Resources Used

- [Drizzle ORM Migrations Documentation](https://orm.drizzle.team/docs/kit-overview)
- [Microsoft Business Account free trial](https://www.microsoft.com/en-us/microsoft-365/business/microsoft-365-business-standard-one-month-trial)
