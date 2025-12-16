# Database Setup Guide

This guide will walk you through setting up the database for your Leaderboard application using [Synth Hosting](https://synthhosting.com/).

## Prerequisites

- Node.js installed on your local machine or whereever you plan to host it.
- Synth Hosting account

## 1. Database Hosting Setup

### Step 1: Create Bot Hosting Instance

1. Visit [Synth Hosting Bot Hosting](https://synthhosting.com/bots)
2. Select a suitable hosting plan for your bot
3. Click **Deploy** to create your hosting instance
4. Wait for the deployment to complete

### Step 2: Access Your Bot Panel

1. Log into your Synth Hosting account
2. Navigate to your **Bot Panel**
3. Select your newly created bot instance

### Step 3: Create Database

1. In your bot panel, navigate to the **Database** tab
2. Click **Create Database**
3. Give your database a meaningful name (e.g., `leaderboard_db`)
4. Wait for the database to be created

### Step 4: Get Database Connection String

1. Once created, you'll see a **"JDBC Connection String"**
2. Copy the entire connection string
3. **Important**: Remove the `jdbc:` prefix from the beginning of the URL

**Example:**
- Original: `jdbc:mysql://host:port/database?user=username&password=password`
- Modified: `mysql://host:port/database?user=username&password=password`

## 2. Environment Configuration

### Step 1: Set Database URL

1. In your project root directory, create or edit your `.env` file
2. Add the database URL:

```env
DATABASE_URL="mysql://host:port/database?user=username&password=password"
```

Replace the connection string with your actual modified JDBC string from Synth Hosting.

### Step 2: Verify Environment Variables

Ensure your `.env` file also contains other required variables:

```env
# Database
DATABASE_URL="your_database_connection_string_here"

# NextAuth Configuration
NEXTAUTH_SECRET="your_nextauth_secret_here"
NEXTAUTH_URL="http://localhost:3000"

# Steam API
STEAM_API_KEY="your_steam_api_key_here"
```

## 3. Database Initialization

### Step 1: Install Dependencies

```bash
npm install
```

### Step 2: Generate Prisma Client

```bash
npx prisma generate
```

### Step 3: Push Database Schema

```bash
npx prisma db push
```

This command will:
- Create all the necessary tables in your database
- Set up the schema based on your `prisma/schema.prisma` file

### Step 4: Seed the Database

To populate your database with initial data including default themes and permissions:

```bash
node prisma/seed.js
```

This will create:
- Default theme settings
- Available permissions
- Sample theme presets

## 4. Adding Your First Admin

### Method 1: Direct Database Insertion (Recommended)

1. **First, get your Steam ID:**
   - Visit [SteamID.io](https://steamid.io/)
   - Enter your Steam profile URL
   - Copy your **SteamID64** (17-digit number)

2. **Use Prisma Studio to add admin:**
   ```bash
   npx prisma studio
   ```
   
3. **In Prisma Studio:**
   - Navigate to the `Admins` table
   - Click **Add record**
   - Fill in the following fields:
     - `steamId`: Your SteamID64
     - `displayName`: Your display name
     - `ownerId`: Your SteamID64 (same as steamId for owner access)
   - Click **Save**

4. **Add admin permissions:**
   - First, check the `Permissions` table to find the ID of the `owner_access` permission
   - Navigate to the `AdminPermissions` table
   - Click **Add record**
   - Set `adminSteamId` to your SteamID64
   - Set `permissionId` to the ID number of the `owner_access` permission (usually 7 if using default seed data)
   - Click **Save**

### Method 2: Using the Application

1. **Start your application:**
   ```bash
   npm run dev
   ```

2. **Login with Steam:**
   - Visit `http://localhost:3000`
   - Click **Login with Steam**
   - Complete the Steam authentication

3. **Manually grant permissions:**
   - After logging in, your admin record will NOT be automatically created
   - You must manually add yourself to the `Admins` table using Prisma Studio
   - Then add the `owner_access` permission in the `AdminPermissions` table

## 5. Verification

### Check Database Connection

```bash
npx prisma db pull
```

This should complete without errors if your database connection is working.

### Verify Tables

```bash
npx prisma studio
```

You should see all the required tables:
- `Admins`
- `AdminPermissions`
- `Permissions`
- `Servers`
- `WipeSchedules`
- `ThemeSettings`
- `Stats`

### Test Admin Access

1. Start your application: `npm run dev`
2. Login with Steam using your configured admin account
3. Try accessing the admin panel at `/admin`
4. You should have access to all admin features

## 6. Available Permissions

Your application supports the following permission levels:

- `owner_access` - Full owner access to all features and settings
- `view_stats` - View player statistics and leaderboard data  
- `manage_admins` - Add, edit, and remove admin users
- `manage_permissions` - Assign and revoke admin permissions
- `view_admin_panel` - Access the admin panel interface
- `edit_player_stats` - Modify player statistics and records
- `delete_player_stats` - Delete player statistics and records
- `server_management` - Manage server configurations and settings
- `manage_themes` - Create, edit, and manage theme settings

**Note:** The `owner_access` permission grants access to all features. For granular control, use the specific permissions listed above.

## 7. Troubleshooting

### Common Issues

**Connection Errors:**
- Verify your DATABASE_URL is correctly formatted
- Ensure you removed the `jdbc:` prefix
- Check that your Synth Hosting database is running

**Permission Denied:**
- Make sure your admin has the correct Steam ID in the `Admins` table
- Verify the admin has the appropriate permissions in the `AdminPermissions` table

**Schema Issues:**
- Run `npx prisma db push` to sync your schema
- Check for any migration errors

### Getting Help

If you encounter issues:
1. Check the application logs for detailed error messages
2. Verify your database connection in Synth Hosting panel
3. Use `npx prisma studio` to inspect your database directly
4. Ensure all environment variables are correctly set

## 8. Production Deployment

When deploying to production:

1. Update your `NEXTAUTH_URL` in environment variables
2. Use a secure `NEXTAUTH_SECRET`
3. Ensure your database connection string is secure
4. Consider setting up database backups through Synth Hosting

---

**Need Support?** 
Visit [Synth Hosting Support](https://synthhosting.com/) for hosting-related issues, or check your application logs for debugging information.
