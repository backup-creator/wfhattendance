# WFH Attendance

Role-based WFH attendance system built for Cloudflare Workers and D1.

## Access rules
- Employee: create one attendance record per date, check out, view own records, update daily accomplishment/remarks only.
- Admin: view all attendance, delete attendance records, manage employee/admin accounts, and view reports/export CSV.
- Check-in, check-out, and creation timestamps are server-generated and cannot be changed through the ordinary accomplishment update endpoint.
- The first-run setup creates one admin and one employee account. It runs only while the users table is empty, so it will not create duplicate seed accounts.
- Passwords use PBKDF2-SHA-256 hashes; sessions use HttpOnly, Secure, SameSite=Lax cookies. Both initial accounts must change their password on first login.

## Deploy to Cloudflare
1. Install Node.js, then run `npm install`.
2. Authenticate: `npx wrangler login`.
3. Create the D1 database: `npx wrangler d1 create wfhattendance-db`.
4. Copy the database ID returned by Cloudflare into `wrangler.toml`, replacing `REPLACE_WITH_YOUR_D1_DATABASE_ID`.
5. Apply the database migration: `npx wrangler d1 migrations apply wfhattendance-db --remote`.
6. Deploy: `npx wrangler deploy`.
7. Open the deployed Worker URL and complete first-time setup with unique usernames and passwords of at least 12 characters.

For local development, run `npx wrangler d1 migrations apply wfhattendance-db --local` and then `npx wrangler dev`.

## Before production use
Configure backups and retention, rate limiting, account recovery, and audit logging. Admin deletion is permanent. The Worker API is the authority for permissions; hiding buttons in the UI is not the security boundary. Never commit real passwords, tokens, or the actual D1 database ID into a public repository.