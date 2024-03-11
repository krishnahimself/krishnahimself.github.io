+++
title = 'How to use Vue 3 template refs'
description = 'How to use Vue 3 template refs'
date = 2022-08-21T17:21:27+05:45
draft = false
+++

After working with Vue 2 refs and switching to Vue 3 refs can be tricky for the first time as we do not have access to `this.$refs` in Vue3

### Problem:

Recently, I was working on Vue 3 project and using VeeValidate for client-side form validation. I was also using the bootstrap modal for the form. Now, I need to reset the form when someone clicks on add/edit button. There is resetForm method available on the VeeValidate Form component so we need to get the form and call resetForm method.

With Vue 2 we can do the following things:

```vue
// In template
<Form ref="form">...</Form>
// In code
this.$refs.form.resetForm();
```

But this thing will not work with Vue 3 as we do not have access to this.$refs inside our script so we need to follow a different approach.

### Solution:

```vue
import { ref } from 'vue';
// Make sure to declare the variable name as template ref value.
const form = ref(null);
// reset form while calling create user method.
const createUser = () => {
   form.value.resetForm();
};
// In template
<Form ref="form">....</Form>
```

*Note: To obtain the reference with Composition API, we need to declare a ref with the same name*
