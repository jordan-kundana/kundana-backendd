# Deployment Instructions for Kundana Dating Website

This guide provides step-by-step instructions for deploying the Kundana dating website to your own domain with full control, including login functionality and an admin dashboard.

## Prerequisites

Before you begin, ensure you have:

1. A domain name (e.g., yourdomain.com)
2. Web hosting service or cloud provider account (AWS, DigitalOcean, Vercel, etc.)
3. Basic knowledge of command line operations
4. Node.js and npm installed on your local machine
5. MongoDB Atlas account (for database) or alternative database service

## Option 1: Static Website Deployment (Basic)

If you only need to host the static website without login functionality or admin dashboard:

### Step 1: Upload Static Files

1. Extract the `kundana_static_website.zip` file to your local machine
2. Upload the contents to your web hosting service using FTP or their file manager
3. Point your domain to the hosting service according to their instructions

This option is quick and simple but doesn't include login functionality or an admin dashboard.

## Option 2: Full Website Deployment with Authentication and Admin Dashboard

For complete functionality including user authentication and admin dashboard:

### Step 1: Set Up Your Development Environment

1. Extract the `kundana_website.zip` file to your local machine
2. Open a terminal and navigate to the extracted directory
3. Install dependencies:

```bash
npm install
```

### Step 2: Set Up MongoDB Database

1. Create a MongoDB Atlas account if you don't have one
2. Create a new cluster and database
3. Create the following collections:
   - users
   - profiles
   - messages
   - matches
   - transactions
   - user_activity
4. Get your MongoDB connection string from Atlas

### Step 3: Configure Environment Variables

Create a `.env.local` file in the project root with the following variables:

```
# Database
MONGODB_URI=your_mongodb_connection_string
DB_NAME=kundana

# Authentication
JWT_SECRET=your_secure_secret_key
NEXTAUTH_URL=https://yourdomain.com
NEXTAUTH_SECRET=another_secure_secret_key

# Admin
ADMIN_EMAIL=your_admin_email@example.com
```

Replace the placeholder values with your actual configuration.

### Step 4: Implement Backend Authentication

Follow the instructions in the `backend_auth_implementation.md` document to:

1. Create API routes for authentication
2. Set up database connection
3. Implement middleware for protected routes
4. Update frontend components to use authentication

### Step 5: Implement Admin Dashboard

Follow the instructions in the `admin_dashboard_implementation.md` document to:

1. Create admin layout and pages
2. Implement admin API routes
3. Connect frontend components to API endpoints
4. Secure admin routes

### Step 6: Test Locally

1. Run the development server:

```bash
npm run dev
```

2. Open your browser and navigate to `http://localhost:3000`
3. Test user registration, login, and admin functionality

### Step 7: Build for Production

Once everything is working correctly, build the application for production:

```bash
npm run build
```

### Step 8: Deploy to Hosting Service

#### Option A: Vercel (Recommended for Next.js)

1. Install Vercel CLI:

```bash
npm install -g vercel
```

2. Deploy to Vercel:

```bash
vercel
```

3. Follow the prompts to link your project to your Vercel account
4. Configure your custom domain in the Vercel dashboard

#### Option B: Traditional Hosting

1. Upload the `.next`, `public`, `package.json`, and `next.config.js` files to your server
2. Install dependencies on the server:

```bash
npm install --production
```

3. Start the Next.js server:

```bash
npm start
```

4. Set up a process manager like PM2 to keep the application running:

```bash
npm install -g pm2
pm2 start npm --name "kundana" -- start
pm2 save
pm2 startup
```

#### Option C: Docker Deployment

1. Create a `Dockerfile` in the project root:

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

RUN npm run build

EXPOSE 3000

CMD ["npm", "start"]
```

2. Build and run the Docker container:

```bash
docker build -t kundana .
docker run -p 3000:3000 -d kundana
```

### Step 9: Set Up Domain and SSL

1. Point your domain to your hosting provider's nameservers
2. Configure DNS settings according to your hosting provider's instructions
3. Set up SSL certificate (many providers offer free Let's Encrypt certificates)

### Step 10: Create Admin Account

1. Access your MongoDB database directly or through a tool like MongoDB Compass
2. Find your user document in the users collection
3. Add the `isAdmin: true` field to your user document

## Maintenance and Updates

### Regular Backups

Set up regular backups of your MongoDB database:

```bash
mongodump --uri="your_mongodb_connection_string" --out=backup_directory
```

### Monitoring

Consider setting up monitoring for your application using services like:
- Uptime Robot (for uptime monitoring)
- Sentry (for error tracking)
- Google Analytics (for user analytics)

### Updates

To update the application in the future:

1. Pull the latest code changes
2. Install any new dependencies:

```bash
npm install
```

3. Build the application:

```bash
npm run build
```

4. Restart the application:

```bash
pm2 restart kundana
```

## Troubleshooting

### Database Connection Issues

If you encounter database connection issues:
1. Verify your MongoDB connection string in the `.env.local` file
2. Ensure your IP address is whitelisted in MongoDB Atlas
3. Check that your database user has the correct permissions

### Authentication Problems

If users cannot log in:
1. Check that your JWT_SECRET and NEXTAUTH_SECRET are properly set
2. Verify that your NEXTAUTH_URL matches your actual domain
3. Check browser console for any JavaScript errors

### Admin Access Issues

If you cannot access the admin dashboard:
1. Verify that your user has the `isAdmin: true` field in the database
2. Check that you're properly logged in
3. Clear browser cookies and try logging in again

## Support

For additional support or custom development needs, please contact the Kundana development team.
