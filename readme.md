# Create react app

```bash
yarn create vite
(or)
npm create vite@latest
```

- Step 1. Project name
- Step 2. React, JavaScript/Typescript + SWC

```bash
yarn add react-router-dom axios clsx sass-loader react-query react-error-boundary @loadable/component react-toastify
(npm install = yarn add )
```

```bash
yarn dev (npm run dev)
```

Folder Structure

```
src
│   ├── api
│   │   ├── config
│   │   │   ├── endpoints.js
│   │   │   ├── http-client.js
│   │   │   ├── query-provider.js
│   │   │   └── index.js
│   │   └── user.js
│   ├── assets
│   │   ├── fonts
│   │   ├── img
│   │   └── style
│   │   ├── └── theme.scss
│   ├── components
│   ├── hooks
│   ├── layout
│   ├── main.jsx
│   ├── pages
│   │   └── App/Home.js
│   │   └── App/routes.js
│   │   └── App/index.js
│   │   └── Member/Dashboard.js
│   │   └── router.js
│   └── store
```

## http-client.js

```jsx
import axios from "axios";

const Axios = axios.create({
  baseURL: "http://localhost:8000",
  timeout: 5000000,
  headers: {
    "Content-Type": "application/json",
  },
});
// Change request data/error here
Axios.interceptors.request.use((config: any) => {
  const token = localStorage.getItem("token");
  config.headers = {
    ...config.headers,
    Authorization: `${token ? token : ""}`,
  };
  return config;
});

// Change response data/error here
Axios.interceptors.response.use(
  (response) => response,
  (error) => {
    if (
      (error.response && error.response.status === 401) ||
      (error.response && error.response.status === 403) ||
      (error.response && error.response.data.message === "NOT_AUTHORIZED")
    ) {
      localStorage.removeItem("token");
      window.location.href = "/login";
    }
    return Promise.reject(error);
  }
);

export class HttpClient {
  static async get<T>(url: string, params?: unknown) {
    const response = (await Axios.get) < T > (url, { params });
    return response.data;
  }

  static async post<T>(url: string, data: unknown, options?: any) {
    const response = (await Axios.post) < T > (url, data, options);
    return response.data;
  }

  static async put<T>(url: string, data: unknown) {
    const response = (await Axios.put) < T > (url, data);
    return response.data;
  }
  static async patch<T>(url: string, data: unknown) {
    const response = (await Axios.patch) < T > (url, data);
    return response.data;
  }

  static async delete<T>(url: string) {
    const response = (await Axios.delete) < T > url;
    return response.data;
  }
}
```

## endpoints.js

Your backend endpoints NestJs(controllers), Fastapi (Routes)

```jsx
export const API_ENDPOINTS = {
  COURSE: "/course",
};
```

## api/config/index.js

```js
import { API_ENDPOINTS } from "./api-endpoints";
import { HttpClient } from "./http-client";

class Client {
  course = {
    getAll: () => HttpClient.get(API_ENDPOINTS.COURSE),
    get: (id: number) => HttpClient.get(`${API_ENDPOINTS.COURSE}/${id}`),
    create: (data: any) => HttpClient.post(API_ENDPOINTS.COURSE, data),
    update: (id: number, data: any) =>
      HttpClient.patch(`${API_ENDPOINTS.COURSE}/${id}`, data),
    delete: (id: number) => HttpClient.delete(`${API_ENDPOINTS.COURSE}/${id}`),
  };
  attachment = {
    getAll: () => HttpClient.get(API_ENDPOINTS.ATTACHMENT),
    get: (id: number) => HttpClient.get(`${API_ENDPOINTS.ATTACHMENT}/${id}`),
    create: (formData: FormData) =>
      HttpClient.post(API_ENDPOINTS.ATTACHMENT, formData, {
        headers: {
          "Content-Type": "multipart/form-data",
        },
      }),
    createMultiple: (formData: FormData) =>
      HttpClient.post(`${API_ENDPOINTS.ATTACHMENT}/multiple`, formData, {
        headers: {
          "Content-Type": "multipart/form-data",
        },
      }),
    update: (id: number, data: any) =>
      HttpClient.put(`${API_ENDPOINTS.ATTACHMENT}/${id}`, data),
    delete: (id: number) =>
      HttpClient.delete(`${API_ENDPOINTS.ATTACHMENT}/${id}`),
  };
}

export default new Client();
```

## api/config/query-provider.js

```js
import { useState } from "react";
import { QueryClient, QueryClientProvider } from "react-query";
import { ReactQueryDevtools } from "react-query/devtools";
import React from "react";

interface QueryProviderProps {
  pageProps: any;
}

export default function QueryProvider({
  pageProps,
  children,
}: React.PropsWithChildren<QueryProviderProps>) {
  const [queryClient] = useState(() => new QueryClient());

  return (
    <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>
  );
}
```

## src/main.jsx

```jsx
import React, { Suspense } from 'react';
import { BrowserRouter } from 'react-router-dom';
import ReactDOM from 'react-dom/client';
import { ErrorBoundary } from 'react-error-boundary';
import { ToastContainer } from 'react-toastify';
import loadable from '@loadable/component';
import QueryProvider from './api/client/query-provider';
import './assets/style/theme.scss';


const MainApp = loadable(() => import('./pages'));

const container = document.getElementById('app') as HTMLElement;

ReactDOM.createRoot(container).render(
  <>
    <Suspense fallback={<div />}>
      <ToastContainer />
      <ErrorBoundary fallbackRender={({ error }) => <>{console.error(error)}</>}>
           <BrowserRouter>
            <QueryProvider>
              <MainApp />
            </QueryProvider>
          </BrowserRouter>
       </ErrorBoundary>
    </Suspense>
  </>,
);

```

## pages/App

```jsx

```

## pages/Member
