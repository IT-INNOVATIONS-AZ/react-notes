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

## add language to project

```bash
yarn add react-i18next i18next i18next-browser-languagedetector
```

## az.json

```json
{
  "app": {
    "yes": "Bəli",
    "no": "Xeyr",
    "unknown": "Naməlum"
  },
  "errorPage": {
    "error": "Xəta",
    "title": "Səhifə tapılmadı 🕵🏻‍♀️",
    "description": "Oops! 😖 axtardığınız səhifə tapılmadı.",
    "goBack": "Ana səhifəyə qayıt"
  },
  "dashboard": {},
  "cardMedal": {
    "title": "Təbriklər 🎉  {{name}}",
    "description": "Ay ərzindən <b>{{count}}</b> coin qazanıbsınız.",
    "button": "Coinlərim 💰"
  },
  "cardStatistics": {
    "title": "Statistika",
    "updated": "{{date}} əvvəl yeniləndi"
  },
  "cardComment": {
    "title": "Rəy",
    "updated": "{{date}} əvvəl yeniləndi"
  },
  "homeworkTracker": {
    "title": "Ev Tapşırıq"
  }
}
```

## en.json

```js
{
	"app": {
		"yes": "Yes",
		"no": "No",
		"to": "to",
		"unknown": "Unknown"
	},
	"errorPage": {
		"error": "Error",
		"title": "Page Not Found 🕵🏻‍♀️",
		"description": "Oops! 😖 the page you are looking for is not found.",
		"goBack": "Back to home"
	},
	"dashboard": {},
	"cardMedal": {
		"title": "Congratulation 🎉  {{name}}",
		"description": "You have earned <b>{{count}}</b> coins during the month.",
		"button": "My Coins 💰"
	}
}
```

```js
export enum LANGUAGES {
	AZ = "az",
	EN = "en"
}

export const LanguageList = [
	{
		key: LANGUAGES.AZ,
		value: "Azərbaycan dili",
	},
	{
		key: LANGUAGES.EN,
		value: "English",
	}
];
```

### add to main.(jsx|tsx) to initalize i18n

## config/i18n.js

```js
// ** I18n Imports
import i18n from "i18next";
import Backend from "i18next-xhr-backend";
import { initReactI18next } from "react-i18next";
import LanguageDetector from "i18next-browser-languagedetector";
import { LANGUAGES } from "@lang/languages";

const lang = {
  default: "az",
  set(lang) {
    return (this.default = lang);
  },
};

export const cangeLang = (lang) => lang.set(lang);

const az = require("../lang/az.json");
const en = require("../lang/en.json");

export const initI18n = (): void => {
  i18n
    .use(Backend)
    .use(LanguageDetector)
    .use(initReactI18next)
    .init({
      resources: { en, az },
      lng: lang.default,
      fallbackLng: LANGUAGES.EN,
      debug: false,
      keySeparator: false,
      react: {
        useSuspense: true,
      },
      interpolation: {
        escapeValue: false,
        formatSeparator: ",",
      },
    });
};
export default i18n;
```

```jsx
import { initI18n } from "@configs/i18n";

// other imports

initI18n();
```

#### usage in page

```jsx
import { useTranslation } from "react-i18next";

const functName = () => {
  const [t] = useTranslation("errorPage"); // -> errorPage is the key of json object

  return <h1 className="mb-4">{t("title")}</h1>; // -> "Page Not Found 🕵🏻‍♀️",
};
```

## Routing

```bash
yarn add react-router-dom
```

#### adding browser router to main.(jsx|tsx)

```jsx
import { BrowserRouter } from 'react-router-dom';

export default function App() {
  return (
    <BrowserRouter>
      {/* routes go here, as children */}
    </BrowserRouter>
  );
}
```

### Example of routing

```jsx
// About Page

function About() {
  return <>about</>   
}

// App.jsx

import { BrowserRouter } from 'react-router-dom';

export default function App() {
  return (
    <BrowserRouter>
      <Route path="/about" component={About} />
    </BrowserRouter>
  );
}


```


## Global state to get data from everywhere

```bash
yarn add zustand
```

```jsx
import { create } from 'zustand'

export const useStore = create((set) => ({
  count: 1,
  inc: () => set((state) => ({ count: state.count + 1 })),
}))

function Counter() {
  const { count, inc } = useStore()
  return (
    <div>
      <span>{count}</span>
      <button onClick={inc}>one up</button>
    </div>
  )
}
```