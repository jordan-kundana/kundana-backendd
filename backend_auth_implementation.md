# Backend Authentication Implementation for Kundana Dating Website

## Overview

This document outlines how to implement backend authentication for the Kundana dating website, allowing user registration, login functionality, and an admin dashboard.

## Technology Stack

For implementing backend authentication and admin functionality, we recommend using:

1. **Next.js API Routes**: For server-side functionality
2. **MongoDB or PostgreSQL**: For database storage
3. **NextAuth.js**: For authentication
4. **JWT (JSON Web Tokens)**: For secure authentication
5. **bcrypt**: For password hashing

## Implementation Steps

### 1. Set Up Database

```bash
# Install MongoDB driver or PostgreSQL client
npm install mongodb
# OR
npm install pg
```

Create database schemas for:
- Users (id, name, email, password_hash, gender, age, location, phone, created_at, premium_status)
- Profiles (user_id, interests, about, photos)
- Messages (sender_id, receiver_id, content, timestamp, read_status)
- Matches (user1_id, user2_id, status, created_at)

### 2. Install Authentication Libraries

```bash
npm install next-auth bcrypt jsonwebtoken
```

### 3. Create Authentication API Routes

Create the following API routes in `src/app/api/auth/`:

#### Registration Endpoint (`src/app/api/auth/register/route.ts`):

```typescript
import { NextResponse } from 'next/server';
import bcrypt from 'bcrypt';
import { connectToDatabase } from '@/lib/db';

export async function POST(request: Request) {
  try {
    const { name, email, password, gender, age, location, phone } = await request.json();
    
    // Validate input
    if (!name || !email || !password || !gender || !age || !location || !phone) {
      return NextResponse.json({ error: 'Missing required fields' }, { status: 400 });
    }
    
    const db = await connectToDatabase();
    const users = db.collection('users');
    
    // Check if user already exists
    const existingUser = await users.findOne({ email });
    if (existingUser) {
      return NextResponse.json({ error: 'User already exists' }, { status: 409 });
    }
    
    // Hash password
    const saltRounds = 10;
    const passwordHash = await bcrypt.hash(password, saltRounds);
    
    // Create user
    const result = await users.insertOne({
      name,
      email,
      password_hash: passwordHash,
      gender,
      age,
      location,
      phone,
      created_at: new Date(),
      premium_status: false
    });
    
    return NextResponse.json({ 
      message: 'User registered successfully',
      userId: result.insertedId 
    }, { status: 201 });
    
  } catch (error) {
    console.error('Registration error:', error);
    return NextResponse.json({ error: 'Internal server error' }, { status: 500 });
  }
}
```

#### Login Endpoint (`src/app/api/auth/login/route.ts`):

```typescript
import { NextResponse } from 'next/server';
import bcrypt from 'bcrypt';
import jwt from 'jsonwebtoken';
import { connectToDatabase } from '@/lib/db';

export async function POST(request: Request) {
  try {
    const { email, password } = await request.json();
    
    // Validate input
    if (!email || !password) {
      return NextResponse.json({ error: 'Email and password are required' }, { status: 400 });
    }
    
    const db = await connectToDatabase();
    const users = db.collection('users');
    
    // Find user
    const user = await users.findOne({ email });
    if (!user) {
      return NextResponse.json({ error: 'Invalid credentials' }, { status: 401 });
    }
    
    // Verify password
    const passwordMatch = await bcrypt.compare(password, user.password_hash);
    if (!passwordMatch) {
      return NextResponse.json({ error: 'Invalid credentials' }, { status: 401 });
    }
    
    // Generate JWT token
    const token = jwt.sign(
      { 
        userId: user._id,
        email: user.email,
        isAdmin: user.isAdmin || false
      },
      process.env.JWT_SECRET || 'your-secret-key',
      { expiresIn: '7d' }
    );
    
    // Set HTTP-only cookie with the token
    const response = NextResponse.json({ 
      message: 'Login successful',
      user: {
        id: user._id,
        name: user.name,
        email: user.email,
        isAdmin: user.isAdmin || false
      }
    }, { status: 200 });
    
    response.cookies.set({
      name: 'auth-token',
      value: token,
      httpOnly: true,
      secure: process.env.NODE_ENV === 'production',
      sameSite: 'strict',
      maxAge: 60 * 60 * 24 * 7 // 7 days
    });
    
    return response;
    
  } catch (error) {
    console.error('Login error:', error);
    return NextResponse.json({ error: 'Internal server error' }, { status: 500 });
  }
}
```

### 4. Create Authentication Middleware

Create a middleware to protect routes (`src/middleware.ts`):

```typescript
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { jwtVerify } from 'jose';

export async function middleware(request: NextRequest) {
  // Get token from cookie
  const token = request.cookies.get('auth-token')?.value;
  
  // Check if path requires authentication
  const isAuthPath = request.nextUrl.pathname.startsWith('/api/protected') || 
                     request.nextUrl.pathname.startsWith('/admin');
  
  if (!isAuthPath) {
    return NextResponse.next();
  }
  
  // If no token and path requires auth, redirect to login
  if (!token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  
  try {
    // Verify token
    const secret = new TextEncoder().encode(process.env.JWT_SECRET || 'your-secret-key');
    const { payload } = await jwtVerify(token, secret);
    
    // Check if admin route and user is admin
    if (request.nextUrl.pathname.startsWith('/admin') && !payload.isAdmin) {
      return NextResponse.redirect(new URL('/unauthorized', request.url));
    }
    
    // Add user info to request headers
    const requestHeaders = new Headers(request.headers);
    requestHeaders.set('x-user-id', payload.userId as string);
    requestHeaders.set('x-user-email', payload.email as string);
    
    return NextResponse.next({
      request: {
        headers: requestHeaders,
      },
    });
    
  } catch (error) {
    // Token is invalid
    return NextResponse.redirect(new URL('/login', request.url));
  }
}

// Configure which paths should be processed by this middleware
export const config = {
  matcher: ['/api/protected/:path*', '/admin/:path*'],
};
```

### 5. Create Database Connection Utility

Create a database connection utility (`src/lib/db.ts`):

```typescript
import { MongoClient } from 'mongodb';

const uri = process.env.MONGODB_URI || 'mongodb://localhost:27017';
const dbName = process.env.DB_NAME || 'kundana';

let cachedClient: MongoClient | null = null;
let cachedDb: any = null;

export async function connectToDatabase() {
  if (cachedDb) {
    return cachedDb;
  }

  if (!cachedClient) {
    cachedClient = new MongoClient(uri);
    await cachedClient.connect();
  }

  cachedDb = cachedClient.db(dbName);
  return cachedDb;
}
```

## Environment Setup

Create a `.env.local` file with the following variables:

```
MONGODB_URI=mongodb://localhost:27017
DB_NAME=kundana
JWT_SECRET=your-secure-secret-key
NEXTAUTH_URL=http://yourdomain.com
NEXTAUTH_SECRET=another-secure-secret-key
```

## Next Steps

After implementing the authentication backend:

1. Update the frontend forms to connect to these API endpoints
2. Implement protected routes for user profiles and messaging
3. Create the admin dashboard (covered in the next document)
