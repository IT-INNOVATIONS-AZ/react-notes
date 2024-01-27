# Create react app

```bash
yarn create vite
(or)
npm create vite@latest
 ```

 - Step 1. Project name
 - Step 2. React, JavaScript/Typescript + SWC

```bash
yarn add react-router-dom axios clsx sass-loader react-query 
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
│   │   │   └── http-client.js
│   │   └── user.js
│   ├── assets
│   │   ├── fonts
│   │   ├── img
│   │   └── style
│   ├── components
│   ├── hooks
│   ├── layout
│   ├── main.jsx
│   ├── pages
│   │   └── Home.js
│   └── store
```

## http-client.js
```jsx
import axios from 'axios';

const Axios = axios.create({
  baseURL: 'http://localhost:8000',
  timeout: 5000000,
  headers: {
    'Content-Type': 'application/json',
  },
});
// Change request data/error here
Axios.interceptors.request.use((config: any) => {
  const token = localStorage.getItem('token');
  config.headers = {
    ...config.headers,
    Authorization: `${token ? token : ''}`,
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
      (error.response && error.response.data.message === 'NOT_AUTHORIZED')
    ) {
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  },
);

export class HttpClient {
  static async get<T>(url: string, params?: unknown) {
    const response = await Axios.get<T>(url, { params });
    return response.data;
  }

  static async post<T>(url: string, data: unknown, options?: any) {
    const response = await Axios.post<T>(url, data, options);
    return response.data;
  }

  static async put<T>(url: string, data: unknown) {
    const response = await Axios.put<T>(url, data);
    return response.data;
  }
  static async patch<T>(url: string, data: unknown) {
    const response = await Axios.patch<T>(url, data);
    return response.data;
  }

  static async delete<T>(url: string) {
    const response = await Axios.delete<T>(url);
    return response.data;
  }
}
```

## endpoints.js

