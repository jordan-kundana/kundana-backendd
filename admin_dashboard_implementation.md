# Admin Dashboard Implementation for Kundana Dating Website

## Overview

This document outlines how to implement an admin dashboard for the Kundana dating website, allowing site administrators to manage users, monitor activity, and handle content moderation.

## Technology Stack

For implementing the admin dashboard, we recommend using:

1. **Next.js**: For the frontend framework
2. **React**: For UI components
3. **Tailwind CSS**: For styling (already in use)
4. **React Query**: For data fetching and state management
5. **Chart.js**: For analytics visualizations

## Implementation Steps

### 1. Install Required Packages

```bash
npm install react-query chart.js react-chartjs-2 @headlessui/react
```

### 2. Create Admin Layout

Create an admin layout component (`src/app/admin/layout.tsx`):

```typescript
import { ReactNode } from 'react';
import Link from 'next/link';

export default function AdminLayout({ children }: { children: ReactNode }) {
  return (
    <div className="min-h-screen bg-gray-100">
      <nav className="bg-white shadow-md">
        <div className="container mx-auto px-4 py-4 flex justify-between items-center">
          <Link href="/admin" className="text-2xl font-bold text-blue-600">Kundana Admin</Link>
          <div className="space-x-4">
            <Link href="/" className="text-gray-600 hover:text-blue-600">View Site</Link>
            <button className="bg-red-600 text-white px-4 py-2 rounded-md hover:bg-red-700">Logout</button>
          </div>
        </div>
      </nav>
      
      <div className="flex">
        {/* Sidebar */}
        <div className="w-64 bg-white shadow-md h-[calc(100vh-4rem)] p-4">
          <div className="space-y-2">
            <p className="text-gray-500 uppercase text-xs font-semibold mb-2">Dashboard</p>
            <Link href="/admin" className="block py-2 px-4 rounded hover:bg-blue-50 hover:text-blue-600">Overview</Link>
            
            <p className="text-gray-500 uppercase text-xs font-semibold mt-6 mb-2">User Management</p>
            <Link href="/admin/users" className="block py-2 px-4 rounded hover:bg-blue-50 hover:text-blue-600">All Users</Link>
            <Link href="/admin/premium-users" className="block py-2 px-4 rounded hover:bg-blue-50 hover:text-blue-600">Premium Users</Link>
            
            <p className="text-gray-500 uppercase text-xs font-semibold mt-6 mb-2">Content</p>
            <Link href="/admin/profiles" className="block py-2 px-4 rounded hover:bg-blue-50 hover:text-blue-600">Profile Moderation</Link>
            <Link href="/admin/reports" className="block py-2 px-4 rounded hover:bg-blue-50 hover:text-blue-600">User Reports</Link>
            
            <p className="text-gray-500 uppercase text-xs font-semibold mt-6 mb-2">Payments</p>
            <Link href="/admin/transactions" className="block py-2 px-4 rounded hover:bg-blue-50 hover:text-blue-600">Transactions</Link>
            <Link href="/admin/subscriptions" className="block py-2 px-4 rounded hover:bg-blue-50 hover:text-blue-600">Subscriptions</Link>
            
            <p className="text-gray-500 uppercase text-xs font-semibold mt-6 mb-2">Settings</p>
            <Link href="/admin/settings" className="block py-2 px-4 rounded hover:bg-blue-50 hover:text-blue-600">Site Settings</Link>
          </div>
        </div>
        
        {/* Main Content */}
        <div className="flex-1 p-8 overflow-y-auto">
          {children}
        </div>
      </div>
    </div>
  );
}
```

### 3. Create Admin Dashboard Overview Page

Create the main admin dashboard page (`src/app/admin/page.tsx`):

```typescript
'use client';

import { useState, useEffect } from 'react';
import { Chart as ChartJS, ArcElement, CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend } from 'chart.js';
import { Doughnut, Line } from 'react-chartjs-2';

// Register ChartJS components
ChartJS.register(ArcElement, CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend);

export default function AdminDashboard() {
  const [stats, setStats] = useState({
    totalUsers: 0,
    premiumUsers: 0,
    activeToday: 0,
    newSignups: 0,
    revenue: 0
  });
  
  // Mock data - replace with actual API calls
  useEffect(() => {
    // Simulate API call
    setTimeout(() => {
      setStats({
        totalUsers: 10542,
        premiumUsers: 2187,
        activeToday: 3256,
        newSignups: 124,
        revenue: 1250000
      });
    }, 500);
  }, []);
  
  // User type distribution chart data
  const userTypeData = {
    labels: ['Free Users', 'Premium Users'],
    datasets: [
      {
        data: [stats.totalUsers - stats.premiumUsers, stats.premiumUsers],
        backgroundColor: ['#3b82f6', '#8b5cf6'],
        borderColor: ['#2563eb', '#7c3aed'],
        borderWidth: 1,
      },
    ],
  };
  
  // User activity chart data (last 7 days)
  const activityData = {
    labels: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
    datasets: [
      {
        label: 'Active Users',
        data: [2100, 2300, 2000, 2800, 3100, 3400, 3256],
        borderColor: '#3b82f6',
        backgroundColor: 'rgba(59, 130, 246, 0.5)',
        tension: 0.3,
      },
      {
        label: 'New Signups',
        data: [95, 102, 87, 105, 120, 132, 124],
        borderColor: '#8b5cf6',
        backgroundColor: 'rgba(139, 92, 246, 0.5)',
        tension: 0.3,
      },
    ],
  };

  return (
    <div>
      <h1 className="text-3xl font-bold text-gray-900 mb-8">Dashboard Overview</h1>
      
      {/* Stats Cards */}
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
        <div className="bg-white rounded-lg shadow-md p-6">
          <p className="text-sm text-gray-500 mb-1">Total Users</p>
          <p className="text-2xl font-bold">{stats.totalUsers.toLocaleString()}</p>
        </div>
        <div className="bg-white rounded-lg shadow-md p-6">
          <p className="text-sm text-gray-500 mb-1">Premium Users</p>
          <p className="text-2xl font-bold">{stats.premiumUsers.toLocaleString()} <span className="text-sm text-gray-500 font-normal">({Math.round(stats.premiumUsers / stats.totalUsers * 100)}%)</span></p>
        </div>
        <div className="bg-white rounded-lg shadow-md p-6">
          <p className="text-sm text-gray-500 mb-1">Active Today</p>
          <p className="text-2xl font-bold">{stats.activeToday.toLocaleString()}</p>
        </div>
        <div className="bg-white rounded-lg shadow-md p-6">
          <p className="text-sm text-gray-500 mb-1">New Signups (Today)</p>
          <p className="text-2xl font-bold">{stats.newSignups.toLocaleString()}</p>
        </div>
      </div>
      
      {/* Charts */}
      <div className="grid grid-cols-1 lg:grid-cols-2 gap-8 mb-8">
        <div className="bg-white rounded-lg shadow-md p-6">
          <h2 className="text-xl font-semibold text-gray-900 mb-4">User Distribution</h2>
          <div className="h-64 flex items-center justify-center">
            <Doughnut data={userTypeData} options={{ maintainAspectRatio: false }} />
          </div>
        </div>
        <div className="bg-white rounded-lg shadow-md p-6">
          <h2 className="text-xl font-semibold text-gray-900 mb-4">User Activity (Last 7 Days)</h2>
          <div className="h-64">
            <Line 
              data={activityData} 
              options={{ 
                maintainAspectRatio: false,
                scales: {
                  y: {
                    beginAtZero: true
                  }
                }
              }} 
            />
          </div>
        </div>
      </div>
      
      {/* Revenue Section */}
      <div className="bg-white rounded-lg shadow-md p-6 mb-8">
        <h2 className="text-xl font-semibold text-gray-900 mb-4">Revenue Overview</h2>
        <div className="flex items-center">
          <div className="text-3xl font-bold text-gray-900">{(stats.revenue / 100).toLocaleString()} RWF</div>
          <div className="ml-4 px-2 py-1 bg-green-100 text-green-800 rounded text-sm">+12.5% from last month</div>
        </div>
      </div>
      
      {/* Recent Activity */}
      <div className="bg-white rounded-lg shadow-md p-6">
        <h2 className="text-xl font-semibold text-gray-900 mb-4">Recent Activity</h2>
        <table className="w-full">
          <thead>
            <tr className="border-b border-gray-200">
              <th className="text-left py-3 px-4 text-sm font-medium text-gray-500">User</th>
              <th className="text-left py-3 px-4 text-sm font-medium text-gray-500">Action</th>
              <th className="text-left py-3 px-4 text-sm font-medium text-gray-500">Time</th>
            </tr>
          </thead>
          <tbody>
            <tr className="border-b border-gray-200">
              <td className="py-3 px-4">Marie K.</td>
              <td className="py-3 px-4">Upgraded to Premium</td>
              <td className="py-3 px-4 text-gray-500">10 minutes ago</td>
            </tr>
            <tr className="border-b border-gray-200">
              <td className="py-3 px-4">Jean B.</td>
              <td className="py-3 px-4">Updated profile</td>
              <td className="py-3 px-4 text-gray-500">25 minutes ago</td>
            </tr>
            <tr className="border-b border-gray-200">
              <td className="py-3 px-4">Claire M.</td>
              <td className="py-3 px-4">Reported a user</td>
              <td className="py-3 px-4 text-gray-500">1 hour ago</td>
            </tr>
            <tr className="border-b border-gray-200">
              <td className="py-3 px-4">Patrick N.</td>
              <td className="py-3 px-4">New registration</td>
              <td className="py-3 px-4 text-gray-500">2 hours ago</td>
            </tr>
            <tr>
              <td className="py-3 px-4">Diane R.</td>
              <td className="py-3 px-4">Sent 5 messages</td>
              <td className="py-3 px-4 text-gray-500">3 hours ago</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  );
}
```

### 4. Create User Management Page

Create the user management page (`src/app/admin/users/page.tsx`):

```typescript
'use client';

import { useState, useEffect } from 'react';
import Link from 'next/link';

interface User {
  id: string;
  name: string;
  email: string;
  phone: string;
  location: string;
  age: number;
  gender: string;
  premium: boolean;
  joinDate: string;
  lastActive: string;
}

export default function UsersManagement() {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [searchTerm, setSearchTerm] = useState('');
  const [filter, setFilter] = useState('all'); // all, premium, free
  
  // Mock data - replace with actual API calls
  useEffect(() => {
    // Simulate API call
    setTimeout(() => {
      const mockUsers: User[] = [
        {
          id: '1',
          name: 'Marie Kamikazi',
          email: 'marie@example.com',
          phone: '078XXXXXXX',
          location: 'Kigali',
          age: 26,
          gender: 'Female',
          premium: true,
          joinDate: '2025-03-15',
          lastActive: '2025-04-05'
        },
        {
          id: '2',
          name: 'Jean Bizimana',
          email: 'jean@example.com',
          phone: '079XXXXXXX',
          location: 'Huye',
          age: 30,
          gender: 'Male',
          premium: true,
          joinDate: '2025-02-20',
          lastActive: '2025-04-04'
        },
        {
          id: '3',
          name: 'Claire Mutoni',
          email: 'claire@example.com',
          phone: '078XXXXXXX',
          location: 'Musanze',
          age: 24,
          gender: 'Female',
          premium: false,
          joinDate: '2025-03-25',
          lastActive: '2025-04-05'
        },
        {
          id: '4',
          name: 'Patrick Niyomugabo',
          email: 'patrick@example.com',
          phone: '079XXXXXXX',
          location: 'Kigali',
          age: 28,
          gender: 'Male',
          premium: false,
          joinDate: '2025-01-10',
          lastActive: '2025-04-03'
        },
        {
          id: '5',
          name: 'Diane Rukundo',
          email: 'diane@example.com',
          phone: '078XXXXXXX',
          location: 'Rubavu',
          age: 27,
          gender: 'Female',
          premium: false,
          joinDate: '2025-03-01',
          lastActive: '2025-04-02'
        }
      ];
      
      setUsers(mockUsers);
      setLoading(false);
    }, 500);
  }, []);
  
  // Filter and search users
  const filteredUsers = users.filter(user => {
    const matchesSearch = user.name.toLowerCase().includes(searchTerm.toLowerCase()) || 
                         user.email.toLowerCase().includes(searchTerm.toLowerCase()) ||
                         user.location.toLowerCase().includes(searchTerm.toLowerCase());
                         
    if (filter === 'all') return matchesSearch;
    if (filter === 'premium') return matchesSearch && user.premium;
    if (filter === 'free') return matchesSearch && !user.premium;
    
    return matchesSearch;
  });
  
  // Handle user actions
  const handleUserAction = (userId: string, action: 'delete' | 'suspend' | 'promote') => {
    // In a real application, this would make an API call
    console.log(`Action: ${action} on user ${userId}`);
    
    if (action === 'delete') {
      setUsers(users.filter(user => user.id !== userId));
    } else if (action === 'promote') {
      setUsers(users.map(user => 
        user.id === userId ? { ...user, premium: true } : user
      ));
    }
  };

  return (
    <div>
      <h1 className="text-3xl font-bold text-gray-900 mb-8">User Management</h1>
      
      {/* Filters and Search */}
      <div className="flex flex-col md:flex-row justify-between items-center mb-6 gap-4">
        <div className="flex space-x-2">
          <button 
            className={`px-4 py-2 rounded-md ${filter === 'all' ? 'bg-blue-600 text-white' : 'bg-gray-200 text-gray-800'}`}
            onClick={() => setFilter('all')}
          >
            All Users
          </button>
          <button 
            className={`px-4 py-2 rounded-md ${filter === 'premium' ? 'bg-blue-600 text-white' : 'bg-gray-200 text-gray-800'}`}
            onClick={() => setFilter('premium')}
          >
            Premium
          </button>
          <button 
            className={`px-4 py-2 rounded-md ${filter === 'free' ? 'bg-blue-600 text-white' : 'bg-gray-200 text-gray-800'}`}
            onClick={() => setFilter('free')}
          >
            Free
          </button>
        </div>
        
        <div className="relative w-full md:w-64">
          <input 
            type="text" 
            placeholder="Search users..." 
            className="w-full px-4 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
            value={searchTerm}
            onChange={(e) => setSearchTerm(e.target.value)}
          />
          <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5 text-gray-400 absolute right-3 top-1/2 transform -translate-y-1/2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
          </svg>
        </div>
      </div>
      
      {/* Users Table */}
      <div className="bg-white rounded-lg shadow-md overflow-hidden">
        {loading ? (
          <div className="p-8 text-center">Loading users...</div>
        ) : (
          <table className="w-full">
            <thead>
              <tr className="bg-gray-50">
                <th className="text-left py-3 px-4 text-sm font-medium text-gray-500">Name</th>
                <th className="text-left py-3 px-4 text-sm font-medium text-gray-500">Email</th>
                <th className="text-left py-3 px-4 text-sm font-medium text-gray-500">Location</th>
                <th className="text-left py-3 px-4 text-sm font-medium text-gray-500">Age</th>
                <th className="text-left py-3 px-4 text-sm font-medium text-gray-500">Status</th>
                <th className="text-left py-3 px-4 text-sm font-medium text-gray-500">Last Active</th>
                <th className="text-left py-3 px-4 text-sm font-medium text-gray-500">Actions</th>
              </tr>
            </thead>
            <tbody>
              {filteredUsers.map(user => (
                <tr key={user.id} className="border-t border-gray-200 hover:bg-gray-50">
                  <td className="py-3 px-4">
                    <div className="font-medium text-gray-900">{user.name}</div>
                    <div className="text-sm text-gray-500">{user.gender}, {user.age}</div>
                  </td>
                  <td className="py-3 px-4">{user.email}</td>
                  <td className="py-3 px-4">{user.location}</td>
                  <td className="py-3 px-4">{user.age}</td>
                  <td className="py-3 px-4">
                    <span className={`px-2 py-1 rounded-full text-xs ${user.premium ? 'bg-green-100 text-green-800' : 'bg-gray-100 text-gray-800'}`}>
                      {user.premium ? 'Premium' : 'Free'}
                    </span>
                  </td>
                  <td className="py-3 px-4">{user.lastActive}</td>
                  <td className="py-3 px-4">
                    <div className="flex space-x-2">
                      <button 
                        className="p-1 text-blue-600 hover:text-blue-800" 
                        title="View Profile"
                        onClick={() => console.log(`View user ${user.id}`)}
                      >
                        <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                          <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                          <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z" />
                        </svg>
                      </button>
                      {!user.premium && (
                        <button 
                          className="p-1 text-green-600 hover:text-green-800" 
                          title="Promote to Premium"
                          onClick={() => handleUserAction(user.id, 'promote')}
                        >
                          <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z" />
                          </svg>
                        </button>
                      )}
                      <button 
                        className="p-1 text-yellow-600 hover:text-yellow-800" 
                        title="Suspend User"
                        onClick={() => handleUserAction(user.id, 'suspend')}
                      >
                        <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                          <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
                        </svg>
                      </button>
                      <button 
                        className="p-1 text-red-600 hover:text-red-800" 
                        title="Delete User"
                        onClick={() => handleUserAction(user.id, 'delete')}
                      >
                        <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                          <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                        </svg>
                      </button>
                    </div>
                  </td>
                </tr>
              ))}
              
              {filteredUsers.length === 0 && (
                <tr>
                  <td colSpan={7} className="py-8 text-center text-gray-500">
                    No users found matching your search criteria.
                  </td>
                </tr>
              )}
            </tbody>
          </table>
        )}
      </div>
    </div>
  );
}
```

### 5. Create API Routes for Admin Dashboard

Create API routes for the admin dashboard in `src/app/api/admin/`:

#### Dashboard Stats API (`src/app/api/admin/stats/route.ts`):

```typescript
import { NextResponse } from 'next/server';
import { connectToDatabase } from '@/lib/db';

export async function GET(request: Request) {
  try {
    const db = await connectToDatabase();
    
    // Get total users count
    const totalUsers = await db.collection('users').countDocuments();
    
    // Get premium users count
    const premiumUsers = await db.collection('users').countDocuments({ premium_status: true });
    
    // Get active users today
    const today = new Date();
    today.setHours(0, 0, 0, 0);
    const activeToday = await db.collection('user_activity').countDocuments({ 
      last_active: { $gte: today } 
    });
    
    // Get new signups today
    const newSignups = await db.collection('users').countDocuments({ 
      created_at: { $gte: today } 
    });
    
    // Get revenue (in cents)
    const thisMonth = new Date();
    thisMonth.setDate(1);
    thisMonth.setHours(0, 0, 0, 0);
    
    const transactions = await db.collection('transactions')
      .find({ created_at: { $gte: thisMonth } })
      .toArray();
      
    const revenue = transactions.reduce((total, tx) => total + tx.amount, 0);
    
    return NextResponse.json({
      totalUsers,
      premiumUsers,
      activeToday,
      newSignups,
      revenue
    });
    
  } catch (error) {
    console.error('Error fetching admin stats:', error);
    return NextResponse.json({ error: 'Internal server error' }, { status: 500 });
  }
}
```

#### Users API (`src/app/api/admin/users/route.ts`):

```typescript
import { NextResponse } from 'next/server';
import { connectToDatabase } from '@/lib/db';

export async function GET(request: Request) {
  try {
    const { searchParams } = new URL(request.url);
    const search = searchParams.get('search') || '';
    const filter = searchParams.get('filter') || 'all';
    const page = parseInt(searchParams.get('page') || '1');
    const limit = parseInt(searchParams.get('limit') || '20');
    const skip = (page - 1) * limit;
    
    const db = await connectToDatabase();
    
    // Build query
    let query: any = {};
    
    if (search) {
      query.$or = [
        { name: { $regex: search, $options: 'i' } },
        { email: { $regex: search, $options: 'i' } },
        { location: { $regex: search, $options: 'i' } }
      ];
    }
    
    if (filter === 'premium') {
      query.premium_status = true;
    } else if (filter === 'free') {
      query.premium_status = false;
    }
    
    // Get users
    const users = await db.collection('users')
      .find(query)
      .sort({ created_at: -1 })
      .skip(skip)
      .limit(limit)
      .toArray();
      
    // Get total count for pagination
    const total = await db.collection('users').countDocuments(query);
    
    // Format user data
    const formattedUsers = users.map(user => ({
      id: user._id.toString(),
      name: user.name,
      email: user.email,
      phone: user.phone,
      location: user.location,
      age: user.age,
      gender: user.gender,
      premium: user.premium_status,
      joinDate: user.created_at.toISOString().split('T')[0],
      lastActive: user.last_active ? user.last_active.toISOString().split('T')[0] : null
    }));
    
    return NextResponse.json({
      users: formattedUsers,
      pagination: {
        total,
        page,
        limit,
        pages: Math.ceil(total / limit)
      }
    });
    
  } catch (error) {
    console.error('Error fetching users:', error);
    return NextResponse.json({ error: 'Internal server error' }, { status: 500 });
  }
}
```

### 6. Secure Admin Routes

Ensure admin routes are properly secured by updating the middleware:

```typescript
// In src/middleware.ts

// Add this to the middleware function
if (request.nextUrl.pathname.startsWith('/admin') && (!payload.isAdmin)) {
  return NextResponse.redirect(new URL('/unauthorized', request.url));
}
```

## Next Steps

After implementing the admin dashboard:

1. Connect the frontend components to the backend API endpoints
2. Implement additional admin features like profile moderation and report handling
3. Add user management functionality (suspend, delete, promote users)
4. Create payment and subscription management interfaces
5. Implement site settings configuration
