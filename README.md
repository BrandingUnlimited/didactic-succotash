Frontend Code Structure

branding-unlimited-frontend/
│── public/                 # Static assets (logos, fonts, images)
│── src/
│   │── components/         # Reusable UI components (buttons, modals, forms)
│   │── pages/              # Next.js pages (Home, Dashboard, Order Tracking)
│   │── styles/             # Global and component-specific styles
│   │── utils/              # Helper functions, API calls
│   │── context/            # React Context API for global state
│   │── hooks/              # Custom React hooks
│   │── services/           # API integrations (backend, AI services)
│   │── assets/             # Icons, branding images
│── .env                    # Environment variables (API keys, database URLs)
│── package.json            # Dependencies and scripts
│── next.config.js          # Next.js configurations
│── tailwind.config.js      # Tailwind CSS configuration
│── tsconfig.json           # TypeScript configuration (if using TypeScript)
│── Dockerfile              # Containerization setup
│── README.md               # Documentation


---

1. Install Dependencies

Run this in your terminal:

npx create-next-app@latest branding-unlimited-frontend
cd branding-unlimited-frontend
npm install tailwindcss axios socket.io-client firebase

This sets up a Next.js project with Tailwind CSS, API handling (axios), real-time updates (socket.io-client), and image storage (firebase).


---

2. Configure Tailwind CSS

Run:

npx tailwindcss init -p

Edit tailwind.config.js:

module.exports = {
  content: ["./src/**/*.{js,ts,jsx,tsx}"],
  theme: {
    extend: {},
  },
  plugins: [],
};

In styles/globals.css, add:

@tailwind base;
@tailwind components;
@tailwind utilities;


---

3. Home Page (pages/index.js)

import Head from 'next/head';
import Link from 'next/link';

export default function Home() {
  return (
    <div className="flex flex-col items-center justify-center min-h-screen bg-gray-100">
      <Head>
        <title>Branding Unlimited KE</title>
      </Head>
      <h1 className="text-4xl font-bold">Welcome to Branding Unlimited KE</h1>
      <p className="mt-4 text-lg text-gray-600">Your AI-powered branding partner.</p>
      <Link href="/design">
        <button className="mt-6 px-6 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700">
          Start Designing
        </button>
      </Link>
    </div>
  );
}


---

4. AI Design Editor (pages/design.js)

import { useState } from "react";

export default function DesignPage() {
  const [design, setDesign] = useState("");

  const generateAI = async () => {
    const response = await fetch("/api/generate", { method: "POST" });
    const data = await response.json();
    setDesign(data.image);
  };

  return (
    <div className="flex flex-col items-center min-h-screen p-6 bg-white">
      <h1 className="text-3xl font-bold">AI-Powered Design</h1>
      <button onClick={generateAI} className="mt-4 px-6 py-3 bg-green-600 text-white rounded-lg">
        Generate Design
      </button>
      {design && <img src={design} alt="AI Design" className="mt-4 w-80 h-80" />}
    </div>
  );
}


---

5. WebSocket for Real-Time Updates (utils/socket.js)

import { io } from "socket.io-client";

const socket = io("http://localhost:4000"); // Replace with your backend URL

export default socket;


---

6. Order Tracking with Live Updates (pages/track.js)

import { useEffect, useState } from "react";
import socket from "../utils/socket";

export default function OrderTracking() {
  const [status, setStatus] = useState("Processing...");

  useEffect(() => {
    socket.on("orderUpdate", (data) => {
      setStatus(data.status);
    });

    return () => {
      socket.off("orderUpdate");
    };
  }, []);

  return (
    <div className="flex flex-col items-center min-h-screen p-6">
      <h1 className="text-3xl font-bold">Order Tracking</h1>
      <p className="mt-4 text-lg">Current Status: {status}</p>
    </div>
  );
}


---

7. Firebase Image Upload (services/firebase.js)

import { initializeApp } from "firebase/app";
import { getStorage, ref, uploadBytes, getDownloadURL } from "firebase/storage";

const firebaseConfig = {
  apiKey: "YOUR_FIREBASE_API_KEY",
  authDomain: "your-project.firebaseapp.com",
  projectId: "your-project",
  storageBucket: "your-project.appspot.com",
  messagingSenderId: "your-messaging-id",
  appId: "your-app-id",
};

const app = initializeApp(firebaseConfig);
const storage = getStorage(app);

export const uploadImage = async (file) => {
  const storageRef = ref(storage, `uploads/${file.name}`);
  await uploadBytes(storageRef, file);
  return getDownloadURL(storageRef);
};


---

8. Vendor Portal (pages/vendor.js)

import { useState, useEffect } from "react";

export default function VendorDashboard() {
  const [orders, setOrders] = useState([]);

  useEffect(() => {
    fetch("/api/orders")
      .then((res) => res.json())
      .then((data) => setOrders(data));
  }, []);

  return (
    <div className="p-6">
      <h1 className="text-3xl font-bold">Vendor Dashboard</h1>
      <ul className="mt-4">
        {orders.map((order) => (
          <li key={order.id} className="p-4 border-b">
            {order.customer} - {order.status}
          </li>
        ))}
      </ul>
    </div>
  );
}


---