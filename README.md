# Database Setup Guide

This guide will walk you through setting up the database for your Leaderboard application using [Synth Hosting](https://synthhosting.com/).

## Prerequisites

- Node.js installed on your local machine
- Git repository cloned
- Synth Hosting account

## 1. Database Hosting Setup

### Step 1: Create Bot Hosting Instance

1. Visit [Synth Hosting Bot Hosting](https://synthhosting.com/bots)
2. Select a suitable hosting plan for your bot
3. Tip: make sure you use code 4BOTSFREE - this give you a free bot :D
4. Click **Deploy** to create your hosting instance
5. Wait for the deployment to complete

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

1. In your project root directory, create or edit your `.env.local` file
2. Add the database URL:

```env
DATABASE_URL="mysql://host:port/database?user=username&password=password"
```

Replace the connection string with your actual modified JDBC string from Synth Hosting.

### Step 2: Verify Environment Variables

Ensure your `.env.local` file also contains other required variables:

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

### Step 4: Seed the Database (Optional)

To populate your database with initial data including default themes and permissions:

```bash
npm run seed
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
   - Navigate to the `User` table
   - Click **Add record**
   - Fill in the following fields:
     - `steamId`: Your SteamID64
     - `name`: Your display name
     - `image`: Your Steam avatar URL (optional)
   - Click **Save**

4. **Add admin permissions:**
   - Navigate to the `UserPermission` table
   - Click **Add record**
   - Set `userId` to the ID of the user you just created
   - Set `permission` to `super_admin`
   - Click **Save**

### Method 2: Direct SQL (Advanced Users)

If you prefer using SQL directly:

```sql
-- Insert user (replace with your actual Steam ID and details)
INSERT INTO User (steamId, name, image) 
VALUES ('76561198000000000', 'Your Name', 'https://steamcdn-a.akamaihd.net/steamcommunity/public/images/avatars/your_avatar.jpg');

-- Get the user ID (note the ID from the previous insert)
-- Replace 'USER_ID_HERE' with the actual ID returned

-- Grant super admin permissions
INSERT INTO UserPermission (userId, permission) 
VALUES (USER_ID_HERE, 'super_admin');
```

### Method 3: Using the Application

1. **Start your application:**
   ```bash
   npm run dev
   ```

2. **Login with Steam:**
   - Visit `http://localhost:3000`
   - Click **Login with Steam**
   - Complete the Steam authentication

3. **Manually grant permissions:**
   - Use Prisma Studio or direct SQL to add the `super_admin` permission to your user
   - Your user record will be automatically created during the Steam login process

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
- `User`
- `UserPermission`
- `Server`
- `WipeSchedule`
- `ThemeSettings`
- `PlayerStats`

### Test Admin Access

1. Start your application: `npm run dev`
2. Login with Steam using your configured admin account
3. Try accessing the admin panel at `/admin`
4. You should have access to all admin features

## 6. Available Permissions

Your application supports the following permission levels:

- `super_admin` - Full access to all features
- `manage_players` - Can manage player statistics and data
- `manage_servers` - Can manage server configurations
- `manage_wipes` - Can manage wipe schedules and execute wipes
- `manage_themes` - Can manage theme settings and customization
- `view_stats` - Can view statistics and reports

## 7. Troubleshooting

### Common Issues

**Connection Errors:**
- Verify your DATABASE_URL is correctly formatted
- Ensure you removed the `jdbc:` prefix
- Check that your Synth Hosting database is running

**Permission Denied:**
- Make sure your user has the correct Steam ID
- Verify the user has the appropriate permissions in the `UserPermission` table

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
