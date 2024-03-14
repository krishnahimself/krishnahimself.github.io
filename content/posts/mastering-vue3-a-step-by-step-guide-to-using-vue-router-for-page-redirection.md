+++
title = 'Mastering Vue3: A Step-by-Step Guide to Using vue-router for Page Redirection'
description = 'Mastering Vue3: A Step-by-Step Guide to Using vue-router for Page Redirection'
date = 2023-02-25T17:21:27+05:45
draft = false
+++

First of all we need to import the function called `useRouter` from the `vue-router` plugin

```vue
import { useRouter} from 'vue-router';
```

The `useRouter` function provides us with a convenient way to access the Vue router instance and perform redirects to different pages. Let's assign the result of `useRouter` function to the constant called `router`

```vue
const router = useRouter();
```

Finally, we can use this `router` to navigate between pages. Example:

```vue
const handleSubmit = () => {
    axios.post('/api/appointments/create', form)
    .then((response) => {
        router.push('/admin/appointments');
    });
};
```
